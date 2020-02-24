# Peer Setup

- lapr organization (Enroll Certs lapr Peer/Admin/User)

```
export FABRIC_CA_CLIENT_MSPDIR=msp
export FABRIC_CA_CLIENT_TLS_CERTFILES=$PWD/lapr/ca-cert.pem

# Peer
export FABRIC_CA_CLIENT_HOME=$PWD/lapr/peer1
fabric-ca-client enroll -d -u https://peer1:peer1pw@ca.lapr.network:65523
export FABRIC_CA_CLIENT_HOME=$PWD/lapr/peer2
fabric-ca-client enroll -d -u https://peer2:peer2pw@ca.lapr.network:65523

# Admin
export FABRIC_CA_CLIENT_HOME=$PWD/lapr/admin1
fabric-ca-client enroll -d -u https://admin1:admin1pw@ca.lapr.network:65523

# User
export FABRIC_CA_CLIENT_HOME=$PWD/lapr/user1
fabric-ca-client enroll -d -u https://user1:user1pw@ca.lapr.network:65523
```

- Enroll TLSCerts lapr Peer
```
export FABRIC_CA_CLIENT_MSPDIR=tls
export FABRIC_CA_CLIENT_TLS_CERTFILES=$PWD/lapr/tls-ca-cert.pem

# Peer
export FABRIC_CA_CLIENT_HOME=$PWD/lapr/peer1
fabric-ca-client enroll -d -u https://peer1:peer1pw@tlsca.lapr.network:65522 --enrollment.profile tls --csr.hosts peer1.lapr.network
export FABRIC_CA_CLIENT_HOME=$PWD/lapr/peer2
fabric-ca-client enroll -d -u https://peer2:peer2pw@tlsca.lapr.network:65522 --enrollment.profile tls --csr.hosts peer2.lapr.network
```

- Set admin certs
```
cp -r lapr/admin1/msp/signcerts/ lapr/peer1/msp/admincerts
cp -r lapr/admin1/msp/signcerts/ lapr/peer2/msp/admincerts
```

- create org'MSP
```
mkdir lapr/msp lapr/msp/admincerts lapr/msp/cacerts lapr/msp/users lapr/msp/tlscacerts
cp lapr/admin1/msp/signcerts/* lapr/msp/admincerts/
cp lapr/ca-cert.pem lapr/msp/cacerts/
cp lapr/tls-ca-cert.pem lapr/msp/tlscacerts/
cp lapr/user1/msp/signcerts/* lapr/msp/users/
```

- tlsca certs 파일명 변경
```
cp lapr/peer1/tls/tlscacerts/* lapr/peer1/tls/ca.crt
cp lapr/peer1/tls/signcerts/* lapr/peer1/tls/server.crt
cp lapr/peer1/tls/keystore/* lapr/peer1/tls/server.key

cp lapr/peer2/tls/tlscacerts/* lapr/peer2/tls/ca.crt
cp lapr/peer2/tls/signcerts/* lapr/peer2/tls/server.crt
cp lapr/peer2/tls/keystore/* lapr/peer2/tls/server.key
```


- oshop organization
```
# Enroll Certs oshop Peer/Admin/User

export FABRIC_CA_CLIENT_MSPDIR=msp
export FABRIC_CA_CLIENT_TLS_CERTFILES=$PWD/oshop/ca-cert.pem

export FABRIC_CA_CLIENT_HOME=$PWD/oshop/peer1
fabric-ca-client enroll -d -u https://peer1:peer1pw@ca.oshop.network:65521
export FABRIC_CA_CLIENT_HOME=$PWD/oshop/peer2
fabric-ca-client enroll -d -u https://peer2:peer2pw@ca.oshop.network:65521

export FABRIC_CA_CLIENT_HOME=$PWD/oshop/admin1
fabric-ca-client enroll -d -u https://admin1:admin1pw@ca.oshop.network:65521
export FABRIC_CA_CLIENT_HOME=$PWD/oshop/user1
fabric-ca-client enroll -d -u https://user1:user1pw@ca.oshop.network:65521


# Enroll TLSCerts oshop Peer

export FABRIC_CA_CLIENT_MSPDIR=tls
export FABRIC_CA_CLIENT_TLS_CERTFILES=$PWD/oshop/tls-ca-cert.pem

export FABRIC_CA_CLIENT_HOME=$PWD/oshop/peer1
fabric-ca-client enroll -d -u https://peer1:peer1pw@tlsca.oshop.network:65520 --enrollment.profile tls --csr.hosts peer1.oshop.network
export FABRIC_CA_CLIENT_HOME=$PWD/oshop/peer2
fabric-ca-client enroll -d -u https://peer2:peer2pw@tlsca.oshop.network:65520 --enrollment.profile tls --csr.hosts peer2.oshop.network

cp -r oshop/admin1/msp/signcerts/ oshop/peer1/msp/admincerts
cp -r oshop/admin1/msp/signcerts/ oshop/peer2/msp/admincerts

mkdir oshop/msp oshop/msp/admincerts oshop/msp/cacerts oshop/msp/users oshop/msp/tlscacerts
cp oshop/admin1/msp/signcerts/* oshop/msp/admincerts/
cp oshop/ca-cert.pem oshop/msp/cacerts/
cp oshop/tls-ca-cert.pem oshop/msp/tlscacerts/
cp oshop/user1/msp/signcerts/* oshop/msp/users/

cp oshop/peer1/tls/tlscacerts/* oshop/peer1/tls/ca.crt
cp oshop/peer1/tls/signcerts/* oshop/peer1/tls/server.crt
cp oshop/peer1/tls/keystore/* oshop/peer1/tls/server.key

cp oshop/peer2/tls/tlscacerts/* oshop/peer2/tls/ca.crt
cp oshop/peer2/tls/signcerts/* oshop/peer2/tls/server.crt
cp oshop/peer2/tls/keystore/* oshop/peer2/tls/server.key
```
- docker-compose

