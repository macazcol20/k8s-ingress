
export VAULT_ADDR=https://vault.pixiescloud.com/
export VAULT_TOKEN=hvs.xxxx
vault login $VAULT_TOKEN

## Step 0: Configure DNS or Local Resolution
- Or add a DNS A record in your domain/Route53/etc.

  A-Record --> 10.0.0.92 grafana.pixiescloud.com

## STEP 1: Create Root CA + Intermediate CA 
```sh
## 1. Create Directory Structure
mkdir -p kapistio-ca/root
mkdir -p kapistio-ca/intermediate
mkdir -p kapistio-ca/grafana-certs

## 2. Create Root CA [ or if you already have your root, we have to move into Vault issuer]
cd kapistio-ca/root

# 3. Create Root CA cert
cat > root-openssl.cnf <<EOF
[ req ]
default_bits       = 4096
distinguished_name = dn
x509_extensions    = v3_ca
prompt             = no

[ dn ]
C  = US
ST = Florida
L  = Miami
O  = PixiesCloud
OU = Security
CN = PixiesCloud Root CA

[ v3_ca ]
subjectKeyIdentifier=hash
authorityKeyIdentifier=keyid:always,issuer
basicConstraints = critical,CA:true,pathlen:2
keyUsage = critical, digitalSignature, cRLSign, keyCertSign
EOF

## 4. Generate Root Key and Certificate
openssl req -x509 -new -nodes \
  -keyout rootCA.key.pem \
  -out rootCA.cert.pem \
  -days 20 \
  -config root-openssl.cnf


## 5. Create Intermediate CA CSR (Certificate Signing Request) W Vault
- Instead of trying to import your OpenSSL-generated key, we’ll create the intermediate key inside Vault, then sign it externally using your Root CA.
- Using your internally signed Intermediate CA
- Issuing valid certs for vault.kapistiogroup.com
- With working TLS and signed by your custom CA chain
```

## STEP 2. Enable Vault PKI for ICA (Intermediate CA)
```sh
## 1. To login my prod vault server
export VAULT_ADDR=https://vault.pixiescloud.com/
export VAULT_TOKEN=hvs.xxxx
vault login $VAULT_TOKEN

## 2. vault secrets disable pki_int
vault secrets enable -path=pki_int pki
vault secrets tune -max-lease-ttl=43800h pki_int

## 3. Generate Intermediate CSR in Vault from the root directory:
vault write -format=json pki_int/intermediate/generate/internal \
  common_name="PixiesCloud Intermediate CA" \
  ttl="43800h" > ../intermediate/intermediate_csr.json

jq -r '.data.csr' ../intermediate/intermediate_csr.json > ../intermediate/intermediate.csr.pem


## 4. Sign that CSR with your [existing] Root CA:
cd ..
cd intermediate/

## 5. Create intermediate-openssl.cnf
cat > intermediate-openssl.cnf <<EOF
[ v3_ca ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid,issuer
basicConstraints = critical,CA:TRUE,pathlen:0
keyUsage = critical, keyCertSign, cRLSign
EOF

## 6. Sign ICA CSR with Root
openssl x509 -req \
  -in intermediate.csr.pem \
  -CA ../root/rootCA.cert.pem \
  -CAkey ../root/rootCA.key.pem \
  -CAcreateserial \
  -out intermediate.cert.pem \
  -days 2 \
  -sha256 \
  -extfile intermediate-openssl.cnf \
  -extensions v3_ca

## 7. Create Chain and Import into Vault Import into Vault: You're telling Vault: “Here’s the trusted Intermediate + Root CA chain
cat intermediate.cert.pem ../root/rootCA.cert.pem > intermediate-chain.pem

vault write pki_int/config/ca pem_bundle=@intermediate-chain.pem

## 8. configure pki_int/config/urls
vault write pki_int/config/urls \
    issuing_certificates="https://vault.pixiescloud.com/v1/pki_int/ca" \
    crl_distribution_points="https://vault.pixiescloud.com/v1/pki_int/crl"

## 9. Create a role: I want daily Rotation

# vault write pki_int/roles/grafana-role \
#   allowed_domains="pixiescloud.com" \
#   allow_subdomains=true \
#   enforce_hostnames=true \
#   max_ttl="72h"

vault write pki_int/roles/grafana-role \
    allowed_domains="pixiescloud.com" \
    allow_subdomains=true \
    enforce_hostnames=true \
    ttl="2h" \
    max_ttl="4h"


## 10: Issue Cert for Grafana
cd ..
cd grafana-certs/

vault write -format=json pki_int/issue/grafana-role \
  common_name="grafana.pixiescloud.com" ttl="1h" > grafana-cert.json

jq -r '.data.certificate' grafana-cert.json > grafana.crt
jq -r '.data.private_key' grafana-cert.json > grafana.key
jq -r '.data.issuing_ca' grafana-cert.json > ca.crt

## NOTE: If you have access to the application server, perform steps 11, 12, 13 on the application server.   

## OR Continue 12 and 13 doing this on the Vault server

# ## 11: copy the certs to my grafana server for TLS
# sudo scp grafana.crt grafana.key ca.crt cafanwii@10.0.0.92:/tmp/

# ## 12. Grant access to these paths
# sudo chown cafanwii:cafanwii /etc/grafana
# sudo chown cafanwii:cafanwii /etc/grafana/*
# sudo chmod 400 /etc/grafana/*

# sudo chown cafanwii:cafanwii /etc/consul-template
# sudo chown cafanwii:cafanwii /etc/consul-template/*
# sudo chmod 400 /etc/consul-template/*

# sudo chown cafanwii:cafanwii /etc/consul-template/templates
# sudo chown cafanwii:cafanwii /etc/consul-template/templates/*
# sudo chmod 400 /etc/consul-template/templates/*

# ## 13. Copy the certs to a path in Vault for Consul use
# cp -r grafana.crt grafana.key ca.crt /etc/grafana


```

### NEXT... Move to the Grafana server and configure the rest