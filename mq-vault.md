## PART 1: Installing IBM MQ on a Server
Prerequisites
A supported OS (e.g., RHEL, Ubuntu, SUSE)
At least 2 GB RAM and 2 CPUs
OpenSSL installed
Root or sudo privileges

###  Install IBM MQ
Download IBM MQ

You can get IBM MQ from IBM Passport Advantage or use a trial from IBM MQ download page.

For Linux, it's usually a .tar.gz package.

```sh
tar -xvzf MQ_Install.tar.gz
cd MQServer
sudo ./mqlicense.sh -accept
sudo ./installmq
```

### Create MQ User and Queue Manager
```sh
sudo useradd mqm
sudo su - mqm
crtmqm QM1
strmqm QM1
```

## PART 2: SSL/TLS Configuration for IBM MQ with Vault

```sh
## Enable the PKI engine
vault secrets enable pki
vault secrets tune -max-lease-ttl=87600h pki
## Generate a root or intermediate CA
vault write pki/root/generate/internal \
    common_name="example.com" \
    ttl=87600h
## Configure URLs
vault write pki/config/urls \
    issuing_certificates="http://vault.example.com:8200/v1/pki/ca" \
    crl_distribution_points="http://vault.example.com:8200/v1/pki/crl"
## Create a role for MQ certificates
vault write pki/roles/mq-server \
    allowed_domains="mq.example.com" \
    allow_subdomains=true \
    generate_lease=true \
    max_ttl="72h"
```

```sh
## Step 2: Request and Install Certificates Request a certificate from Vault
### Vault will return: certificate, issuing_ca, private_key
vault write pki/issue/mq-server \
    common_name="mq1.mq.example.com" \
    ttl="72h"
## Save them to files on the MQ server:
echo "<certificate>" > /var/mqm/ssl/mqcert.pem
echo "<issuing_ca>" > /var/mqm/ssl/ca.pem
echo "<private_key>" > /var/mqm/ssl/key.pem

## Convert to PKCS#12 and CMS format (MQ expects .kdb sometimes)
openssl pkcs12 -export -in mqcert.pem -inkey key.pem -certfile ca.pem -out mq.p12 -name mqcert
## Then use gskit to import into a CMS key database if needed:
gsk8capicmd_64 -keydb -create -db mq.kdb -pw changeit -type cms -stash
gsk8capicmd_64 -cert -add -db mq.kdb -pw changeit -label mqcert -file mq.p12 -format pkcs12
```

```sh
## Step 3: Configure MQ to Use TLS Set SSL configuration in qm.ini
SSL:
  KeyRepository=/var/mqm/ssl/mq.kdb

## Define CipherSpec on listener
ALTER QMGR SSLKEYR('/var/mqm/ssl/mq') SSLCIPH('TLS_RSA_WITH_AES_256_CBC_SHA256')
## Restart MQ Queue Manager
endmqm QM1
strmqm QM1
## Test the TLS listener
DISPLAY LISTENER(SYSTEM.LISTENER.TCP) TRPTYPE
DISPLAY QMGR SSLKEYR SSLCIPH
```

## PART 3: Automating Certificate Renewal with Vault
```sh
### You can automate the renewal using a cronjob or systemd timer: - Example script:


#!/bin/bash

VAULT_TOKEN=<your-token>
CERT_DIR=/var/mqm/ssl

vault write -format=json pki/issue/mq-server common_name="mq1.mq.example.com" ttl="72h" > $CERT_DIR/cert.json

jq -r '.data.certificate' $CERT_DIR/cert.json > $CERT_DIR/mqcert.pem
jq -r '.data.issuing_ca' $CERT_DIR/cert.json > $CERT_DIR/ca.pem
jq -r '.data.private_key' $CERT_DIR/cert.json > $CERT_DIR/key.pem

openssl pkcs12 -export -in $CERT_DIR/mqcert.pem -inkey $CERT_DIR/key.pem -certfile $CERT_DIR/ca.pem \
  -out $CERT_DIR/mq.p12 -password pass:changeit -name mqcert

# Import into MQ's .kdb if needed
gsk8capicmd_64 -cert -delete -db $CERT_DIR/mq.kdb -pw changeit -label mqcert
gsk8capicmd_64 -cert -add -db $CERT_DIR/mq.kdb -pw changeit -label mqcert -file $CERT_DIR/mq.p12 -format pkcs12
```



 
