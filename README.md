
# Implementing Multiple Chaincodes on Multiple Channels in hyperledger fabric 
```
../bin/cryptogen generate --config=./crypto-config.yaml
 ```
 ```
export FABRIC_CFG_PATH=$PWD
 ```
 ```
../bin/configtxgen -profile FourOrgsOrdererGenesis -channelID byfn-sys-channel -outputBlock ./channel-artifacts/genesis.block
 ```
 ```
export CHANNEL_ONE_NAME=channelone
export CHANNEL_ONE_PROFILE=TwoOrgsChannel
export CHANNEL_TWO_NAME=channeltwo
export CHANNEL_TWO_PROFILE=FourOrgsChannel
export ORDERER_CA=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem
 ```
 ```
../bin/configtxgen -profile ${CHANNEL_ONE_PROFILE} -outputCreateChannelTx ./channel-artifacts/${CHANNEL_ONE_NAME}.tx -channelID $CHANNEL_ONE_NAME
```
```
../bin/configtxgen -profile ${CHANNEL_TWO_PROFILE} -outputCreateChannelTx ./channel-artifacts/${CHANNEL_TWO_NAME}.tx -channelID $CHANNEL_TWO_NAME
```
```
../bin/configtxgen -profile ${CHANNEL_ONE_PROFILE} -outputAnchorPeersUpdate ./channel-artifacts/Org1MSPanchors_${CHANNEL_ONE_NAME}.tx -channelID $CHANNEL_ONE_NAME -asOrg Org1MSP
 ```
 ```
../bin/configtxgen -profile ${CHANNEL_ONE_PROFILE} -outputAnchorPeersUpdate ./channel-artifacts/Org2MSPanchors_${CHANNEL_ONE_NAME}.tx -channelID $CHANNEL_ONE_NAME -asOrg Org2MSP
 ```
 ```
../bin/configtxgen -profile ${CHANNEL_TWO_PROFILE} -outputAnchorPeersUpdate ./channel-artifacts/Org1MSPanchors_${CHANNEL_TWO_NAME}.tx -channelID $CHANNEL_TWO_NAME -asOrg Org1MSP
 ```
 ```
../bin/configtxgen -profile ${CHANNEL_TWO_PROFILE} -outputAnchorPeersUpdate ./channel-artifacts/Org2MSPanchors_${CHANNEL_TWO_NAME}.tx -channelID $CHANNEL_TWO_NAME -asOrg Org2MSP
```
```
../bin/configtxgen -profile ${CHANNEL_TWO_PROFILE} -outputAnchorPeersUpdate ./channel-artifacts/Org3MSPanchors_${CHANNEL_TWO_NAME}.tx -channelID $CHANNEL_TWO_NAME -asOrg Org3MSP
 ```
 ```
../bin/configtxgen -profile ${CHANNEL_TWO_PROFILE} -outputAnchorPeersUpdate ./channel-artifacts/Org4MSPanchors_${CHANNEL_TWO_NAME}.tx -channelID $CHANNEL_TWO_NAME -asOrg Org4MSP
 ```
 ```
docker-compose -f docker-compose-cli.yaml up -d
```
```
docker exec -it cli bash
```
```
export CHANNEL_ONE_NAME=channelone
export CHANNEL_ONE_PROFILE=TwoOrgsChannel
export CHANNEL_TWO_NAME=channeltwo
export CHANNEL_TWO_PROFILE=FourOrgsChannel
export 
ORDERER_CA=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem
```

## Creating Channel One as Org1 Peer   
#### Join the Channel One as Org1 Peer
```
peer channel create -o orderer.example.com:7050 -c $CHANNEL_ONE_NAME -f ./channel-artifacts/${CHANNEL_ONE_NAME}.tx --tls --cafile $ORDERER_CA
```
```
peer channel join -b channelone.block
```

### Update variables and join channel as Org2 Peer
```
CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp
CORE_PEER_ADDRESS=peer0.org2.example.com:9051
CORE_PEER_LOCALMSPID="Org2MSP"
CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt
```
```
peer channel join -b channelone.block
```
### Update variables as Org3 Peer
```
CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org3.example.com/users/Admin@org3.example.com/msp
CORE_PEER_ADDRESS=peer0.org3.example.com:11051
CORE_PEER_LOCALMSPID="Org3MSP"
CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org3.example.com/peers/peer0.org3.example.com/tls/ca.crt
```

### Update variables as Org4 Peer
```
CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org4.example.com/users/Admin@org4.example.com/msp
CORE_PEER_ADDRESS=peer0.org4.example.com:13051
CORE_PEER_LOCALMSPID="Org4MSP"
CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org4.example.com/peers/peer0.org4.example.com/tls/ca.crt
```


