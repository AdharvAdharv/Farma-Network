# Farma-Network

## 🛠️ Built With
Hyperledger Fabric (Blockchain Platform)

Go (for Chaincode Development)

Gin Framework (for Backend API)

## 📌 Purpose of the Project
To track the lifecycle of medicines from manufacturers to pharmacies.

Ensure secure, transparent, and tamper-proof data using blockchain technology.

Avoid issues like data manipulation, price changes, and politics in the pharma supply chain.

## 📌 Why Hyperledger Fabric?
In normal systems, one party controls the data, leading to risks.

In Fabric:

Every organization has its own peer and a shared, tamper-proof ledger.

No one can secretly change or hide data.

Ensures fairness, security, and trust among all pharma participants.

## 📌 Private Data Collections
Sensitive info like:

Order quantities

Prices

Distributor details

Are shared only between selected members.

Keeps important data private, secure, and policy-controlled while maintaining overall transparency.



## Bring up the Test network

```bash
cd fabric-samples/test-network
```
```bash
./network.sh up createChannel -c farmanetwork -ca -s couchdb
```
## Addding org3
```bash
cd addOrg3
./addOrg3.sh up -c farmanetwork -ca -s couchdb
```
```bash
cd ..
```
## Deploy Chaincode
```bash
./network.sh deployCC -ccn Farma-Network -ccp ../../FarmaNetwork/Chaincode/ -ccl go -c farmanetwork -cccg ../../FarmaNetwork/Chaincode/collections.json
```

## Deploy chaincode with updation
```bash
./network.sh deployCC -ccn Farma-Network -ccp ../../FarmaNetwork/Chaincode/ -ccl go -c farmanetwork -ccv 2.0 -ccs 2 -cccg ../../FarmaNetwork/Chaincode/collections.json
```

## General Environment variables
```bash
export FABRIC_CFG_PATH=$PWD/../config/

export ORDERER_CA=${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

export ORG1_PEER_TLSROOTCERT=${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt

export ORG2_PEER_TLSROOTCERT=${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt

export CORE_PEER_TLS_ENABLED=true
```

## Environment variables for Org1
```bash
export CORE_PEER_LOCALMSPID=Org1MSP

export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt

export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp

export CORE_PEER_ADDRESS=localhost:7051
```
## Invoke-Create Medicine

```bash
 peer chaincode invoke \
  -o localhost:7050 \
  --ordererTLSHostnameOverride orderer.example.com \
  --tls --cafile $ORDERER_CA \
  -C farmanetwork \
  -n Farma-Network \
  --peerAddresses localhost:7051 --tlsRootCertFiles $ORG1_PEER_TLSROOTCERT \
  --peerAddresses localhost:9051 --tlsRootCertFiles $ORG2_PEER_TLSROOTCERT \
  -c '{"function":"CreateMedicine","Args":["MED-002", "Paracetamol 500", "Sun Pharma", "2024-06-01", "2026-06-01", "45","200"]}'



```
## Query- Get Medicine by ID
```bash
peer chaincode query   -C farmanetwork   -n Farma-Network   -c '{"Args":["MedicineContract:ReadMedicine","MED-002"]}'
```
## Query -Get all Medicine
```bash
peer chaincode query \
  -C farmanetwork \
  -n Farma-Network \
  -c '{"Args":["MedicineContract:GetAllMedicines"]}'
```

## Environment variables for Org2
```bash
export CORE_PEER_LOCALMSPID=Org2MSP

export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt

export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp

export CORE_PEER_ADDRESS=localhost:9051
```
## CreateOrder Only Org2
```bash
 export MEDICINE_NAME=$(echo -n "Dolo-600" | base64 | tr -d \n)                                                   
export QUANTITY=$(echo -n "400" | base64 | tr -d \n)
export DISTRIBUTOR=$(echo -n "HealthCareDist" | base64 | tr -d \n)


```
```bash
peer chaincode invoke \
  -o localhost:7050 \
  --ordererTLSHostnameOverride orderer.example.com \
  --tls \
  --cafile $ORDERER_CA \
  -C farmanetwork \
  -n Farma-Network \
  --peerAddresses localhost:7051 \
  --tlsRootCertFiles $ORG1_PEER_TLSROOTCERT \
  --peerAddresses localhost:9051 \
  --tlsRootCertFiles $ORG2_PEER_TLSROOTCERT \
  -c '{"Args":["OrderContract:CreateOrder","ORD-04"]}' \
  --transient "{\"medicineName\":\"$MEDICINE_NAME\",\"quantity\":\"$QUANTITY\",\"distributor\":\"$DISTRIBUTOR\"}"


```
## Read Order by ID
```bash
peer chaincode query   -C farmanetwork   -n Farma-Network   -c '{"function":"OrderContract:ReadOrder","Args":["ORD-001"]}'
```
## Read all orders
```bash
peer chaincode query   -C farmanetwork   -n Farma-Network   -c '{"function":"OrderContract:GetAllOrders","Args":[]}'

```
## Query Command for GetOrdersByRange
```bash
peer chaincode invoke \
  -o localhost:7050 \
  --ordererTLSHostnameOverride orderer.example.com \
  --tls --cafile $ORDERER_CA \
  -C farmanetwork \
  -n Farma-Network \
  --peerAddresses localhost:7051 --tlsRootCertFiles $ORG1_PEER_TLSROOTCERT \
  --peerAddresses localhost:9051 --tlsRootCertFiles $ORG2_PEER_TLSROOTCERT \
  --transient "{\"medicineName\":\"UGFyYWNldGFtb2wgNTAw\",\"quantity\":\"MTAw\",\"distributor\":\"SGVhbHRoQ2FyZURpc3Q=\"}" \
  -c '{"function":"OrderContract:GetOrdersByRange","Args":["ORD-001","ORD-002"]}'
```

## Environment variables for Org3:
```bash
export CORE_PEER_LOCALMSPID=Org3MSP

export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org3.example.com/peers/peer0.org3.example.com/tls/ca.crt

export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org3.example.com/users/Admin@org3.example.com/msp

export CORE_PEER_ADDRESS=localhost:11051
```
## Invoke Command — Assign Medicine To Pharmacy
```bash
peer chaincode invoke \
  -o localhost:7050 \
  --ordererTLSHostnameOverride orderer.example.com \
  --tls --cafile $ORDERER_CA \
  -C farmanetwork \
  -n Farma-Network \
  --peerAddresses localhost:7051 --tlsRootCertFiles $ORG1_PEER_TLSROOTCERT \
  --peerAddresses localhost:9051 --tlsRootCertFiles $ORG2_PEER_TLSROOTCERT \
   -c '{"function":"PharmacyContract:AssignMedicineToPharmacy","Args":["MED-001","GreenPharmacy","50"]}'

```
## Query Command — Read Pharmacy Assignment
```bash
peer chaincode query \
  -C farmanetwork \
  -n Farma-Network \
  --peerAddresses localhost:9051 --tlsRootCertFiles $ORG2_PEER_TLSROOTCERT \
  -c '{"function":"PharmacyContract:ReadPharmacyAssignment","Args":["MED-001"]}'
```

## Query for gell all medicine by ORG-3
```bash
 peer chaincode query \
  -C farmanetwork \
  -n Farma-Network \
  -c '{"Args":["MedicineContract:GetAllMedicines"]}' --peerAddresses localhost:9051 --tlsRootCertFiles $ORG2_PEER_TLSROOTCERT
```
