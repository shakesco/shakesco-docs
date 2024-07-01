---
title: Recurring payments
type: docs
prev: docs/mpc
next: docs/autopayments/integration
sidebar:
  open: true
---

### Ethereum Recurring payments

Ethereum is growing. Bitcoin and Ethereum have enabled anyone around the world to receive value regardless of their country of residence. With adoption comes an additional need for functionality. One of them is recurring payments. Auto-payments have been a success in traditional finance, with companies like Stripe and PayPal processing more than [1% of world GDP.](https://finance.yahoo.com/news/stripe-crosses-1-trillion-total-154015081.html?guccounter=1&guce_referrer=aHR0cHM6Ly93d3cuZ29vZ2xlLmNvbS8&guce_referrer_sig=AQAAAGkAeKlXHDsXVKLicymNY052JJPqFXVqJ2y-QQ_VSJkhzsmZlZQXw4jf3QeOdnndfRHzqtZwvjZKJ0bUIlRmJH6v6i-x7XDGmQgeyehSc-jjmKT4wc6RT32VLFKGDke7qEn4zSTAouj04m5KnrfI-RyfN0k_sMUgk0yCJCsaoCgw)  

This is a hurdle in Ethereum, mostly Bitcoin because they only allows push payments. With Ethereum, we can solve this with contracts. Bitcoin doesn't have a Turing-complete language to enable this. The easiest solution is to give up your private key to a custodial service. The service will use your private key to generate a valid signature every time a recurring payment is needed. How will that be different from what we have now with Stripe?

If we decide to use Ethereum contracts, who will be making the calls every time a payment is requested? If we make this custodial, we can control who gets to use the system and who doesn't.

So how do you solve the push-and-pull problem? Or the decentralizion problem? The goal is to create a self-custody, censorship-resistant, permissionless, decentralized, and secure auto-payment system fully built on the Ethereum ecosystem. If you said Account Abstraction, you are right! In the next steps, we will explain how it works and how to integrate and start receiving value today.