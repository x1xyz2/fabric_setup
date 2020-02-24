# Peer Setup

- Enroll Certs lapr Peer/Admin/User

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
