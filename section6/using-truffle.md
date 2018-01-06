# Create Project Using Truffle

create project

```bash
mkdir -p /path/to/GreetingsTruffle/ && cd $_
truffle init
```

remove example files `contracts/ConvertLib.sol` `contracts/MetaCoin.sol` `test/*`

create file `contracts/Greetings.sol`

```Solidity
pragma solidity ^0.4.11;

contract Greetings {
  string message;

  function Greetings() public {
    message = "I am ready!";
  }

  function setGreetings(string _message) public {
    message = _message;
  }

  function getGreetings() public returns (string) {
    return message;
  }
}
```

edit file `migrations/2_deploy_contracts.js`

```js
var Greetings = artifacts.require("./Greetings.sol");

module.exports = function(deployer) {
  deployer.deploy(Greetings);
};
```

start testrpc in another terminal

```bash
testrpc
```

deploy the contract

```bash
truffle migrate  # deploy the contract to the test rpc node
truffle migrate  # this will do nothing because the contract is already deployed
truffle migrate --reset  # this will force deploy the contract again
```

start a truffle console

```bash
truffle console
```

inside the truffle console

```js
Greetings.address  // show the address of greeting contract
web3.eth.accounts  // list all the accounts
Greetings.deployed().then(function (instance) {app = instance})  // get greeting contract instance
app.getGreetings.call()  // "I am ready!", this do not need gas
web3.eth.getBalance(web3.eth.accounts[1]).toNumber()  // 100000000000000000000, default balance set by testrpc
app.setGreetings("42", {from : web3.eth.accounts[1]})  // account[1] execute the contract
web3.eth.getBalance(web3.eth.accounts[1]).toNumber()  // 99996722600000000000
app.getGreetings.call()  // "42"
```



