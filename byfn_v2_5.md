# 테스트 채널 생성

### 준비사항
- channel command 실행 시, orderer의 tlsca의 rootcerts 파일이 있어야 함!!
- peer의 cli 컨테이너로 tlsca rootcerts 파일 복사

> ~/orderer1/tls/ca.cert -> cli 컨테이너 내

### cli 컨테이너 접속
```
$ docker -exec -it lapr_cli bash
```

### channel 생성 (by lapr peer1)
- --cafile 경로 주의 (orderer tlsca의 rootcerts 파일)
```
export CHANNEL_NAME=flk-test-channel
$ peer channel create -o orderer1.flk.network:8050 -c $CHANNEL_NAME -f ./channel-artifacts/channel.tx --tls --cafile $PWD/scripts/orderer/orderer1/tls/ca.crt
```

### Join peer1/lapr to the channel
```
$ peer channel join -b flk-test-channel.block

```

### Join peer2/lapr to the channel
- 환경 변수를 변경한 이후 peer join command를 수행해야 함
```
$ export CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/oshop/admin1/msp
$ export CORE_PEER_ADDRESS=peer1.oshop.network:65502
$ export CORE_PEER_LOCALMSPID="oshopMSP" 
$ export CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/oshop/peer1/tls/ca.crt
$ peer channel join -b flk-test-channel.block
2020-02-24 10:48:59.686 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
2020-02-24 10:48:59.705 UTC [channelCmd] executeJoin -> INFO 002 Successfully submitted proposal to join channel
```

### PeerAnchor 설정 Update (lapr)
- 환경 변수 재 설정을 위해, 컨테이너에 재 접속 후 아래와 같이 실행해야 함
```
$ peer channel update -o orderer1.flk.network:8050 -c $CHANNEL_NAME -f ./channel-artifacts/testlapranchors.tx --tls --cafile $PWD/scripts/orderer/orderer1/tls/ca.crt
2020-02-24 10:53:06.840 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
2020-02-24 10:53:06.891 UTC [channelCmd] update -> INFO 002 Successfully submitted channel update
```

### PeerAnchor 설정 Update (oshop)
-
```
$ peer channel update -o orderer1.flk.network:8050 -c $CHANNEL_NAME -f ./channel-artifacts/testoshopanchors.tx --tls --cafile $PWD/scripts/orderer/orderer1/tls/ca.crt
2020-02-24 10:53:13.812 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
2020-02-24 10:53:13.868 UTC [channelCmd] update -> INFO 002 Successfully submitted channel update

```
