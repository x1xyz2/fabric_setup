# Orderer setup

### Organization Setup

- 5 orderers, 1 admin

```
export FABRIC_CA_CLIENT_MSPDIR=msp
export FABRIC_CA_CLIENT_TLS_CERTFILES=$PWD/flk/ca-cert.pem

export FABRIC_CA_CLIENT_HOME=$PWD/flk/orderer1
fabric-ca-client enroll -d -u https://orderer1:orderer1pw@ca.flk.network:65525
export FABRIC_CA_CLIENT_HOME=$PWD/flk/orderer2
fabric-ca-client enroll -d -u https://orderer2:orderer2pw@ca.flk.network:65525
export FABRIC_CA_CLIENT_HOME=$PWD/flk/orderer3
fabric-ca-client enroll -d -u https://orderer3:orderer3pw@ca.flk.network:65525
export FABRIC_CA_CLIENT_HOME=$PWD/flk/orderer4
fabric-ca-client enroll -d -u https://orderer4:orderer4pw@ca.flk.network:65525
export FABRIC_CA_CLIENT_HOME=$PWD/flk/orderer5
fabric-ca-client enroll -d -u https://orderer5:orderer5pw@ca.flk.network:65525
export FABRIC_CA_CLIENT_HOME=$PWD/flk/admin1
fabric-ca-client enroll -d -u https://admin1:admin1pw@ca.flk.network:65525

cp -r flk/admin1/msp/signcerts/ flk/orderer1/msp/admincerts
cp -r flk/admin1/msp/signcerts/ flk/orderer2/msp/admincerts
cp -r flk/admin1/msp/signcerts/ flk/orderer3/msp/admincerts
cp -r flk/admin1/msp/signcerts/ flk/orderer4/msp/admincerts
cp -r flk/admin1/msp/signcerts/ flk/orderer5/msp/admincerts

mkdir flk/msp flk/msp/admincerts flk/msp/cacerts flk/msp/users flk/msp/tlscacerts
cp flk/admin1/msp/signcerts/* flk/msp/admincerts/
cp flk/ca-cert.pem flk/msp/cacerts/
cp flk/tls-ca-cert.pem flk/msp/tlscacerts/

export FABRIC_CA_CLIENT_MSPDIR=tls
export FABRIC_CA_CLIENT_TLS_CERTFILES=$PWD/flk/tls-ca-cert.pem

export FABRIC_CA_CLIENT_HOME=$PWD/flk/orderer1
fabric-ca-client enroll -d -u https://orderer1:orderer1pw@tlsca.flk.network:65524 --enrollment.profile tls --csr.hosts orderer1.flk.network
export FABRIC_CA_CLIENT_HOME=$PWD/flk/orderer2
fabric-ca-client enroll -d -u https://orderer2:orderer2pw@tlsca.flk.network:65524 --enrollment.profile tls --csr.hosts orderer2.flk.network
export FABRIC_CA_CLIENT_HOME=$PWD/flk/orderer3
fabric-ca-client enroll -d -u https://orderer3:orderer3pw@tlsca.flk.network:65524 --enrollment.profile tls --csr.hosts orderer3.flk.network
export FABRIC_CA_CLIENT_HOME=$PWD/flk/orderer4
fabric-ca-client enroll -d -u https://orderer4:orderer4pw@tlsca.flk.network:65524 --enrollment.profile tls --csr.hosts orderer4.flk.network
export FABRIC_CA_CLIENT_HOME=$PWD/flk/orderer5
fabric-ca-client enroll -d -u https://orderer5:orderer5pw@tlsca.flk.network:65524 --enrollment.profile tls --csr.hosts orderer5.flk.network

cp flk/orderer1/tls/tlscacerts/* flk/orderer1/tls/ca.crt
cp flk/orderer1/tls/signcerts/* flk/orderer1/tls/server.crt
cp flk/orderer1/tls/keystore/* flk/orderer1/tls/server.key

cp flk/orderer2/tls/tlscacerts/* flk/orderer2/tls/ca.crt
cp flk/orderer2/tls/signcerts/* flk/orderer2/tls/server.crt
cp flk/orderer2/tls/keystore/* flk/orderer2/tls/server.key

cp flk/orderer3/tls/tlscacerts/* flk/orderer3/tls/ca.crt
cp flk/orderer3/tls/signcerts/* flk/orderer3/tls/server.crt
cp flk/orderer3/tls/keystore/* flk/orderer3/tls/server.key

cp flk/orderer4/tls/tlscacerts/* flk/orderer4/tls/ca.crt
cp flk/orderer4/tls/signcerts/* flk/orderer4/tls/server.crt
cp flk/orderer4/tls/keystore/* flk/orderer4/tls/server.key

cp flk/orderer5/tls/tlscacerts/* flk/orderer5/tls/ca.crt
cp flk/orderer5/tls/signcerts/* flk/orderer5/tls/server.crt
cp flk/orderer5/tls/keystore/* flk/orderer5/tls/server.key
```
### configtx.yaml

