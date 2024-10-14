---
title: Account abstraction
type: docs
prev: docs/
next: docs/Multi-Party Computation
---

Account abstraction is a new proposal in the Ethereum ecosystem that aims to change every wallet into a smart wallet. This is possible without any consensus layer changes and is done by introducing a higher-layer pseudo-transaction object called a UserOperation. Users send UserOperation objects into a new separate mempool. Bundlers package up a set of these objects into a single transaction by making a call to a special contract, and that transaction then gets included in a block. But why do we need this? What's wrong with the current wallet?

## Wallets

Ethereum has 2 types of accounts: External Owned Accounts(EOA) and Contract accounts. When Ethereum was proposed in 2014, only EOAs could initiate a transaction. This is because ONLY EOAs have asymmetric keys(Private and Public keys) for one to perform computation. So if you want to call a function or send some Ether you had to use an EOA to provide a valid signature and prove that you are authorized to perform that action.

### Why EOAs suck

As Ethereum grew, user realized how awful EOAs were. First, EOAs are vulnerable. With one wrong transaction, you lose everything. This is because asymmetric keys are static. They are the only way to prove to the protocol that you are authorized to perform a computation. So anyone with your keys can perform any computation and the protocol would just accept. Second, EOAs are ugly. You visit a dapp and you have to sign 5 transactions before you can perform a swap for example. Third, gas fees are hard. A user wants to send WBTC but can't do so because they don't have Ether. Lastly, and perhaps most importantly, the Elliptic Curve Digital Signature Algorithm (ECDSA) is vulnerable to quantum computers. Quantum computers have the capability to efficiently solve the discrete logarithm problem, a task that would take today's classical computers an impractical amount of time to complete.

So how do we solve all these issues. How do we create an agglomeration of decentralized finance around a wallet? Where you can perform a private transaction, recurring payments, launch tokens, launch NFTs and there is no single point of failure.

## Shakespay

Shakespay is the FIRST real smart wallet. Our goal is to remove all the issues named above. From better security to agglomeration of decentralized services we have done exactly that! Let's get to the specifics:

### Security

We avoid seed phrases. To make Bitcoin and Ethereum more accessible to everyone, we need to make them easy to use. However, we should not sacrifice self-custody! So when you see "Sign up with Google" you are actually going through a MPC protocol that generate private shares without any trusted party, this is explained more [here](../multi-party-computation). One share is encrypted and stored securely on the remote server then, using a secure transport layer with [AEAD](https://en.wikipedia.org/wiki/Authenticated_encryption#Authenticated_encryption_with_associated_data_(AEAD)) cipher, the other enrypted share is sent to your mobile device. If backed up through Google Drive or Icloud you can have it on any device you want! This removes the single point of failure that is a custom in 99% of todays wallets. How is this self-custody? Well we cannot access YOUR coins because we don't have your share. You and You alone can access and decrypt the remote share stored in the remote server. We are however working on an alternative. You may not trust the remote server. You should have a choice on who has access to those shares. This will however make transacting hard. That why we are supporting protocols like [passport](https://0xpass.io/)

### Accounts

When you want to open a Savings, Card or Auto account in Shakespay you get hit with this page:

<div
 style="display: flex;">
  <img
   src="/images/delegate.jpg" alt="User side features" width="300" height="200">
</div>

Seem centralized. Well its not! Actually when you submit a [transaction](https://polygonscan.com/tx/0xed78887c71b2d2cb2240eaaa27d368810d646824360bc9969a2c4311f03e35fd) you can see you are calling a contract. What's happening here?

<div
 style="display: flex;">
  <img
   src="/images/smart.png" alt="User side features">
</div>

The MPC protocol also outputs a public key. With this we can generate an address, through [hashing](https://github.com/ethereumbook/ethereumbook/blob/develop/04keys-addresses.asciidoc#ethereum-addresses). With this address we can deploy a smart wallet, with the address as the owner and a random salt value.

```solidity {filename="AccountFactory.sol"}
 function deployWallet(
        address walletOwner,
        uint256 salt
    ) external returns (ShakescoAccount) {
        address walletAddress = computeAddress(walletOwner, salt);

        uint256 codeSize = walletAddress.code.length;
        if (codeSize > 0) {
            return ShakescoAccount(payable(walletAddress));
        } else {
            return
                ShakescoAccount(
                    payable(
                        new ERC1967Proxy{salt: bytes32(salt)}(
                            address(accountImplementation),
                            abi.encodeCall(
                                ShakescoAccount.initialize,
                                (walletOwner)
                            )
                        )
                    )
                );
        }
    }
```

You now have a smart wallet! AWESOME 😁. Now you want to have a card account to perform autopayments, you want to save some Ether or you want to pull funds from your friends card every month. How do you own all these accounts without having 3 different keys? This is simple. Deploy the savings, card or auto wallets with your generated smart wallet address as the owner.

```solidity {filename="SavingsFactory.sol"}
function deploySaving(
        address payable accountAddress,
        uint256 salt,
        uint256 amountToReach,
        uint256 timeToReach
    ) external returns (ShakescoSavings) {
        address walletAddress = computeAddress(
            accountAddress,
            salt,
            amountToReach,
            timeToReach
        );

        uint256 codeSize = walletAddress.code.length;
        if (codeSize > 0) {
            return ShakescoSavings(payable(walletAddress));
        } else {
            return
                ShakescoSavings(
                    payable(
                        new ERC1967Proxy{salt: bytes32(salt)}(
                            address(savingImplementation),
                            abi.encodeCall(
                                ShakescoSavings.initialize,
                                (accountAddress, amountToReach, timeToReach)
                            )
                        )
                    )
                );
        }
    }
```

Because [ERC-4337](https://eips.ethereum.org/EIPS/eip-4337) allows us to batch transactions, when we need to interact with any of the three contracts/wallets we will route from the smart wallet.

```solidity {filename="Account.sol"}
function executeBatch(
        address payable[] calldata _to,
        uint256[] calldata _amount,
        bytes[] calldata func
    ) external onlyEntryPoint {
        if (
            _to.length != func.length &&
            (_amount.length != 0 || _amount.length != func.length)
        ) {
            revert ACCOUNT__INVALIDLENGTH();
        }
        if (_amount.length == 0) {
            for (uint256 i = 0; i < _to.length; i++) {
                _call(_to[i], 0, func[i]);
            }
        } else {
            for (uint256 i = 0; i < _to.length; i++) {
                _call(_to[i], _amount[i], func[i]);
            }
        }
    }
```

And that it! That's how Shakespay works! Self-custody and secure.

## Next

Dive right into the following section to get started:

{{< cards >}}
{{< card link=https://smart.shakesco.com title="Smart wallet" icon="finger-print" subtitle="Deploy smart wallet for your dAPP users" >}}
{{< card link=https://get.shakesco.com title="Download" icon="device-mobile" subtitle="I am convinced Shakespay is the ultimate smart wallet, i wanna sign up!" >}}
{{< /cards >}}
