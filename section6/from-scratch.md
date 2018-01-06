# Create Project From Starch

create npm project

```bash
mkdir -p /path/to/greetings/ && cd $_
npm init
npm install web3 solc
```

create  file `/path/to/greetings/Greetings.sol`

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

  function getGreetings() public constant returns (string) {
    return message;
  }
}
```

start testrpc node in another terminal

```bash
testrpc
```

start node console

```bash
cd /path/to/greetings/
node
```

inside the node console

```js
const Web3 = require('web3')
const fs = require('fs')
const web3 = new Web3(new Web3.providers.HttpProvider('http://localhost:8545'))
const solc = require('solc')

const sourceCode = fs.readFileSync('./Greetings.sol').toString()
const compiledCode = solc.compile(sourceCode)

const byteCode = compiledCode.contracts[':Greetings'].bytecode

const contractABI = JSON.parse(compiledCode.contracts[':Greetings'].interface)
const greetingsContract = web3.eth.contract(contractABI)

const greetingsDeployed = greetingsContract.new({data: byteCode, from: web3.eth.accounts[0], gas: 4700000})

const greetingsInstance = greetingsContract.at(greetingsDeployed.address)

greetingsInstance.getGreetings()  // I am ready!
greetingsInstance.setGreetings("42", {from: web3.eth.accounts[0]})
greetingsInstance.getGreetings()  // 42
```
