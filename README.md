# Introduction 
General blockchain network setup

# Getting Started
1.  Make sure organization _msp_ folder is under _certs/ORGANIZATION(lower case).com  _
    _msp_ folder requires _admincerts_, _cacerts_ and _tlscacerts_ subfolders  
    Take *org* for example,  
    certs/org.com/  
    &emsp; └── msp  
    &emsp; &emsp; ├── admincerts  
    &emsp; &emsp; │&emsp; └── Admin@org.com-cert.pem  
    &emsp; &emsp; ├── cacerts  
    &emsp; &emsp; │&emsp; └── ca.org.com-cert.pem  
    &emsp; &emsp; └── tlscacerts  
    &emsp; &emsp; &ensp; &emsp; └── tlsca.org.com-cert.pem
2.  Make sure configtx.yaml is under _configs/(lower case)  _
    Take *org* for example,  
    configs/org/  
    &emsp; └── configtx.yaml
3. Needs .env. Checkout .env.example for setting up environment variables


## preparation for docker(container)
- in folder _scripts/prepare_
- related folder will be generated in _docker_ folder
- run `start.sh` to start container individually (_Admin@ORGANIZATION.com_ not included)

### ca / cert-provider
1. `./scripts/prepare/ca.sh`
2. `./scripts/Dockerfile/cert-provider/build.sh` to make cert-provider image
3. Needs 2 files in _ca.ORGANIZATION.com/server-home_: *ca-cert.pem* / *ca-key.pem*
> in ca
> - `./enrollCAAdmin.sh`: 1st thing to do
> - `./registerAdmin.sh Admin Adminpw`: register admin
> - `./registerOrderer.sh ${ORDERER_NAME} ${ORDERER_SECRET}`: register orderer
> - `./registerPeer.sh ${PEER_NAME} ${PEER_SECRET}`: register peer

### zookeeper / kafka
1. `./scripts/prepare/zookeeper${ID}.sh`
2. `./scripts/prepare/kafka${ID}.sh`

### orderer / cli.orderer
1. `./scripts/prepare/ordererKafka.sh ${ORDERER_NAME} ${ORDERER_SECRET}`
2. May appear error messages like required binaries not installed or no necessary admin folder. In such cases run the codes shown
3. Have to start cli.orderer first to create genesisBlock
4. Copy the genesis block (in _channel-artifacts_ folder) to orderer.ORGANIZATION.com
> in cli.orderer
> - `./scripts/genesisBlock.sh`: generate genesis block in folder _channel-artifacts_

### peer / cli.peer
1. `./scripts/prepare/ordererKafka.sh ${PEER_NAME} ${PEER_SECRET}`
2. May appear error messages like required binaries not installed or no necessary admin folder. In such cases run the codes shown
> in cli.peer
> - `./scripts/createJoinChannel.sh ${CHANNEL}`: create and join a channel whose name is CHANNEL
> - `./scripts/newOrgCertsConfigs.sh ${NEW_ORG_LOWER} ${MSP_NAME} ${CA_URL}`: generate basic configtx of the new org by first get org certs through CA_URL
> - `./scripts/createNewOrgTx.sh ${CHANNEL} ${NEW_ORG}`: generate tx for adding a new org to a channel
> - `./scripts/signNewTx.sh ${CHANNEL} ${NEW_ORG}`: sign the tx generated by createNewOrgTx
> - `./scripts/addNewOrg.sh ${CHANNEL} ${NEW_ORG}`: update the channel to let new org join
> - `./scripts/joinChannel.sh ${CHANNEL}`: join the channel only if it is allowed# Private-Docker-Registry
