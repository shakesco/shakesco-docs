---
title: API
type: docs
prev: docs/autopayments/integration
next: docs/autopayments/sdk
---

Before getting started please get your API keys from the [dashboard](https://users.shakesco.com/login) and deploy your Business auto account from the [Shakespay app](https://apps.apple.com/us/app/shakespay-bitcoin-ethereum/id6478241603).

Go through [Integration](../../autopayments/integration#requesting-token) to see how to parse values.

### Request

{{< callout type="info" >}}
This is a POST request
{{< /callout >}}

To request a Customer or a Business, send a request to this url `https://autopay.shakesco.com/request`. Here is an Example:

```javascript {filename="index.js"}
const user_delegate_address = ""; // Users Shakespay card/ Business card address
const your_auto_address = ""; // Your Business auto account address. Can be found in Dashboard. Depending on Network.
const Network = ""; // Depending on where your Business auto account address is deployed. Enter between Ethereum and Polygon
const is_business = ""; // Are you requesting business
const Period = ""; // Period to request payment
const Amount = ""; // Amount to request
const Token = ""; // If you want token.
const Split = ""; // If you want to allow split payment.
const Splitters = []; // split payers.
const split_amounts = []; // Split amount
const config = {
  method: "POST",
  url: "https://autopay.shakesco.com/request",
  headers: {
    accept: "application/json",
    Authorization: `Bearer ${process.env.SHAKESCOQUERYAPIKEY}`,
  },
  data: {
    auto_address: your_auto_address,
    network: Network,
    delegate_address: user_delegate_address,
    is_business: is_business,
    period: Period,
    amount: Amount,
    token_address: Token,
    should_split: Split,
    splitters: Splitters,
    splitters_amount: split_amounts,
  },
};

axios
  .request(config)
  .then((response) => {
    console.log(response.data);
  })
  .catch((error) => {
    console.log(error);
  });
```

#### Info

#### Response

```shell {filename="cmd"}
id: 1,
delegate_address: "0xB808ff0E0F4fC24D0cECeED6014f04ecE5bfca36",
result: "Requested 0xB808ff0E0F4fC24D0cECeED6014f04ecE5bfca36 successfully",
```

### Has Paid

{{< callout type="info" >}}
This is a GET request
{{< /callout >}}

To check if payment has been made, send a request to this url `https://autopay.shakesco.com/has_paid`. Here is an Example:

```javascript {filename="index.js"}
const user_delegate_address = ""; // Users Shakespay card/ Business card address
const your_auto_address = ""; // Your Business auto account address. Can be found in Dashboard. Depending on Network.
const Network = ""; // Depending on where your Business auto account address is deployed. Enter between Ethereum and Polygon
const config = {
  method: "GET",
  url: "https://autopay.shakesco.com/has_paid",
  headers: {
    accept: "application/json",
    Authorization: `Bearer ${process.env.SHAKESCOQUERYAPIKEY}`,
  },
  data: {
    delegate_address: user_delegate_address,
    auto_address: your_auto_address,
    network: Network,
  },
};

axios
  .request(config)
  .then((response) => {
    console.log(response.data);
  })
  .catch((error) => {
    console.log(error);
  });
```

#### Response

```shell {filename="cmd"}
{
    id: 1,
    delegate_address: "0xB808ff0E0F4fC24D0cECeED6014f04ecE5bfca36",
    has_paid: "true",
}
```

### Has Requested

{{< callout type="info" >}}
This is a GET request
{{< /callout >}}

To check if request has been made, send a request to this url `https://autopay.shakesco.com/request_sent`. Here is an Example:

```javascript {filename="index.js"}
const user_delegate_address = ""; // Users Shakespay card/ Business card address
const your_auto_address = ""; // Your Business auto account address. Can be found in Dashboard. Depending on Network.
const Network = ""; // Depending on where your Business auto account address is deployed. Enter between Ethereum and Polygon
const config = {
  method: "GET",
  url: "https://autopay.shakesco.com/request_sent",
  headers: {
    accept: "application/json",
    Authorization: `Bearer ${process.env.SHAKESCOQUERYAPIKEY}`,
  },
  data: {
    delegate_address: user_delegate_address,
    auto_address: your_auto_address,
    network: Network,
  },
};

axios
  .request(config)
  .then((response) => {
    console.log(response.data);
  })
  .catch((error) => {
    console.log(error);
  });
```

#### Response

```shell {filename="cmd"}
{
    id: 1,
    delegate_address: "0xB808ff0E0F4fC24D0cECeED6014f04ecE5bfca36",
    requested: "false",
}
```

### Errors

#### 401

```shell {filename="cmd"}
{
    error: "Unauthorized";
}
```

Check if you have parsed API_KEY. Also parse API_KEY like so `Bearer API_KEY` in Authorization.

#### 403

```shell {filename="cmd"}
{
    error: "Forbidden";
}
```

Check if you have parsed your API_KEY correctly. Also check if you have parsed the auto_address associated with your API_KEY.

#### 500

```shell {filename="cmd"}
{
    error: "Internal Server Error";
}
```

Something went wrong. If the issue persists, [contact us immediatelty](https://shakesco.com/contact)
