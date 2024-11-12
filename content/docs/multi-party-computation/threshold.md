---
title: Threshold Cryprography
type: docs
prev: docs/Multi-Party Computation/mpc_vs_smart
next: docs/autopayments/
---

## Threshold Secret Sharing(TSS)

> _This sections borrows from [Binance](https://github.com/bnb-chain/tss-lib "tss.lib")_

Threshold cryptography or __TSS__ is used in this case to secure wallets for shakesco users.
Rather than having a single private key that can be exploited by an attacker with adequate
resources and time, the system distributes shares of this private key _`a`_ to _`n`_ parties
where _`t+1`_ parties can provide a valid signature for any computation they want to achieve, in
this case signing wallet transactions. The system is somehow similar to traditional [__Multisig__](https://en.wikipedia.org/wiki/Cryptocurrency_wallet#Multisignature_wallet "Multisig")
but with advantages:

1. Transactions don't reveal the _`t+1`_ parties that signed
2. One signature is produced for verification
3. Due to reason 2 above nodes don't have to go through extra computation to validate signature

To have a secure system we employ [__TLS-AEAD__ (Transport Layer Security-Advanced Encryption with Associated Data)](https://en.wikipedia.org/wiki/Authenticated_encryption#Authenticated_encryption_with_associated_data_(AEAD)). Participants can be sure that any output by the __TSS__ system is end-to-end encypted.

> 📓 __Note__: To make this user friendly at the start, user stores an encrypted share on a remote server and the other encrypted share is sent to their device. In the future, they will decide the m-of-n structure that they want to employ.

The following are the steps to produce shares between parties:

1. [__Key Generation__](#key-generation "Keygen")
2. [__Signing__](#signing "Sign")
3. [__Resharing__](#resharing "Reshare")

### Key Generation

With no trusted dealer _`n`_ parties can produce shares of private key _`a`_ encrypt and store them locally and any _`t+1`_ parties can produce a valid signature. The system must ensure that the _`n`_ parties have the same public key __`P`__ but different secret shares _`a`_<sub>_`n`_</sub>. The system guarantees that no share will be leaked to any eavesdropper and that the public key is a function of the secret shares _`a`_<sub>_`n`_</sub>

### Signing

When users want to perform computation,in this case signing, only _`t+1`_ can participate to produce a valid signature. The parties don't have to reconstruct the shares in order to produce a valid signature unlike what is observed in [__Shamir Secret Sharing__](https://en.wikipedia.org/wiki/Shamir%27s_secret_sharing "SSS"). This introduce an attack vector we avoided with distbuted key generation above, single point of failure.  The system guarantees that during this computation the _`t+1`_ parties learn nothing about _`a`_<sub>_`n`_</sub> and the output _`sig`_ can be verified by the public key __`P`__.

### Resharing

In case any of the _`n`_ participants is compromised the participants can reshare their secret shares and get new ones. Old shares will be made invalid and they can use the new shares for future computations. This is a naive approach and we believe [proactive refresh](https://eprint.iacr.org/2022/1656.pdf) is better. We are working on it!