- fabric-samples 내 byfn 예제의 configtx.yaml 파일을 수정
- OrdererOrg -> flkOrg (flkMSP)
- Org1 -> laprOrg (laprMSP)
- Org2 -> oshopOrg (oshopMSP)
- MSPDir, AnchorPeers 값 수정

```
Organizations:

    # SampleOrg defines an MSP using the sampleconfig.  It should never be used
    # in production but may be used as a template for other definitions
    - &flkOrg
        # DefaultOrg defines the organization which is used in the sampleconfig
        # of the fabric.git development environment
        Name: flkOrg

        # ID to load the MSP definition as
        ID: flkMSP

        # MSPDir is the filesystem path which contains the MSP configuration
        MSPDir: flk/msp

        # Policies defines the set of policies at this level of the config tree
        # For organization policies, their canonical path is usually
        #   /Channel/<Application|Orderer>/<OrgName>/<PolicyName>
        Policies:
            Readers:
                Type: Signature
                Rule: "OR('flkMSP.member')"
            Writers:
                Type: Signature
                Rule: "OR('flkMSP.member')"
            Admins:
                Type: Signature
                Rule: "OR('flkMSP.admin')"

    - &laprOrg
        # DefaultOrg defines the organization which is used in the sampleconfig
        # of the fabric.git development environment
        Name: laprOrg

        # ID to load the MSP definition as
        ID: laprMSP

        MSPDir: lapr/msp

        # Policies defines the set of policies at this level of the config tree
        # For organization policies, their canonical path is usually
        #   /Channel/<Application|Orderer>/<OrgName>/<PolicyName>
        Policies:
            Readers:
                Type: Signature
                Rule: "OR('laprMSP.admin', 'laprMSP.peer', 'laprMSP.client')"
            Writers:
                Type: Signature
                Rule: "OR('laprMSP.admin', 'laprMSP.client')"
            Admins:
                Type: Signature
                Rule: "OR('laprMSP.admin')"

        # leave this flag set to true.
        AnchorPeers:
            # AnchorPeers defines the location of peers which can be used
            # for cross org gossip communication.  Note, this value is only
            # encoded in the genesis block in the Application section context
            - Host: peer1.lapr.network
              Port: 65500

    - &oshopOrg
        # DefaultOrg defines the organization which is used in the sampleconfig
        # of the fabric.git development environment
        Name: oshopOrg

        # ID to load the MSP definition as
        ID: oshopMSP

        MSPDir: oshop/msp

        # Policies defines the set of policies at this level of the config tree
        # For organization policies, their canonical path is usually
        #   /Channel/<Application|Orderer>/<OrgName>/<PolicyName>
        Policies:
            Readers:
                Type: Signature
                Rule: "OR('oshopMSP.admin', 'oshopMSP.peer', 'oshopMSP.client')"
            Writers:
                Type: Signature
                Rule: "OR('oshopMSP.admin', 'oshopMSP.client')"
            Admins:
                Type: Signature
                Rule: "OR('oshopMSP.admin')"

        AnchorPeers:
            # AnchorPeers defines the location of peers which can be used
            # for cross org gossip communication.  Note, this value is only
            # encoded in the genesis block in the Application section context
            - Host: peer1.oshop.network
              Port: 65502

```

- Orderer의 Type을 etcdraft로 변경하고, 추가로 아래와 같이 EtcdRaft 설정을 추가로 변경

