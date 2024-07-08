---
title: Stealth payments
type: docs
prev: docs/autopayments/sdk
next: docs/userop/
sidebar:
  open: true
---

## Private transaction with stealth addresses

Most of todays transactions are still processed using cash. Reason? Well privacy and finality.
Blockchain systems have done a good job at enabling finality but not so much on privacy. When
Bob knows Alice's address he can track her payments because everything is public. This is good
and bad. Good in that we can track malicious activity incase of a hack, bad in that even businesses
can't move their activities to crypto because their pivate financial activity will be monitored. Stealth addresses aims to solve this problem.

### How it works

Now if Charles wants to send Alice some funds he will have to generate a random number **`r`** multiply it with Alice's public key **`P`** and the send funds to the address generated from the resulting public key. In math terms: **`P * r`** = **`P'`**. Elliptic curve math helps us produces the private key of the resulting address by just taking the private key of the receiver and multiplying it with the random number **`r`**. **`p * r`** = **`s`**. Now Alice can spend the funds from the newly generated address and Bob has no idea that Alice received money from charles. As pointed out by [**Stealth payments**](https://eips.ethereum.org/EIPS/eip-5564 "Private") we take a different approach to avoid storing users private key. Alice generates view and spending key pairs and makes the public keys **`P_view`** and **`P_spend`** public. Now when Charles want to send Alice some funds he will generate a random number **`r`** and multiply it with **`P_spend`** to generate the stealth address. He then encrypts **`r`** with **`P_view`** and a random ephemeral private key. He sends funds to the stealth address and also sends the encrypted random number **`r`** and ephemeral public key outputted from the encryption for alice to prove funds belong to her. Alice starts computation by taking her view private key **`p_view`** and ephemeral public key received from charles and decrypts the random number. She then multiplies **`r`** with the spending private key **`p_spend`** and computes an address from this private key. If the address matches the stealth address charles sent funds to she owns the funds.

## Next

{{< cards >}}
{{< card link=https://eips.ethereum.org/EIPS/eip-5564 title="EIP" icon="document-search" subtitle="Read the stealth address EIP" >}}
{{< card link=https://app.umbra.cash/faq#how-does-it-work-technical title="Umbra" icon="document-search" subtitle="Read more about the protocol from Umbra" >}}
{{< /cards >}}
