# Event

update `contracts/ChainList.sol` implement a event and trigger it when sellArticle
```Solidity
pragma solidity ^0.4.2;

contract ChainList {
    // State variables
    address seller;
    string name;
    string description;
    uint256 price;

    // Events
    event sellArticleEvent(address indexed _seller, string _name, uint256 _price);  // +

    // sell an article
    function sellArticle(string _name, string _description, uint256 _price) public {
        seller = msg.sender;
        name = _name;
        description = _description;
        price = _price;
        sellArticleEvent(seller, name, price);  // +
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

start truffle develop console
```bash
truffle develop
```

inside the truffle console
```js
truffle(develop)> migrate
...  // migrate output
truffle(develop)> ChainList.deployed().then(i => app = i)

// here {fromBlock: 0, toBlock: 'latest'} means event is registered on all contracts on all blocks
truffle(develop)> var sellEvent = app.sellArticleEvent({}, {fromBlock: 0, toBlock: 'latest'}).watch((err, event) => console.log(event))
truffle(develop)> app.sellArticle('article 1', 'description 1', web3.toWei(1, 'ether'), {from: web3.eth.accounts[1]})
...  // sellArticle output

truffle(develop)> { logIndex: 0,
  transactionIndex: 0,
  transactionHash: '0x794bb7b7950f6889c5b2b3c0444679986c2622a7f65c2019728ee56f60d48d36',
  blockHash: '0x4eafc8ebee4a48c4ec07246ed5cea1adb4aabe4e965308419092e5b8ecae86a0',
  blockNumber: 16,
  address: '0x82d50ad3c1091866e258fd0f1a7cc9674609d254',
  type: 'mined',
  event: 'sellArticleEvent',
  args:
   { _seller: '0xf17f52151ebef6c7334fad080c5704d77216b732',
     _name: 'article 1',
     _price: BigNumber { s: 1, e: 18, c: [Array] } } }

truffle(develop)> sellEvent.stopWatching()
true

truffle(develop)> app.sellArticle('article 2', 'description 2', web3.toWei(2, 'ether'), {from: web3.eth.accounts[2]})
...  // sellArticle output

// event not triggered
```

event can also be tested, modify `test/ChanListHappyPath.js` add the test case
```js
var watcher = null;
// Test case: should check events
it('should trigger an event when a new article is sold', function () {
    return ChainList.deployed().then(function (instance) {
        chainListInstance = instance;
        watcher = chainListInstance.sellArticleEvent();
        return chainListInstance.sellArticle(
            articleName,
            articleDescription,
            articlePriceWei,
            {from: seller}
        );
    }).then(function (receipt) {
        assert.equal(receipt.logs.length, 1, 'should have received one event');
        assert.equal(receipt.logs[0].args._seller, seller, 'seller must be ' + seller);
        assert.equal(receipt.logs[0].args._name, articleName, 'article name must be ' + articleName);
        assert.equal(receipt.logs[0].args._price.toNumber(), articlePriceWei, 'article price must be ' + articlePriceWei);
    })
})
```