```
Orderer: &OrdererDefaults

    # Orderer Type: The orderer implementation to start
    # Available types are "solo","kafka"  and "etcdraft"
    OrdererType: etcdraft

    Addresses:
        - orderer1.flk.network:7050

    EtcdRaft:
        # The set of Raft replicas for this network. For the etcd/raft-based
        # implementation, we expect every replica to also be an OSN. Therefore,
        # a subset of the host:port items enumerated in this list should be
        # replicated under the Orderer.Addresses key above.
        Consenters:
            - Host: orderer1.flk.network
              Port: 7050
              ClientTLSCert: flk/orderer1/tls/server.crt
              ServerTLSCert: flk/orderer1/tls/server.crt
            - Host: orderer2.flk.network
              Port: 7050
              ClientTLSCert: flk/orderer2/tls/server.crt
              ServerTLSCert: flk/orderer2/tls/server.crt
            - Host: orderer3.flk.network
              Port: 7050
              ClientTLSCert: flk/orderer3/tls/server.crt
              ServerTLSCert: flk/orderer3/tls/server.crt
            - Host: orderer4.flk.network
              Port: 7050
              ClientTLSCert: flk/orderer4/tls/server.crt
              ServerTLSCert: flk/orderer4/tls/server.crt
            - Host: orderer5.flk.network
              Port: 7050
              ClientTLSCert: flk/orderer5/tls/server.crt
              ServerTLSCert: flk/orderer5/tls/server.crt
              
    # Organizations is the list of orgs which are defined as participants on
    # the orderer side of the network
    Organizations:
     << 이 곳에 무엇을 추가 해야 할지 확인 필요 >>
```
- Profiles 내 TestChannel, FeelingkMultiNodeEtcdRaft 항목 추가 (

```
Profiles:
    TestChannel:
        Consortium: SampleConsortium
        <<: *ChannelDefaults
        Application:
            <<: *ApplicationDefaults
            Organizations:
                - *laprOrg
                - *oshopOrg
            Capabilities:
                <<: *ApplicationCapabilities

    FeelingkMultiNodeEtcdRaft:
        <<: *ChannelDefaults
        Capabilities:
            <<: *ChannelCapabilities
        Orderer:
            <<: *OrdererDefaults
            OrdererType: etcdraft
            EtcdRaft:
                Consenters:
                - Host: orderer1.flk.network
                  Port: 7050
                  ClientTLSCert: flk/orderer1/tls/server.crt
                  ServerTLSCert: flk/orderer1/tls/server.crt
                - Host: orderer2.flk.network
                  Port: 7050
                  ClientTLSCert: flk/orderer2/tls/server.crt
                  ServerTLSCert: flk/orderer2/tls/server.crt
                - Host: orderer3.flk.network
                  Port: 7050
                  ClientTLSCert: flk/orderer3/tls/server.crt
                  ServerTLSCert: flk/orderer3/tls/server.crt
                - Host: orderer4.flk.network
                  Port: 7050
                  ClientTLSCert: flk/orderer4/tls/server.crt
                  ServerTLSCert: flk/orderer4/tls/server.crt
                - Host: orderer5.flk.network
                  Port: 7050
                  ClientTLSCert: flk/orderer5/tls/server.crt
                  ServerTLSCert: flk/orderer5/tls/server.crt
            Addresses:
                - orderer1.flk.network:7050
                - orderer2.flk.network:7050
                - orderer3.flk.network:7050
                - orderer4.flk.network:7050
                - orderer5.flk.network:7050

            Organizations:
            - *flkOrg
            Capabilities:
                <<: *OrdererCapabilities


```
- Genesis 블럭 생성: 시스템 채널 명 <flk-sys-channel>

```
configtxgen -profile FeelingkMultiNodeEtcdRaft -outputBlock channel-artifacts/genesis.block -channelID flk-sys-channel
```

- Channel 생성 설정 파일: 채널 명 <flk-test-channel>
```
configtxgen -profile TestChannel -outputCreateChannelTx channel-artifacts/channel.tx -channelID flk-test-channel
```

- Anchor Peer 생성 설정 파일
```
configtxgen -profile TestChannel -outputAnchorPeersUpdate ./channel-artifacts/testlapranchors.tx -channelID flk-test-channel -asOrg laprOrg
configtxgen -profile TestChannel -outputAnchorPeersUpdate ./channel-artifacts/testoshopanchors.tx -channelID flk-test-channel -asOrg oshopOrg
```

### docker-compose.yaml

```
version: '2'

networks:
  fabric:

services:

  orderer1.flk.network:
    container_name: orderer1.flk.network
    extends:
      file:  base/peer-base.yaml
      service: orderer-base
    networks:
    - fabric
    ports:
    - 8050:7050
    environment:
    - ORDERER_GENERAL_LOCALMSPID=flkMSP
    volumes:
        - ./channel-artifacts/genesis.block:/var/hyperledger/orderer/orderer.genesis.block
        - ./flk/orderer1/msp:/var/hyperledger/orderer/msp
        - ./flk/orderer1/tls:/var/hyperledger/orderer/tls
        - ./flk/orderer1/production:/var/hyperledger/production/orderer

  orderer2.flk.network:
    container_name: orderer2.flk.network
    extends:
      file:  base/peer-base.yaml
      service: orderer-base
    networks:
    - fabric
    ports:
    - 9050:7050
    environment:
    - ORDERER_GENERAL_LOCALMSPID=flkMSP
    volumes:
        - ./channel-artifacts/genesis.block:/var/hyperledger/orderer/orderer.genesis.block
        - ./flk/orderer2/msp:/var/hyperledger/orderer/msp
        - ./flk/orderer2/tls:/var/hyperledger/orderer/tls
        - ./flk/orderer2/production:/var/hyperledger/production/orderer

  orderer3.flk.network:
    container_name: orderer3.flk.network
    extends:
      file:  base/peer-base.yaml
      service: orderer-base
    networks:
    - fabric
    ports:
    - 10050:7050
    environment:
    - ORDERER_GENERAL_LOCALMSPID=flkMSP
    volumes:
        - ./channel-artifacts/genesis.block:/var/hyperledger/orderer/orderer.genesis.block
        - ./flk/orderer3/msp:/var/hyperledger/orderer/msp
        - ./flk/orderer3/tls:/var/hyperledger/orderer/tls
        - ./flk/orderer3/production:/var/hyperledger/production/orderer


  orderer4.flk.network:
    container_name: orderer4.flk.network
    extends:
      file:  base/peer-base.yaml
      service: orderer-base
    networks:
    - fabric
    ports:
    - 11050:7050
    environment:
    - ORDERER_GENERAL_LOCALMSPID=flkMSP
    volumes:
        - ./channel-artifacts/genesis.block:/var/hyperledger/orderer/orderer.genesis.block
        - ./flk/orderer4/msp:/var/hyperledger/orderer/msp
        - ./flk/orderer4/tls:/var/hyperledger/orderer/tls
        - ./flk/orderer4/production:/var/hyperledger/production/orderer



  orderer5.flk.network:
    container_name: orderer5.flk.network
    extends:
      file:  base/peer-base.yaml
      service: orderer-base
    networks:
    - fabric
    ports:
    - 12050:7050
    environment:
    - ORDERER_GENERAL_LOCALMSPID=flkMSP
    volumes:
        - ./channel-artifacts/genesis.block:/var/hyperledger/orderer/orderer.genesis.block
        - ./flk/orderer5/msp:/var/hyperledger/orderer/msp
        - ./flk/orderer5/tls:/var/hyperledger/orderer/tls
        - ./flk/orderer5/production:/var/hyperledger/production/orderer
```
### base/peer-base.yaml

```
version: '2'

services:
  orderer-base:
    image: hyperledger/fabric-orderer:1.4.4
    environment:
      - FABRIC_LOGGING_SPEC=DEBUG
      #- FABRIC_LOGGING_SPEC=INFO                                                                      
      - ORDERER_GENERAL_LISTENADDRESS=0.0.0.0
      - ORDERER_GENERAL_GENESISMETHOD=file
      - ORDERER_GENERAL_GENESISFILE=/var/hyperledger/orderer/orderer.genesis.block
      # - ORDERER_GENERAL_LOCALMSPID=OrdererMSP                                                         
      - ORDERER_GENERAL_LOCALMSPDIR=/var/hyperledger/orderer/msp
      # enabled TLS                                                                                   
      - ORDERER_GENERAL_TLS_ENABLED=true
      - ORDERER_GENERAL_TLS_PRIVATEKEY=/var/hyperledger/orderer/tls/server.key
      - ORDERER_GENERAL_TLS_CERTIFICATE=/var/hyperledger/orderer/tls/server.crt
      - ORDERER_GENERAL_TLS_ROOTCAS=[/var/hyperledger/orderer/tls/ca.crt]
      - ORDERER_KAFKA_TOPIC_REPLICATIONFACTOR=1
      - ORDERER_KAFKA_VERBOSE=true
      - ORDERER_GENERAL_CLUSTER_CLIENTCERTIFICATE=/var/hyperledger/orderer/tls/server.crt
      - ORDERER_GENERAL_CLUSTER_CLIENTPRIVATEKEY=/var/hyperledger/orderer/tls/server.key
      - ORDERER_GENERAL_CLUSTER_ROOTCAS=[/var/hyperledger/orderer/tls/ca.crt]
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric
    command: orderer
    extra_hosts:
      - "peer1.lapr.network:-"
      - "peer2.lapr.network:-"
      - "peer1.oshop.network:-"
      - "peer2.oshop.network:-"

```
