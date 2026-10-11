# 📘 ASPFIY API Documentation

Welcome to the **ASPFIY API** reference documentation. ASPFIY provides endpoints for receiving bank transfer payments in Nigeria via reserved accounts (Paga, PalmPay) and dynamic temporary accounts (SafeHaven), as well as initiating outgoing bank transfers.

---

## 🔑 Base URL & Authentication

*   **Base URL:** `https://api-v1.aspfiy.com`
*   **Content-Type:** `application/json`
*   **Authentication Header:** All API calls require an authorization secret key passed via header:
    ```
    Authorization: Bearer YOUR_SECRET_KEY
    ```

---

## 🛡️ Webhook Notifications & Security

ASPFIY notifies your server of incoming transactions and disbursement status via HTTP callbacks to your specified `webhookUrl` or `callback_url`.

### Webhook Security Header
To verify that webhook requests originate from ASPFIY, every webhook payload includes an HTTP header:
*   **Header Name:** `x-wiaxy-signature`
*   **Value:** The **MD5 hash** of your secret key.

> **Verification Step:** Check for the presence of `x-wiaxy-signature` in headers and verify that its value matches `md5(YOUR_SECRET_KEY)`.

### Webhook Payloads

#### 1. Reserved Account Payment Webhook Payload
*   **Event:** `PAYMENT_NOTIFICATION`
*   **Type:** `RESERVED_ACCOUNT_TRANSACTION`

```json
{
  "event": "PAYMENT_NOTIFICATION",
  "data": {
    "type": "RESERVED_ACCOUNT_TRANSACTION",
    "reference": "transaction_reference",
    "merchant_reference": "account_reference",
    "aspfiy_ref": "inter_bank_reference",
    "amount": "payment_amount",
    "created_at": "receiving_date",
    "account": {
      "account_number": "customer_account_number",
      "account_name": "customer_account_name",
      "bank_name": "customer_bank_name",
      "created_at": "account_creation_date"
    },
    "payer": {
      "account_number": "payer_acct_num",
      "first_name": "payer_first_name",
      "last_name": "payer_last_name",
      "createdAt": "payment_date"
    }
  }
}
```

#### 2. Transfer Disbursement Webhook Payload
*   **Event:** `DISBURSEMENT`
*   **Type:** `TRANSFER_NOTIFICATION`

```json
{
  "event": "DISBURSEMENT",
  "data": {
    "type": "TRANSFER_NOTIFICATION",
    "reference": "transaction_reference",
    "amount": "transfer_amount",
    "status": "transaction_status",
    "created_at": "initiation_date",
    "beneficiary": {
      "account_number": "recepient_account_number",
      "bank_code": "bank_code",
      "account_name": "recepient_account_name",
      "bank_name": "recepient_bank_name"
    }
  }
}
```

---

## 🏛️ 1. Reserved Account Payment API

Reserved virtual accounts allow you to map dedicated account numbers to specific customers. These account numbers do not expire and are ideal for funding user wallets.

### 1.1 Reserve Paga Account
Reserve a Paga virtual account for a customer.

*   **Endpoint:** `POST /reserve-paga/`
*   **Headers:**
    *   `Authorization: Bearer YOUR_SECRET_KEY`
    *   `Content-Type: application/json`
*   **Request Body:**
    ```json
    {
      "email": "customer@example.com",
      "reference": "unique_customer_reference_001",
      "firstName": "John",
      "lastName": "Doe",
      "webhookUrl": "https://yourdomain.com/webhooks/aspfiy",
      "phone": "08012345678"
    }
    ```
*   **Request Parameters:**
    | Parameter | Type | Required | Description |
    |---|---|---|---|
    | `email` | string | Yes | Customer's email address |
    | `reference` | string | Yes | Unique merchant reference for the account |
    | `firstName` | string | Yes | Customer's first name |
    | `lastName` | string | Yes | Customer's last name |
    | `webhookUrl` | string | Yes | Callback URL for payment notification events |
    | `phone` | string | Yes | Customer's unique phone number |

---

### 1.2 Reserve PalmPay Account
Reserve a PalmPay virtual account for a customer.

*   **Endpoint:** `POST /reserve-palmpay/`
*   **Headers:**
    *   `Authorization: Bearer YOUR_SECRET_KEY`
    *   `Content-Type: application/json`
*   **Request Body:**
    ```json
    {
      "email": "customer@example.com",
      "reference": "unique_customer_reference_002",
      "firstName": "Jane",
      "lastName": "Doe",
      "webhookUrl": "https://yourdomain.com/webhooks/aspfiy",
      "phone": "08087654321"
    }
    ```
*   **Request Parameters:**
    | Parameter | Type | Required | Description |
    |---|---|---|---|
    | `email` | string | Yes | Customer's email address |
    | `reference` | string | Yes | Unique merchant reference for the account |
    | `firstName` | string | Yes | Customer's first name |
    | `lastName` | string | Yes | Customer's last name |
    | `webhookUrl` | string | Yes | Callback URL for payment notification events |
    | `phone` | string | Yes | Customer's unique phone number |

