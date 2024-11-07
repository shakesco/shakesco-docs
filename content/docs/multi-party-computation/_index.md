---
title: Multi-Party Computation
type: docs
prev: docs/smart-wallet
next: docs/Multi-Party Computation/mpc_vs_smart
sidebar:
  open: true
---

## Multi-Party Computation (MPC)

Multi-Party Computation (MPC) is an advanced cryptographic method that allows multiple parties to collaboratively compute a function over their private inputs, ensuring that no single party learns anything beyond the final result. This technique enables parties to jointly perform calculations on their private data without disclosing the data itself, thereby maintaining privacy and confidentiality.

### Purpose

For example, consider three parties: Alice, Bob, and Charles. They want to determine who makes the most among them without revealing their individual earnings to each other. With MPC, they can perform this computation with certain assurances about the output, which include:

1. Neither Alice, Bob or Charles will learn anything beside the output of the computation. Ie: Who makes the highest amount.
2. Parties follow the protocol exactly, performing their tasks without trying to learn about others' inputs or undermine the computation's integrity. Even if some parties share information or deviate from the rules, the MPC protocol prevents them from causing honest parties to produce incorrect results or reveal any confidential information.

### In Shakespay

In the case of Shakespay, we use MPC to eliminate the single point of failure common in almost all wallets today. MPC private shares are distributed without any single issuer, computations (signatures) are produced without any party with a share learning anything beyond the output, and parties can reshare their private shares if compromised, without compromising their old or new shares.
