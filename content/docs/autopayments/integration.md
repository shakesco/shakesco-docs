---
type: docs
prev: docs/autopayments/
next: docs/autopayments/api
---

# Let's start with how autopayments works

For autopayments to be possible their must be a pull and a push. When you give [**Stripe**](https://stripe.com/ "stripe") your [**Visa**](https://www.visa.com/ "visa") or [**MasterCard**](https://www.mastercard.com/global/en.html "mastercard") details you are actually giving them permission to pull from your account. The system will think that you are pushing payment but this has actually been executed by stripe. This system has been successful in traditional finance but with some drawbacks. One you are trusting Stripe to facilitate auto payments. You are trusting that they have exceptional security and will not leak your card details to attackers. Two, the stripe system is centralized and not censorship-resistant, they perform the pull/push payments. If stripe falls, not that we are hoping for so, many companies such as [X](https://x.com "XXX") will have to look for alternative. They can also lock you out of the system. For example: sanctioned countries cannot use Stripe but can use Bitcoin or Ethereum.

## Problem

So we are trying to solve a number of issues with this system. We are trying to create a permissionless, censorship-resistant, self-custody and decentralized system. Permissionless in that anyone with an internet connection can perform autopayments, we cannot censor them, we don't control it and the self-custody part makes it interesting in that you can set up autopayments with anyone not just restricted to businesses.
Ethereum has two accounts, EOA(Externally Owned Accounts) and contract accounts. EOAs are not programmable so creating an automation system is not possible. Even if we did setup a contract that the EOA would deploy and control, it would be cumbersome for the payer because we would require them to sign everytime the system wants to perform auto-payments. Contract accounts on the other hand cannot initiate a transaction. They must originate from the EOA.

### Solution

[Account abstraction](https://eips.ethereum.org/EIPS/eip-4337) is an Ethereum standard that aims to turn every account on Ethereum to a programmable account. This standard will help us solve the problem with Ethereum accounts. We can now create a contract that doesn't care about the how(signature) but cares about the who(address) that's initiating auto-payments. This helps us make the system decentralized in that not just one entity is making the push/pull calls and the user still keeps custody of his private information(private keys).

### How shakesco works

Shakesco works as explained above but we made some changes due to our Security architecture. Users and businesses can set up auto-payments the same way they do, or even much easier, with other payment infrastructure. You ask the payee for their identity, then you set the amount and period. Here we are going to explain how Customer to Business(C2B) or Business to Business(B2B) works:

#### Autopayment works only once

The autopayment system is a bit different from the traditional system. In the traditional system if Bob gives Alice his card, alice will use Bob's card to perform autopayment in your business and Bob can do the same. In this system, if Bob gives Alice their address, service will only be given to Bob.

#### One-time Payment

{{< callout type="info" >}}
Enter period as **0** and start period as **0**
{{< /callout >}}

One time payment is here! When you want to pull payment only once just enter period as **0** ans start period as **0**. The user will still need to accept this request, but you will only pull payment once.

The user will always return has_paid as true, until you request again. If you request again, you will reset everything and they will need to make another one time payment to be marked as paid.

#### Requesting token

We have added support for you to request tokens from users. For now your Polygon auto-payment account supports WETH, WBTC, USDT, DAI and USDC. On Ethereum: USDT, DAI and USDC.

{{< callout type="warning" >}}
When entering token address, please make sure it is a checksummed address. Otherwise your autopayment account will not work properly. Especially with STABLECOINS! If you don't want to accept tokens, just enter an empty string in its place.
{{< /callout >}}

Thankfully, we've taken care of the work for you. Starting with Polygon:

1. **WETH** - "0x7ceB23fD6bC0adD59E62ac25578270cFf1b9f619"
2. **WBTC** - "0x1BFD67037B42Cf73acF2047067bd4F2C47D9BfD6"
3. **USDT** - "0xc2132D05D31c914a87C6611C10748AEb04B58e8F"
4. **USDC** - "0x3c499c542cEF5E3811e1192ce70d8cC03d5c3359"
5. **DAI** - "0x8f3Cf7ad23Cd3CaDbD9735AFf958023239c6A063"

   For Ethereum:

6. **USDC** - "0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48"
7. **USDT** - "0xdAC17F958D2ee523a2206206994597C13D831ec7"
8. **DAI** - "0x6B175474E89094C44Da98b954EedeAC495271d0F"

#### Split

Split payments for recurring payments are here! This is only for C2B transactions. `requestUser` from the [SDK](../../autopayments/sdk) and `/request` from the [API](../../autopayments/api) are now editted to allow split payments. When you are requesting users, do they want to split payment with their friends? Set `wantstosplit` from the [SDK](../../autopayments/sdk) or `should_split` from the [API](../../autopayments/api), to true or false accordingly. If true populate `split` and `splitamount` with the number of users who will be participating in the split payment. Make sure they are the same length.

##### Things to note on split

{{< callout type="warning" >}}
This is important please note the following
{{< /callout >}}

- Make sure `split` and `splitamount` are the same length.
- The `address` parameter in `requestUser` [SDK](../../autopayments/sdk) or the `delegate_address` in `/request` [API](../../autopayments/api), is the 'group leader' in the split payment. If all split members pay, the service will **ONLY** be given to `address` / `delegate_address`.
- **Unless you want them to select how much each should pay,** `splitamount` should be set by you to avoid bad user experience. If there are 3 split participants + the 'group leader' just say eg: 20 / 4, push 5 to `splitamount` array 3 times and then set another 5 on the `amount` parameter for the `address` parameter.
- Check that all participants have been requested. Use `isRequested` in [SDK](../../autopayments/sdk) or `/request_sent` from [API](../../autopayments/api) to loop through the `split` participants and check that all participants have been requested.
- Be careful with the number of participants you allow. A small number of participants is advised so that your business does not get less value. Split payment is for services that previously had one person paying while others didn't have to pay as the payer could just share the log in information with them. With split payment, each would have to pay but the total amount will be split among them. This will be more beneficial to your business than the traditional system!
- Participants will need to work together to make sure their accounts are funded for split autopayments to work. If some take advantage of others then they can opt out and move to other groups or move to single autopayments.
- There is no grace period for split participants.
- If a user is switching from split pull to personal pull, they will have to pay again, **the full amount** to start their own timeline. If they won't pay again at that time, service will not be provided.
- If a user is switching group members, please tell them to accept again. This is important so they can be marked as paid. If not the next time your autopayment system will run the pull payment they will be marked as paid.
- If a user was in group and then switches to single recurring payment, they will pay the full amount, regardless if they paid in their group auto-payment or not.
- [One time payment](../../autopayments/integration#one-time-payment) are available for split payments

#### Changing amount

{{< callout type="info" >}}
When you want to change price, feel free to do so. The service will still be given to the user until the end of the payment period. The system will automatically update and they will start paying with the new amount so that service can be provided.  
{{< /callout >}}

#### Changing period

{{< callout type="info" >}}
Same as amount, when you want to change period, feel free to do so. The service will still be given to the user until the end of the payment period. The system will automatically update and they will start the new period.
{{< /callout >}}

#### Requests

For you to start pulling payments, you need to make a pull request. So you use the request method, `requestUser` or `requestBusiness` from the [SDK](../../autopayments/sdk) or the `/request` from [API](../../autopayments/api), request the user and await approval (Tell user/business to accept request from shakesco app). The reason we did this is because your wallet is guarded by [TSS(Threshold Signature Scheme)](../../multi-party-computation/threshold) meaning when creating a wallet, you went through a protocol that split shares between you and a remote server. This will make it cumbersome for your ui to integrate so we took this approach.

#### Payment

To check if payment was made, use the `hasPaid` method from the [SDK](../../autopayments/sdk) or use `/has_paid` from the [API](../../autopayments/api).

{{< callout type="warning" >}}
Please check this method anywhere you are offering the service. This is the only way to check if you should release service or not.
{{< /callout >}}

#### Period

Period should be given in seconds. If you want to do monthly, set period as `2592000`. (Assuming you want 30 day period), set period as `604800`. (Assuming you want 1 week period), set period as `31536000`. (Assuming you want 1 year period) or custom depending on your choice of period.

{{< callout type="info" >}}
Don't use commas. Eg: `604,800`
{{< /callout >}}

{{< callout type="warning" >}}
Please enter period in seconds. Also don't enter a period less that 2 days/172800 sec because calls are made after 2 days. Eg: Putting 86400 sec, you are telling the system to pull everyday, but the system will only pull after two days. If you require 1 day intervals [reach out](https://shakesco.com/contact "Shakesco")!
{{< /callout >}}

#### Amount

Amount should be given in units. So if you want 20 USD, enter `20`. Make sure to use `parseUnits` as directed in the docs.

{{< callout type="warning" >}}
Please enter amount in USD if using SDK. Convert from local currency accordingly. For API calls, check the list of [supported currencies](../../autopayments/api#currency-codes).
{{< /callout >}}

#### Fees

{{< callout type="info" >}}
Please visit [this url](https://shakesco.com/charge) to check charges for all transactions. If you are only interested in recurring payments, check under "Card Charges".
{{< /callout >}}

#### Discount period

When deploying your `Business Auto` account, you had to enter a discount period. A discount period is a period when users can enjoy your service to test it out, free trial. They will be required to pay after the end of the free trial. Users CANNOT manipulate this with their account. It is only offered once. We are looking at how we can make it even more efficient that you don't have to worry about users creating numerous accounts to get free tier all the time.

#### Grace period

Grace period is the period after pay day that the user can enjoy your services before they are cut off if they don't make payment. If you have deployed with or without it okay because you can edit.

{{< callout type="info" >}}
We also don't offer Daily intervals, if you require 1 [reach out](https://shakesco.com/contact "Shakesco")!
{{< /callout >}}
