# Ethereum Private Network

## Launch Private Ethereum Network

### go ethereum 이미지 다운로드

```
$ docker pull ethereum/client-go
```

https://hub.docker.com/r/ethereum/client-go/


### genesis.json 생성하기

Private 네트워크를 구축하기 위한 최초의 블록인 genesis block 생성하기 (`genesis.json`)

데이터 볼륨 생성

```
$ mkdir eth_data
```

genesis block 생성

```
docker run -v $(pwd)/eth_data:/root/.ethereum ethereum/client-go init /root/.ethereum/genesis.json
```


```
WARN [11-16|07:44:32.575] Sanitizing cache to Go's GC limits       provided=1024 updated=666
INFO [11-16|07:44:32.577] Maximum peer count                       ETH=25 LES=0 total=25
INFO [11-16|07:44:32.580] Allocated cache and file handles         database=/root/.ethereum/geth/chaindata cache=16 handles=16
INFO [11-16|07:44:32.592] Writing custom genesis block 
INFO [11-16|07:44:32.593] Persisted trie from memory database      nodes=0 size=0.00B time=25.3µs gcnodes=0 gcsize=0.00B gctime=0s livenodes=1 livesize=0.00B
INFO [11-16|07:44:32.595] Successfully wrote genesis state         database=chaindata                      hash=5e1fc7…d790e0
INFO [11-16|07:44:32.595] Allocated cache and file handles         database=/root/.ethereum/geth/lightchaindata cache=16 handles=16
INFO [11-16|07:44:32.606] Writing custom genesis block 
INFO [11-16|07:44:32.606] Persisted trie from memory database      nodes=0 size=0.00B time=26.8µs gcnodes=0 gcsize=0.00B gctime=0s livenodes=1 livesize=0.00B
INFO [11-16|07:44:32.609] Successfully wrote genesis state         database=lightchaindata                      hash=5e1fc7…d790e0
```

로그 마지막 줄에 `Successfully wrote genesis state` 와 같은 메시지가 있으면 성공

### 이더리움 실행하기

```
$ docker run -it -p 30303:30303 -p 8545:8545 -v \
$(pwd)/eth_data:/root/.ethereum --name ethereum_container \
ethereum/client-go --networkid 4649 --nodiscover --maxpeers 0 \
--rpc --rpcaddr "0.0.0.0" --rpccorsdomain "*" --rpcapi \
"admin,db,eth,debug,miner,net,shh,txpool,personal,web3" console
```

정상적으로 실행되면 다음과 같은 메시지가 나타납니다.

```
Welcome to the Geth JavaScript console!

instance: Geth/v1.8.19-unstable-ffe2fc3b/linux-amd64/go1.11.2
 modules: admin:1.0 debug:1.0 eth:1.0 ethash:1.0 miner:1.0 net:1.0 personal:1.0 rpc:1.0 txpool:1.0 web3:1.0

>
```

그리고 eth_data 디렉토리에 geth/nodes 관련 코드들이 생긴다.

> 이더리움 private network 설치 완료!

## Interacting Private Ethereum Network




