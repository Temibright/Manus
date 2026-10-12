# 💳 StroWallet Comprehensive API Documentation & Parameter Reference

Welcome to the complete **StroWallet API Reference Manual**. This document provides detailed information on endpoints, HTTP methods, server URLs, and parameter schemas (query, header, and body parameters) for all StroWallet services.

---

## 📌 General Information & Authentication
* **Official Reference Portal:** [https://strowallet.readme.io/reference](https://strowallet.readme.io/reference)
* **API Version:** `v1.0`
* **Authentication:** Most endpoints require your StroWallet public key (`public_key`) or API key supplied in the request body or parameters.
* **Content-Type:** `application/json` (unless specified otherwise).

---

## 1. GETTING STARTED

### Welcome
- **Reference URL:** `https://strowallet.readme.io/reference/welcome`

## 2. VIRTUAL CARD

### Create Lite Card
- **Method:** `POST`
- **Endpoint URL:** `https://ziiropay.com/api/bitvcard/create_litecard`
- **Description:** This is Non reloadable virtual dollar card

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `public_key` | `query` (string) | Yes |  |
| `amount` | `query` (string) | Yes |  |
| `brand` | `query` (string) | Yes | VISA or MASTERCARD |
| `name_on_card` | `query` (string) | Yes |  |
| `id_number` | `query` (string) | Yes |  |
| `id_type` | `query` (string) | Yes | national_id, passport, nin, bvn, driver_license |
| `mode` | `query` (string) | No |  |

## 3. VIRTUAL BANK ACCOUNT

### Create Virtual Bank Account (Default - Nombank MFB)
- **Method:** `POST`
- **Endpoint URL:** `https://strowallet.com/api/virtual-bank/new-customer`
- **Description:** Creates a default Nombank MFB virtual bank account for automated customer payment collection.

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `public_key` | `body` (string) | Yes | Strowallet public key |
| `email` | `body` (string) | Yes | To receive Payment Notifications |
| `account_name` | `body` (string) | Yes | Bank Account Name |
| `phone` | `body` (string) | Yes | Phone number |
| `webhook_url` | `body` (string) | Yes | Provide webhook url to get auto notification |
| `mode` | `body` (string) | No | Environment mode (sandbox/live) |
| `developer_code` | `body` (string) | No | Provided by strowallet (optional) |

---

### Create PAGA Bank Account
- **Method:** `POST`
- **Endpoint URL:** `https://strowallet.com/api/virtual-bank/paga`
- **Description:** Creates a dedicated PAGA Bank virtual account.

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `public_key` | `body` (string) | Yes | Strowallet public key |
| `email` | `body` (string) | Yes | To receive Payment Notifications |
| `account_name` | `body` (string) | Yes | Bank Account Name |
| `phone` | `body` (string) | Yes | Phone number |
| `webhook_url` | `body` (string) | Yes | Provide webhook url to get auto notification |
| `mode` | `body` (string) | No | Environment mode (sandbox/live) |
| `developer_code` | `body` (string) | No | Provided by strowallet (optional) |

---

### Create Sterling Bank Account
- **Method:** `POST`
- **Endpoint URL:** `https://strowallet.com/api/virtual-bank/sterling`
- **Description:** Creates a dedicated Sterling Bank virtual account.

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `public_key` | `body` (string) | Yes | Strowallet public key |
| `email` | `body` (string) | Yes | To receive Payment Notifications |
| `account_name` | `body` (string) | Yes | Bank Account Name |
| `phone` | `body` (string) | Yes | Phone number |
| `webhook_url` | `body` (string) | Yes | Provide webhook url to get auto notification |
| `mode` | `body` (string) | No | Environment mode (sandbox/live) |
| `developer_code` | `body` (string) | No | Provided by strowallet (optional) |

---

### Create Nombank MFB Account
- **Method:** `POST`
- **Endpoint URL:** `https://strowallet.com/api/virtual-bank/amucha`
- **Description:** Creates a dedicated Nombank MFB virtual account.

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `public_key` | `body` (string) | Yes | Strowallet public key |
| `email` | `body` (string) | Yes | To receive Payment Notifications |
| `account_name` | `body` (string) | Yes | Bank Account Name |
| `phone` | `body` (string) | Yes | Phone number |
| `webhook_url` | `body` (string) | Yes | Provide webhook url to get auto notification |
| `mode` | `body` (string) | No | Environment mode (sandbox/live) |
| `developer_code` | `body` (string) | No | Provided by strowallet (optional) |

---

### Create Fidelity Bank Account
- **Method:** `POST`
- **Endpoint URL:** `https://strowallet.com/api/virtual-bank/fidelitybank`
- **Description:** Creates a dedicated Fidelity Bank virtual account.

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `public_key` | `body` (string) | Yes | Strowallet public key |
| `email` | `body` (string) | Yes | To receive Payment Notifications |
| `account_name` | `body` (string) | Yes | Bank Account Name |
| `phone` | `body` (string) | Yes | Phone number |
| `webhook_url` | `body` (string) | Yes | Provide webhook url to get auto notification |
| `mode` | `body` (string) | No | Environment mode (sandbox/live) |
| `developer_code` | `body` (string) | No | Provided by strowallet (optional) |


## 4. MONEY TRANSFER

### Get Bank Lists
- **Method:** `GET`
- **Endpoint URL:** `https://strowallet.com/api/banks/lists`
- **Description:** Get all Bank names and  Bank codes

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `public_key` | `query` (string) | Yes | Strowallet Public key |

## 5. NAIRA CARD (VIRTUAL & ATM)

### Introduction
- **Reference URL:** `https://strowallet.readme.io/reference/introduction`

### Naira Card User
- **Method:** `POST`
- **Endpoint URL:** `https://strowallet.com/api/naira_carduser`
- **Description:** Create Naira Card Customer

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `public_key` | `query` (string) | Yes |  |
| `firstname` | `query` (string) | Yes |  |
| `lastname` | `query` (string) | Yes |  |
| `email` | `query` (string) | Yes |  |
| `phone` | `query` (string) | Yes | international format e.g 2348012345678 |
| `nin` | `query` (string) | Yes |  |
| `dob` | `query` (string) | Yes | YYYY/MM/DD |
| `name` | `query` (string) | Yes | unique e.g Your customer's username |
| `line1` | `query` (string) | Yes | Address |
| `city` | `query` (string) | Yes |  |
| `state` | `query` (string) | Yes | E.g lg for lagos, oy for Oyo, og for Ogun |
| `mode` | `query` (string) | No |  |
| `provider` | `query` (string) | Yes | black or white |

## 6. PAYMENT COLLECTION

### Checkout Payment
- **Method:** `POST`
- **Endpoint URL:** `https://strowallet.com/pay`

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `public_key` | `query` (string) | Yes |  |
| `amount` | `query` (string) | Yes |  |
| `currency` | `query` (string) | Yes | NGN and USD supported |
| `description` | `query` (string) | Yes | example: Purchase Software |
| `customer_name` | `query` (string) | Yes | John Doe |
| `customer_email` | `query` (string) | Yes | johndoe@gmail.com |
| `callback_url` | `query` (string) | Yes |  |
| `success_url` | `query` (string) | Yes | Where do you want users to be redirected after successful payment |
| `cancel_url` | `query` (string) | Yes | Where do you want users to be redirected after cancel payment |

### Checkout Status
- **Method:** `GET`
- **Endpoint URL:** `https://strowallet.com/api/checkout/status`

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `public_key` | `query` (string) | Yes |  |
| `reference` | `query` (string) | Yes |  |

### Checkout callback
## 7. WALLET SERVICES API

### Create Subaccount
- **Method:** `POST`
- **Endpoint URL:** `https://strowallet.com/api/create_subaccount`

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `first_name` | `query` (string) | Yes |  |
| `last_name` | `query` (string) | Yes |  |
| `email` | `query` (string) | Yes |  |
| `phone` | `query` (string) | Yes |  |
| `bvn` | `query` (string) | Yes |  |
| `state` | `query` (string) | Yes |  |
| `pin` | `query` (string) | Yes |  |
| `address` | `query` (string) | Yes |  |
| `country` | `query` (string) | Yes |  |
| `secret_key` | `query` (string) | Yes |  |
| `merchant_id` | `query` (string) | Yes |  |
| `website_url` | `query` (string) | Yes |  |
| `cac` | `query` (string) | No | optional |
| `business` | `query` (string) | No | optional |
| `company_type` | `query` (string) | No | RC or BN (optional) |

### SubAccount Details
- **Method:** `GET`
- **Endpoint URL:** `https://strowallet.com/api/get_subaccount`

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `subaccount_id` | `query` (string) | Yes |  |
| `secret_key` | `query` (string) | Yes |  |
| `merchant_id` | `query` (string) | Yes |  |
| `website_url` | `query` (string) | Yes |  |

### Update Subaccount
- **Method:** `PUT`
- **Endpoint URL:** `https://strowallet.com/api/update_subaccount`

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `subaccount_id` | `query` (string) | Yes |  |
| `first_name` | `query` (string) | Yes |  |
| `last_name` | `query` (string) | Yes |  |
| `email` | `query` (string) | Yes |  |
| `phone` | `query` (string) | Yes |  |
| `bvn` | `query` (string) | Yes |  |
| `state` | `query` (string) | Yes |  |
| `address` | `query` (string) | Yes |  |
| `country` | `query` (string) | Yes |  |
| `secret_key` | `query` (string) | Yes |  |
| `merchant_id` | `query` (string) | Yes |  |
| `website_url` | `query` (string) | Yes |  |

### Get Subaccounts
- **Method:** `GET`
- **Endpoint URL:** `https://strowallet.com/api/subaccounts`

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `secret_key` | `query` (string) | Yes |  |
| `merchant_id` | `query` (string) | Yes |  |
| `website_url` | `query` (string) | Yes |  |

### Assign Bank
- **Method:** `POST`
- **Endpoint URL:** `https://strowallet.com/api/assign_bankdetails`

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `subaccount_id` | `query` (string) | Yes |  |
| `account_name` | `query` (string) | Yes |  |
| `secret_key` | `query` (string) | Yes |  |

### Assign Terminal
- **Method:** `POST`
- **Endpoint URL:** `https://strowallet.com/api/assign_terminal`

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `secret_key` | `query` (string) | Yes |  |
| `subaccount_id` | `query` (string) | Yes |  |
| `serialNumber` | `query` (string) | Yes | POS SN |
| `merchant_id` | `query` (string) | Yes |  |
| `website_url` | `query` (string) | Yes |  |

### UnAssign Terminal
- **Method:** `POST`
- **Endpoint URL:** `https://strowallet.com/api/unassign_terminal`

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `secret_key` | `query` (string) | Yes |  |
| `subaccount_id` | `query` (string) | Yes |  |

### Get Subhistory
- **Method:** `GET`
- **Endpoint URL:** `https://strowallet.com/api/get_subhistory`

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `subaccount_id` | `query` (string) | Yes |  |
| `secret_key` | `query` (string) | Yes |  |

### Change Pin
- **Method:** `POST`
- **Endpoint URL:** `https://strowallet.com/api/sub_changepin/`

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `subaccount_id` | `query` (string) | Yes |  |
| `secret_key` | `query` (string) | Yes |  |
| `pin` | `query` (string) | Yes |  |
| `pin_confirmation` | `query` (string) | Yes |  |

### Debit Subaccount
- **Method:** `POST`
- **Endpoint URL:** `https://strowallet.com/api/debitsub`

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `subaccount_id` | `query` (string) | Yes |  |
| `amount` | `query` (string) | Yes |  |
| `pin` | `query` (string) | Yes | sub account pin |
| `secret_key` | `query` (string) | Yes |  |

### Transfer to Bank
- **Method:** `POST`
- **Endpoint URL:** `https://strowallet.com/api/fundtrffromsub`

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `subaccount_id` | `query` (string) | Yes |  |
| `pin` | `query` (string) | Yes |  |
| `amount` | `query` (string) | Yes |  |
| `beneficiaryAccountNumber` | `query` (string) | Yes |  |
| `beneficiaryBankCode` | `query` (string) | Yes |  |
| `beneficiaryName` | `query` (string) | Yes |  |
| `beneficiaryBankName` | `query` (string) | Yes |  |
| `narration` | `query` (string) | Yes |  |
| `secret_key` | `query` (string) | Yes |  |

### Change status
- **Method:** `POST`
- **Endpoint URL:** `https://strowallet.com/api/sub_status`

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `subaccount_id` | `query` (string) | Yes |  |
| `secret_key` | `query` (string) | Yes |  |
| `status` | `query` (string) | Yes | 0 for Deactivate   1 for activate |

### Sub Buy airtime
- **Method:** `POST`
- **Endpoint URL:** `https://strowallet.com/api/sub_airtime`

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `secret_key` | `query` (string) | Yes |  |
| `serviceID` | `query` (string) | Yes | mtn, glo, airtel, etisalat |
| `amount` | `query` (string) | Yes |  |
| `phone` | `query` (string) | Yes |  |
| `pin` | `query` (string) | Yes |  |
| `subaccount_id` | `query` (string) | Yes |  |

### Sub Buy Data
- **Method:** `POST`
- **Endpoint URL:** `https://strowallet.com/api/sub_buyData`

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `secret_key` | `query` (string) | Yes |  |
| `subaccount_id` | `query` (string) | Yes |  |
| `serviceID` | `query` (string) | Yes | mtn-data, glo-data, airtel-data, etisalat-data |
| `variation_code` | `query` (string) | Yes | get it from https://strowallet.readme.io/reference/get-data-plans |
| `phone` | `query` (string) | No |  |
| `pin` | `query` (string) | No |  |

### Sub CableTv
- **Method:** `POST`
- **Endpoint URL:** `https://strowallet.com/api/sub_buyCabletv`
- **Description:** Use this api https://strowallet.readme.io/reference/verify-smartcard-number to verify the customer before subscrib

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `secret_key` | `query` (string) | Yes |  |
| `subaccount_id` | `query` (string) | Yes |  |
| `serviceID` | `query` (string) | Yes | gotv, dstv, startimes, showmax |
| `pin` | `query` (string) | Yes |  |
| `variation_code` | `query` (string) | Yes | Use this to get it https://strowallet.readme.io/reference/get-cabletv-plans |
| `billersCode` | `query` (string) | Yes | Smartcard/IUC Number |
| `phone` | `query` (string) | Yes |  |

## 8. BILLS PAYMENTS

### Buy Airtime
- **Method:** `POST`
- **Endpoint URL:** `https://strowallet.com/api/buyairtime/request`

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `public_key` | `body` (string) | Yes |  |
| `amount` | `body` (string) | Yes |  |
| `phone` | `body` (string) | Yes |  |
| `service_name` | `body` (string) | Yes | mtn, glo, airtel, etisalat |

### Get Data Plans
- **Method:** `GET`
- **Endpoint URL:** `https://strowallet.com/api/buydata/plans`

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `public_key` | `query` (string) | Yes |  |
| `service_name` | `query` (string) | Yes | mtn-data, airtel-data, glo-data,  etisalat-data, spectranet, smile-direct |

### Buy Data
- **Method:** `POST`
- **Endpoint URL:** `https://strowallet.com/api/buydata/request`

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `public_key` | `body` (string) | No |  |
| `amount` | `body` (string) | No |  |
| `phone` | `body` (string) | No |  |
| `service_name` | `body` (string) | No | e.g mtn data 100mb #100, glo 2.5gb data #500, |
| `service_id` | `body` (string) | No | mtn-data, airtel-data, glo-data, etisalat-data, spectranet, smile-direct |
| `variation_code` | `body` (string) | No | You'll get this from ( Get Data Plan endpoint ) |

### Get CableTv Plans
- **Method:** `GET`
- **Endpoint URL:** `https://strowallet.com/api/cable-subscription/plans`

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `public_key` | `query` (string) | Yes |  |
| `service_id` | `query` (string) | Yes | dstv | gotv | startimes | showmax |

### Verify SmartCard Number
- **Method:** `POST`
- **Endpoint URL:** `https://strowallet.com/api/cable-subscription/verify-merchant`
- **Description:** Verify the SmartCard number to get the owner's name before recharge

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `public_key` | `query` (string) | No |  |
| `service_id` | `query` (string) | No | dstv | gotv | startimes | showmax |
| `customer_id` | `query` (string) | No | Smartcard Number |

## 9. SMS NOTIFICATION

### Submit SenderID
- **Method:** `POST`
- **Endpoint URL:** `https://strowallet.com/api/senderid-sms`

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `sender_id` | `query` (string) | Yes |  |
| `public_key` | `query` (string) | Yes |  |

### Send SMS
- **Method:** `POST`
- **Endpoint URL:** `https://strowallet.com/api/bulk-sms`

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `public_key` | `query` (string) | Yes | Your strowallet public key |
| `sender_id` | `query` (string) | Yes | Sender name like Your brand/company name |
| `message` | `query` (string) | Yes | the message you want to send |
| `phone` | `query` (string) | Yes | Recipent e.g 080123456 or 234801234 |

### OTP SMS
- **Method:** `POST`
- **Endpoint URL:** `https://strowallet.com/api/Otp-sms`

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `public_key` | `query` (string) | Yes |  |
| `phone` | `query` (string) | Yes |  |
| `otp` | `query` (string) | Yes | Your generated OTP ( 4 to 6 characters ) |

### Alert SMS
- **Method:** `POST`
- **Endpoint URL:** `https://strowallet.com/api/Alert-sms`
- **Description:** This is transactional sms which you can use to send credit and debit alert to your customers

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `public_key` | `query` (string) | Yes |  |
| `phone` | `query` (string) | Yes |  |
| `alert_type` | `query` (string) | Yes | credit or debit |
| `sender_id` | `query` (string) | Yes | This is your brand name |
| `amount` | `query` (string) | Yes | amount you credit or debit customer |
| `user_balance` | `query` (string) | Yes | Your custober balance after credited or debited |
| `desc` | `query` (string) | Yes | the purpose or narration of this transaction |

## 10. KYC VERIFICATION

### Verify BVN
- **Method:** `POST`
- **Endpoint URL:** `https://strowallet.com/api/kyc_bvn`

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `public_key` | `query` (string) | Yes |  |
| `number` | `query` (string) | Yes | BVN NUMBER if sandbox then use 12345678901 |
| `firstName` | `query` (string) | Yes | in Capiter Letter |
| `lastName` | `query` (string) | Yes | in Capital letter |
| `dateOfBirth` | `query` (string) | Yes | Sample DD-MM-YYYY |
| `phoneNumber` | `query` (string) | Yes | in local format example 08100000000 |
| `mode` | `query` (string) | No |  |

### Confirm BVN
- **Method:** `POST`
- **Endpoint URL:** `https://strowallet.com/api/kyc_bvnotp`

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `public_key` | `query` (string) | Yes |  |
| `trx` | `query` (string) | Yes | from verify bvn response if sandbox then use 1234567890 |
| `otp` | `query` (string) | Yes | which sent to customer's phone if sandbox then use 123456 |
| `mode` | `query` (string) | No |  |

### Verify CAC
- **Method:** `POST`
- **Endpoint URL:** `https://strowallet.com/api/kyb_cac`
- **Description:** Use this to verify your customer's business

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `public_key` | `query` (string) | Yes |  |
| `rc_number` | `query` (string) | Yes |  |
| `company_type` | `query` (string) | Yes | RC or BN |
| `entity_type` | `query` (string) | Yes | RC or BN |
| `company_name` | `query` (string) | Yes | in Capital letter |
| `surname` | `query` (string) | Yes | director surname |
| `firstname` | `query` (string) | Yes | director firstname |
| `email` | `query` (string) | Yes | that attached to CAC |
| `phoneNumber` | `query` (string) | Yes | that attached to CAC |
| `gender` | `query` (string) | Yes | MALE or FEMALE |
| `city` | `query` (string) | Yes | from your CAC |
| `occupation` | `query` (string) | Yes | from your CAC |

## 11. USSD BANKING

### Introduction
- **Reference URL:** `https://strowallet.readme.io/reference/introduction-1`

### USSD MENU
- **Reference URL:** `https://strowallet.readme.io/reference/ussd-menu`

### Readymade Code
- **Reference URL:** `https://strowallet.readme.io/reference/readymade-code`

## 12. WHATSAPP BOT API

### Whatsapp Banking Api
- **Reference URL:** `https://strowallet.readme.io/reference/whatsapp-banking`

### Add Phone Number
- **Method:** `POST`
- **Endpoint URL:** `https://strowallet.com/api/LinkCustomerPhoneWabot`
- **Description:** Add phone number you want to allow to access your Whatsapp bot

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `public_key` | `query` (string) | Yes |  |
| `customer_phone` | `query` (string) | Yes |  |
| `customer_name` | `query` (string) | Yes |  |
| `tenant_id` | `query` (string) | Yes |  |

## 13. OTHERS

### Ziiro SubAcct
- **Method:** `POST`
- **Endpoint URL:** `https://www.ziiropay.com/api/CreateSubAcct`
- **Description:** Must have account with ziiropay.com

**Request Parameters:**

| Parameter | In / Type | Required | Description |
| :--- | :---: | :---: | :--- |
| `public_key` | `query` (string) | Yes |  |
| `firstname` | `query` (string) | Yes |  |
| `lastname` | `query` (string) | Yes |  |
| `email` | `query` (string) | Yes |  |
| `phone` | `query` (string) | Yes |  |
| `country` | `query` (string) | Yes |  |
| `state` | `query` (string) | Yes |  |
| `city` | `query` (string) | Yes |  |
| `address` | `query` (string) | Yes |  |
| `mode` | `query` (string) | No | sandbox for testing and remove this for production |
