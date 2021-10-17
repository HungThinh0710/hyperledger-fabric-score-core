# Hướng dẫn cài đặt Hyperledge Fabric 2.2 (LTS) trên WSL Ubuntu 18.04

## Sau khi cài đặt xong WSL Ubuntu 18.04 -> Sudo su ngay

## Cài đặt Prerequisites

### Intall Git

```bash
sudo apt update && sudo apt install git
```

### Install CURL (Already installed)

`already installed at blow step`

### Install Docker

```bash
sudo apt-get update
```

```bash
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpp
```

```bash
echo \
 "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \ 
 $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

```bash
sudo apt-get update
```

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

```bash
sudo service docker start
```

```bash
sudo usermod -a -G docker <username>
```

### Install docker-compose

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

```bash
sudo chmod +x /usr/local/bin/docker-compose
```

```bash
docker-compose --version
```

` ==> docker-compose version 1.29.2, build 5becea4c1 | Là xong`

## Install Node 12

```bash
curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
```

```bash
sudo apt-get install -y nodejs
```

> Check `node -v` và `npm -v`

## Cài đặt Samples, Binaries & Docker Images

Xác định hiện tại phải đang ở `/home/<username>` Sau đó chạy lệnh sau

```bash
curl -sSL https://bit.ly/2ysbOFE | bash -s -- 2.2.4 1.5.2
```

Nếu bị đứng thì Ctrl & C rồi chạy

```bash
curl -sSL https://raw.githubusercontent.com/hyperledger/fabric/release-2.2/scripts/bootstrap.sh | bash -s -- 2.2.4 1.5.2
```

Sau khi cài đặt xong các `docker-images` và pull `fabric-samples` như sau

```bash
ll && cd fabric-samples
```

> Nhớ checkout qua bản v2.2.3 chứ không lỗi `osnadmin: command not found` <- Cái này provide từ bản 2.3 trở lên

```bash
git checkout v2.2.3
```

```bash
pwd
```

Copy path hiện tại, ví dụ: `/home/phoenix/fabric-samples `

Thay vào  `<path to download location> ` ở command bên dưới rồi chạy trên console

```bash
export PATH=<path to download location>/bin:$PATH
```

Ví dụ: 

```bash
export PATH=/home/phoenix/fabric-samples/bin:$PATH
```

### Cài đặt Go để biên dịch

```bash
wget -c https://dl.google.com/go/go1.14.9.linux-amd64.tar.gz -O - | tar -xz -C /usr/local
```

Thay `<path-fabric-samples>` như trước

```bash
echo 'export PATH="$PATH:/usr/local/go/bin:<path-fabric-samples>/bin"' >> $HOME/.profile
```

Ví dụ:

```bash
echo 'export PATH="$PATH:/usr/local/go/bin:/home/phoenix/fabric-samples/bin"' >> $HOME/.profile
```

```bash
source ~/.profile
```

point the GOPATH env var to the base fabric workspace folder

Hiện tại phải đang ở `/home/<username>`

```bash
mkdir fabirc
```

```bash
echo 'export GOPATH="/home/phoenix/fabric"' >> $HOME/.profile
```

Reload profile

```bash
source ~/.profile
```

Kiểm tra go đã cài đặt hay chưa

```bash
go version
==> go version go1.14.9 linux/amd64
```

check the vars

```bash
printenv | grep PATH
```

## Try test-network

```bash
cd fabric-samples/test-network
```

Down network

```bash
./network.sh down
```

Up network và tạo channel mặc định và mychannel

```bash
./network.sh up createChannel
```

Tạo channel 1

```bash
./network.sh up createChannel -c channel1
```

> Nếu mà lỗi: `Error: got unexpected status: BAD_REQUEST -- channel creation request not allowed because the orderer system channel is not defined`
>
> Thì down lại network rồi chạy lại

Chạy xong thì đóng gói lại cái gì đó :v méo biết

```bash
./network.sh deployCC -ccn basic -ccp ../asset-transfer-basic/chaincode-go -ccl go

