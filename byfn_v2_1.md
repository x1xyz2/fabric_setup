# byfn version2

- No use cryptogen
- Use FabricCA
- Multi System

## System Info

- Server1: OrdererOrg (5 orderers + 1 admin)
- Server2: Org1, Org2 (2 peers + 1 admin + 1 client)

## Fabric CA 설정

> byfn 의 tls 인증서를 분석한 바, 아래와 같이 Organization 별로 tlsca 발급 기관이 서로 다름을 알 수 있다

```
### Command 
$ openssl x509 -noout -text -in server.crt|grep Issuer

### OrdererOrg (CN=tlsca.example.com)
        Issuer: C=US, ST=California, L=San Francisco, O=example.com, CN=tlsca.example.com
### Org1 (CN=tlsca.org1.example.com)
        Issuer: C=US, ST=California, L=San Francisco, O=example.com, CN=CN=tlsca.org1.example.com
### Org2 (CN=tlsca.org2.example.com)
        Issuer: C=US, ST=California, L=San Francisco, O=example.com, CN=tlsca.org2.example.com
```

> 결론: Organization 별로 1 Enrollment-CA + 1 TLS-CA가 존재하는 형상으로 구성이 필요 함


## Hostname Definitions

```
flk.network
    |-- CA
    |    |-- ca.flk.network
    |    |-- tlsca.flk.network
    |
    |-- Orderer     
    |    |-- orderer1.flk.network
    |    |-- orderer2.flk.network
    |    |-- orderer3.flk.network
    |    |-- orderer4.flk.network
    |    |-- orderer5.flk.network
    |-- Admin
         |-- admin.flk.network

lapr.network
    |-- CA
    |    |-- ca.lapr.network
    |    |-- tlsca.lapr.network
    |
    |-- Peer
    |    |-- peer1.lapr.network
    |    |-- peer2.lapr.network
    |-- Admin
    |    |-- admin.lapr.network
    |-- Client
         |-- user.lapr.network

oshop.network
    |-- CA
    |    |-- ca.oshop.network
    |    |-- tlsca.oshop.network
    |
    |-- Peer
    |    |-- peer1.oshop.network
    |    |-- peer2.oshop.network
    |-- Admin
    |    |-- admin.oshop.network
    |-- Client
         |-- user.oshop.network
```

#### NodeOUs Enable (config.yaml)

```
NodeOUs:
  Enable: true
  ClientOUIdentifier:
    Certificate: cacerts/cert.pem
    OrganizationalUnitIdentifier: client
  PeerOUIdentifier:
    Certificate: cacerts/cert.pem
    OrganizationalUnitIdentifier: peer
  AdminOUIdentifier:
    Certificate: cacerts/cert.pem
    OrganizationalUnitIdentifier: admin
  OrdererOUIdentifier:
    Certificate: cacerts/cert.pem
    OrganizationalUnitIdentifier: orderer
```
