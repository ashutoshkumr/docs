## Get a linux server IP address and ensure it has docker installed with TCP port 2376 open
```sh
docker --version
firewall-cmd --add-port=2376/tcp --zone=public
```

## Enable password-less ssh login
```sh
ssh-copy-id root@ip
```

## Create docker machine
```sh
docker-machine create --driver generic --generic-ip-address=${SERVER_IP} --generic-ssh-key ~/.ssh/id_rsa --generic-ssh-user ${SERVER_USER} ${NICKNAME}
```

## Set environment
```sh
eval $(docker-machine env ${NICKNAME})
```
