---
title: API
type: docs
prev: docs/autopayments/start
next: docs/autopayments/sdk
---

Before getting started, please obtain your API keys from the [dashboard](https://users.shakesco.com/login) and deploy your Business Auto account through the [Shakesco app](https://get.shakesco.com).

Refer to the [Integration](../../autopayments/integration#requesting-token) guide for instructions on parsing values. Additionally, visit the [Start](../../autopayments/start) section to ensure your auto-payment account is configured to process payments.

If you want to test out the system, just go [here.](#test)

### Test

{{< callout type="info" >}}
We fund your test delegate address automatically, so you don't need to enter an amount in testing mode.
{{< /callout >}}

Before getting started, send a test request to make sure everything is okay.

#### Request test address (User)

{{< callout type="info" >}}
This is a POST request
{{< /callout >}}

First, request a delegate address by entering your business smart wallet address (it's the same for both Ethereum and Polygon) along with your API key:

Send a request to this url `https://autopay.shakesco.com/delegate_address`. Here is an Example:

```javascript {filename="index.js"}
const config = {
  method: "POST",
  url: "https://autopay.shakesco.com/delegate_address",
  headers: {
    accept: "application/json",
    Authorization: `Bearer ${process.env.YOUR_API_KEY}`,
  },
  data: {
    smart_wallet: your_smart_wallet_address,
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

##### Response

```shell {filename="cmd"}
{
  id: 1,
  test_delegate_address: "0xB808ff0E0F4fC24D0cECeED6014f04ecE5bfca36",
}
```

#### Request test address (Business)

{{< callout type="info" >}}
This is a POST request
{{< /callout >}}

If you want to test a request to a business, request a business delegate address by entering your business smart wallet address (it's the same for both Ethereum and Polygon) along with your API key:

Send a request to this url `https://autopay.shakesco.com/buss_delegate_address`. Here is an Example:

```javascript {filename="index.js"}
const config = {
  method: "POST",
  url: "https://autopay.shakesco.com/buss_delegate_address",
  headers: {
    accept: "application/json",
    Authorization: `Bearer ${process.env.YOUR_API_KEY}`,
  },
  data: {
    smart_wallet: your_smart_wallet_address,
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

##### Response

```shell {filename="cmd"}
{
  id: 1,
  test_delegate_address: "0xB808ff0E0F4fC24D0cECeED6014f04ecE5bfca36",
}
```

#### Test Request

You can now send a test request:

{{< callout type="info" >}}
This is a POST request
{{< /callout >}}

Select the period you want and enter the delegate address requested above. Enter the details as advised below.

Send a request to this url `https://autopay.shakesco.com/request`. Here is an Example:

```javascript {filename="index.js"}
const config = {
  method: "POST",
  url: "https://autopay.shakesco.com/request",
  headers: {
    accept: "application/json",
    Authorization: `Bearer ${process.env.YOUR_API_KEY}`,
  },
  data: {
    "auto_address": "0x309E7d835ccE6E74BC72A2E523fa7f79FFC0d413",
    "network": "11155111",
    "delegate_address": YOUR_TEST_DELEGATE_ADDRESS,
    "currency_code": "USD",
    "period": PERIOD,
    "amount": "0",
    "token_address": "",
    "should_split": false,
    "splitters": [],
    "splitters_amount": []
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

##### Response

```shell {filename="cmd"}
{
  id: 1,
  test_delegate_address: "0xB808ff0E0F4fC24D0cECeED6014f04ecE5bfca36",
  result: "Requested User successfully"
}
```

##### Response (If Business)

```shell {filename="cmd"}
{
  id: 1,
  test_delegate_address: "0xB808ff0E0F4fC24D0cECeED6014f04ecE5bfca36",
  result: "Requested Business successfully",
}
```

{{< callout type="info" >}}
Check if the request is sent to your Shakesco app. If you request business check on Business wallet side, if you request user, check on Personal wallet side.
{{< /callout >}}

### Live Request

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

##### user_delegate_address

User's Shakesco card/ Business card address.

##### your_auto_address

Your Business auto account address. Can be found in [Dashboard](https://users.shakesco.com "login"). Depending on Network.

##### Network

Depending on where your Business auto account address is deployed. Enter 1 for Ethereum or 137 for Polygon

##### Period

Period to request payment. Make sure it is in seconds

##### Amount

Amount to request. Parse amount depending on the currency code.

##### code

Currency code that you want to request payment with

##### Token

If you want to request a token, enter the token address. If you want to request Ethereum or Polygon, enter an empty string.

##### Split

If you want to allow split payment. Enter true or false.

##### Splitters

Addresses of splitters. Enter an empty array if you don't want split auto-payment.

##### split_amounts

Amount each splitter will pay. Parse depending on currency code

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
This is a POST request
{{< /callout >}}

{{< callout type="warning" >}}
Check this request wherever you're offering your service.
{{< /callout >}}

To check if payment has been made, send a request to this url `https://autopay.shakesco.com/has_paid`. Here is an Example:

```javascript {filename="index.js"}
const config = {
  method: "POST",
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

##### user_delegate_address

User's Shakesco card/ Business card address.

##### your_auto_address

Your Business auto account address. Can be found in [Dashboard](https://users.shakesco.com "login"). Depending on Network.

##### Network

Depending on where your Business auto account address is deployed. Enter 1 for Ethereum or 137 for Polygon

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
This is a POST request
{{< /callout >}}

To check if request has been made, send a request to this url `https://autopay.shakesco.com/request_sent`. Here is an Example:

```javascript {filename="index.js"}
const config = {
  method: "POST",
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

##### user_delegate_address

User's Shakesco card/ Business card address.

##### your_auto_address

Your Business auto account address. Can be found in [Dashboard](https://users.shakesco.com "login"). Depending on Network.

##### Network

Depending on where your Business auto account address is deployed. Enter 1 for Ethereum or 137 for Polygon

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

#### 402

```shell {filename="cmd"}
{
    error: "Payment Required";
}
```

Make payment because you have reached your request limit.

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
