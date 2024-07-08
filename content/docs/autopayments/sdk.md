---
title: SDK
type: docs
prev: docs/autopayments/checkout
next: docs/private
---

Before getting started please get your API keys from the [dashboard](https://users.shakesco.com/login) and deploy your Business auto account from the [Shakespay app](https://apps.apple.com/us/app/shakespay-bitcoin-ethereum/id6478241603).

Go through [Integration](../../autopayments/integration#requesting-token) to see how to parse values.

## @shakesco/automation

This repository makes it easy for businesss to interact with their autopayment contract and enable automation!

Before intergrating please read this [short explanation on how the system works](../../autopayments/integration). It will help you understand how you may want to setup your auto-payments.

### Install

```shell {filename=cmd}
npm i @shakesco/automation
```

After installing:

```javascript {filename=index.js}
const { Automation, parseUnits } = require("@shakesco/automation");
```

{{< callout type="info" >}}
Visit [**Shakesco**](https://shakesco.com/pricing "Shakesco") to get fee rates and then get your api keys [here](https://users.shakesco.com).  
If you want to see the fee charged for every transaction, go [**here**](../../autopayments/integration#fees "here")
{{< /callout >}}

### Request user

Send request to user. Ask user for Shakespay card/ Business card address.

```javascript {filename=index.js}
  const address = /* Initialize your automation address. Can be found in your dashboard https://users.shakesco.com */

  const shakescocontract = new Automation(address, process.env.SHAKESCOAPIKEY, "Ethereum");

  const period = "604800" //1 week

  const amount = parseUnits("20", 18); //amount to request regularly

  const delegateAddress = /*Ask user/business for their Shakespay card/delegate/business card ONLY.*/

  const requestUser = await shakescocontract.requestUser(delegateAddress, "", period, amount, false, [], []);//request user

  const requestBusiness = await shakescocontract.requestBusiness(delegateAddress,"", period, amount);//or request business

  console.log(requestUser);//Requested user successfully
  console.log(requestBusiness);//Requested business successfully
```

{{< callout type="info" >}}
Period should be in seconds. Amount should be in USD. Eg: "20" is in USD. Boolean values are returned as **STRINGS**
{{< /callout >}}

### Request Split payment

When you want to request split payment:

```javascript {filename=index.js}
  const address = /* Initialize your automation/Shakespay auto/business auto address. Can be found in your dashboard https://users.shakesco.com */

  const shakescocontract = new Automation(address, process.env.SHAKESCOAPIKEY);

  const period = "604800" //1 week

  const mainAddress = /** Users Shakespay card/delegate/business card ONLY.*/

  const friends = ["", ""]; //request friend details. Shakespay card/delegate/business card ONLY.

  const friendsAmount = [parseUnits("10", 18), parseUnits("10", 18)];

  const mainamount = parseUnits("10", 18);

  const requestUser = await shakescocontract.requestUser(mainAddress, "", period, mainamount, true, friends, friendsAmount);//request user

  console.log(requestUser); //Requested user successfully (Returns for mainAddress only)

  //so check the rest, "friends".
  for (let i = 0; i < friends.length; i++) {
    const isrequested = await shakescocontract.isRequested(friends[i]);
    console.log(isrequested); // true / false
  }
```

{{< callout type="info" >}}
Amount should be in USD. Eg: "10" is in USD. Boolean values are returned as **STRINGS**
{{< /callout >}}

{{< callout type="info" >}}
You will be receiving USD 30. But it is split among 3 friends.
{{< /callout >}}

### Request Tokens

{{< callout type="info" >}}
Get token addresses [**here**](../../autopayments/integration#requesting-token)
{{< /callout >}}

When you want to request a token:

```javascript {filename=index.js}
  const address = /* Initialize your automation address. Can be found in your dashboard https://users.shakesco.com */

  const shakescocontract = new Automation(address, process.env.SHAKESCOAPIKEY, "Ethereum");

  const period = "604800" //1 week

  const amount = parseUnits("20", 18); //amount to request regularly

  const delegateAddress = /*Ask user/business for their Shakespay card/delegate/business card ONLY.*/

  const tokenAddress = "" //Make sure it is checksummed

  const requestUser = await shakescocontract.requestUser(delegateAddress, tokenAddress, period, amount, false, [], []);//request user

  const requestBusiness = await shakescocontract.requestBusiness(delegateAddress, tokenAddress, period, amount);//or request business

  console.log(requestUser);//Requested user successfully
  console.log(requestBusiness);//Requested business successfully
```

{{< callout type="info" >}}
Amount should be in USD. Eg: "20" is in USD.
{{< /callout >}}

### Check payment status

To check if user has made payment or not:

```javascript {filename=index.js}
  const address = /*Initialize your automation address*/

  let delegateAddress = /*Request user for their info: Shakespay card/delegate/business card address ONLY*/

  const shakescocontract = new Automation(address, process.env.SHAKESCOAPIKEY);

  const requestUser = await shakescocontract.hasPaid(delegateAddress);
  console.log(requestUser); //"true"

```

{{< callout type="warning" >}}
Check the above step where you are offering your service. Otherwise you will offer service for free without knowing.
{{< /callout >}}

{{< callout type="info" >}}
For split auto-payment, service is only given to 'group leader'. So always query them alone, the rest will just return false.
{{< /callout >}}

We also don't offer daily automation. If your business requires daily automation [please reach out to us](https://shakesco.com/contact "Shakesco")!

ENJOY YOUR FINANCIAL FREEDOM😁!!!
