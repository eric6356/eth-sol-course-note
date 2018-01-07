# Buy Article

update the contract `contracts/ChainList.sol` add `sellArticleEvent` and `buyArticle` function
```Solidity
event buyArticleEvent(address indexed _seller, address indexed _buyer, string _name, uint256 _price);
```
```Solidity
// buy an article
function buyArticle() payable public {
    // should be ready for sale
    require(seller != 0x00);

    // should not be sold
    require(buyer == 0x00);

    // you cannot by your own article
    require(msg.sender != seller);

    // the value send transacted corresponds to the article price
    require(msg.value == price);

    // keep the buyer's information
    buyer = msg.sender;

    // the buyer can buy the article
    seller.transfer(msg.value);

    //trigger event
    buyArticleEvent(seller, buyer, name, price);
}
```
4 way to interrupt a contract function:  
1. `require` (preconditions)
2. `assert` (internal error)
3. `revert` (other business errors)
4. `throw` (legacy)


When a function call is interrupted:  
1. Transaction value will be refunded to message sender.
2. Any state change will be reverted.
3. Execution is interrupted here and no more gas will be spend.
4. All gas spend till this point will be paid to the miner.

Try to sell and buy article in truffle develop console.
```js
truffle(develop)> migrate
...

truffle(develop)> ChainList.deployed().then(i => app = i)
...

truffle(develop)> app.sellArticle('article 1', 'Description of article 1', web3.toWei(10, 'ether'), {from: web3.eth.accounts[1]})
{ tx: '0xe92110d8af885dca7067277d5bf85642eef350aaa98c42a688ba87c24f0f524f',
  receipt:
   { transactionHash: '0xe92110d8af885dca7067277d5bf85642eef350aaa98c42a688ba87c24f0f524f',
     transactionIndex: 0,
     blockHash: '0x6619aca468c6054c48e5658a6bc069760cf3fcb276eca9edc506f4bb7b90dc1f',
     blockNumber: 11,
     gasUsed: 110369,
     cumulativeGasUsed: 110369,
     contractAddress: null,
     logs: [ [Object] ],
     status: 1 },
  logs:
   [ { logIndex: 0,
       transactionIndex: 0,
       transactionHash: '0xe92110d8af885dca7067277d5bf85642eef350aaa98c42a688ba87c24f0f524f',
       blockHash: '0x6619aca468c6054c48e5658a6bc069760cf3fcb276eca9edc506f4bb7b90dc1f',
       blockNumber: 11,
       address: '0x345ca3e014aaf5dca488057592ee47305d9b3e10',
       type: 'mined',
       event: 'sellArticleEvent',
       args: [Object] } ] }

truffle(develop)> app.getArticle.call()
[ '0xf17f52151ebef6c7334fad080c5704d77216b732',
  '0x0000000000000000000000000000000000000000',
  'article 1',
  'Description of article 1',
  BigNumber { s: 1, e: 19, c: [ 100000 ] } ]

truffle(develop)> var buyEvent = app.buyArticleEvent({_seller: web3.eth.accounts[1]}, {fromBlock: 0, toBlock: 'latest'}).watch((err, event) => console.log(event));
undefined

truffle(develop)> var txRes = app.buyArticle({from: web3.eth.accounts[2], value: web3.toWei(10, 'ether')})
undefined

truffle(develop)> { logIndex: 0,
  transactionIndex: 0,
  transactionHash: '0xc400c89a4a734c7059996768fb259740b79bee34675ffc5c0c36894926280733',
  blockHash: '0x18894af68e2f1436d8f09df6b5dfbf4c24d9713989bdef74d2c357045b604127',
  blockNumber: 12,
  address: '0x345ca3e014aaf5dca488057592ee47305d9b3e10',
  type: 'mined',
  event: 'buyArticleEvent',
  args:
   { _seller: '0xf17f52151ebef6c7334fad080c5704d77216b732',
     _buyer: '0xc5fdf4076b8f3a5357c5e395ab970b5b54098fef',
     _name: 'article 1',
     _price: BigNumber { s: 1, e: 19, c: [Array] } } }

truffle(develop)> app.getArticle.call()
[ '0xf17f52151ebef6c7334fad080c5704d77216b732',
  '0xc5fdf4076b8f3a5357c5e395ab970b5b54098fef',
  'article 1',
  'Description of article 1',
  BigNumber { s: 1, e: 19, c: [ 100000 ] } ]

truffle(develop)> web3.eth.getBalance(web3.eth.accounts[1]).toString()
'109971865100000000000'

truffle(develop)> web3.eth.getBalance(web3.eth.accounts[2]).toString()
'89994545200000000000'
```

Restart the console and try to buy the article with insufficient ETH.
```js
truffle(develop)> migrate
...

truffle(develop)> ChainList.deployed().then(i => app = i)
...

truffle(develop)> app.sellArticle('article 1', 'Description of article 1', web3.toWei(10, 'ether'), {from: web3.eth.accounts[1]})
...

truffle(develop)> web3.eth.getBalance(web3.eth.accounts[1]).toString()
'99988963100000000000'

truffle(develop)> var txRes = app.buyArticle({from: web3.eth.accounts[2], value: web3.toWei(5, 'ether')})
undefined

truffle(develop)> (node:20581) UnhandledPromiseRejectionWarning: Error: VM Exception while processing transaction: revert
    at Object.InvalidResponse (/usr/local/lib/node_modules/truffle/build/cli.bundled.js:41484:16)
    at /usr/local/lib/node_modules/truffle/build/cli.bundled.js:329530:36
    at /usr/local/lib/node_modules/truffle/build/cli.bundled.js:325200:9
    at XMLHttpRequest.request.onreadystatechange (/usr/local/lib/node_modules/truffle/build/cli.bundled.js:328229:7)
    at XMLHttpRequestEventTarget.dispatchEvent (/usr/local/lib/node_modules/truffle/build/cli.bundled.js:176415:18)
    at XMLHttpRequest._setReadyState (/usr/local/lib/node_modules/truffle/build/cli.bundled.js:176705:12)
    at XMLHttpRequest._onHttpResponseEnd (/usr/local/lib/node_modules/truffle/build/cli.bundled.js:176860:12)
    at IncomingMessage.<anonymous> (/usr/local/lib/node_modules/truffle/build/cli.bundled.js:176820:24)
    at IncomingMessage.emit (events.js:164:20)
    at endReadableNT (_stream_readable.js:1062:12)
(node:20581) UnhandledPromiseRejectionWarning: Unhandled promise rejection. This error originated either by throwing inside of an async function without a catch block, or by rejecting a promise which was not handled with .catch(). (rejection id: 1)
(node:20581) [DEP0018] DeprecationWarning: Unhandled promise rejections are deprecated. In the future, promise rejections that are not handled will terminate the Node.js process with a non-zero exit code.

truffle(develop)> web3.eth.getBalance(web3.eth.accounts[1]).toString()
'99988963100000000000'

truffle(develop)> web3.eth.getBalance(web3.eth.accounts[2]).toString()
'99997757100000000000'
```
