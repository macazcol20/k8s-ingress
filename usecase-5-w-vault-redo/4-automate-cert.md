## Step 4: Automate with Consul Template 
Issue a TLS certificate for grafana.pixiescloud.com using Vault
Configure Grafana to use the certificate
Later, automate renewal using Consul Template or Vault Agent

## 0: install Consul - https://releases.hashicorp.com/consul-template/
```sh
wget https://releases.hashicorp.com/consul-template/0.40.0/consul-template_0.40.0_linux_amd64.zip
unzip -o consul-template_0.40.0_linux_amd64.zip
sudo mv consul-template /usr/local/bin/
consul-template --version
```

Create the sesources in this tree format:


└── consul-template
    ├── grafana.hcl
    └── templates
        ├── grafana.ca.tpl
        ├── grafana.crt.tpl
        └── grafana.key.tpl

## Step 1: Create this directory:
- after creating files.  vi and add the code contents in files

```sh
sudo mkdir -p /etc/consul-template/templates

##  Grant access to these paths
sudo chown cafanwii:cafanwii /etc/consul-template
sudo chown cafanwii:cafanwii /etc/consul-template/*
sudo chmod 700 /etc/consul-template/*
sudo chmod 700 /etc/consul-template/

sudo chown cafanwii:cafanwii /etc/consul-template/templates
sudo chown cafanwii:cafanwii /etc/consul-template/templates/
sudo chown cafanwii:cafanwii /etc/consul-template/templates/*
sudo chmod 700 /etc/consul-template/templates/
```

## Create the files needed by consul 
```sh
cd /etc/consul-template/templates
touch grafana.ca.tpl grafana.crt.tpl grafana.key.tpl

cd /etc/consul-template
touch grafana.hcl
```

<!-- ## OPTIONAL: to avoid consul asking for password
```sh
## Edit the sudoers file safely:
sudo visudo
## Add this line to the bottom of the file:
cafanwii ALL=(ALL) NOPASSWD: /bin/systemctl restart grafana-server
``` -->

##  Execute the consul command
```sh
consul-template -config /etc/consul-template/grafana.hcl   ## OR
consul-template -config /etc/consul-template/grafana.hcl -once -log-level=debug
```

## now lets make it free flowing
```sh
sudo nano /etc/systemd/system/consul-template.service


#################################################################
[Unit]
Description=Consul Template for Grafana TLS
After=network.target

[Service]
User=cafanwii
ExecStart=/usr/local/bin/consul-template -config=/etc/consul-template/grafana.hcl
Restart=on-failure
Environment=VAULT_ADDR=https://vault.pixiescloud.com

[Install]
WantedBy=multi-user.target
###########################################################################


## Then:
sudo systemctl daemon-reload
sudo systemctl enable consul-template
sudo systemctl start consul-template
systemctl status consul-template

sudo systemctl restart nginx.service
sudo systemctl restart grafana-server
```

## Check cert renewal later in about  1 hr
```sh
openssl x509 -in /etc/grafana/grafana.crt -noout -enddate
```





















<!-- ## Automate TLS Cert Renewal for Grafana
```sh
## 1. Create Template Files for Consul Template


<!-- # What to expect
Build your own internal certificate authority (CA) setup, just like Let's Encrypt or DigiCert — but private to your organization.
I will be using my own internal CA (self-signed or private intermediate) for my existing domain that i own - kapistiogroup.com

| **How You'll Use This Setup**     | **Cert Used**                                      |
|-----------------------------------|----------------------------------------------------|
| Sign Vault, NGINX certs           | Use `intermediateCA.key` to sign server certs      |
| Trust the CA in clients           | Install `rootCA.pem` on dev laptops, systems       |
| Serve HTTPS via Vault             | Use `ca-chain.pem` as the CA chain in TLS config   |
| Rotate certs                      | Use Intermediate to generate new certs without touching Root |

***Use OpenSSL to Create***:
  - A self-signed Root CA
  - An Intermediate CA signed by your Root CA
  - Use the Intermediate CA to issue certs (for Vault, services, etc.)

## STEP 1: Create Root CA + Intermediate CA 
```sh
##  1. Create Directory Structure
mkdir -p kapistio-ca/root
mkdir -p kapistio-ca/intermediate

## 2. Create Root CA [ or if you already have your root, we have to move into Vault issuer]
cd kapistio-ca/root
### Generate private key
openssl genrsa -out rootCA.key 4096  

# Create Root CA cert
cat > root-openssl.cnf <<EOF
[ req ]
default_bits       = 4096
distinguished_name = dn
x509_extensions    = v3_ca
prompt             = no

days = 3650

[ dn ]
C  = US
ST = FL
L  = Orlando
O  = Kapistio
OU = kapistio org
CN = Testing Root

[ v3_ca ]
subjectKeyIdentifier=hash
authorityKeyIdentifier=keyid:always,issuer
basicConstraints = critical,CA:true,pathlen:2
keyUsage = critical, digitalSignature, cRLSign, keyCertSign
EOF

openssl req -x509 -new -key rootCA.key -sha256 -days 3650 \
  -out rootCA.key -config root-openssl.cnf -extensions v3_ca