./network.sh deployCC -ccn basic -ccp ../asset-transfer-basic/chaincode-javascript -ccl javascript
```

> Nó mà lỗi thì down network rồi chạy lại :>

```bash
# show if some containers are running
docker ps
docker-compose -f docker/docker-compose-test-net.yaml ps
```

Biên dịch thành công thì

```bash
export PATH=${PWD}/../bin:$PATH
```

```bash
export FABRIC_CFG_PATH=$PWD/../config/
```

Chạy từng `export`

```bash
export CORE_PEER_TLS_ENABLED=true
export CORE_PEER_LOCALMSPID="Org1MSP"
export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
export CORE_PEER_ADDRESS=localhost:7051
```

# TỚI ĐÂY TỪ TỪ :V CHƯA NGHIÊN CỨU

Chạy lệnh sau để khởi tạo sổ cái với các tài sản:

```bash
peer chaincode invoke -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n basic --peerAddresses localhost:7051 --tlsRootCertFiles ${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt --peerAddresses localhost:9051 --tlsRootCertFiles ${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt -c '{"function":"InitLedger","Args":[]}'
```

Nó mà bay ra cái đống này là thành công (Hiện tại sẽ bị 404 - network _test not found)

> Bị lỗi 404 thì chuyển cái file .env vào folder docker

```bash
-> INFO 001 Chaincode invoke successful. result: status:200
```

Bây giờ bạn có thể truy vấn sổ cái từ CLI của mình. Chạy lệnh sau để nhận danh sách nội dung đã được thêm vào sổ cái kênh của bạn:

```bash
peer chaincode query -C mychannel -n basic -c '{"Args":["GetAllAssets"]}'
```

If successful, you should see the following output:

```bash
[
  {"ID": "asset1", "color": "blue", "size": 5, "owner": "Tomoko", "appraisedValue": 300},
  {"ID": "asset2", "color": "red", "size": 5, "owner": "Brad", "appraisedValue": 400},
  {"ID": "asset3", "color": "green", "size": 10, "owner": "Jin Soo", "appraisedValue": 500},
  {"ID": "asset4", "color": "yellow", "size": 10, "owner": "Max", "appraisedValue": 600},
  {"ID": "asset5", "color": "black", "size": 15, "owner": "Adriana", "appraisedValue": 700},
  {"ID": "asset6", "color": "white", "size": 15, "owner": "Michel", "appraisedValue": 800}
]
```

[Using the Fabric test network — hyperledger-fabricdocs master documentation](https://hyperledger-fabric.readthedocs.io/en/release-2.2/test_network.html)



## Tích hợp Hyperledge Explore

### Cài đặt postgreSQL

```bash
sudo apt-get install wget ca-certificates
```

```bash
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
```

```bash
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" >> /etc/apt/sources.list.d/pgdg.list'
```

```bash
sudo apt-get update
```

```bash
sudo apt-get install postgresql postgresql-contrib
```

```bash
sudo apt show postgresql
```

```bash
sudo service postgresql restart
```

### Cấu hình Hyperledger Explorer

Tạo 1 folder ở `/home/<username>` Ví dụ: 

```bash
mkdir explorer
```

Sau đó `cd explorer` và chạy cái mớ sau

```bash
wget https://raw.githubusercontent.com/hyperledger/blockchain-explorer/main/examples/net1/config.json
wget https://raw.githubusercontent.com/hyperledger/blockchain-explorer/main/examples/net1/connection-profile/test-network.json -P connection-profile
wget https://raw.githubusercontent.com/hyperledger/blockchain-explorer/main/docker-compose.yaml
```

Tiếp theo copy cái folder `organizations` nằm trong `test-network` qua `explorer`

```bash
cp -r /home/phoenix/fabric-samples/test-network/organizations/ /home/phoenix/explorer/
```

Thay `phoenix` thành username tương ứng

Dùng cái VS Code cài WSL Remote nhét vào để chỉnh

Lỗi permission khi lưu thì chạy

```bash
sudo chown -R phoenix /home/phoenix
```

- ```
  $ docker-compose up -d
  ```

  sudo ln -s /root/.nvm/versions/node/v12.22.7/bin/node /usr/bin/node

## 

## Referer

- [samlinux/htsc: Samlinux Academy - a place to learn about Hyperledger Fabric (github.com)](https://github.com/samlinux/htsc)

