# Understanding Gas
[Ethereum Yellow Paper](https://ethereum.github.io/yellowpaper/paper.pdf)
[Ethereum Gas Station](https://ethgasstation.info/validatedTable.php)

start testrpc
```bash
testrpc
```

attach geth to the testrpc node
```bash
geth attach http://localhost:8545
```

inside the geth console
```js
eth.getBalance(eth.coinbase)  // 100000000000000000000, default value by testrpc
eth.getBalance(eth.accounts[1])  // 100000000000000000000, default value by testrpc
```

deploy the greeting contract
```bash
cd /path/to/GreetingsTruffle/
truffle migrate
```
gas usage of all 4 transactions for migration can be found in the testrpc console output
```
eth_sendTransaction

  Transaction: 0x73bf9a721d52a4f25871fa437de0fddb7a6116f84bffb864ccd39dc443e17af9
  Contract created: 0x35fc8e72b8ef1cd181479c23cd114fa883d91c3b
  Gas usage: 201556
  Block Number: 1
  Block Time: Sat Jan 06 2018 19:28:15 GMT-0500 (EST)

eth_newBlockFilter
eth_getFilterChanges
eth_getTransactionReceipt
eth_getCode
eth_uninstallFilter
eth_sendTransaction

  Transaction: 0x9938e87e1b05021756f46119a44a40f5c6dfb4c58e5a258b47b6af5ba77244b1
  Gas usage: 41965
  Block Number: 2
  Block Time: Sat Jan 06 2018 19:28:15 GMT-0500 (EST)

eth_getTransactionReceipt
eth_accounts
net_version
net_version
eth_sendTransaction

  Transaction: 0x9e68f0e2d6fb8a696752c062e79e8b9bd4532f25c4e35241fe3a98e49beb94ef
  Contract created: 0xe72cdb00874db8d9e6e4f694eff6edb89bac4d6c
  Gas usage: 275623
  Block Number: 3
  Block Time: Sat Jan 06 2018 19:28:15 GMT-0500 (EST)

eth_newBlockFilter
eth_getFilterChanges
eth_getTransactionReceipt
eth_getCode
eth_uninstallFilter
eth_sendTransaction

  Transaction: 0x151bd718be6586cf9af9440747fad01cac6e7b92f29602e059e1ca3e20c9e713
  Gas usage: 26965
  Block Number: 4
  Block Time: Sat Jan 06 2018 19:28:15 GMT-0500 (EST)

eth_getTransactionReceipt
```
total actual gas usage = 201556 + 41965 + 275623 + 26965 = 546109

back to the geth console
```js
eth.getBalance(eth.coinbase)  // 99945389100000000000
eth.getTransaction('0x9e68f0e2d6fb8a696752c062e79e8b9bd4532f25c4e35241fe3a98e49beb94ef').gasPrice  // 100000000000, unit gas price in this transaction
eth.getTransaction('0x9e68f0e2d6fb8a696752c062e79e8b9bd4532f25c4e35241fe3a98e49beb94ef').gas  // 4712388, gas that sender willing to pay for this transaction
```
total actual gas price = 546109 * 100000000000 = 100000000000000000000 - 99945389100000000000

create truffle console
```bash
cd /path/to/GreetingsTruffle/
truffle console
```

inside the truffle console
```js
web3.eth.getBalance(web3.eth.accounts[1]).toNumber() // 100000000000000000000, default value set by testrpc
Greetings.deployed().then(instance => app = instance)
app.setGreetings('new value', {from: web3.eth.accounts[1]})
{ tx: '0x171aa57620aa64cdf1ff4480fbea726401a413577591a5fbfb7d5ea3fdedfae6',
  receipt:
   { transactionHash: '0x171aa57620aa64cdf1ff4480fbea726401a413577591a5fbfb7d5ea3fdedfae6',
     transactionIndex: 0,
     blockHash: '0x5d2e4edb781679879c750ed1785873df5ded8d5ed244ba870a8527720dba6793',
     blockNumber: 5,
     gasUsed: 33222,
     cumulativeGasUsed: 33222,
     contractAddress: null,
     logs: [],
     status: 1 },
  logs: [] }
web3.eth.getBalance(web3.eth.accounts[1]).toNumber() // 99996677800000000000
```
actual gas price used for  set greetings is 33222 * 100000000000 = 100000000000000000000 - 99996677800000000000
