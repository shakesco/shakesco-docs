---
type: docs
prev: docs/autopayments/integration
next: docs/autopayments/api
---

# Fund your Auto-payment Account

For auto-payments to work you need to fund your auto-account with chainlink. We have made this simple so that you don't have to worry about swapping from any other asset.

## Pre-requisite

First, open your auto-account. We suggest Polygon. Ethereum is too expensive!

### Getting started

You will notice your auto-account already has some LINK. This is because when you open an account, your smart wallet performs a batch transaction to allow your auto-account to go live!

<img
src="/images/1728.png"  
alt="Dashboard"
height="200">

From your Dashboard you can see your Chainlink balance and minimum balance. If your balance is approaching minimum or is below minimum , your auto-account will __NOT__ perform payments. So it is important to keep it above minimum.

To fund, go to your app. Swipe to your Business auto account on the wallet page. Click and go to the account you want to view. You should see a page like the one below:

<img
src="/images/1830.png"  
alt="Dashboard" width="300"  
height="200">

Go to fund, enter the amount you want to fund, and your smart wallet will take care of the rest.

### Fee

To request a user, a transaction is sent to the blockchain. This is made easy by our API and SDK. We cover the fee for you but you have to refund it.
You will not be able to send any request to the user without adding the fee. You can view your fee balance from the dashboard.

To fund your fee account, go the same page as above:

<img
src="/images/1831.png"  
alt="Dashboard" width="300"  
height="200">

Go the button circled above, enter amount and check to see that your fee balance reflects on the dashboard.

With that you are ready to accept auto-payment on Ethereum or Polygon 🥳
