---
title: User-operation
type: docs
prev: docs/support/api
sidebar:
  open: true
---

## @shakesco/userop

This repository makes it easy for developers to create userop and estimate gas that can be sent
to bundlers for execution!

### Install

To install:

```shell {filename=cmd}
npm i @shakesco/userop
```

After installing:

```javascript {filename=index.js}
const shakesco = require("@shakesco/userop");
```

### What is a User operation

With traditional wallets, you had to specify the address that will receive the funds and the amount only. However smart wallets are different. To send funds using a smart wallet, you actually have to send a request to a bundler for execution. That request is call User Operation or simply Userop/intent.

To send a user operation or intents you need:

1. __Sender__ - This is the smart account
2. __Nonce__ - This is the smart accounts number of intent sent from deployment
3. __initCode__ - This is the code used to deploy the sender if not yet on-chain.
4. __callData__ - Data that you want the smart account to execute
5. __callGasLimit__ - Gas limit for the execution phase
6. __verificationGasLimit__ - Gas limit for the verification phase
7. __preVerificationGas__ - Gas to compensate the bundler for the overhead to submit the User Operation.
8. __maxFeePerGas__ - Similar to eip 1559
9. __maxPriorityFeePerGas__ - Similar to eip 1559
10. __paymasterAndData__ - The paymaster plus data used to sponsor transaction and verify legibility
11. __signature__ - The signature from the owner of the smart wallet.

So let's start with nonce because sender is pretty straightforward.

#### Nonce

To get the nonce call the function below from your smart account:

```javascript {filename=index.js}
const ACCOUNT = new ethers.Contract(sender, smartWalletABI, provider);
const nonce = await ACCOUNT.getNonce();
```

{{< callout type="info" >}}
This assumes you have deployed smart wallet. If not populate nonce with "0x0"
{{< /callout >}}

#### Initcode

To get the initcode you need the [_factory_](https://github.com/eth-infinitism/account-abstraction/blob/develop/contracts/samples/SimpleAccountFactory.sol "Factory") contract. You can create the initcode by simply doing:

```javascript {filename=index.js}
  const factory = new ethers.Interface(accountFactoryABI);
  const initCode = ethers.concat([
    accountFactoryAddress,
    factory.encodeFunctionData("deployWallet", [
      owner, //owner of the smart wallet
      salt, //random salt value
    ]),
  ]);
```

#### Calldata

The calldata is the function you want to execute. So you encode the function as below:

```javascript {filename=index.js}
const accountABI = new ethers.Interface(smartWalletABI);

const calldata = accountABI.encodeFunctionData("execute", 
    [
        address,
        ethers.parseEther(value),
        "0x",
    ]);
```

{{< callout type="info" >}}
Above calldata is a simple one requesting the smart wallet to send eth to address.
{{< /callout >}}

#### Gas

Now to the gas value:

Use our package to get the eip 1559 gas value as below:

```javascript {filename=index.js}
  const provider = new ethers.JsonRpcProvider(
    process.env.RPC_URL
  );
  const { maxFeePerGas, maxPriorityFeePerGas } = await shakesco.getEIP1559(
    provider
  );
  console.log(maxFeePerGas); //0x1500000016
  console.log(maxPriorityFeePerGas); //0x1500000000
```

To get the other userop gas value:

```javascript {filename=index.js}
  const { callGasLimit, preVerificationGas, verificationGasLimit } =
    await shakesco.useropGasValues(
      smartWallerAddress,
      provider,
      calldata,
      initCode
    );
  console.log(callGasLimit); //0x83074
  console.log(preVerificationGas); //0x01228e
  console.log(verificationGasLimit); //0x0186a0
```

{{< callout type="info" >}}
If smart wallet is deployed pass `initCode` as empty string. If you also don't want to call any data from the smart wallet pass `calldata` an empty string.
{{< /callout >}}

#### Prepare for signing

Finally we need to sign the userop so as to send the userop. We can do this by:

```javascript {filename=index.js}
 const arraifiedHash = shakesco.useropHash(
    smartWallerAddress,
    nonce,
    initCode,
    calldata,
    callGasLimit,
    verificationGasLimit,
    preVerificationGas,
    maxFeePerGas,
    maxPriorityFeePerGas,
    paymasterAndDataNOSIG,
    chainId
  );
console.log(arraifiedHash);
```

#### Sign

After getting the hash we can sign this by doing:

```javascript {filename=index.js}
   const signer = new ethers.Wallet(process.env.PRIV_KEY, provider);
   const signature = await signer.signMessage(arraifiedHash);
```

{{< callout type="info" >}}
This assumes that you passed one owner while deploying wallet. If using multi-signature or Threshold signatures this will not work.
{{< /callout >}}

You now have all the value to send the userop. Pass them as required by your bundler provider.