## Update the Anchor Peers in Channel One

### Org1 Peer
```
peer channel update -o orderer.example.com:7050 -c $CHANNEL_ONE_NAME -f ./channel-artifacts/Org1MSPanchors_${CHANNEL_ONE_NAME}.tx --tls --cafile $ORDERER_CA
```

### Org2 Peer
```
peer channel update -o orderer.example.com:7050 -c $CHANNEL_ONE_NAME -f ./channel-artifacts/Org2MSPanchors_${CHANNEL_ONE_NAME}.tx --tls --cafile $ORDERER_CA
```

## Creating Channel Two as Org2 Peer
```
peer channel create -o orderer.example.com:7050 -c $CHANNEL_TWO_NAME -f ./channel-artifacts/${CHANNEL_TWO_NAME}.tx --tls --cafile $ORDERER_CA
```
### Joining Channel One as Org1 Peer
```peer channel join -b channeltwo.block```

### Joining Channel Two as Org2 Peer
```peer channel join -b channeltwo.block```

### Joining Channel Two as Org3 Peer
```peer channel join -b channeltwo.block```

### Joining Channel Two as Org4 Peer
```peer channel join -b channeltwo.block```

## Update the Anchor peer in channel Two

### Org1 Peer
```
peer channel update -o orderer.example.com:7050 -c $CHANNEL_TWO_NAME -f ./channel-artifacts/Org1MSPanchors_${CHANNEL_TWO_NAME}.tx --tls --cafile $ORDERER_CA
```

### Org2 Peer
```
peer channel update -o orderer.example.com:7050 -c $CHANNEL_TWO_NAME -f ./channel-artifacts/Org2MSPanchors_${CHANNEL_TWO_NAME}.tx --tls --cafile $ORDERER_CA
```

### Org3 Peer
```
peer channel update -o orderer.example.com:7050 -c $CHANNEL_TWO_NAME -f ./channel-artifacts/Org3MSPanchors_${CHANNEL_TWO_NAME}.tx --tls --cafile $ORDERER_CA
```

### Org4 Peer
```
peer channel update -o orderer.example.com:7050 -c $CHANNEL_TWO_NAME -f ./channel-artifacts/Org4MSPanchors_${CHANNEL_TWO_NAME}.tx --tls --cafile $ORDERER_CA
```

## Install Chaincode (firstchaincode) in Org1 Peer
```
peer chaincode install -n myacc -v 1.0 -l node -p /opt/gopath/src/github.com/chaincode/chaincode_example02/node/
```

## Instantiate Chaincode (firstchaincode) in Org1 Peer
```
peer chaincode instantiate -o orderer.example.com:7050 --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C $CHANNEL_ONE_NAME -n myacc -l node -v 1.0 -c '{"Args":["init","a", "100", "b","200"]}' -P "AND ('Org1MSP.peer','Org2MSP.peer')"
```

## Install Chaincode (firstchaincode) in Org2 Peer
```
peer chaincode install -n myacc -v 1.0 -l node -p /opt/gopath/src/github.com/chaincode/chaincode_example02/node/
```

## Install & Instantiate (secondchaincode) in Org2 Peer
```
peer chaincode install -n myacc1 -v 1.0 -l node -p /opt/gopath/src/github.com/chaincode/chaincode_example02/node/
```
```
peer chaincode instantiate -o orderer.example.com:7050 --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C $CHANNEL_TWO_NAME -n myacc1 -l node -v 1.0 -c '{"Args":["init","c", "100", "d","200"]}' -P "AND ('Org1MSP.peer','Org2MSP.peer','Org3MSP.peer','Org4MSP.peer')"
```
## Instal (secondchaincode) in Org1 Peer
```
peer chaincode install -n myacc1 -v 1.0 -l node -p /opt/gopath/src/github.com/chaincode/chaincode_example02/node/
```
## Install (secondchaincode) in Org3 Peer
```
peer chaincode install -n myacc1 -v 1.0 -l node -p /opt/gopath/src/github.com/chaincode/chaincode_example02/node/
```

## Install (secondchaincode) in Org4 Peer
```
peer chaincode install -n myacc1 -v 1.0 -l node -p /opt/gopath/src/github.com/chaincode/chaincode_example02/node/	
```
### Query on org1, org2
```
peer chaincode query -C $CHANNEL_ONE_NAME -n myacc -c '{"Args":["query","a"]}'
```
### Query on org1, org2, org3 and org4
```
peer chaincode query -C $CHANNEL_TWO_NAME -n myacc1 -c '{"Args":["query","c"]}'
```


