# 💳 StroWallet API Reference & Documentation

Welcome to the **StroWallet API Reference Documentation**. StroWallet provides financial infrastructure allowing businesses to issue USD and Naira Virtual Cards, generate Virtual Bank Accounts, handle Money Transfers & Payroll, process Payment Collections, perform Bills Payments, send SMS Notifications, execute KYC Verifications, and manage Crypto/USDT wallets.

---

## 📌 Overview & Base Layout
* **Official Documentation Portal:** [https://strowallet.readme.io/reference](https://strowallet.readme.io/reference)
* **API Version:** `v1.0`
* **Authentication:** API Key obtained from the StroWallet dashboard (`https://strowallet.com/user/api-key`). Pass your key in request headers or payloads as required by the endpoint.

---

## 📂 Core Structure & Endpoint Categories

The StroWallet API suite is organized into 13 major reference categories:

1. [Getting Started](#1-getting-started)
2. [Virtual Card](#2-virtual-card)
3. [Virtual Bank Account](#3-virtual-bank-account)
4. [Money Transfer](#4-money-transfer)
5. [NAIRA CARD (virtual & ATM)](#5-naira-card-virtual--atm)
6. [Payment Collection](#6-payment-collection)
7. [Wallet Services API](#7-wallet-services-api)
8. [Bills Payments](#8-bills-payments)
9. [SMS Notification](#9-sms-notification)
10. [KYC Verification](#10-kyc-verification)
11. [USSD Banking](#11-ussd-banking)
12. [WhatsApp Bot API](#12-whatsapp-bot-api)
13. [Others & Crypto/Exchange](#13-others--cryptoexchange)

---

## 1. 🚀 Getting Started

Quick start guide for setting up StroWallet credentials.

| Endpoint / Topic | Path / URL Reference | Description |
| :--- | :--- | :--- |
| **Welcome** | `/reference/welcome.md` | Introduction to StroWallet features and platform overview. |
| **ApiKey** | `/reference/apikey.md` | Guide to obtaining live API keys from `https://strowallet.com/user/api-key`. |

---

## 2. 💳 Virtual Card

Endpoints to issue and manage USD Virtual Cards (including Lite cards and contactless NFC cards).

| Endpoint | Method | Reference Path | Description |
| :--- | :---: | :--- | :--- |
| **Create Lite Card** | `POST` | `/reference/create-lite-card.md` | Create non-reloadable virtual dollar card. |
| **Submit Card KYC** | `POST` | `/reference/create-customer.md` | Register cardholder KYC credentials. |
| **Get KYC Status** | `GET` | `/reference/get-customer.md` | Retrieve user KYC submission status. |
| **Create Dollar Card** | `POST` | `/reference/create-nfc-card.md` | Create card supporting NFC payments (Google Pay & Apple Pay) without separate cardholder creation. |
| **Get Card Details** | `GET` | `/reference/nfc-card-details.md` | Retrieve virtual card numbers, CVV, expiry date, and balance. |
| **Get Card History** | `GET` | `/reference/nfc-card-history.md` | Fetch transaction logs for a specific virtual card. |
| **Fund / Withdraw Card** | `POST` | `/reference/fund-withdraw-nfc.md` | Deposit funds into or withdraw balance from a virtual dollar card. |
| **Freeze / Activate Card** | `POST` | `/reference/freezeactivate-nfc.md` | Toggle card active status (freeze/unfreeze). |
| **Terminate Card** | `POST` | `/reference/fund-card.md` | Terminate card and return remaining balance. |
| **Ziiropay Balance** | `GET` | `/reference/ziiropay-balance.md` | Fetch balance from Ziiropay virtual card provider account. |
| **Webhook for Card** | `POST` | `/reference/webhook-for-card.md` | Webhook notification payload for card authorizations and charges. |

---

## 3. 🏦 Virtual Bank Account

Generate and manage dedicated and dynamic virtual bank accounts for automated payment collection.

### 3.1 Bank Account Creation Endpoints

By default, **Nombank MFB** accounts are created using the default endpoint. For bank-specific virtual accounts, use the respective endpoints:

| Bank Provider | Endpoint URL | Method |
| :--- | :--- | :---: |
| **Default (New Customer)** | `https://strowallet.com/api/virtual-bank/new-customer` | `POST` |
| **PAGA Bank Account** | `https://strowallet.com/api/virtual-bank/paga` | `POST` |
| **Sterling Bank Account** | `https://strowallet.com/api/virtual-bank/sterling` | `POST` |
| **Nombank MFB Account** | `https://strowallet.com/api/virtual-bank/amucha` | `POST` |
| **Fidelity Bank Account** | `https://strowallet.com/api/virtual-bank/fidelitybank` | `POST` |

### 3.2 Request Body Parameters (`application/json`)

| Parameter | Type | Required / Optional | Description |
| :--- | :---: | :---: | :--- |
| `public_key` | `string` | **Required** | StroWallet Public Key |
| `email` | `string` | **Required** | Email address to receive payment notifications |
| `account_name` | `string` | **Required** | Desired Bank Account Name |
| `phone` | `string` | **Required** | Customer Phone Number |
| `webhook_url` | `string` | **Required** | Webhook URL for auto notifications on payments |
| `mode` | `string` | Optional | Environment mode (Default: `"sandbox"`, set to `"live"` for production) |
| `developer_code` | `string` | Optional | Developer tracking code provided by StroWallet |

#### Example Request Payload:
```json
{
  "public_key": "YOUR_PUBLIC_KEY",
  "email": "customer@example.com",
  "account_name": "John Doe Enterprises",
  "phone": "08012345678",
  "webhook_url": "https://yourdomain.com/api/webhook/virtual-bank",
  "mode": "sandbox",
  "developer_code": "DEV123"
}
```

### 3.3 Other Virtual Bank Account Endpoints

| Endpoint | Method | Reference Path | Description |
| :--- | :---: | :--- | :--- |
| **WebHook** | Callback | `/reference/webhook.md` | Real-time webhook structure to receive account credit notifications. |
| **Verify SessionID** | `GET` | `/reference/verify-sessionid.md` | Verify NIP bank transfer transaction using session ID. |
| **Dynamic Virtual Account** | `POST` | `/reference/dynamic-virtual-acct.md` | Generate short-lived (30-minute) virtual accounts for checkout sessions. |
| **Get All Accounts** | `GET` | `/reference/get-all-accounts.md` | Fetch list of all generated virtual bank accounts. |

---

## 4. 💸 Money Transfer & Payroll

Send payouts to Nigerian bank accounts and run bulk payroll disbursements.

| Endpoint | Method | Reference Path | Description |
| :--- | :---: | :--- | :--- |
| **Get Bank Lists** | `GET` | `/reference/get-bank-lists.md` | Retrieve list of all supported financial institutions and bank codes. |
| **Get Account Name** | `GET` | `/reference/get-account-name.md` | Resolve account number to verify recipient owner name before transfer. |
| **Bank Transfer** | `POST` | `/reference/bank-transfer.md` | Initiate single bank transfer. |
| **Webhook** | Callback | `/reference/bank-transfer-webhook.md` | Status notification for payout transfers. |
| **Submit Payroll** | `POST` | `/reference/submit-payroll.md` | Create bulk payout batches for staff salaries, disbursements, or giveaways. |
| **Get Payroll** | `GET` | `/reference/get-payroll.md` | Fetch created payroll accounts and batches. |
| **Edit Payroll** | `POST` | `/reference/edit-payroll.md` | Modify active payroll batch details. |
| **Delete From Payroll** | `PUT` | `/reference/delete-from-payroll.md` | Remove recipient from bulk payroll batch. |
| **Payroll Payment** | `GET` | `/reference/payroll-payment.md` | View summary of total amounts, fees, and ready status prior to processing. |
| **Payroll FundTransfer** | `POST` | `/reference/payroll-fundtransfer.md` | Execute processing of funded payroll batch. |
| **Payroll Transfer Status** | `GET` | `/reference/payroll-transfer-status.md` | Query execution status of payroll transfers. |

---

## 5. 💳 NAIRA CARD (Virtual & Physical ATM)

Issue Naira Verve / Mastercard virtual cards and physical ATM cards.

| Endpoint | Method | Reference Path | Description |
| :--- | :---: | :--- | :--- |
| **Introduction** | Info | `/reference/introduction.md` | Introduction to Naira Card API. |
| **Naira Card User** | `POST` | `/reference/naira-card-user.md` | Register customer profile for Naira card issuance. |
| **Create Naira Card** | `POST` | `/reference/create-naira-card.md` | Request Virtual or Physical ATM Naira Card. |
| **View Card** | `GET` | `/reference/view-card.md` | View Naira card details and status. |
| **Generate Test Card** | `POST` | `/reference/generate-test-card.md` | Generate sandbox test card for integration verification. |
| **Card History** | `GET` | `/reference/card-history.md` | View Naira card transaction history. |
| **Change Card Pin** | `POST` | `/reference/change-card-pin.md` | Update PIN for Verve or physical ATM card. |
| **Enable 2FA** | `POST` | `/reference/enable-2fa.md` | Enable SafeToken / 2FA security on Naira cards. |
| **Reset CardPIN** | `PUT` | `/reference/reset-cardpin.md` | Reset forgotten card PIN. |
| **Create Dispute** | `POST` | `/reference/create-dispute.md` | Submit chargeback or transaction dispute. |
| **Update Card** | `PUT` | `/reference/update-card.md` | Activate or deactivate Naira card. |
| **Card Authorization** | Info | `/reference/card-authorization.md` | Authorization rules and hooks for Naira cards. |

---

## 6. 🛒 Payment Collection

Integrate hosted payment checkouts and platform plugins.

| Endpoint | Method | Reference Path | Description |
| :--- | :---: | :--- | :--- |
| **Checkout Payment** | `POST` | `/reference/checkout-payment.md` | Initiate hosted web checkout session. |
| **Checkout Status** | `GET` | `/reference/checkout-status.md` | Query current checkout payment status. |
| **Checkout Callback** | Callback | `/reference/checkout-callback.md` | Callback URL payload sent upon completion. |
| **WooCommerce Plugin** | Plugin | [WordPress Repository](https://wordpress.org/plugins/payment-gateway-for-strowallet-on-woo/) | Official WooCommerce payment gateway plugin. |
| **WHMCS Module** | Module | [WHMCS Marketplace](https://marketplace.whmcs.com/product/6635-strowallet-whmcs-payment-module) | Official WHMCS module for web hosting billing. |

---

## 7. 💼 Wallet Services API

Manage agency subaccounts, POS terminals, and wallet operations.

| Endpoint | Method | Reference Path | Description |
| :--- | :---: | :--- | :--- |
| **Create Subaccount** | `POST` | `/reference/create-subaccount.md` | Register agency or merchant subaccount. |
| **SubAccount Details** | `GET` | `/reference/subaccount-details.md` | Retrieve subaccount info and balance. |
| **Update Subaccount** | `PUT` | `/reference/update-subaccount.md` | Modify subaccount parameters. |
| **Get Subaccounts** | `GET` | `/reference/get-subaccounts.md` | List all registered subaccounts. |
| **Assign Bank** | `POST` | `/reference/assign-bank.md` | Link dedicated bank account to subaccount. |
| **Assign Terminal** | `POST` | `/reference/assign-terminal.md` | Link POS terminal hardware to subaccount. |
| **UnAssign Terminal** | `POST` | `/reference/un-assign-terminal.md` | Unlink POS terminal hardware. |
| **Get Subhistory** | `GET` | `/reference/get-subhistory.md` | Retrieve subaccount transaction history. |
| **Change Pin** | `POST` | `/reference/change-pin.md` | Change subaccount transaction PIN. |
| **Debit Subaccount** | `POST` | `/reference/debit-subaccount.md` | Perform admin debit on subaccount. |
| **Transfer to Bank** | `POST` | `/reference/transfer-to-bank.md` | Subaccount bank payout. |
| **Change Status** | `POST` | `/reference/change-status.md` | Update active/suspended status of subaccount. |
| **Sub Buy Airtime / Data** | `POST` | `/reference/buy-airtime-1.md` / `/reference/sub-buy-data.md` | Airtime & Data purchase via subaccount. |
| **Sub CableTV / Disco** | `POST` | `/reference/sub-cabletv.md` / `/reference/sub-disco.md` | Cable TV & Electricity bill payment via subaccount. |
| **Set-up Fee** | `PUT` | `/reference/set-up-fee.md` | Configure setup fee rules. |
| **Get All SubHistories** | `GET` | `/reference/get-all-subhistories.md` | Retrieve unified log of all subaccount transactions. |

---

## 8. 📱 Bills Payments

Utility and telecom bill payment suite.

| Endpoint | Method | Reference Path | Description |
| :--- | :---: | :--- | :--- |
| **Buy Airtime** | `POST` | `/reference/buy-airtime.md` | Purchase mobile airtime top-up. |
| **Get Data Plans** | `GET` | `/reference/get-data-plans.md` | Fetch available data bundles by network provider. |
| **Buy Data** | `POST` | `/reference/buy-data.md` | Purchase mobile data plan. |
| **Get CableTV Plans** | `GET` | `/reference/get-cabletv-plans.md` | Fetch bouquet options (DSTV, GOTV, Startimes). |
| **Verify SmartCard Number** | `POST` | `/reference/verify-smartcard-number.md` | Validate SmartCard/IUC number to resolve owner name. |
| **Subscribe CableTV** | `POST` | `/reference/recharge-cabletv.md` | Renew cable TV subscription. |
| **Verify Meter Number** | `POST` | `/reference/verify-meter-number.md` | Validate electricity meter number before purchase. |
| **Subscribe Electricity Bill**| `POST` | `/reference/subscribe-electricity.md` | Purchase electricity token / post-paid payment. |
| **Educational Pin** | `POST` | `/reference/educational.md` | Purchase WAEC result checker PINs. |
| **Recharge Card Printing** | `POST` | `/reference/recharge-card-printing.md` | Generate e-PINs for MTN, Glo, 9mobile, Airtel. |
| **SME Data Services** | Various | `/reference/get-smeplans.md` / `buysme-data.md` / `query-sme.md` | Fetch SME plans, purchase SME data, and query transaction status. |

---

## 9. 💬 SMS Notification

Send transactional SMS, OTPs, and WhatsApp verifications.

| Endpoint | Method | Reference Path | Description |
| :--- | :---: | :--- | :--- |
| **Submit SenderID** | `POST` | `/reference/submit-senderid.md` | Request custom SMS Sender ID registration. |
| **Send SMS** | `POST` | `/reference/send-sms.md` | Send general bulk SMS messages. |
| **OTP SMS** | `POST` | `/reference/otp-sms.md` | Send high-priority One Time Passwords. |
| **Alert SMS** | `POST` | `/reference/alert-sms.md` | Send credit/debit transactional alerts. |
| **WhatsApp OTP** | `POST` | `/reference/whatsapp-otp.md` | Dispatch OTP code over WhatsApp API. |

---

## 10. 🔍 KYC Verification

Identity verification endpoints for BVN, NIN, and CAC business registration.

| Endpoint | Method | Reference Path | Description |
| :--- | :---: | :--- | :--- |
| **Verify BVN / Confirm BVN** | `POST` | `/reference/verify-bvn.md` / `confirm-bvn.md` | Initiate and confirm Bank Verification Number lookup. |
| **Verify CAC** | `POST` | `/reference/verify-cac.md` | Verify registered business details with CAC. |
| **Get BVN Details** | `GET` | `/reference/get-bvn-details.md` | Fetch basic profile tied to BVN. |
| **Get NIN Details** | `GET` | `/reference/get-nin-details.md` | Query National Identity Number records. |
| **Get CAC Details** | `GET` | `/reference/get-cac-details.md` | Query full corporate status details. |
| **Get BVN Full Details** | `GET` | `/reference/get-bvn-details-advance.md` | Fetch advanced BVN record including photo and biographics. |
| **Verify NIN** | `POST` | `/reference/verify-nin.md` | Verify NIN credentials. |

---

## 11. 📞 USSD Banking

Interactive USSD banking interface setup and code execution.

| Endpoint / Topic | Reference Path | Description |
| :--- | :--- | :--- |
| **Introduction** | `/reference/introduction-1.md` | Intro to USSD banking integration. |
| **USSD Menu** | `/reference/ussd-menu.md` | Menu tree structure and configuration. |
| **Readymade Code** | `/reference/readymade-code.md` | Sample USSD handler code scripts. |

---

## 12. 🤖 WhatsApp Bot API

Automate conversational banking via WhatsApp bot.

| Endpoint | Method | Reference Path | Description |
| :--- | :---: | :--- | :--- |
| **WhatsApp Banking API** | Info | `/reference/whatsapp-banking.md` | Overview of WhatsApp Bot integration. |
| **Add Phone Number** | `POST` | `/reference/add-phone-number.md` | Whitelist phone number for bot interaction. |
| **Remove Phone Number** | `POST` | `/reference/remove-phone-number.md` | Revoke WhatsApp bot access for a phone number. |
| **WA Bot Customers** | `GET` | `/reference/wa-bot-customers.md` | Fetch active bot users. |

---

## 13. 💱 Others & Crypto/Exchange

Crypto wallet creation, currency exchange, balance checking, and Ziiro subaccounts.

| Endpoint | Method | Reference Path | Description |
| :--- | :---: | :--- | :--- |
| **Ziiro Subaccounts** | Various | `/reference/ziiro-subacct.md` / `ziiro-sub-details.md` / `ziiro-all-subs.md` / `ziiro-subhistory.md` | Manage Ziiro pay subaccounts and logs. |
| **Available Coins** | `GET` | `/reference/available-coins.md` | Retrieve list of supported crypto currencies. |
| **Create Coin Address** | `POST` | `/reference/create-coin-address.md` | Generate crypto deposit address. |
| **Ziiro Send Coin** | `POST` | `/reference/ziiro-send-coin.md` | Transfer crypto assets via Ziiro. |
| **Create USDT Address** | `POST` | `/reference/create-usdt-address.md` | Generate USDT TRC20 wallet address for deposits. |
| **Get USDT History** | `GET` | `/reference/get-usdt-transaction-history.md` | Fetch USDT deposit and transfer log. |
| **Send USDT** | `POST` | `/reference/send-usdt.md` | Withdraw/transfer USDT on TRC20 network. |
| **Exchange Rate** | `GET` | `/reference/exchange-rate.md` | Query live NGN/USD or USD/NGN exchange rates. |
| **Exchange Currency** | `POST` | `/reference/exchange-currency.md` | Convert balance between NGN and USD. |
| **Check Balance** | `GET` | `/reference/check-balance.md` | Retrieve main StroWallet account balances (USD / NGN). |
| **Validate License** | `POST` | `/reference/validate-license.md` | Verify StroWallet integration key and license. |
| **Crypto Webhook** | Callback | `/reference/webhook-1.md` | Receive webhooks for USDT / Crypto deposit wallet top-ups. |
