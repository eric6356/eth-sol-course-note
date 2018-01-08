# Multiple Articles
- [mappings](http://solidity.readthedocs.io/en/develop/types.html#mappings)
- [struct](http://solidity.readthedocs.io/en/develop/types.html#structs)

update `contracts/ChainList.sol`
```Solidity
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
        return (articleCounter);
    }

    // fetch and returns all article IDs available for sale
    function getArticleForSale() public constant returns (uint[]) {
        // there should be at least 1 article
        require(articleCounter > 0);

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
        return (forSale);
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
}
```

play with it in the truffle develop console
```js
truffle(develop)> migrate
...

truffle(develop)> ChainList.deployed().then(i => app = i)
...

truffle(develop)> web3.eth.getBalance(web3.eth.accounts[1]).toString()
'100000000000000000000'

truffle(develop)> web3.eth.getBalance(web3.eth.accounts[2]).toString()
'100000000000000000000'

truffle(develop)> var sellEvent = app.sellArticleEvent({}, {fomBlock: 0, toBlock: 'latest'}).watch((err, ev) => console.log(ev))
truffle(develop)> var buyEvent = app.buyArticleEvent({}, {fromBlock: 0, toBlock: 'latest'}).watch((err, ev) => console.log(ev))
truffle(develop)> var txRes = app.sellArticle('Article 1', 'Description 1', web3.toWei(10, 'ether'), {from: web3.eth.accounts[1]})
{ logIndex: 0,
  transactionIndex: 0,
  transactionHash: '0x73a8a6ca8cb3bcbe7f24c14643d23e8c2c462e55f97cffbb68e0e6341a950647',
  blockHash: '0x88342c0be2ce957c7cca0fe5d3b906bcd6d91166773ec6796bee0047b9ba2cb8',
  blockNumber: 5,
  address: '0x345ca3e014aaf5dca488057592ee47305d9b3e10',
  type: 'mined',
  event: 'sellArticleEvent',
  args:
   { _id: BigNumber { s: 1, e: 0, c: [Array] },
     _seller: '0xf17f52151ebef6c7334fad080c5704d77216b732',
     _name: 'Article 1',
     _price: BigNumber { s: 1, e: 19, c: [Array] } } }

truffle(develop)> var txRes2 = app.sellArticle('Article 2', 'Description 2', web3.toWei(20, 'ether'), {from: web3.eth.accounts[1]})
{ logIndex: 0,
  transactionIndex: 0,
  transactionHash: '0xddf8c02eba4a9573adb09f1f3815138cd0e7ddffaff610255ff933e5c366f530',
  blockHash: '0xbbba86b285c0f439d2413902d07bd1f619b24b7e107f21c035a5c3929f143dad',
  blockNumber: 6,
  address: '0x345ca3e014aaf5dca488057592ee47305d9b3e10',
  type: 'mined',
  event: 'sellArticleEvent',
  args:
   { _id: BigNumber { s: 1, e: 0, c: [Array] },
     _seller: '0xf17f52151ebef6c7334fad080c5704d77216b732',
     _name: 'Article 2',
     _price: BigNumber { s: 1, e: 19, c: [Array] } } }

truffle(develop)> app.getArticleForSale()
[ BigNumber { s: 1, e: 0, c: [ 1 ] },
  BigNumber { s: 1, e: 0, c: [ 2 ] } ]

truffle(develop)> app.getNumberOfArticles()
BigNumber { s: 1, e: 0, c: [ 2 ] }

truffle(develop)> app.articles(1)
[ BigNumber { s: 1, e: 0, c: [ 1 ] },
  '0xf17f52151ebef6c7334fad080c5704d77216b732',
  '0x0000000000000000000000000000000000000000',
  'Article 1',
  'Description 1',
  BigNumber { s: 1, e: 19, c: [ 100000 ] } ]

truffle(develop)> var buyRes = app.buyArticle(1, {from: web3.eth.accounts[2], value: web3.toWei(10, 'ether')})
{ logIndex: 0,
  transactionIndex: 0,
  transactionHash: '0xd479817e3772a2c58452073ec00e1bd138aa3765b366700a61d532ac4d68c260',
  blockHash: '0x3514d0fe3587aa69549a7f231bab5b155a2e32b5788652d043335493159dfccc',
  blockNumber: 7,
  address: '0x345ca3e014aaf5dca488057592ee47305d9b3e10',
  type: 'mined',
  event: 'buyArticleEvent',
  args:
   { _id: BigNumber { s: 1, e: 0, c: [Array] },
     _seller: '0xf17f52151ebef6c7334fad080c5704d77216b732',
     _buyer: '0xc5fdf4076b8f3a5357c5e395ab970b5b54098fef',
     _name: 'Article 1',
     _price: BigNumber { s: 1, e: 19, c: [Array] } } }

truffle(develop)> app.getArticleForSale()
[ BigNumber { s: 1, e: 0, c: [ 2 ] } ]

truffle(develop)> web3.eth.getBalance(web3.eth.accounts[1]).toString()
'109970382400000000000'

truffle(develop)> web3.eth.getBalance(web3.eth.accounts[2]).toString()
'89994448300000000000'

truffle(develop)> app.articles(1)
[ BigNumber { s: 1, e: 0, c: [ 1 ] },
  '0xf17f52151ebef6c7334fad080c5704d77216b732',
  '0xc5fdf4076b8f3a5357c5e395ab970b5b54098fef',
  'Article 1',
  'Description 1',
  BigNumber { s: 1, e: 19, c: [ 100000 ] } ]
```