openssl x509 -in rootCA.key -noout -subject -issuer

## 3. Create Intermediate CA CSR (Certificate Signing Request) W Vault
- Instead of trying to import your OpenSSL-generated key, we’ll create the intermediate key inside Vault, then sign it externally using your Root CA.
- Using your internally signed Intermediate CA
- Issuing valid certs for vault.kapistiogroup.com
- With working TLS and signed by your custom CA chain

## 1. To login my prod vault server
export VAULT_ADDR=https://vault.pixiescloud.com/
export VAULT_TOKEN=hvs.xxxx
vault login $VAULT_TOKEN

## 2. Enable Vault PKI for ICA (Intermediate CA)
# vault secrets disable pki_int
vault secrets enable -path=pki_int pki
vault secrets tune -max-lease-ttl=43800h pki_int



## 3. Generate Intermediate CSR in Vault:

vault write -format=json pki_int/intermediate/generate/internal \
  common_name="Kapistiogroup Intermediate CA" \
  ttl="43800h" \
  key_type=rsa \
  key_bits=4096 \
  issuer_name="kapistio-ica" \
  | jq -r '.data.csr' > vault_intermediate.csr

## 4. Sign that CSR with your [existing] Root CA:
openssl x509 -req \
  -in vault_intermediate.csr \
  -CA ../root/rootCA.pem \
  -CAkey ../root/rootCA.key \
  -CAcreateserial \
  -out signed_intermediate_cert.pem \
  -days 1825 -sha256 \
  -extfile ../root/openssl-intermediate.cnf \
  -extensions v3_ca


## 5. Import the signed intermediate cert into Vault:
vault write pki_int/intermediate/set-signed certificate=@signed_intermediate_cert.pem



## 6. Configure urls:
vault write pki_int/config/urls \
  issuing_certificates="https://vault.kapistiogroup.com/v1/pki_int/ca" \
  crl_distribution_points="https://vault.kapistiogroup.com/v1/pki_int/crl"


## 7. Create a role:
vault write pki_int/roles/kapistiogroup-dot-com \
  allowed_domains="kapistiogroup.com" \
  allow_subdomains=true \
  allow_any_name=true \
  max_ttl="72h"

## Issue a certificate:
vault write pki_int/issue/kapistiogroup-dot-com \
  common_name="vault.kapistiogroup.com" \
  ttl="2h"

```

## STEP 3: INstall vault following the install-vault.md file:

```sh
When you access vault url on vault/kapostiogroup.com, the certificate is:

- Issued by: Kapistiogroup Intermediate CA
- Signed for: vault.kapistiogroup.com
- The Certificate is Valid and working (browser or curl sees it); which means:
  - HTTPS encryption	          --> Enabled (TLS with your own certs)
  - Certificate validation	    --> Only trusted by you/your org (not public browsers)
  - Issued by trusted CA	      --> Not a public CA like Let's Encrypt or DigiCert
  - Internal PKI best practice  --> Yes, this is how internal CAs work for services like 
```

### OPTIONAL: To Make This "Trusted"
To get rid of browser warnings and make your private CA trusted:

***Option 1: Trusted by Your Org Only (recommended for internal use)***

- Install rootCA.pem on a Developer laptops (macOS: Keychain, Ubuntu: /usr/local/share/ca-certificates)
- Then Next: Now they trust anything signed by your internal CA

***Option 2: Get a Public TLS Cert***

- Use Let’s Encrypt or Buy from a Public CA
- Only needed if you plan to expose Vault to the internet or public clients

## STEP 4: Automate cert with Vault


## STEP 5: Automating Consul templete
/etc/consul-template/
├── vault.hcl                  <-- consul-template config
├── templates/
│   ├── vault-cert.tpl         <-- template for fullchain cert
│   ├── vault-key.tpl          <-- template for private key
│   └── vault-ca.tpl           <-- template for root/intermediate CA

```sh
## 1. Upload the certs you will want to update to path or use existing path of choice:
/etc/ssl/vault/
## for my example I have the following uploaded
/etc/ssl/vault/vault.kapistiogroup.com.crt
/etc/ssl/vault/vault.kapistiogroup.com.key
/etc/ssl/vault/ca-chain.pem

## 2.  Start consul-template as a service - Quick test first:
sudo consul-template -config=/etc/consul-template/vault.hcl -once

## IF It works, then lets  a systemd service:

## 3. In /etc/systemd/system/ , create consul-template.service
# - /etc/systemd/system/consul-template.service
# - add below content in the file

[Unit]
Description=Consul Template
After=network-online.target
Wants=network-online.target

[Service]
ExecStart=/usr/local/bin/consul-template -config=/etc/consul-template/vault.hcl
Restart=on-failure

[Install]
WantedBy=multi-user.target

## 4. Then Enable and start:
sudo systemctl daemon-reexec
sudo systemctl enable consul-template
sudo systemctl start consul-template
```

vault write pki_int/issue/kapistiogroup-dot-com \
  common_name="vault.kapistiogroup.com" \
  ttl="2h" --> -->
