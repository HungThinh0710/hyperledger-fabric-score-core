## Sửa lỗi mất `peer` và các môi trường khi thoát Ubuntu WSL

- B1. Đăng nhập vào WLS Ubuntu xong gõ lệnh: `sudo su` (Tài khoản root)
- B2. Gõ lệnh: `nano ~/.bashrc`
- B3. Thêm tất cả "BIẾN MÔI TRƯỜNG" vào cuối file `.bashrc`

### Biến môi trường

```bash
# Hyperledger execution variables
export FABRIC_CFG_PATH=/home/phoenix/hyperledger-fabric-score-core/fabric-core/config/ 
export PATH="$PATH:/home/phoenix/hyperledger-fabric-score-core/fabric-core/bin:$PATH"

# Hyperledger ORG1 Variables
export CORE_PEER_TLS_ENABLED=true
export CORE_PEER_LOCALMSPID="Org1MSP"
export CORE_PEER_TLS_ROOTCERT_FILE=/home/phoenix/hyperledger-fabric-score-core/fabric-core/production-network/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
export CORE_PEER_MSPCONFIGPATH=/home/phoenix/hyperledger-fabric-score-core/fabric-core/production-network/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
export CORE_PEER_ADDRESS=localhost:7051

# Hyperledger ORG2 Variables
export CORE_PEER_TLS_ENABLED=true
export CORE_PEER_LOCALMSPID="Org2MSP"
export CORE_PEER_TLS_ROOTCERT_FILE=/home/phoenix/hyperledger-fabric-score-core/fabric-core/production-network/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt
export CORE_PEER_MSPCONFIGPATH=/home/phoenix/hyperledger-fabric-score-core/fabric-core/production-network/organizations/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp
export CORE_PEER_ADDRESS=localhost:9051
```

* Thoát WLS ra và vào lại -> `peer channel list` và tận hưởng