# Deploy
- [Rinkeby](https://www.rinkeby.io)
- [Etherscan](https://rinkeby.etherscan.io)

start rinkey node
```bash
geth \
    --rinkeby \
    --rpc \
    --rpcapi="personal,eth,network,web3,net" \
    --ipcpath "~/Library/Ethereum/geth.ipc" \
    --rpccorsdomain "*"
```

request faucet found [here](https://www.rinkeby.io/#faucet)

modify `truffle.js`
```diff
 module.exports = {
     // See <http://truffleframework.com/docs/advanced/configuration>
     // for more about customizing your Truffle configuration!
     networks: {
         development: {
             host: "127.0.0.1",
             port: 9545,
              network_id: "*" // Match any network id
+         },
+         rinkeby: {
+             host: "127.0.0.1",
+             port: 8545,
+             network_id: "4",
+             gas: 5000000,  // gas for migration
+             from: "0x0d6eafe9ca0258b97839b07230a7ea8fa61b632a"
          }
     }
 };
```

migrate
```bash
truffle migrate --network rinkeby
```
