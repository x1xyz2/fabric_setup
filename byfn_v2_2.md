# CA Setup

#### docker-compose.yaml

- ca/tlsca.flk.network: 65525(ca), 65524(tlsca)
- ca/tlsca.lapr.network: 65523(ca), 65522(tlsca)
- ca/tlsca.oshop.network: 65521(ca), 65520(tlsca)

```
version: '2'

services:
  ca.flk.network:
    image: hyperledger/fabric-ca
    environment:
    - FABRIC_CA_SERVER_HOME=/data/hyperledger/fabric-ca/crypto
    - FABRIC_CA_SERVER_TLS_ENABLED=true
    - FABRIC_CA_SERVER_CSR_CN=ca.flk.network
    - FABRIC_CA_SERVER_CSR_HOSTS=ca.flk.network
    - FABRIC_CA_SERVER_DEBUG=true
    ports:
    - 65525:65525
    container_name: ca.flk.network
    volumes:
    - $PWD/flk/ca:/data/hyperledger/fabric-ca
    command: sh -c 'fabric-ca-server start -d -b admin:admin1234 --port 65525'


  tlsca.flk.network:
    image: hyperledger/fabric-ca
    environment:
    - FABRIC_CA_SERVER_HOME=/data/hyperledger/fabric-ca/crypto
    - FABRIC_CA_SERVER_TLS_ENABLED=true
    - FABRIC_CA_SERVER_CSR_CN=tlsca.flk.network
    - FABRIC_CA_SERVER_CSR_HOSTS=tlsca.flk.network
    - FABRIC_CA_SERVER_DEBUG=true
    ports:
    - 65524:65524
    container_name: tlsca.flk.network
    volumes:
    - $PWD/flk/tlsca:/data/hyperledger/fabric-ca
    command: sh -c 'fabric-ca-server start -d -b admin:admin1234 --port 65524'

  ca.lapr.network:
    image: hyperledger/fabric-ca
    environment:
    - FABRIC_CA_SERVER_HOME=/data/hyperledger/fabric-ca/crypto
    - FABRIC_CA_SERVER_TLS_ENABLED=true
    - FABRIC_CA_SERVER_CSR_CN=ca.lapr.network
    - FABRIC_CA_SERVER_CSR_HOSTS=ca.lapr.network
    - FABRIC_CA_SERVER_DEBUG=true
    ports:
    - 65523:65523
    container_name: ca.lapr.network
    volumes:
    - $PWD/lapr/ca:/data/hyperledger/fabric-ca
    command: sh -c 'fabric-ca-server start -d -b admin:admin1234 --port 65523'

  tlsca.lapr.network:
    image: hyperledger/fabric-ca
    environment:
    - FABRIC_CA_SERVER_HOME=/data/hyperledger/fabric-ca/crypto
    - FABRIC_CA_SERVER_TLS_ENABLED=true
    - FABRIC_CA_SERVER_CSR_CN=tlsca.lapr.network
    - FABRIC_CA_SERVER_CSR_HOSTS=tlsca.lapr.network
    - FABRIC_CA_SERVER_DEBUG=true
    ports:
    - 65522:65522
    container_name: tlsca.lapr.network
    volumes:
    - $PWD/lapr/tlsca:/data/hyperledger/fabric-ca
    command: sh -c 'fabric-ca-server start -d -b admin:admin1234 --port 65522'

  ca.oshop.network:
    image: hyperledger/fabric-ca
    environment:
    - FABRIC_CA_SERVER_HOME=/data/hyperledger/fabric-ca/crypto
    - FABRIC_CA_SERVER_TLS_ENABLED=true
    - FABRIC_CA_SERVER_CSR_CN=ca.oshop.network
    - FABRIC_CA_SERVER_CSR_HOSTS=ca.oshop.network
    - FABRIC_CA_SERVER_DEBUG=true
    ports:
    - 65521:65521
    container_name: ca.oshop.network
    volumes:
    - $PWD/oshop/ca:/data/hyperledger/fabric-ca
    command: sh -c 'fabric-ca-server start -d -b admin:admin1234 --port 65521'

  tlsca.oshop.network:
    image: hyperledger/fabric-ca
    environment:
    - FABRIC_CA_SERVER_HOME=/data/hyperledger/fabric-ca/crypto
    - FABRIC_CA_SERVER_TLS_ENABLED=true
    - FABRIC_CA_SERVER_CSR_CN=tlsca.oshop.network
    - FABRIC_CA_SERVER_CSR_HOSTS=tlsca.oshop.network
    - FABRIC_CA_SERVER_DEBUG=true
    ports:
    - 65520:65520
    container_name: tlsca.oshop.network
    volumes:
    - $PWD/oshop/tlsca:/data/hyperledger/fabric-ca
    command: sh -c 'fabric-ca-server start -d -b admin:admin1234 --port 65520'

```

#### Fabric CA Server Start

```
$ mkdir -p flk/ca flk/tlsca lapr/ca lapr/tlsca oshop/ca oshop/tlsca
$ docker-compose up -d
```

#### Enroll CA/TLSCA Admin

- add each ca/tlsca hostname to /etc/hosts

```
# /etc/hosts
0.0.0.0         ca.flk.network tlsca.flk.network ca.lapr.network tlsca.lapr.network ca.oshop.network tlsca.oshop.network
```

