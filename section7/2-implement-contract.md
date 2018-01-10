# Implement ChainList Contract

implement `contracts/ChainList.sol`
```Solidity
pragma solidity ^0.4.2;

contract ChainList {
    // State variables
    address seller;
    string name;
    string description;
    uint256 price;

    // sell an article
    function sellArticle(string _name, string _description, uint256 _price) public {
        seller = msg.sender;
        name = _name;
        description = _description;
        price = _price;
    }

    // get the article
    function getArticle() public constant returns (
        address _seller,
        string _name,
        string _description,
        uint256 _price) {
        return (seller, name, description, price);
    }
}
```

implement `migrations/2_deploy_contract.js`
```js
var ChainList = artifacts.require('./ChainList.sol');

module.exports = function (deployer) {
    deployer.deploy(ChainList);
};
```

modify `truffle.js` set port to `9545` and then start truffle develop console
```bash
truffle develop
```

inside the truffle console
```
truffle(develop)> migrate
Compiling ./contracts/ChanList.sol...
Compiling ./contracts/Migrations.sol...

Compilation warnings encountered:
...

Writing artifacts to ./build/contracts

Using network 'develop'.

Running migration: 1_initial_migration.js
  Deploying Migrations...
  ... 0x03db4231b3b3a633869e59d79919ec27f20aa3bfa634b7ce8ddbd123fc8dcada
  Migrations: 0x8cdaf0cd259887258bc13a92c0a6da92698644c0
Saving successful migration to network...
  ... 0xd7bc86d31bee32fa3988f1c1eabce403a1b5d570340a3a9cdba53a472ee8c956
Saving artifacts...
Running migration: 2_deploy_contract.js
  Deploying ChainList...
  ... 0xd344c708fd7ec698b5780feac65c6999335167456cc6746a42c7fc0347825b67
  ChainList: 0x345ca3e014aaf5dca488057592ee47305d9b3e10
Saving successful migration to network...
  ... 0xf36163615f41ef7ed8f4a8f192149a0bf633fe1a2398ce001bf44c43dc7bdda0
```

when the ChanList contract is deployed, we can call it
```js
ChainList.deployed().then(i => app = i);

app.getArticle.call()
[ '0x0000000000000000000000000000000000000000',
  '',
  '',
  BigNumber { s: 1, e: 0, c: [ 0 ] } ]

app.sellArticle('iPhone7', 'Selling it in order to by an iPhone8', web3.toWei(3, 'ether'), {from: web3.eth.accounts[1]})
{ tx: '0x61db8412c3f678a3bd7208b03256a44607781370847ff48d3453073753839df3',
  receipt:
   { transactionHash: '0x61db8412c3f678a3bd7208b03256a44607781370847ff48d3453073753839df3',
     transactionIndex: 0,
     blockHash: '0x2dd54df47ceef3fee9bff1064509a0fe0f11a55cfed43c72c21eff39db8db43b',
     blockNumber: 5,
     gasUsed: 147799,
     cumulativeGasUsed: 147799,
     contractAddress: null,
     logs: [],
     status: 1 },
  logs: [] }

app.getArticle.call()
[ '0xf17f52151ebef6c7334fad080c5704d77216b732',
  'iPhone7',
  'Selling it in order to by an iPhone8',
  BigNumber { s: 1, e: 18, c: [ 30000 ] } ]
```
