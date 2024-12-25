---
title: Stealth Address Integration
type: docs
prev: docs/private/
next: docs/support
---

> Special credit to [_umbra-cash_](https://app.umbra.cash/ "Umbra").

This package will allow you to perform private transactions where only the sender and receiver
know the destination of the transaction. To understand how it works: [**umbra-docs**](https://app.umbra.cash/faq#how-does-it-work-technical "Umbra"), [**EIP 5564**](https://eips.ethereum.org/EIPS/eip-5564 "EIP 5564")

## @shakesco/private

_We assume that you have a single private key securing your wallet and that you are signing the same message hash. The former is not advised which will not lead to the latter._

### Install

To get started:

```shell {filename=cmd}
npm i @shakesco/private
```

After installing:

```javascript {filename="index.js"}
const shakesco = require("@shakesco/private");
const { KeyPair, RandomNumber, StealthKeyRegistry, utils } = shakesco;
const { IsUsersFunds, generateKeyPair, prepareSend } = shakesco;
```

### Check for stealth keys

We use the umbra registry to register stealth keys. To check if user has keys:

```javascript {filename=index.js}
const provider = new ethers.JsonRpcProvider(process.env.RPC_URL);
const registry = new StealthKeyRegistry(provider);

const { spendingPublicKey, viewingPublicKey } = await registry.getStealthKeys(
  recipientId
);
console.log(spendingPublicKey);
console.log(viewingPublicKey);
```

### Register stealth keys

If an empty string is returned the user has not registered for private transactions. So you register them as follows:

#### For Smart wallet

```javascript {filename=index.js}
const provider = new ethers.JsonRpcProvider(process.env.RPC_URL);
const signer = new ethers.Wallet(process.env.PRIV_KEY, provider);
const signature = await signer.signMessage(messageHash);
const { spendingKeyPair, viewingKeyPair } = await generateKeyPair(signature);
console.log(viewingKeyPair.privateKeyHex); // storing this for the user is okay! To fetch transactions for them easily. You can also choose to not store it.
const registry = new StealthKeyRegistry(provider);

const { spendingPrefix, spendingPubKeyX, viewingPrefix, viewingPubKeyX } =
  await registry.setSmartStealthKeys(
    spendingKeyPair.publicKeyHex,
    viewingKeyPair.publicKeyHex
  );
```

{{< callout type="info" >}}
You can call the registry contract with the above details as the parameter.
{{< /callout >}}

like so:

```javascript {filename=index.js}
calldata = accountABI.encodeFunctionData("execute", [
  CONTRACTS[chainID]["StealthRegistry"],
  0,
  stealthABI.encodeFunctionData("setStealthKeys", [
    spendingPrefix,
    spendingPubKeyX,
    viewingPrefix,
    viewingPubKeyX,
  ]),
]);
```

#### For EOAs

```javascript {filename=index.js}
const provider = new ethers.JsonRpcProvider(process.env.RPC_URL);
const { spendingKeyPair, viewingKeyPair } = await generateKeyPair(setupSig);
const registry = new StealthKeyRegistry(provider);

const { spendingPrefix, spendingPubKeyX, viewingPrefix, viewingPubKeyX } =
  await registry.SetEOAStealthKeys(
    spendingKeyPair.publicKeyHex,
    viewingKeyPair.publicKeyHex
  );
```

### Get stealth address

Your user is now ready to perform private transactions. To prepare the payee to receive a private transaction:

```javascript {filename=index.js}
   const payee = //payee address
   const provider = //node provider eg: alchemy
   const { stealthKeyPair, pubKeyXCoordinate, encrypted } =
        await prepareSend(address, provider);
   console.log(stealthKeyPair.address);// address funds should be sent to. This is a stealth address that the payee can control.
   console.log(pubKeyXCoordinate); // Public key that the payee will use to decrypt the ciphertext hence proving funds belong to them
   console.log(encrypted.ciphertext);// Encrypted random number used to generate the stealth address.
```

### Scan funds

{{< callout type="info" >}}
You need to send the ciphertext and publickey to the payee. Otherwise they will not be able to prove ownership of funds. You can use tools like [**the graph**](https://thegraph.com/en/ "Graph") or [**moralis**](https://moralis.io/ "Moralis") to query the 'Announcement' from your private contract after a transaction has been initiated.
{{< /callout >}}

```solidity {filename=private.sol}
 event Announcement(
        address indexed receiver, // put stealth address here
        uint256 amount,
        address indexed tokenAddress,
        bytes32 pkx, //publickey here
        bytes32 ciphertext //ciphertext here
    );
```

### Check for funds

To check if funds belong to a certain user:

```javascript {filename=index.js}
IsUsersFunds(object.announcements[i], provider, secret, sender).then((data) => {
  if (data.isForUser) {
    //belongs to user
    //perform any action you want with the data.
  }
});
```

### Private key for stealth address

If the funds belong to the user they can spend the funds. To create the private key that will be able to do this:

```javascript {filename=index.js}
const provider = new ethers.JsonRpcProvider(process.env.RPC_URL);
const signer = new ethers.Wallet(process.env.PRIV_KEY, provider);
const signature = await signer.signMessage(messageHash);
const { spendingKeyPair, viewingKeyPair } = await generateKeyPair(signature);

const payload = {
  ephemeralPublicKey: uncompressedPubKey,
  ciphertext: ciphertext,
};

const random = await viewkey.decrypt(payload);

const privkey = KeyPair.computeStealthPrivateKey(
  spendingKeyPair.privateKeyHex,
  random //decrypted random number
);

const wallet = new ethers.Wallet(privkey, provider);
const txResponse = await wallet.sendTransaction({
  value: ethers.parseEther(value),
  to: address,
});
const response = await txResponse.wait();
```

You have successfully sent a private transaction. We aim to help umbra expand the adoption of stealth payments. This will however be replaced by ZK but this is a good start to make ethereum more private!

### Contribute

If you love what we do to progress privacy, [contribute](https://donate.shakesco.com/@shakesco.sns "shakesco_donations") to further development