- ca.flk.network enroll / register 
```
$ export FABRIC_CA_CLIENT_TLS_CERTFILES=$PWD/flk/ca/crypto/ca-cert.pem
$ export FABRIC_CA_CLIENT_HOME=$PWD/flk/ca/admin

# enroll admin
$ fabric-ca-client enroll -d -u https://admin:admin1234@ca.flk.network:65525

# register orderer 1~5
$ fabric-ca-client register -d --id.name orderer1 --id.secret orderer1pw --id.type orderer -u https://ca.flk.network:65525
$ fabric-ca-client register -d --id.name orderer2 --id.secret orderer2pw --id.type orderer -u https://ca.flk.network:65525
$ fabric-ca-client register -d --id.name orderer3 --id.secret orderer3pw --id.type orderer -u https://ca.flk.network:65525
$ fabric-ca-client register -d --id.name orderer4 --id.secret orderer4pw --id.type orderer -u https://ca.flk.network:65525
$ fabric-ca-client register -d --id.name orderer5 --id.secret orderer5pw --id.type orderer -u https://ca.flk.network:65525

# register admin
$ fabric-ca-client register -d --id.name admin1 --id.secret admin1PW --id.type admin --id.attrs "hf.Registrar.Roles=client,hf.Registrar.Attributes=*,hf.Revoker=true,hf.GenCRL=true,admin=true:ecert,abac.init=true:ecert" -u https://ca.flk.network:65525
```
- tlsca.flk.network enroll / register 
```
$ export FABRIC_CA_CLIENT_TLS_CERTFILES=$PWD/flk/tlsca/crypto/ca-cert.pem
$ export FABRIC_CA_CLIENT_HOME=$PWD/flk/tlsca/admin

# enroll admin
$ fabric-ca-client enroll -d -u https://admin:admin1234@tlsca.flk.network:65524

# register orderer 1~5
$ fabric-ca-client register -d --id.name orderer1 --id.secret orderer1pw --id.type orderer -u https://tlsca.flk.network:65524
$ fabric-ca-client register -d --id.name orderer2 --id.secret orderer2pw --id.type orderer -u https://tlsca.flk.network:65524
$ fabric-ca-client register -d --id.name orderer3 --id.secret orderer3pw --id.type orderer -u https://tlsca.flk.network:65524
$ fabric-ca-client register -d --id.name orderer4 --id.secret orderer4pw --id.type orderer -u https://tlsca.flk.network:65524
$ fabric-ca-client register -d --id.name orderer5 --id.secret orderer5pw --id.type orderer -u https://tlsca.flk.network:65524
```

- ca.lapr.network enroll / register 
```
$ export FABRIC_CA_CLIENT_TLS_CERTFILES=$PWD/lapr/ca/crypto/ca-cert.pem
$ export FABRIC_CA_CLIENT_HOME=$PWD/lapr/ca/admin

# enroll admin
$ fabric-ca-client enroll -d -u https://admin:admin1234@ca.lapr.network:65523

# register peer1/2
$ fabric-ca-client register -d --id.name peer1 --id.secret peer1pw --id.type peer -u https://ca.lapr.network:65523
$ fabric-ca-client register -d --id.name peer2 --id.secret peer2pw --id.type peer -u https://ca.lapr.network:65523

# register admin/user
$ fabric-ca-client register -d --id.name admin1 --id.secret admin1PW --id.type admin -u https://ca.lapr.network:65523
$ fabric-ca-client register -d --id.name user1 --id.secret user1PW --id.type client -u https://ca.lapr.network:65523
```
- tlsca.flk.network enroll / register 
```
$ export FABRIC_CA_CLIENT_TLS_CERTFILES=$PWD/lapr/tlsca/crypto/ca-cert.pem
$ export FABRIC_CA_CLIENT_HOME=$PWD/lapr/tlsca/admin

# enroll admin
$ fabric-ca-client enroll -d -u https://admin:admin1234@tlsca.lapr.network:65522

# register orderer 1~5
$ fabric-ca-client register -d --id.name peer1 --id.secret peer1pw --id.type peer -u https://tlsca.lapr.network:65522
$ fabric-ca-client register -d --id.name peer2 --id.secret peer2pw --id.type peer -u https://tlsca.lapr.network:65522
```

- ca.oshop.network enroll / register 
```
$ export FABRIC_CA_CLIENT_TLS_CERTFILES=$PWD/oshop/ca/crypto/ca-cert.pem
$ export FABRIC_CA_CLIENT_HOME=$PWD/oshop/ca/admin

# enroll admin
$ fabric-ca-client enroll -d -u https://admin:admin1234@ca.oshop.network:65521

# register peer1/2
$ fabric-ca-client register -d --id.name peer1 --id.secret peer1pw --id.type peer -u https://ca.oshop.network:65521
$ fabric-ca-client register -d --id.name peer2 --id.secret peer2pw --id.type peer -u https://ca.oshop.network:65521

# register admin/user
$ fabric-ca-client register -d --id.name admin1 --id.secret admin1PW --id.type admin -u https://ca.oshop.network:65521
$ fabric-ca-client register -d --id.name user1 --id.secret user1PW --id.type client -u https://ca.oshop.network:65521
```
- tlsca.flk.network enroll / register 
```
$ export FABRIC_CA_CLIENT_TLS_CERTFILES=$PWD/oshop/tlsca/crypto/ca-cert.pem
$ export FABRIC_CA_CLIENT_HOME=$PWD/oshop/tlsca/admin

# enroll admin
$ fabric-ca-client enroll -d -u https://admin:admin1234@tlsca.oshop.network:65520

# register orderer 1~5
$ fabric-ca-client register -d --id.name peer1 --id.secret peer1pw --id.type peer -u https://tlsca.oshop.network:65520
$ fabric-ca-client register -d --id.name peer2 --id.secret peer2pw --id.type peer -u https://tlsca.oshop.network:65520
```

