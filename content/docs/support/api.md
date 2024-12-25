---
title: API
type: docs
prev: docs/support/
next: docs/userop
sidebar:
  open: true
---

Before getting started, please obtain your API keys from the [dashboard](https://users.shakesco.com/login)

## Request

{{< callout type="info" >}}
This is a POST request
{{< /callout >}}

Send a request to this url `https://autopay.shakesco.com/support_session`. Here is an Example:

```javascript {filename="index.js"}
const config = {
  method: "POST",
  url: "https://autopay.shakesco.com/support_session",
  headers: {
    accept: "application/json",
    Authorization: `Bearer ${process.env.YOUR_API_KEY}`,
  },
  data: {
    description: your_description,
    currency_code: code,
    amount: amount,
    private: privacy,
    type: subdomain,
    tokens: tokens
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

### Info

#### your_description

Choose a small description, maybe thanking the payer for the conribution.

#### Amount

Amount to request. Parse amount depending on the currency code below.

#### code

Currency code that you want to request payment with. To check currency codes we support, [go here](../../autopayments/api#currency-codes)

#### privacy

Choose if you want to receive the contibution as a private or public transaction. Private transactions are masked from the public, protecting you and the payer. Should be a boolean, true for private and false for public.

#### subdomain

Choose the subdomain that best suits your contribution need, choose **ONLY** the following:

  1. tip
  2. support
  3. donate
  4. coffee
  5. pay
  6. sponsor

#### tokens

Enter the tokens you want to receive, should be an array. **ONLY** the following:

1. BTC
2. ETH
3. USDT
4. USDC
5. DAI
6. WBTC
7. WETH
8. POL

If you choose to receive private transactions, you can only be sent: Bitcoin, Ethereum and Polygon

## Response

```shell {filename="cmd"}
{
  id: 1,
  url: "https://donate.shakesco.com/@shawnkimtai.sns/08CC9AA7E50CBFDF025414C4ECB004C1",
}
```

## Errors

### 400

Check if you have parsed data as explained above

```shell {filename="cmd"}
{
    error: "Bad Request"
}
```

### 404

Register a Username from the [Shakesco app](https://get.shakesco.com) to continue

```shell {filename="cmd"}
{
    error: "Username required"
}
```

#### 500

Something went wrong. If the issue persists, [contact us immediatelty](https://shakesco.com/contact)

```shell {filename="cmd"}
{
    error: "Internal Server Error"
}
```