---

## ⏱️ 2. Dynamic Account Payment API

Dynamic virtual account numbers are created for single time-bound payment operations.

### 2.1 Create Dynamic Account (SafeHaven)
Create a temporary dynamic virtual account.

*   **Endpoint:** `POST /reserve-dynamic-account/`
*   **Headers:**
    *   `Authorization: Bearer YOUR_SECRET_KEY`
    *   `Content-Type: application/json`
*   **Request Body:**
    ```json
    {
      "reference": "dynamic_order_ref_1001",
      "phone": "08012345678",
      "email": "customer@example.com",
      "lastName": "Doe",
      "firstName": "John",
      "amount": "5000"
    }
    ```
*   **Request Parameters:**
    | Parameter | Type | Required | Description |
    |---|---|---|---|
    | `reference` | string | Yes | Unique order or transaction reference |
    | `phone` | string | Yes | Customer's phone number |
    | `email` | string | Yes | Customer's email address |
    | `firstName` | string | Yes | Customer's first name |
    | `lastName` | string | Yes | Customer's last name |
    | `amount` | string | Yes | Expected transfer amount |

---

## 💸 3. Transfers API

The Transfers API enables payout from your ASPFIY merchant wallet to any bank account in Nigeria. A standard payout flow involves listing supported banks, resolving recipient NUBAN details, and initiating the transfer.

### 3.1 Fetch Supported Banks
Retrieves the list of financial institutions and their corresponding bank codes.

*   **Endpoint:** `GET /transfers/banks/`
*   **Headers:**
    *   `Authorization: Bearer YOUR_SECRET_KEY`
*   **Success Response (`200 OK`):**
    ```json
    {
      "status": true,
      "message": "Banks fetched succesfully",
      "data": [
        {
          "bank_code": "044",
          "bank_name": "Access Bank"
        },
        {
          "bank_code": "033",
          "bank_name": "United Bank for Africa"
        },
        {
          "bank_code": "057",
          "bank_name": "Zenith bank PLC"
        }
      ]
    }
    ```

---

### 3.2 Resolve Account Details (NUBAN Verification)
Validate bank account details and confirm the recipient's registered account name prior to initiating a fund transfer.

*   **Endpoint:** `POST /transfers/resolve-nuban`
*   **Headers:**
    *   `Authorization: Bearer YOUR_SECRET_KEY`
    *   `Content-Type: application/json`
*   **Request Body:**
    ```json
    {
      "account_number": "1234567890",
      "bank_code": "033"
    }
    ```
*   **Request Parameters:**
    | Parameter | Type | Required | Description |
    |---|---|---|---|
    | `account_number` | string | Yes | 10-digit NUBAN account number |
    | `bank_code` | string | Yes | Destination bank code obtained from banks list |

*   **Success Response (`200 OK`):**
    ```json
    {
      "status": true,
      "message": "Validated Successfully",
      "data": {
        "bank_code": "033",
        "account_number": "1234567890",
        "account_name": "AHMAD NAZIRU"
      }
    }
    ```
*   **Error Response (`400 Bad Request`):**
    ```json
    {
      "status": false,
      "message": "Account Number Name not Found"
    }
    ```

---

### 3.3 Initiate Transfer
Disburse funds to a verified bank account.

*   **Endpoint:** `POST /Initiate Transfer`
*   **Headers:**
    *   `Authorization: Bearer YOUR_SECRET_KEY`
    *   `Content-Type: application/json`
*   **Request Body:**
    ```json
    {
      "account_number": "1234567890",
      "bank_code": "033",
      "amount": "50",
      "narration": "Payout for order #1001",
      "callback_url": "https://yourdomain.com/webhooks/transfers"
    }
    ```
*   **Request Parameters:**
    | Parameter | Type | Required | Description |
    |---|---|---|---|
    | `account_number` | string | Yes | Recipient account number |
    | `bank_code` | string | Yes | Recipient bank code |
    | `amount` | string | Yes | Transfer amount |
    | `callback_url` | string | Yes | Callback URL for disbursement updates |
    | `narration` | string | No | Transaction memo or description |

*   **Success Response (`200 OK`):**
    ```json
    {
      "status": true,
      "message": "Transfer Initiated Successfully",
      "data": {
        "reference": "82833a3c44570a96",
        "amount": 50,
        "pre_balance": 100,
        "post_balance": 50,
        "status": "INITIATED",
        "beneficiary": {
          "account_number": "1234567890",
          "account_name": "AHMAD NAZIRU",
          "bank_name": "UNITED BANK FOR AFRICA PLC",
          "bank_code": "033"
        },
        "created_at": "2022-08-14T14:49:16.000Z"
      }
    }
    ```
