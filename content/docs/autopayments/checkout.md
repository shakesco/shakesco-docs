---
title: Ethereum Auto-payment Checkout
type: docs
prev: docs/autopayments/api
next: docs/autopayments/sdk
---

Before getting started please get your API keys from the [dashboard](https://users.shakesco.com/login) and deploy your Business auto account from the [Shakespay app](https://apps.apple.com/us/app/shakespay-bitcoin-ethereum/id6478241603).

Go through [Integration](../../autopayments/integration#requesting-token) to see how to parse values.

## Easily accept auto-payments on Ethereum

Shakespay checkout makes it easy for you to start receiving recurring payments. You can embed it directly into your website or redirect users for them to checkout. With over 150+ currency support, you can start receiving value from anywhere in the world. For now it only allows recurring payments.

<div
style="display: flex;">
  <img
   src="/images/checkout.png" alt="User side features">
</div>

## Get started

To get started, first you need to send an API call to create a session for a specific user. This will return a URL that you can embed or redirect users to for them to complete the request. So when a user is ready to send a request, send the following API call:

### URL Request

{{< callout type="info" >}}
This is a POST request
{{< /callout >}}

To get the URL, send a request to this url `https://autopay.shakesco.com/start_session`. Here is an Example:

```javascript {filename="index.js"}
const config = {
  method: "POST",
  url: "https://autopay.shakesco.com/start_session",
  headers: {
    accept: "application/json",
    Authorization: `Bearer ${process.env.YOUR_API_KEY}`,
  },
  data: {
    auto_address: your_auto_address,
    network: Network,
    period: Period,
    description: user_delegate_address,
    redirect_url: Splitters,
    number: split_amounts,
    currency_code: code,
    amount: Amount,
    token_address: Token,
    should_split: Split,
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

{{< callout type="info" >}}
Understand the information needed and parse as advised.
{{< /callout >}}

##### auto_address

Your Business auto account address. Can be found in Dashboard. Depending on Network.

##### network

Depending on where your Business auto account address is deployed. Enter 1 for Ethereum or 137 for Polygon

##### period

Period to request payment. Make sure it is in seconds

##### description

Enter your business description. Here you can enter what the subscription is about.

##### redirect_url

Enter URL that the checkout should redirect to if the request is successful.

{{< callout type="info" >}}
It is advised to redirect to a page where you can check if the user has accepted the request from the Shakespay app.
{{< /callout >}}

##### number

Enter the number of splitter you will allow. If you want a total of 3 Splitters, enter 2. This is only allow a user to have 2 splitters plus themselves.

{{< callout type="info" >}}
Enter an empty string if you don't want split autopayment.
{{< /callout >}}

##### currency_code

Currency code that you want to request payment with. Check currency code  s [here](../../autopayments/api#currency-codes)

##### amount

Amount to request. Parse amount depending on the currency code.

##### token_address

Here enter an array of all the tokens and/or coins you want to accept.

Eg: ["Polygon", "WETH", "WBTC"]

Here are the tokens and coins your auto account supports:

###### Polygon

1. **Polygon**
2. **WBTC**
3. **WETH**
4. **USDT**
5. **USDC**

###### Ethereum

1. **Ethereum**
2. **WBTC**
3. **USDT**
4. **USDC**

Tokens and coins are displayed the way you parse them in the array.

##### should_split

If you want to allow split payment. Enter true or false.

#### Response

```shell {filename="cmd"}
{
    "id": 1,
    "url": "https://checkout.shakesco.com?ZXlKaGJHY2lPaUpJVXpJMU5pSXNJblI1Y0NJNklrcFhWQ0o5LmV5SmhkWFJ2WDJGa1pISmxjM01pT2lJd2VESTBOalkxTURFek1ERmlPRFpsWlRjNVpFUTFSak0wTjJReU5qWTVNakV3T0RJMk1EZ3dNallpTENKdVpYUjNiM0pySWpvaU1UTTNJaXdpY0dWeWFXOWtJam9pTmpBME9EQXdJaXdpWTNWeWNtVnVZM2xmWTI5a1pTSTZJbFZUUkNJc0luSmxaR2x5WldOMFgzVnliQ0k2SW1oMGRIQnpPaTh2YzJoaGEyVnpZMjh1WTI5dElpVkVNaUxDSlhSVlJJSWwwc0luTm9iM1ZzWkY5emNHeHBkQ0k2Wm1Gc2MyVXNJa0ZRU1Y5TFJWa2lPaUpsZFdSTmJuQjVhVVpMTUV3NVdIQlFNMFUyZEd0alRHVktSa015YzNJNFJuUTBaVFZrYTFwV01UQTRNbUkzTVdVaUxDSnBZWFFpT2pFM01qQXpOemd4TURNc0ltVjRjQ0k2TVRjeU1ETTNPRFF3TTMwLkpWbGUzd0QxNld0NTkwWW1WSUpsUE5rWEZlb3FyTDZrRHc0RnZmMVEyYlk="
}
```

### Sessions

After sending the request and receiving your url, the user has **5 minutes** before the URL expires. You will need to send the same request or a different request to get a new URL that the user can use.

### Check payment

As seen [here](../../autopayments/checkout#redirect_url), the user will be redirected to your website or application if the request was successful. This URL should be a page where you can check if the user has made payment / accepted your request. To check this, you can easily send an [API call](../../autopayments/api#has-paid).
