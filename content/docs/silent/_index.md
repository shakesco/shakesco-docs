---
title: Silent Payments
type: docs
prev: docs/autopayments/sdk
next: docs/silent/integration
sidebar:
  open: true
---

## Bitcoin Silent Payments

When Satoshi, the pseudonymous creator of Bitcoin, wrote the [Bitcoin whitepaper](https://bitcoin.org/bitcoin.pdf "whitepaper"), a section[10] was dedicated to the topic of privacy. The goal was to achieve privacy by using a different key pair for every transaction or what we call deterministic wallets. This level of privacy is good. First, it requires low mental overhead because Alice only needs to store the master secret and can recover all funds sent to the private keys (addresses) generated from the master secret. Second, without much scrutiny, it provides normal privacy.

But with on-chain sleuths, this level of privacy is awful. If Alice sends Bitcoin to Bob, then funds are sent to a new address, which she then uses to send to Charles, Bob or even Charles can respectively track the destination or origin of the Bitcoins.

There has already been proposals to address this issue. One is [BIP 47](https://bips.dev/47/ "proposal"), which was used by the famous Samourai Wallet.(#FREESAMOURAI) This proposal utilizes a single notification transaction to signal to the recipient to watch for payments to a specific public key, instead of including payment code signaling in every payment. This affects two things: privacy and fees. Since you have to send two transactions on-chain, the notification and the funds, you need to pay higher fees than what a normal transaction would cost. Additionally, sending a notification already disrupts privacy, as anyone on-chain can see that notification.

So we are looking for a solution that:

1. Does not required additional fee.
2. Does not break privacy.
3. Should be easy to use. Mostly static address.

### How it works

{{< callout type="info" >}}
Lowercase represent private keys. Upper case represent public key. `G` is a generator point on the curve, secp256k1. `·` represents scalar multiplication.
{{< /callout >}}

Alice published her public key A for Bob to send her some Bitcoin privately. Bob takes her public key and creates a shared secret with it to derive a destination address from which Alice can spend funds from. In math terms **`A + hash(b · A) · G`** = **`P`**

`b` in this case is bob's Unspent Transaction Output(UTXO) private key. Since `b · A == a · B` [**(Elliptic Curve Diffie-Hellman)**](https://en.wikipedia.org/wiki/Elliptic-curve_Diffie%E2%80%93Hellman "ECDH"), Alice can prove the Bitcoin sent is hers by taking every output public key from transactions and computing: **`A + hash(a · B) · G`** = **`P`**

If Bob continues to spend from the same UTXO with the public key B, he will end up deriving the same output P. To avoid this Bob can include the input hash, hash of the transaction id and output index, in his computation:

**`A + hash(input_hash · b · A) · G`** = **`P`**

As clear from the computations above, Alice will need to provide her private key, `a` for scanning. This is risky, especially if Alice wants to delegate this work to a third party. Alice therefore generates 2 private keys `a_spend` and `a_view` with their corresponding public keys, `A_spend` and `A_view`. She published the public keys for Bob who then tweaks his computation as so:

**`A_spend + hash(input_hash · b · A_view) · G`** = **`P`**

Alice can use her `a_view` private key for scanning and keep her spend private key, `a_spend`, private.

**`A_spend + hash(input_hash · B · a_view) · G`** = **`P`**

When Alice wants to spend the Bitcoins sent to the taproot address, she can use the following computation to derive the address's private key:

**`a_spend + hash(input_hash · B · a_view) mod n`** = **`p`**

Charles on the other hand has no idea that Alice has received some Bitcoins from Bob 🎊

That's it. For a more simplified or complex explanation, check below.

Now let's integrate! 🏃

## Next

{{< cards >}}
{{< card link=https://github.com/bitcoin/bips/blob/master/bip-0352.mediawiki title="BIP" icon="document-search" subtitle="Get to understand how silent payment works" >}}
{{< card link=https://silentpayments.xyz/docs/explained/ title="Silent payments" icon="document-search" subtitle="Read more about silent payments" >}}
{{< /cards >}}
