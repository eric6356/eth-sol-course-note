# Selfdestruct

update `contracts/ChainList.sol`
```diff
 pragma solidity ^0.4.2;

 contract ChainList {
     // Custom types
     struct Article {
         uint id;
         address seller;
         address buyer;
         string name;
         string description;
         uint256 price;
     }

     // State variables
     mapping(uint => Article) public articles;
     uint articleCounter;
+    address owner;

     // Events
     event sellArticleEvent(
         uint indexed _id,
         address indexed _seller,
         string _name,
         uint256 _price
     );
     event buyArticleEvent(
         uint indexed _id,
         address indexed _seller,
         address indexed _buyer,
         string _name,
         uint256 _price
     );

+    // Modifiers
+    modifier onlyOwner() {
+        require(msg.sender == owner);
+        _;  // placeholder of the modified code block
+    }
+
+    // constructor
+    function ChainList() {
+        owner = msg.sender;
+    }


     // sell an article
     function sellArticle(string _name, string _description, uint256 _price) public {
         articleCounter++;

         articles[articleCounter] = Article(
             articleCounter,
             msg.sender,
             0x0,
             _name,
             _description,
             _price
         );

         sellArticleEvent(articleCounter, msg.sender, _name, _price);
     }

     // fetch the number of articles in the contract
     function getNumberOfArticles() public constant returns (uint) {
        return articleCounter;
     }

     // fetch and returns all article IDs available for sale
     function getArticlesForSale() public constant returns (uint[]) {
         if (articleCounter == 0) {
             return new uint[](0);
         }

         // prepare output arrays
         uint[] memory articleIds = new uint[](articleCounter);

         uint numberOfArticlesForSale = 0;
         for (uint i = 1; i <= articleCounter; i++) {
             if (articles[i].buyer == 0x0) {
                 articleIds[numberOfArticlesForSale] = articles[i].id;
                 numberOfArticlesForSale++;
             }
         }

         // copy result to smaller array
         uint[] memory forSale = new uint[](numberOfArticlesForSale);
         for (i = 0; i < numberOfArticlesForSale; i++) {
             forSale[i] = articleIds[i];
         }
        return forSale;
     }

     // buy an article
     function buyArticle(uint _id) payable public {
         // there should be at least 1 article
         require(articleCounter > 0);

         // the article should exist
         require(_id > 0 && _id <= articleCounter);

         // retrieve the article
         Article storage article = articles[_id];

         // should not be sold
         require(article.buyer == 0x00);

         // you cannot by your own article
         require(msg.sender != article.seller);

         // the value send transacted corresponds to the article price
         require(msg.value == article.price);

         // keep the buyer's information
         article.buyer = msg.sender;

         // the buyer can buy the article
         article.seller.transfer(msg.value);

         //trigger event
         buyArticleEvent(_id, article.seller, article.buyer, article.name, article.price);
     }
+
+    // kill the smart contract
+    function kill() onlyOwner {
+        selfdestruct(owner);
+    }
 }
```

create 2 articles in the frontend and in the truffle development console
```js
truffle(develop)> app.getArticlesForSale()
[ BigNumber { s: 1, e: 0, c: [ 1 ] },
  BigNumber { s: 1, e: 0, c: [ 2 ] } ]

// Try to kill from a wrong account
truffle(develop)> app.kill({from: web3.eth.accounts[1]})
Error: VM Exception while processing transaction: revert
    at Object.InvalidResponse (/usr/local/lib/node_modules/truffle/build/cli.bundled.js:41484:16)
    at /usr/local/lib/node_modules/truffle/build/cli.bundled.js:329530:36
    at /usr/local/lib/node_modules/truffle/build/cli.bundled.js:325200:9
    at XMLHttpRequest.request.onreadystatechange (/usr/local/lib/node_modules/truffle/build/cli.bundled.js:328229:7)
    at XMLHttpRequestEventTarget.dispatchEvent (/usr/local/lib/node_modules/truffle/build/cli.bundled.js:176415:18)
    at XMLHttpRequest._setReadyState (/usr/local/lib/node_modules/truffle/build/cli.bundled.js:176705:12)
    at XMLHttpRequest._onHttpResponseEnd (/usr/local/lib/node_modules/truffle/build/cli.bundled.js:176860:12)

// Try the correct one
truffle(develop)> app.kill({from: web3.eth.accounts[0]})
{ tx: '0xf9f1cc96f2a94b8a67cf5b743aca44f247cd05d2f593813d87f8afd7726bc8bf',
  receipt:
   { transactionHash: '0xf9f1cc96f2a94b8a67cf5b743aca44f247cd05d2f593813d87f8afd7726bc8bf',
     transactionIndex: 0,
     blockHash: '0x706fb8a3d98c6caaa479f76254ed3f651bf2eded8936018734e046675501afde',
     blockNumber: 8,
     gasUsed: 13467,
     cumulativeGasUsed: 13467,
     contractAddress: null,
     logs: [],
     status: 1 },
  logs: [] }

truffle(develop)> app.getArticlesForSale()
[]

truffle(develop)> app.buyArticle(1, {from: web3.eth.accounts[9], value: web3.toWei(1, 'ether')})
{ tx: '0xc5d27c88418c399dba259c607d76f536dfa4463b0c7c4d6076307c2470b0988b',
  receipt:
   { transactionHash: '0xc5d27c88418c399dba259c607d76f536dfa4463b0c7c4d6076307c2470b0988b',
     transactionIndex: 0,
     blockHash: '0x63120ace674993eb9a9bb8dd0ffb35cebfca62481b1e5cab746b6810f1220085',
     blockNumber: 12,
     gasUsed: 21464,
     cumulativeGasUsed: 21464,
     contractAddress: null,
     logs: [],
     status: 1 },
  logs: [] }
```
You can still call function of selfdestructed contract and if it is a payable function call, your ETH will be lost.
