# build your first n/w

> cryptogen

```
$ cryptogen generate --config=./crypto-config.yaml
org1.example.com
org2.example.com
```

> configtxgen (genesis.block, SampleMultiNodeEtcdRaft mode)

1.4.5 버전의 configtx.yaml 파일을 확인한 바, EtcdRaft 설정이 빠져 있는 경우가 있어, 아래와 같이 추가가 필요할 수도 있음

```
$ vim configtx.yaml

(Orderer Configuration)

    # EtcdRaft defines configuration which must be set when the "etcdraft"
    # orderertype is chosen.
    EtcdRaft:
        # The set of Raft replicas for this network. For the etcd/raft-based
        # implementation, we expect every replica to also be an OSN. Therefore,
        # a subset of the host:port items enumerated in this list should be
        # replicated under the Orderer.Addresses key above.
        Consenters:
          - Host: orderer.example.com
              Port: 7050
              ClientTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer.example.com/tls/server.crt
              ServerTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer.example.com/tls/server.crt
          - Host: orderer2.example.com
              Port: 8050
              ClientTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer2.example.com/tls/server.crt
              ServerTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer2.example.com/tls/server.crt
          - Host: orderer3.example.com
              Port: 9050
              ClientTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer3.example.com/tls/server.crt
              ServerTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer3.example.com/tls/server.crt
          - Host: orderer4.example.com
              Port: 10050
              ClientTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer4.example.com/tls/server.crt
              ServerTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer4.example.com/tls/server.crt
          - Host: orderer5.example.com
              Port: 11050
              ClientTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer5.example.com/tls/server.crt
              ServerTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer5.example.com/tls/server.crt

        # Options to be specified for all the etcd/raft nodes. The values here
        # are the defaults for all new channels and can be modified on a
        # per-channel basis via configuration updates.
        Options:
            # TickInterval is the time interval between two Node.Tick invocations.
            TickInterval: 500ms

            # ElectionTick is the number of Node.Tick invocations that must pass
            # between elections. That is, if a follower does not receive any
            # message from the leader of current term before ElectionTick has
            # elapsed, it will become candidate and start an election.
            # ElectionTick must be greater than HeartbeatTick.
            ElectionTick: 10

            # HeartbeatTick is the number of Node.Tick invocations that must
            # pass between heartbeats. That is, a leader sends heartbeat
            # messages to maintain its leadership every HeartbeatTick ticks.
            HeartbeatTick: 1

            # MaxInflightBlocks limits the max number of in-flight append messages
            # during optimistic replication phase.
            MaxInflightBlocks: 5

            # SnapshotIntervalSize defines number of bytes per which a snapshot is taken
            SnapshotIntervalSize: 20 MB
```

```
$ export FABRIC_CFG_PATH=$PWD
$ configtxgen -profile SampleMultiNodeEtcdRaft -channelID byfn-sys-channel -outputBlock ./channel-artifacts/genesis.block
2020-02-21 12:22:59.775 KST [common.tools.configtxgen] main -> INFO 001 Loading configuration
2020-02-21 12:22:59.810 KST [common.tools.configtxgen.localconfig] completeInitialization -> INFO 002 orderer type: etcdraft
2020-02-21 12:22:59.810 KST [common.tools.configtxgen.localconfig] completeInitialization -> INFO 003 Orderer.EtcdRaft.Options unset, setting to tick_interval:"500ms" election_tick:10 heartbeat_tick:1 max_inflight_blocks:5 snapshot_interval_size:20971520 
2020-02-21 12:22:59.810 KST [common.tools.configtxgen.localconfig] Load -> INFO 004 Loaded configuration: /home/smart/fabric-samples/first-network/configtx.yaml
2020-02-21 12:22:59.846 KST [common.tools.configtxgen.localconfig] completeInitialization -> INFO 005 orderer type: etcdraft
2020-02-21 12:22:59.846 KST [common.tools.configtxgen.localconfig] LoadTopLevel -> INFO 006 Loaded configuration: /home/smart/fabric-samples/first-network/configtx.yaml
2020-02-21 12:22:59.847 KST [common.tools.configtxgen] doOutputBlock -> INFO 007 Generating genesis block
2020-02-21 12:22:59.848 KST [common.tools.configtxgen] doOutputBlock -> INFO 008 Writing genesis block
```

> configtxgen (channel, anchorPeer)

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






