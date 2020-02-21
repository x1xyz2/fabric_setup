# Build your first n/w

- Version: [1.4.4][byfn_link]

[byfn_link]: https://hyperledger-fabric.readthedocs.io/en/release-1.4/build_network.html

- Orderer: etcdraft mode 

# Start

#### cryptogen

```
$ cryptogen generate --config=./crypto-config.yaml
org1.example.com
org2.example.com
```

#### configtxgen (genesis.block, SampleMultiNodeEtcdRaft mode)

```
$ export FABRIC_CFG_PATH=$PWD
$ configtxgen -profile SampleMultiNodeEtcdRaft -channelID byfn-sys-channel -outputBlock ./channel-artifacts/genesis.block

2020-02-21 12:22:59.847 KST [common.tools.configtxgen] doOutputBlock -> INFO 007 Generating genesis block
2020-02-21 12:22:59.848 KST [common.tools.configtxgen] doOutputBlock -> INFO 008 Writing genesis block
```

#### configtxgen (channel, anchorPeer)

```
$ export CHANNEL_NAME=mychannel
$ configtxgen -profile TwoOrgsChannel -outputCreateChannelTx ./channel-artifacts/channel.tx -channelID $CHANNEL_NAME
2020-02-21 12:32:17.274 KST [common.tools.configtxgen.localconfig] LoadTopLevel -> INFO 004 Loaded configuration: /home/smart/fabric-samples/first-network/configtx.yaml
2020-02-21 12:32:17.274 KST [common.tools.configtxgen] doOutputChannelCreateTx -> INFO 005 Generating new channel configtx
2020-02-21 12:32:17.276 KST [common.tools.configtxgen] doOutputChannelCreateTx -> INFO 006 Writing new channel tx

$ configtxgen -profile TwoOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/Org1MSPanchors.tx -channelID $CHANNEL_NAME -asOrg Org1MSP
2020-02-21 12:32:33.612 KST [common.tools.configtxgen.localconfig] LoadTopLevel -> INFO 004 Loaded configuration: /home/smart/fabric-samples/first-network/configtx.yaml
2020-02-21 12:32:33.612 KST [common.tools.configtxgen] doOutputAnchorPeersUpdate -> INFO 005 Generating anchor peer update
2020-02-21 12:32:33.612 KST [common.tools.configtxgen] doOutputAnchorPeersUpdate -> INFO 006 Writing anchor peer update

$ configtxgen -profile TwoOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/Org2MSPanchors.tx -channelID $CHANNEL_NAME -asOrg Org2MSP
2020-02-21 12:32:50.062 KST [common.tools.configtxgen.localconfig] LoadTopLevel -> INFO 004 Loaded configuration: /home/smart/fabric-samples/first-network/configtx.yaml
2020-02-21 12:32:50.062 KST [common.tools.configtxgen] doOutputAnchorPeersUpdate -> INFO 005 Generating anchor peer update
2020-02-21 12:32:50.062 KST [common.tools.configtxgen] doOutputAnchorPeersUpdate -> INFO 006 Writing anchor peer update
```

#### Command Only
```
cryptogen generate --config=./crypto-config.yaml
export FABRIC_CFG_PATH=$PWD
configtxgen -profile SampleMultiNodeEtcdRaft -channelID byfn-sys-channel -outputBlock ./channel-artifacts/genesis.block
export CHANNEL_NAME=mychannel
configtxgen -profile TwoOrgsChannel -outputCreateChannelTx ./channel-artifacts/channel.tx -channelID $CHANNEL_NAME
configtxgen -profile TwoOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/Org1MSPanchors.tx -channelID $CHANNEL_NAME -asOrg Org1MSP
configtxgen -profile TwoOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/Org2MSPanchors.tx -channelID $CHANNEL_NAME -asOrg Org2MSP
```


#### docker 이미지 실행

```
$ docker-compose -f docker-compose-cli.yaml -f docker-compose-etcdraft2.yaml up -d

$ docker-compose -f docker-compose-cli.yaml -f docker-compose-etcdraft2.yaml ps
         Name                Command       State            Ports          
---------------------------------------------------------------------------
cli                      /bin/bash         Up                              
orderer.example.com      orderer           Up      0.0.0.0:7050->7050/tcp  
orderer2.example.com     orderer           Up      0.0.0.0:8050->7050/tcp  
orderer3.example.com     orderer           Up      0.0.0.0:9050->7050/tcp  
orderer4.example.com     orderer           Up      0.0.0.0:10050->7050/tcp 
orderer5.example.com     orderer           Up      0.0.0.0:11050->7050/tcp 
peer0.org1.example.com   peer node start   Up      0.0.0.0:7051->7051/tcp  
peer0.org2.example.com   peer node start   Up      0.0.0.0:9051->9051/tcp  
peer1.org1.example.com   peer node start   Up      0.0.0.0:8051->8051/tcp  
peer1.org2.example.com   peer node start   Up      0.0.0.0:10051->10051/tcp

```

