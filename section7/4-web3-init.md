# Init Web3 in Browser

If using a general browser with MetaMask extension, or a mist browser, `window.web3` will be injected, otherwise it should be initialized manually.
```js
App = {
    web3Provider: null,

    initWeb3: function () {
        // Initialize web3 and set the provider to the truffle dev
        if (typeof web3 !== 'undefined') {
            App.web3Provider = web3.currentProvider;
            // Initialize web3 even if it already exists,
            // because the injected web3 may be created by an outdated library
            web3 = new Web3(web3.currentProvider);
        } else {
            // set the provider you want from Web3.providers
            App.web3Provider = new Web3.providers.HttpProvider('http://localhost:9545');
            web3 = new Web3(App.web3Provider);
        }
    }
}
```