```
--- docker-compose.yaml
 |
 |---base
      |-- docker-compose-base.yaml
      |-- peer-base.yaml
```

- docker-compose.yaml

```
version: '2'

networks:
  fabric:

services:
  peer1.lapr.network:
    container_name: peer1.lapr.network
    extends:
      file:  base/docker-compose-base.yaml
      service: peer1.lapr.network
    networks:
    - fabric

  peer2.lapr.network:
    container_name: peer2.lapr.network
    extends:
      file:  base/docker-compose-base.yaml
      service: peer2.lapr.network
    networks:
    - fabric

  peer1.oshop.network:
    container_name: peer1.oshop.network
    extends:
      file:  base/docker-compose-base.yaml
      service: peer1.oshop.network
    networks:
    - fabric

  peer2.oshop.network:
    container_name: peer2.oshop.network
    extends:
      file:  base/docker-compose-base.yaml
      service: peer2.oshop.network
    networks:
    - fabric

  lapr_cli:
    container_name: lapr_cli
    image: hyperledger/fabric-tools:1.4.4
    tty: true
    stdin_open: true
    environment:
      - SYS_CHANNEL=flk-sys-channel
      - GOPATH=/opt/gopath
      - CORE_VM_ENDPOINT=unix:///host/var/run/docker.sock
      #- FABRIC_LOGGING_SPEC=DEBUG
      - FABRIC_LOGGING_SPEC=INFO
      - CORE_PEER_ID=cli
      - CORE_PEER_ADDRESS=peer1.lapr.network:65500
      - CORE_PEER_LOCALMSPID=laprMSP
      - CORE_PEER_TLS_ENABLED=true
      - CORE_PEER_TLS_CERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/lapr/peer1/tls/server.crt
      - CORE_PEER_TLS_KEY_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/lapr/peer1/tls/server.key
      - CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/lapr/peer1/tls/ca.crt
      - CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/lapr/admin/msp
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric/peer
    command: /bin/bash
    volumes:
        - /var/run/:/host/var/run/
        - ./chaincode/:/opt/gopath/src/github.com/chaincode
        - ./lapr:/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/lapr
        - ./oshop:/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/oshop
        - ./scripts:/opt/gopath/src/github.com/hyperledger/fabric/peer/scripts/
        - ./channel-artifacts:/opt/gopath/src/github.com/hyperledger/fabric/peer/channel-artifacts
    depends_on:
      - peer1.lapr.network
      - peer2.lapr.network
      - peer1.oshop.network
      - peer2.oshop.network
    networks:
      - fabric
    extra_hosts:
      - "orderer1.flk.network:-"
      - "orderer2.flk.network:-"
      - "orderer3.flk.network:-"
      - "orderer4.flk.network:-"
      - "orderer5.flk.network:-"
```
- docker-compose-base.yaml

