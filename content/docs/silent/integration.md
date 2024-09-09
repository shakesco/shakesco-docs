---
title: Bitcoin Silent Payments Integration
type: docs
prev: docs/silent
next: docs/private
---

> Special credit to [_cake wallet_](https://github.com/cake-tech/bitcoin_base/tree/cake-update-v5 "cake wallet").

{{< callout type="info" >}}
This is a Dart package, and we are working on a JavaScript SDK for broader adoption.
{{< /callout >}}

## @shakesco/bitcoin_base

### Install

To get started, add this to your pubspec.yaml:

```shell {filename=pubspec.yaml}
  bitcoin_base:
    git:
      url: https://github.com/shakesco/bitcoin_base
      ref: cake-update-v5
```

After installing:

```dart {filename="index.dart"}
import 'package:bitcoin_base/bitcoin_base.dart' as bitcoin_base;
```

### Generate Silent Payment address

This will generate the silent payment address. It prepares a receiver to receive silent payments.
You can generate a silent payment address in three ways:

##### Private Keys

If you are not a wallet provider, use this method. More specifically, you can make the user sign a message and then derive `b_scan` and `b_spend` from the resulting [signature]()(Use `r` as `b_scan` and `s` as `b_spend` or vice versa).

```dart {filename="index.dart"}
void main() {
  final b_scan = "";
  final b_spend = "";
  final paymentOwner = bitcoin_base.SilentPaymentOwner.fromPrivateKeys(
      network: bitcoin_base.BitcoinNetwork.testnet,
      version: 0,
      b_scan: bitcoin_base.ECPrivate.fromHex(b_scan),
      b_spend: bitcoin_base.ECPrivate.fromHex(b_spend));
  print(paymentOwner.toAddress());
}
```

##### Mnemoni and HD Key

If you are a wallet provider, use this method.

```dart {filename="index.dart"}
void main() {
  final mnemonic = "";
  final paymentOwner = bitcoin_base.SilentPaymentOwner.fromMnemonic(mnemonic);
  print(paymentOwner.toAddress());

//   final paymentOwner = bitcoin_base.SilentPaymentOwner.fromHd();
//   print(paymentOwner.toAddress());
}
```

### Create a taproot address destination

Here is where you create a destination address for the user to send to a newly generated Taproot address, derived from the receiver's silent payment address generated above.
You will need:

1. The Unspent Transaction Output(UTXO) of the user, hash and output_index.
2. The private key of the UTXO in 1 above.
3. Amount the user wants to send. Should be in satoshis(1 BTC = 100<sup>6</sup> satoshis)
4. Finally, the public keys of the 2 secret shares, `B_scan` and `B_spend`

```dart {filename="index.dart"}
void main() {
  final tx_hash = "";
  final tx_id_output_index = "";
  final sender_privateKey = "";
  final amount = 1000;
  final B_scan = bitcoin_base.SilentPaymentAddress.fromAddress(paymentOwner.toAddress()).B_scan;
  final B_spend = bitcoin_base.SilentPaymentAddress.fromAddress(paymentOwner.toAddress()).B_spend;

  final payto = bitcoin_base.SilentPaymentBuilder(vinOutpoints: [
    bitcoin_base.Outpoint(
        txid: tx_hash,
        index: tx_id_output_index)
  ]).createOutputs([
    bitcoin_base.ECPrivateInfo(
        bitcoin_base.ECPrivate.fromHex(sender_privateKey),
        false)
  ], [
    bitcoin_base.SilentPaymentDestination(
        amount: amount,
        network: bitcoin_base.BitcoinNetwork.testnet,
        version: 0,
        scanPubkey: B_scan,
        spendPubkey: B_spend)
  ]);

  print(payto.values.first.first.address
      .toAddress(bitcoin_base.BitcoinNetwork.testnet));
}
```

### Scan for funds

Scanning for funds is a drawback of silent payments. So below is how you can check if a certain transaction belongs to a user. You will need:

1. The transaction input's tx_hash and output_index.
2. Public key outputted.
3. Script and amount from the outputted taproot address

```dart {filename="index.dart"}
void main() {
  final tx_hash = "";
  final tx_id_output_index = "";
  final senders_pubKey = "";
  final amount = "";
  final Script = "";

  Map<String, bitcoin_base.SilentPaymentScanningOutput> output =
      bitcoin_base.SilentPaymentBuilder(
    vinOutpoints: [
      bitcoin_base.Outpoint(
          txid: tx_id,
          index: tx_id_output_index)
    ],
    pubkeys: [
      bitcoin_base.ECPublic.fromHex(senders_pubKey),
    ],
  ).scanOutputs(paymentOwner.b_scan, paymentOwner.B_spend, [
    bitcoin_base.BitcoinScriptOutput(
        script: bitcoin_base.Script(script: [Script]),
        value: BigInt.from(amount))
  ]);

  print(payto.values.first.first.address
      .toAddress(bitcoin_base.BitcoinNetwork.testnet));
}
```

If the address above matches the taproot address from the output in the transaction, it belongs to the user.

### Spend funds

If the funds belong to the user, they can spend like so:

First, you will need:

1. The transaction input's tx_hash and output_index.
2. Public key outputted.
3. Receiver's spend and scan private keys.

```dart {filename="index.dart"}
void main() {
  final tx_hash = "";
  final tx_id_output_index = "";
  final senders_pubKey = "";

  bitcoin_base.ECPrivate spendprivKey = bitcoin_base.SilentPaymentBuilder(
    vinOutpoints: [
      bitcoin_base.Outpoint(
          txid: tx_hash,
          index: tx_id_output_index),
    ],
    pubkeys: [
      bitcoin_base.ECPublic.fromHex(senders_pubKey)
    ],
  ).spendOutputs(paymentOwner.b_scan, paymentOwner.b_spend);
}
```

The receiver can use `spendprivKey` to spend the funds!

Thats it! 🎊🎊🎊

### Contribute

If you love what we do to progress privacy, [contribute](https://me-qr.com/text/vPod5qN0 "btc_addr") to further development

<img
src="/images/bitcoin_address.png"  
alt="btc_addr" width="300"  
height="200">
