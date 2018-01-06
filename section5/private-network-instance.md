# Private Network Instance

[https://geth.ethereum.org/](https://geth.ethereum.org/)

create file genesis.json in `/path/to/private/`

```js
{
 "nonce": "0x0000000000000042",
 "mixhash": "0x0000000000000000000000000000000000000000000000000000000000000000",
 "difficulty": "0x400",
 "alloc": {},
 "coinbase": "0x0000000000000000000000000000000000000000",
 "timestamp": "0x0",
 "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
 "extraData": "0x",
 "gasLimit": "0xffffffff",
 "config": {
    "chainId": 4224,
    "homesteadBlock": 0,
    "eip155Block": 0,
    "eip158Block": 0
 }
}
```

initialize the network instance

```bash
geth --datadir /path/to/private/ init genesis
```

create account

```bash
geth --datadir /path/to/private/ account new
```

list account

```bash
geth --datadir /path/to/private/ account list
```

create password file in `/path/to/private/password.sec`

```txt
***
```

start node

```bash
geth --networkid 4224 \
     --mine \
     --datadir "/path/to/private/" \
     --nodiscover \
     --rpc \
     --rpcport "8545" \
     --port "30303" \
     --rpccorsdomain "*" \
     --nat "any" \
     --rpcapi eth,web3,personal,net \
     --unlock 0 \
     --password /path/to/private/password.sec \
     --ipcpath "~/Library/Ethereum/geth.ipc"
```

attach to the node

```
geth attach
```

inside the console

```js
miner.stop()  // stop minning
miner.start()  // start minning
miner.start(4)  // start minning with 4 threads

eth.accounts  // listing all accounts
eth.coinbase  // listing coinbase accounts
web3.fromWei(eth.getBalance(eth.coinbase), "ether")  // check balabce
eth.sendTransaction({
    from: eth.coinbase, 
    to: eth.accounts[1], 
    value: web3.toWei(100, "ether")
})  // transfer
```



