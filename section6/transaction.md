# Sending ETH Between Accounts
[Tx Calculator](https://ethgasstation.info/calculatorTxV.php)

open 2 consoles  
1. `testrpc`
2. `truffle console`

inside the truffle console
```js
web3.eth.getBalance(web3.eth.accounts[0]).toString()  // '100000000000000000000'
web3.eth.getBalance(web3.eth.accounts[1]).toString()  // '100000000000000000000'
web3.eth.sendTransaction({from : web3.eth.accounts[0], to: web3.eth.accounts[1], value: web3.toWei(5, 'ether')})  // '0xfbf4a0aa80633c94fb811639cb4c02cd87c45142df3d3556b1f2365d5dfc98c8'
```

inside the testrpc console
```
eth_sendTransaction

  Transaction: 0xfbf4a0aa80633c94fb811639cb4c02cd87c45142df3d3556b1f2365d5dfc98c8
  Gas usage: 21000
  Block Number: 1
  Block Time: Sat Jan 06 2018 20:56:09 GMT-0500 (EST)
```

back to truffle console
```js
web3.eth.getTransaction('0xfbf4a0aa80633c94fb811639cb4c02cd87c45142df3d3556b1f2365d5dfc98c8').gasPrice.toString()  // '1'
web3.eth.getBalance(web3.eth.accounts[0]).toString()  // '94999999999999979000'
web3.eth.getBalance(web3.eth.accounts[1]).toString()  // '105000000000000000000'
```

transaction fee = 21000 * 1 = 100000000000000000000 - 5000000000000000000 - 94999999999999979000 = 21000 wei
