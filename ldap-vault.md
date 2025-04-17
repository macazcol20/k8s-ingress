## install docker
sudo apt update
sudo apt install docker.io -y
sudo systemctl enable docker
sudo systemctl start docker
docker version
sudo usermod -aG docker $USER
newgrp docker


## Install vault from release
```sh
curl --silent -Lo /tmp/vault.zip https://releases.hashicorp.com/vault/1.19.0/vault_1.19.0_linux_amd64.zip

cd /tmp
unzip vault.zip
sudo mv vault /usr/local/bin
vault version

export VAULT_ADDR=https://vault.pixiescloud.com/
export VAULT_TOKEN=hvs.
vault login $VAULT_TOKEN
```

## STEPS
```sh
## STEP 0: Create a Custom Docker Network
docker network create vaultlab

## STEP 1: Run OpenLDAP - This is the actual LDAP server.
### - OpenLDAP is now available at ldap://openldap:389 inside the network

docker run --name openldap \
  --network vaultlab \
  --detach \
  --env LDAP_ORGANISATION="bbbb Inc." \
  --env LDAP_DOMAIN="bbbb.com" \
  --env LDAP_ADMIN_PASSWORD="admin" \
  osixia/openldap:1.5.0

## STEP 2: Run phpLDAPadmin (his is a web-based UI tool to manage the LDAP server - to add/edit users)
### - Access GUI: http://localhost:8081
### - Login DN: cn=admin,dc=bbbb,dc=com
### - Password: admin

docker run --name phpldapadmin \
  --network vaultlab \
  --detach \
  --env PHPLDAPADMIN_LDAP_HOSTS=openldap \
  --env PHPLDAPADMIN_HTTPS=false \
  --publish 8081:80 \
  osixia/phpldapadmin:latest

## STEP 3: Add a Test User via CLI
docker exec -i openldap ldapadd -x -D "cn=admin,dc=bbbb,dc=com" -w admin <<EOF
dn: ou=Users,dc=bbbb,dc=com
objectClass: organizationalUnit
ou: Users

dn: cn=jdoe,ou=Users,dc=bbbb,dc=com
objectClass: inetOrgPerson
cn: jdoe
sn: Doe
uid: jdoe
userPassword: password
EOF
###########################################################
#####   You should see: 
#### - adding new entry "ou=Users,dc=bbbb,dc=com"
#### - adding new entry "cn=jdoe,ou=Users,dc=bbbb,dc=com"

## Instal vault locally
docker run --name vault \
  --network vaultlab \
  -e 'VAULT_DEV_ROOT_TOKEN_ID=root' \
  -e 'VAULT_DEV_LISTEN_ADDRESS=0.0.0.0:8200' \
  -p 8200:8200 \
  -d hashicorp/vault:1.19.0

export VAULT_ADDR=http://localhost:8200
export VAULT_TOKEN=root

## STEP 4: Configure Vault to Use LDAP
vault auth enable ldap

vault write auth/ldap/config \
    url="ldap://openldap:389" \
    binddn="cn=admin,dc=bbbb,dc=com" \
    bindpass="admin" \
    userdn="ou=Users,dc=bbbb,dc=com" \
    userattr="uid" \
    groupfilter="(&(objectClass=groupOfNames)(member={{.UserDN}}))" \
    groupattr="cn" \
    insecure_tls=true



##  STEP 5: Create a Test Vault Policy for the user jdoe
### - You are now logged in as jdoe, with the ldap-user-policy attached, which allows:


vault policy write ldap-user-policy - <<EOF
path "secret/*" {
  capabilities = ["read", "list"]
}
EOF

## STEP 6: Map LDAP User to Policy
vault write auth/ldap/users/jdoe policies=ldap-user-policy

## STEP 7: Test LDAP Login
unset VAULT_TOKEN  # optional if previously set

vault login -method=ldap username="jdoe" password="password"
```


