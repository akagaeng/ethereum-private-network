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

### 명령어

### account 확인

account 생성 전까지는 []로 나옴

```
eth.accounts
```

### Account 생성

```
$ personal.newAccount('<YOUR_PASSWORD>')
```

생성되면 "0x....." 값이 출력된다.

계정을 여러 개 만들어본다.

```
> personal.newAccount('password')
"0xc5f05a7..."
> personal.newAccount('password')
"0xdbe25e5..."
> personal.newAccount('password')
"0x145399f..."
>
```

### 채굴 시작하기

```
$ miner.start(1)
```

결과가 다음과 같이 계속 화면에 나타난다.

```
> miner.start(1)
INFO [11-16|08:53:27.641] Updated mining threads                   threads=1
INFO [11-16|08:53:27.643] Transaction pool price threshold updated price=1000000000
INFO [11-16|08:53:27.646] Etherbase automatically configured       address=0xfD154d16f0A59856CDD58dc519c11af7Be728F09
null
> INFO [11-16|08:53:27.653] Commit new mining work                   number=1 sealhash=b31a75…a73154 uncles=0 txs=0 gas=0 fees=0 elapsed=6.224ms
INFO [11-16|08:53:29.138] Generating DAG in progress               epoch=0 percentage=0 elapsed=902.081ms
INFO [11-16|08:53:30.211] Generating DAG in progress               epoch=0 percentage=1 elapsed=1.975s
INFO [11-16|08:53:31.090] Generating DAG in progress               epoch=0 percentage=2 elapsed=2.854s
INFO [11-16|08:53:32.194] Generating DAG in progress               epoch=0 percentage=3 elapsed=3.958s
INFO [11-16|08:53:33.040] Generating DAG in progress               epoch=0 percentage=4 elapsed=4.804s

...


INFO [11-16|08:55:09.577] Generating DAG in progress               epoch=0 percentage=97 elapsed=1m41.477s
INFO [11-16|08:55:10.431] Generating DAG in progress               epoch=0 percentage=98 elapsed=1m42.332s
INFO [11-16|08:55:11.958] Generating DAG in progress               epoch=0 percentage=99 elapsed=1m43.858s
```

percentage=100이 되면 새로운 블록이 생성된다.

```

INFO [11-16|08:55:11.960] Generated ethash verification cache      epoch=0 elapsed=1m43.860s
INFO [11-16|08:55:14.042] Generating DAG in progress               epoch=1 percentage=0  elapsed=1.392s
INFO [11-16|08:55:15.480] Generating DAG in progress               epoch=1 percentage=1  elapsed=2.831s
INFO [11-16|08:55:16.988] Generating DAG in progress               epoch=1 percentage=2  elapsed=4.340s
```

### 채굴이 잘 되어있는지 확인하기

먼저 내 계정 정보 확인하기

```
$ eth.accounts
```

결과는 배열로 나타난다. 

```
["0xfd154d16f0a59856cdd58dc519c11af7be728f09", "0xc5f05a732b73d492a9a56ce8c2e8002e80abd5d1", "0xdbe25e505be854a206a8079b48381fb2b760dbaf", "0x145399f01d060b678511a6265cb0293876798c7d"]
```

### 채굴 보상 확인하기
- 채굴 보상 ETH는 첫번째 생성한 계정에 들어감

```
NFO [11-16|09:11:07.143] 🔨 mined potential block                  number=7 hash=79549a…6a6071
INFO [11-16|09:11:07.144] Commit new mining work                   number=8 sealhash=7e431b…f81521 uncles=0 txs=0 gas=0 fees=0 elapsed=499.9µs
> eth.getBalance(eth.accounts[0])
35000000000000000000
```

