---
title: "A Guide to Handling Cross-Currency Transfers With Flutterwave"
date: 2023-11-26T08:34:29+01:00
author: "Orim Dominic Adah"
description: "Perform cross-currency transfers with the Flutterwave API"
draft: false
showToc: true
TocOpen: false
comments: false
hidemeta: false
disableShare: false
searchHidden: false
cover:
  image: "../posts/images/cross-currency-transfers-with-flutterwave.png"
  alt: "A Guide to Handling Cross-Currency Transfers With Flutterwave"
  caption: ""
  relative: false
tags: ["flutterwave", "payments"]
---

It was a bit challenging for me to understand Flutterwave's [documentation on  cross-currency transfers](https://developer.flutterwave.com/docs/making-payments/transfers/overview/). After discussing with her staff on Slack, I was able to understand how it works. I have put this guide here for anyone who may be going through this challenge.

## The Challenge

I was tasked with the responsibility of implementing a feature that involved a transfer of funds from a Flutterwave USD account (the source account) to a recipient bank account (the destination account) that holds funds in a different currency from the USD.

In summary, here are the steps I took to achieve it with Flutterwave:

1. Convert the fund to be transferred in USD to that of the recipient bank account currency
2. Get the transfer fee for transferring the converted fund
3. Deduct the transfer fee from the converted fund to know the exact amount that the recipient bank account will receive
4. Transfer the converted fund to the recipient bank account

## How to Handle Cross-Currency Transfers with Flutterwave

Let us say that you intend to transfer 100 USD to Jane's bank account. Jane's bank account keeps funds in Kenyan Shillings (KES). In detail,

### 1. Convert the funds to be transferred to that of the recipient bank account currency

You can do this using the [get transfer rates](https://developer.flutterwave.com/reference/endpoints/transfers/#get-transfer-rates) API

```bash
curl --request GET 'https://api.flutterwave.com/v3/transfers/rates?amount=100&destination_currency=KES&source_currency=USD' \
--header 'Authorization: Bearer YOUR_FLUTTERWAVE_SECRET_KEY'
```
where

- `amount` is the amount in the source account, the 100 USD
- `destination_currency` is the currency of Jane's bank account, KES in this case
- `source_currency` is the currency of the account where the funds will be transferred from - our Flutterwave USD account

If you will be transferring from your Flutterwave NGN account, 'USD' will become 'NGN' in the example above.

### 2. Get the transfer fee

Assuming that the result of converting 100 USD to KES is 5000 KES, you may be interested in knowing the exact amount that will be deposited into Jane's bank account. You can figure it out by using the [get transfer fees API](https://developer.flutterwave.com/reference/endpoints/transfers/#get-transfer-fees)

```bash
curl --request GET 'https://api.flutterwave.com/v3/transfers/fee?amount=5000&currency=KES' \
--header 'Authorization: Bearer YOUR_FLUTTERWAVE_SECRET_KEY'
```

- `amount` is the amount being transferred
- `currency` is the currency of the recipient bank account or the currency being transferred.

### 3. Deduct the transfer fee from the converted fund

To figure out how much will be deposited into the account of the recipient, you can deduct whatever the response was from step 2 from the response of step 1. Let us assume that the resulting value is 4500 KES, meaning that the transfer fee is 500 KES.

### 4. Transfer the converted fund to the recipient bank account

At this point, you initiate a transfer of 5000 KES from your Flutterwave USD account to the recipient account. Flutterwave will debit your USD account of the USD equivalent of 5000 KES (at the current Flutterwave exchange rate) and transfer 4500 KES to the recipient bank account (depending on the exchange rate and the transfer fee). The transfer fee will be in KES too.

You can do this with guidance from the [initiate a transfer API documentation](https://developer.flutterwave.com/reference/endpoints/transfers/#initiate-a-transfer) .

```bash
curl -X POST 'https://api.flutterwave.com/v3/transfers' \
-H 'Content-Type: application/json' \
-d '{
    "account_bank": "68",
    "account_number": "0031625807099",
    "amount": 5000,
    "narration": "Withdraw Fiat",
    "currency": "KES",
    "reference": "496_PMCKDU_1",
    "debit_currency":"USD",
    "beneficiary_name": "Flutter wave Developers",
    "callback_url": "https://mycallbackurl.com/kes/callback",
    "meta": [
        {
            "sender": "Flutterwave Developers",
            "sender_country": "UK",
            "mobile_number": "+2348000000000"
        }
    ]
}' \
--header 'Authorization: Bearer YOUR_FLUTTERWAVE_SECRET_KEY'
```

- `debit_currency` is the currency of the account that funds will be transferred from. In this case, it is the Flutterwave USD account.
- `currency` is the currency of the recipient bank account
- `amount` is the amount to be transferred in the currency of the recipient bank account - 5000 KES in this case

You may be interested in checking out the [documentation for pricing and payouts](https://flutterwave.com/ng/support/pricing/pricing-for-transfers-and-payouts) for more information on specific payout currencies.

Cheers!
