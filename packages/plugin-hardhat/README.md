# OpenZeppelin Hardhat Upgrades

[![Docs](https://img.shields.io/badge/docs-%F0%9F%93%84-blue)](https://docs.openzeppelin.com/upgrades-plugins/hardhat-upgrades)
[![NPM Package](https://img.shields.io/npm/v/@openzeppelin/hardhat-upgrades.svg)](https://www.npmjs.org/package/@openzeppelin/hardhat-upgrades)

**Hardhat plugin for deploying and managing upgradeable contracts.** This package adds functions to your Hardhat scripts so you can deploy and upgrade proxies for your contracts. Depends on `ethers.js`.

## Installation

```
npm install --save-dev @openzeppelin/hardhat-upgrades
npm install --save-dev @nomicfoundation/hardhat-ethers ethers # peer dependencies
```

And register the plugin in your [`hardhat.config.js`](https://hardhat.org/config/):

```js
// Javascript
require('@openzeppelin/hardhat-upgrades');

// Typescript
import '@openzeppelin/hardhat-upgrades';
```

## Usage in scripts

### Proxies

You can use this plugin in a [Hardhat script](https://hardhat.org/guides/scripts.html) to deploy an upgradeable instance of one of your contracts via the `deployProxy` function:

```js
// scripts/create-box.js
const { ethers, upgrades } = require("hardhat");

async function main() {
  const Box = await ethers.getContractFactory("Box");
  const box = await upgrades.deployProxy(Box, [42]);
  await box.waitForDeployment();
  console.log("Box deployed to:", await box.getAddress());
}

main();
```

This will automatically check that the `Box` contract is upgrade-safe, deploy an implementation contract for the `Box` contract (unless there is one already from a previous deployment), create a proxy (along with a proxy admin if needed), and initialize it by calling `initialize(42)`.

Then, in another script, you can use the `upgradeProxy` function to upgrade the deployed instance to a new version. The new version can be a different contract (such as `BoxV2`), or you can just modify the existing `Box` contract and recompile it - the plugin will note it changed.

```js
// scripts/upgrade-box.js
const { ethers, upgrades } = require("hardhat");

async function main() {
  const BoxV2 = await ethers.getContractFactory("BoxV2");
  const box = await upgrades.upgradeProxy(BOX_ADDRESS, BoxV2);
  console.log("Box upgraded");
}

main();
```

> Note: While this plugin keeps track of all the implementation contracts you have deployed per network, in order to reuse them and validate storage compatibilities, it does _not_ keep track of the proxies you have deployed. This means that you will need to manually keep track of each deployment address, to supply those to the upgrade function when needed.

The plugin will take care of comparing `BoxV2` to the previous one to ensure they are compatible for the upgrade, deploy the new `BoxV2` implementation contract (unless there is one already from a previous deployment), and upgrade the existing proxy to the new implementation.

### Beacon proxies

You can also use this plugin to deploy an upgradeable beacon for your contract with the `deployBeacon` function, then deploy one or more beacon proxies that point to it by using the `deployBeaconProxy` function.

```js
// scripts/create-box.js
const { ethers, upgrades } = require("hardhat");

async function main() {
  const Box = await ethers.getContractFactory("Box");

  const beacon = await upgrades.deployBeacon(Box);
  await beacon.waitForDeployment();
  console.log("Beacon deployed to:", await beacon.getAddress());

  const box = await upgrades.deployBeaconProxy(beacon, Box, [42]);
  await box.waitForDeployment();
  console.log("Box deployed to:", await box.getAddress());
}

main();
```

Then, in another script, you can use the `upgradeBeacon` function to upgrade the beacon to a new version. When the beacon is upgraded, all of the beacon proxies that point to it will use the new contract implementation.

```js
// scripts/upgrade-box.js
const { ethers, upgrades } = require("hardhat");

async function main() {
  const BoxV2 = await ethers.getContractFactory("BoxV2");

  await upgrades.upgradeBeacon(BEACON_ADDRESS, BoxV2);
  console.log("Beacon upgraded");

  const box = BoxV2.attach(BOX_ADDRESS);
}

main();
```

## Usage in tests

You can also use the plugin's functions from your Hardhat tests, in case you want to add tests for upgrading your contracts (which you should!). The API is the same as in scripts.

### Proxies

```js
const { expect } = require("chai");

describe("Box", function() {
  it('works', async () => {
    const Box = await ethers.getContractFactory("Box");
    const BoxV2 = await ethers.getContractFactory("BoxV2");
  
    const instance = await upgrades.deployProxy(Box, [42]);
    const upgraded = await upgrades.upgradeProxy(await instance.getAddress(), BoxV2);

    const value = await upgraded.value();
    expect(value.toString()).to.equal('42');
  });
});
```

### Beacon proxies

```js
const { expect } = require("chai");

describe("Box", function() {
  it('works', async () => {
    const Box = await ethers.getContractFactory("Box");
    const BoxV2 = await ethers.getContractFactory("BoxV2");

    const beacon = await upgrades.deployBeacon(Box);
    const instance = await upgrades.deployBeaconProxy(beacon, Box, [42]);
    
    await upgrades.upgradeBeacon(beacon, BoxV2);
    const upgraded = BoxV2.attach(await instance.getAddress());

    const value = await upgraded.value();
    expect(value.toString()).to.equal('42');
  });
});
```

## Using safe.global

Integrate safe.global sdk to allow upgradeable smart contracts deployment & upgrades using multi-sig wallet.

### Example usage:

#### Set configuration parameters in .env

```
SIGNER="<< OWNER or DELEGATE >>"
CREATE2_SALT="<< CREATE2_SALT >>"
SAFE_ADDRESS="<< SAFE_ADDRESS >>"
SAFE_TX_SERVICE_URL="<< SAFE_TX_SERVICE_URL >>"
SAFE_SINGLETON_ADDRESS="<< SAFE_SINGLETON_ADDRESS >>"
SAFE_PROXY_FACTORY_ADDRESS="<< SAFE_PROXY_FACTORY_ADDRESS >>"
MULTI_SEND_ADDRESS="<< MULTI_SEND_ADDRESS >>"
MULTI_SEND_CALL_ONLY_ADDRESS="<< MULTI_SEND_CALL_ONLY_ADDRESS >>"
COMPATIBILITY_FALLBACK_HANDLER_ADDRESS="<< COMPATIBILITY_FALLBACK_HANDLER_ADDRESS >>"
SIGN_MESSAGE_LIB_ADDRESS="<< SIGN_MESSAGE_LIB_ADDRESS >>"
CREATE_CALL_ADDRESS="<< CREATE_CALL_ADDRESS >>"
SIMULATE_TX_ACCESSOR_ADDRESS="<< SIMULATE_TX_ACCESSOR_ADDRESS >>"
```

#### Deploy upgradeable contract

```ts
import { ethers, upgrades } from 'hardhat';

async function main () {
  const safeAddress = '0x';
  const Socks = await ethers.getContractFactory('Socks');
  console.log('Deploying Socks...');
  const socks = await upgrades.deployProxy(Socks, [42], { 
    initializer: 'initialize', 
    initialOwner: safeAddress,

    useSafeGlobalDeploy: true,
    txServiceUrl: process.env.SAFE_TX_SERVICE_URL,
    salt: process.env.CREATE2_SALT,
    safeAddress,
    safeSingletonAddress: process.env.SAFE_SINGLETON_ADDRESS,
    safeProxyFactoryAddress: process.env.SAFE_PROXY_FACTORY_ADDRESS,
    multiSendAddress: process.env.MULTI_SEND_ADDRESS,
    multiSendCallOnlyAddress: process.env.MULTI_SEND_CALL_ONLY_ADDRESS,
    fallbackHandlerAddress: process.env.COMPATIBILITY_FALLBACK_HANDLER_ADDRESS,
    signMessageLibAddress: process.env.SIGN_MESSAGE_LIB_ADDRESS,
    createCallAddress: process.env.CREATE_CALL_ADDRESS,
    simulateTxAccessorAddress: process.env.SIMULATE_TX_ACCESSOR_ADDRESS,
  });
  await socks.waitForDeployment();
  console.log('Socks deployed to:', socks.target);
}

main();
```

#### Upgrade:

```ts
// scripts/deploy_upgradeable_box.js
import { ethers, upgrades } from 'hardhat';

async function main () {
  const safeAddress = '0x';
  const proxyAddress = '0x';
  const SocksV2 = await ethers.getContractFactory('SocksV2');
  console.log('Upgrading Socks...');
  await upgrades.upgradeProxy(proxyAddress, SocksV2, {
    useSafeGlobalDeploy: true,
    txServiceUrl: process.env.SAFE_TX_SERVICE_URL,
    salt: process.env.CREATE2_SALT,
    safeAddress,
    safeSingletonAddress: process.env.SAFE_SINGLETON_ADDRESS,
    safeProxyFactoryAddress: process.env.SAFE_PROXY_FACTORY_ADDRESS,
    multiSendAddress: process.env.MULTI_SEND_ADDRESS,
    multiSendCallOnlyAddress: process.env.MULTI_SEND_CALL_ONLY_ADDRESS,
    fallbackHandlerAddress: process.env.COMPATIBILITY_FALLBACK_HANDLER_ADDRESS,
    signMessageLibAddress: process.env.SIGN_MESSAGE_LIB_ADDRESS,
    createCallAddress: process.env.CREATE_CALL_ADDRESS,
    simulateTxAccessorAddress: process.env.SIMULATE_TX_ACCESSOR_ADDRESS,
  });
  console.log('Socks upgraded');
}

main();
```

## Learn more
* Refer to the [API documentation](https://docs.openzeppelin.com/upgrades-plugins/api-hardhat-upgrades).
* Also see the [main documentation](https://docs.openzeppelin.com/upgrades-plugins) for more info.