#### cli 컨테이너 접속

```
$ docker exec -it cli bash
```

#### Create + Join Channel
```
$ export CHANNEL_NAME=mychannel
$ peer channel create -o orderer.example.com:7050 -c $CHANNEL_NAME -f ./channel-artifacts/channel.tx --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem
2020-02-21 05:23:21.937 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
2020-02-21 05:23:21.950 UTC [cli.common] readBlock -> INFO 002 Got status: &{NOT_FOUND}
2020-02-21 05:23:21.951 UTC [channelCmd] InitCmdFactory -> INFO 003 Endorser and orderer connections initialized
2020-02-21 05:23:22.152 UTC [cli.common] readBlock -> INFO 004 Got status: &{SERVICE_UNAVAILABLE}
2020-02-21 05:23:22.154 UTC [channelCmd] InitCmdFactory -> INFO 005 Endorser and orderer connections initialized
2020-02-21 05:23:22.354 UTC [cli.common] readBlock -> INFO 006 Got status: &{SERVICE_UNAVAILABLE}
2020-02-21 05:23:22.356 UTC [channelCmd] InitCmdFactory -> INFO 007 Endorser and orderer connections initialized
2020-02-21 05:23:22.556 UTC [cli.common] readBlock -> INFO 008 Got status: &{SERVICE_UNAVAILABLE}
2020-02-21 05:23:22.558 UTC [channelCmd] InitCmdFactory -> INFO 009 Endorser and orderer connections initialized
2020-02-21 05:23:22.758 UTC [cli.common] readBlock -> INFO 00a Got status: &{SERVICE_UNAVAILABLE}
2020-02-21 05:23:22.760 UTC [channelCmd] InitCmdFactory -> INFO 00b Endorser and orderer connections initialized
2020-02-21 05:23:22.960 UTC [cli.common] readBlock -> INFO 00c Got status: &{SERVICE_UNAVAILABLE}
2020-02-21 05:23:22.962 UTC [channelCmd] InitCmdFactory -> INFO 00d Endorser and orderer connections initialized
2020-02-21 05:23:23.164 UTC [cli.common] readBlock -> INFO 00e Received block: 0

$ peer channel join -b mychannel.block
2020-02-21 05:23:59.398 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
2020-02-21 05:23:59.415 UTC [channelCmd] executeJoin -> INFO 002 Successfully submitted proposal to join channel

$ CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp CORE_PEER_ADDRESS=peer0.org2.example.com:9051 CORE_PEER_LOCALMSPID="Org2MSP" CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt peer channel join -b mychannel.block
2020-02-21 05:24:12.854 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
2020-02-21 05:24:12.872 UTC [channelCmd] executeJoin -> INFO 002 Successfully submitted proposal to join channel

```



#### Set Anchor Peer
```
$ peer channel update -o orderer.example.com:7050 -c $CHANNEL_NAME -f ./channel-artifacts/Org1MSPanchors.tx --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem
2020-02-21 05:25:33.350 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
2020-02-21 05:25:33.358 UTC [channelCmd] update -> INFO 002 Successfully submitted channel update

$ CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp CORE_PEER_ADDRESS=peer0.org2.example.com:9051 CORE_PEER_LOCALMSPID="Org2MSP" CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt peer channel update -o orderer.example.com:7050 -c $CHANNEL_NAME -f ./channel-artifacts/Org2MSPanchors.tx --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem
2020-02-21 05:26:19.090 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
2020-02-21 05:26:19.099 UTC [channelCmd] update -> INFO 002 Successfully submitted channel update
```

#### Install & Instantiate Chaincode
```
$ peer chaincode install -n mycc -v 1.0 -l node -p /opt/gopath/src/github.com/chaincode/chaincode_example02/node/
2020-02-21 05:27:05.842 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
2020-02-21 05:27:05.842 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
2020-02-21 05:27:05.845 UTC [chaincodeCmd] install -> INFO 003 Installed remotely response:<status:200 payload:"OK" > 
$ export CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp
$ export CORE_PEER_ADDRESS=peer0.org2.example.com:9051
$ export CORE_PEER_LOCALMSPID="Org2MSP"
$ export CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt
$ peer chaincode install -n mycc -v 1.0 -l node -p /opt/gopath/src/github.com/chaincode/chaincode_example02/node/
2020-02-21 05:28:31.084 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
2020-02-21 05:28:31.084 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
2020-02-21 05:28:31.088 UTC [chaincodeCmd] install -> INFO 003 Installed remotely response:<status:200 payload:"OK" > 
```


