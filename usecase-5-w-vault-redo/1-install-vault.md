## Install vault  ubuntu 18.04
## https://developer.hashicorp.com/vault/install#Linux
## https://releases.hashicorp.com/vault/

- save the server IP as an A record

go to releases and grab: https://releases.hashicorp.com/vault/1.19.0/

## Install curl
```sh
sudo apt  install curl
sudo apt install unzip
```

## Install vault from release
```sh
curl --silent -Lo /tmp/vault.zip https://releases.hashicorp.com/vault/1.19.0/vault_1.19.0_linux_amd64.zip

cd /tmp
unzip vault.zip
sudo mv vault /usr/local/bin
vault version
```


## give permission to the files  [ exit root ]
```sh
whoami
sudo chown cafanwii:cafanwii /etc/ssl/vault
sudo chown cafanwii:cafanwii /etc/ssl/vault/*
sudo chmod 400 /etc/ssl/vault/*
ls -l /etc/ssl/vault/*
```

## Install nginx
```sh
sudo apt install nginx
```

## Add the proxy code in the file
```sh
cd /etc/nginx/sites-enabled
sudo cp default collins-default
sudo vi default    ## delete all the data in here and replace with data in proxy file
```

## store vault.hcl file
```sh
sudo mkdir /opt/vault-data
sudo mkdir /etc/vault.d
sudo vi /etc/vault.d/vault.hcl   
```

## store vault.service file
```sh
sudo nano /etc/systemd/system/vault.service    # create the service file here
```

## Add group and user to server
```sh
sudo groupadd --system vault
sudo useradd --system -g vault vault
```

## Give permission to directories
```sh
sudo chown -R vault:vault /etc/vault.d /opt/vault-data
sudo chmod 750 /etc/vault.d
sudo chmod 770 /opt/vault-data
```

```sh
sudo systemctl daemon-reload
sudo systemctl start vault.service
sudo systemctl start nginx
```

```sh
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl restart vault
sudo systemctl status vault

sudo systemctl restart nginx
```

## 1. To login my prod vault server
export VAULT_ADDR=https://vault.pixiescloud.com/
export VAULT_TOKEN=xxxxxxxxxxxxx
vault login $VAULT_TOKEN