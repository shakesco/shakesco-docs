---
title: MPC Vs Smart Wallet
type: docs
prev: docs/Multi-Party Computation
next: docs/Multi-Party Computation/threshold/
---

### MPC Vs Smart Wallet

Security is a challenge, as evidenced by the [__1500 BTC that may be lost everyday.__](https://news.bitcoin.com/analyst-1500-bitcoins-lost-every-day-less-than-14-million-coins-will-ever-circulate/). This has driven research into alternatives, leading to innovations like paper wallets, hardware wallets, and multi-sig wallets. While these have improved security, they come with their own shortcomings:

#### Hardware wallets

Hardware wallets are not good enough. If you lose your hardware wallet, you risk losing your funds, and wearing it as a [chain doesn't help.](https://shop.ledger.com/products/ledger-nano-x-onchain) Additionally, they are vulnerable to supply chain attacks. Even though hardware wallets have minimized this risk, hardware cannot be audited in the same way as open-source software.

#### Multi-sig wallets

Multi-sig wallets are better but not perfect. For instance, in a 2-of-4 structure with friends, if one person is compromised, you don't have to wait for another compromise to act. You must move funds to another multi-sig wallet, which incurs fees and takes time.

#### MPC is good

MPC wallets like [Zengo](https://zengo.com/) and [Fireblocks](https://fireblocks.com/) have proven to be superior alternatives. They offer no single point of failure, ease of transactions, and eliminate the need to store a 12-word seed phrase. However, they lack functionalities such as sending private transactions, recurring payments, launching tokens or NFTs, and setting up loyalty programs without using a dApp.

#### Smart Wallets is also good

Smart wallets enable more functionality. You can batch transactions, build your own DeFi, set up guardians (multi-sig) to protect your wallet, and much more. They offer better account management than any wallet mentioned above. However, smart wallets cannot sign transactions themselves; they must be deployed by an EOA, which introduces a single point of failure. This can be addressed by setting up guardians (multi-sig) around the wallet, but it becomes costly if you need to move funds due to compromised shares, and have to set up another smart wallet.

#### Smart Wallets + MPC is better

Smart wallets provide more functionality than standard EOA wallets, enabling private transactions, recurring payments, and more, aiming to create a Venmo or PayPal alternative on Ethereum. There has been debate on whether Ethereum should adopt smart wallets or MPC wallets, but these technologies are not mutually exclusive. As seen on Shakesco, they can be complementary. Shakesco uses MPC for key management and smart wallets for account management.
