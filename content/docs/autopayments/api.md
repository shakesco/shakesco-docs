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
const config = {
  method: "POST",
  url: "https://autopay.shakesco.com/request",
  headers: {
    accept: "application/json",
    Authorization: `Bearer ${process.env.YOUR_API_KEY}`,
  },
  data: {
    auto_address: your_auto_address,
    network: Network,
    delegate_address: user_delegate_address,
    currency_code: code,
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

```shell {filename="info.txt"}
user_delegate_address: "Users Shakespay card/ Business card address."
your_auto_address: "Your Business auto account address. Can be found in Dashboard. Depending on Network."
Network: "Depending on where your Business auto account address is deployed. Enter 1 for Ethereum or 137 for Polygon"
Period: "Period to request payment. Make sure it is in seconds"
Amount: "Amount to request. Parse amount depending on the currency code."
code: "Currency code that you want to request payment with"
Token: "If you want token. Enter the token address. If you want to request Ethereum or Polygon, enter an empty string."
Split: "If you want to allow split payment. Enter true or false."
Splitters: "Address of splitters. Enter an empty array if you don't want split autopayment."
split_amounts: "Amount each splitter will pay. Parse depending on currency code"
```

#### Response

```shell {filename="cmd"}
{
id: 1,
delegate_address: "0xB808ff0E0F4fC24D0cECeED6014f04ecE5bfca36",
result: "Requested User successfully"
}
```

#### Response (If Business)

```shell {filename="cmd"}
{
id: 1,
delegate_address: "0xB808ff0E0F4fC24D0cECeED6014f04ecE5bfca36",
result: "Requested Business successfully",
}
```

#### Response (If Already Requested)

```shell {filename="cmd"}
{
id: 1,
delegate_address: "0xB808ff0E0F4fC24D0cECeED6014f04ecE5bfca36",
result: "User has already been requested",
}
```

#### Response (If Already Requested Business)

```shell {filename="cmd"}
{
id: 1,
delegate_address: "0xB808ff0E0F4fC24D0cECeED6014f04ecE5bfca36",
result: "Business has already been requested",
}
```

### Currency codes

{{< callout type="info" >}}
This is a GET request
{{< /callout >}}

To check all the currency codes we support, send a request to this url `https://autopay.shakesco.com/codes`. Here is an Example:

```javascript {filename="index.js"}
const config = {
  method: "GET",
  url: "https://autopay.shakesco.com/codes",
  headers: {
    accept: "application/json",
    Authorization: `Bearer ${process.env.YOUR_API_KEY}`,
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
    currency_codes: [
        "MATIC",
        "FJD",
        "MXN",
        "ETH",
        "LVL",
        "USDT",
        "CDF",
        "BBD",
        "EUR",
        "UGX",
        "GLM",
        "BTC",
        "RAY",
        "KES",
        "INR",
        "USD"
        .........
        ]
}
```

### Has Paid

{{< callout type="info" >}}
This is a GET request
{{< /callout >}}

To check if payment has been made, send a request to this url `https://autopay.shakesco.com/has_paid`. Here is an Example:

```javascript {filename="index.js"}
const config = {
  method: "GET",
  url: "https://autopay.shakesco.com/has_paid",
  headers: {
    accept: "application/json",
    Authorization: `Bearer ${process.env.YOUR_API_KEY}`,
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

#### Info

```shell {filename="info.txt"}
user_delegate_address: "Users Shakespay card/ Business card address."
your_auto_address: "Your Business auto account address. Can be found in Dashboard. Depending on Network."
Network: "Depending on where your Business auto account address is deployed. Enter 1 for Ethereum or 137 for Polygon"
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
const config = {
  method: "GET",
  url: "https://autopay.shakesco.com/request_sent",
  headers: {
    accept: "application/json",
    Authorization: `Bearer ${process.env.YOUR_API_KEY}`,
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

#### Info

```shell {filename="info.txt"}
user_delegate_address: "Users Shakespay card/ Business card address."
your_auto_address: "Your Business auto account address. Can be found in Dashboard. Depending on Network."
Network: "Depending on where your Business auto account address is deployed. Enter 1 for Ethereum or 137 for Polygon"
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

#### 400

```shell {filename="cmd"}
{
    error: "Bad Request";
}
```

Check the currency code that you are parsing. Make sure it in Upper case. Ie: BTC, ETH etc

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
