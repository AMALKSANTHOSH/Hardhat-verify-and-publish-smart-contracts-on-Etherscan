
# Hardhat verify and publish smart contracts on Etherscan

Smart contract verification is important for decentralized projects. You can publish your project codes on etherscan.io with auto contract verification. Also, read and write function interactions will be available on etherscan.io when you verify the contract.

Although manual verification is available on etherscan.io, Hardhat uses plugin for integration with etherscans contract verification service.This plugin helps you verify the source code for your Solidity contracts on Etherscan.


## Create a hardhat project

Start your project with npx harhat

```bash
  mkdir hardhat-project
  cd hardhat-project
  npm install hardhat
  npx hardhat
```

After this command you have a boilerplate hardhat project.

Let’s code a simple smart contract.



```bash
  touch contracts/VERIFYTOKEN.sol
```
Add below simple lines to VERIFYTOKEN.sol:
    
```bash
  // SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract VERIFYTOKEN is ERC721, Ownable {
    constructor() ERC721("VerifyToken", "VTK") {}

    function safeMint(address to, uint256 tokenId) public onlyOwner {
        _safeMint(to, tokenId);
    }
}
```

Add below lines to hardhat.config.js

```bash
  module.exports = {
  solidity: "0.8.4",
  docker: false,
  networks: {
    rinkeby: {
      url: secrets.url,
      accounts: [secrets.key]
    }
  },
  etherscan: {
    apiKey: secrets.etherscanKey
  }
};
```
When you compile contracts you should see a success message:


```bash
  npx hardhat compile

```
Output:


```bash
  > Compiled 11 Solidity files successfully
```

## Add hardhat verification plugin to the project


```bash
  npm install @nomiclabs/hardhat-etherscan
```

Then add hardhat plugin inside of plugins top of hardhat.config.js
```bash
require(”@nomiclabs/hardhat-etherscan”);
```
Next up, We’ll create a ”networks” object where we add the Rinkeby network with a few secret details. To enable our ”networks” object to access the secret URL and key, we must create a ”secrets.json” file.The reason for doing this is because of the sensitive information stored, which is not something we’d like to share with others.

As such, we can go ahead and create a file called ”secrets.json”.


```bash
  {
    "url":"https://rinkeby.infura.io/v3/YOUR_INFURA_ID",
    "key":"PRIVATE_KEY_FROM_METAMASK",
    "etherscanKey":"KEY_FROM_ETHERSCAN"
}

```
Add those lines at the top of your hardhat.config.js
```bash
  require("@nomiclabs/hardhat-waffle");
  require("@nomiclabs/hardhat-etherscan");
  let secrets = require("./secrets");

```




## Create an api key on Etherscan.io

We need to generate an etherscan.io API key. Sign up and generate your API key here: https://etherscan.io/myapikey

Then add api-keys to hardhat.config.js

```bash
 api_keys: {
      etherscan: secrets.etherscanKey
    }
```



## Create an api key on Infura

Create an account on infura.io and create an Ethereum project (https://infura.io/dashboard/ethereum). Click the settings of your recently created project.

Select Rinkeby testnet and copy your URL. URL should be like that: https://rinkeby.infura.io/v3/YOUR_PROJECT_ID
## Create a deployment script for the contract deployment
```bash
   touch scripts/deploy.js


```
You will see a new file in the scripts folder. We will add those lines to deploy.js
```bash
 const hre = require("hardhat");
async function main() {
  const VERIFYTOKEN = await hre.ethers.getContractFactory("VERIFYTOKEN");
  const verifytoken = await VERIFYTOKEN.deploy();

  await verifytoken.deployed();

  console.log("NFT deployed to:", verifytoken.address);
}
main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });

```
## Deploy the contract
```bash
 npx hardhat run scripts/deploy.js --network rinkeby


```

It will take a few seconds to deploy the contract.

```bash
 NFT deployed to: 0x88e15c8eC5433e70950b0E794Bd9D7E96F562c7E

```


Contract is not verified yet, let’s verify it.




## Verify the contract
```bash
  npx hardhat verify 0x88e15c8eC5433e70950b0E794Bd9D7E96F562c7E  --network rinkeby


```

It will take a while to verify.

```bash
Nothing to compile
Successfully submitted source code for contract
contracts/VERIFYTOKEN.sol:VERIFYTOKEN at 0x88e15c8eC5433e70950b0E794Bd9D7E96F562c7E
for verification on the block explorer. Waiting for verification result...


```