```
version: '2'

services:

  peer1.lapr.network:
    container_name: peer1.lapr.network
    extends:
      file: peer-base.yaml
      service: peer-base
    environment:
      - CORE_PEER_ID=peer1.lapr.network
      - CORE_PEER_ADDRESS=peer1.lapr.network:65500
      - CORE_PEER_LISTENADDRESS=0.0.0.0:65500
      - CORE_PEER_CHAINCODEADDRESS=peer1.lapr.network:65525
      - CORE_PEER_CHAINCODELISTENADDRESS=0.0.0.0:65525
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer2.lapr.network:65501
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer1.lapr.network:65500
      - CORE_PEER_LOCALMSPID=laprMSP
    volumes:
        - /var/run/:/host/var/run/
        - ../lapr/peer1/msp:/etc/hyperledger/fabric/msp
        - ../lapr/peer1/tls:/etc/hyperledger/fabric/tls
        - ../lapr/peer1/production:/var/hyperledger/production
    ports:
      - 65500:65500

  peer2.lapr.network:
    container_name: peer2.lapr.network
    extends:
      file: peer-base.yaml
      service: peer-base
    environment:
      - CORE_PEER_ID=peer2.lapr.network
      - CORE_PEER_ADDRESS=peer2.lapr.network:65501
      - CORE_PEER_LISTENADDRESS=0.0.0.0:65501
      - CORE_PEER_CHAINCODEADDRESS=peer2.lapr.network:65525
      - CORE_PEER_CHAINCODELISTENADDRESS=0.0.0.0:65525
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer1.lapr.network:65500
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer2.lapr.network:65501
      - CORE_PEER_LOCALMSPID=laprMSP
    volumes:
        - /var/run/:/host/var/run/
        - ../lapr/peer2/msp:/etc/hyperledger/fabric/msp
        - ../lapr/peer2/tls:/etc/hyperledger/fabric/tls
        - ../lapr/peer2/production:/var/hyperledger/production
    ports:
      - 65501:65501

  peer1.oshop.network:
    container_name: peer1.oshop.network
    extends:
      file: peer-base.yaml
      service: peer-base
    environment:
      - CORE_PEER_ID=peer1.oshop.network
      - CORE_PEER_ADDRESS=peer1.oshop.network:65502
      - CORE_PEER_LISTENADDRESS=0.0.0.0:65502
      - CORE_PEER_CHAINCODEADDRESS=peer1.oshop.network:65525
      - CORE_PEER_CHAINCODELISTENADDRESS=0.0.0.0:65525
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer2.oshop.network:65503
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer1.oshop.network:65502
      - CORE_PEER_LOCALMSPID=oshopMSP
    volumes:
        - /var/run/:/host/var/run/
        - ../oshop/peer1/msp:/etc/hyperledger/fabric/msp
        - ../oshop/peer1/tls:/etc/hyperledger/fabric/tls
        - ../oshop/peer1/production:/var/hyperledger/production
    ports:
      - 65502:65502

  peer2.oshop.network:
    container_name: peer2.oshop.network
    extends:
      file: peer-base.yaml
      service: peer-base
    environment:
      - CORE_PEER_ID=peer2.oshop.network
      - CORE_PEER_ADDRESS=peer2.oshop.network:65503
      - CORE_PEER_LISTENADDRESS=0.0.0.0:65503
      - CORE_PEER_CHAINCODEADDRESS=peer2.oshop.network:65525
      - CORE_PEER_CHAINCODELISTENADDRESS=0.0.0.0:65525
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer1.oshop.network:65502
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer2.oshop.network:65503
      - CORE_PEER_LOCALMSPID=oshopMSP
    volumes:
        - /var/run/:/host/var/run/
        - ../oshop/peer2/msp:/etc/hyperledger/fabric/msp
        - ../oshop/peer2/tls:/etc/hyperledger/fabric/tls
        - ../oshop/peer2/production:/var/hyperledger/production
    ports:
      - 65503:65503

```

- peer-base.yaml
```
version: '2'

services:
  peer-base:
    image: hyperledger/fabric-peer:1.4.4
    environment:
      - CORE_VM_ENDPOINT=unix:///host/var/run/docker.sock
      # the following setting starts chaincode containers on the same
      # bridge network as the peers
      # https://docs.docker.com/compose/networking/
      - CORE_VM_DOCKER_HOSTCONFIG_NETWORKMODE=chaincode_fabric
      - FABRIC_LOGGING_SPEC=INFO
      #- FABRIC_LOGGING_SPEC=DEBUG
      - CORE_PEER_TLS_ENABLED=true
      - CORE_PEER_GOSSIP_USELEADERELECTION=true
      - CORE_PEER_GOSSIP_ORGLEADER=false
      - CORE_PEER_PROFILE_ENABLED=true
      - CORE_PEER_TLS_CERT_FILE=/etc/hyperledger/fabric/tls/server.crt
      - CORE_PEER_TLS_KEY_FILE=/etc/hyperledger/fabric/tls/server.key
      - CORE_PEER_TLS_ROOTCERT_FILE=/etc/hyperledger/fabric/tls/ca.crt
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric/peer
    command: peer node start
    extra_hosts:
      - "orderer1.flk.network:-"
      - "orderer2.flk.network:-"
      - "orderer3.flk.network:-"
      - "orderer4.flk.network:-"
      - "orderer5.flk.network:-"

```
