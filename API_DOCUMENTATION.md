# 📘 Katpay API Documentation

Welcome to the **Katpay API** documentation. Katpay provides a robust and secure API for processing payments in Nigeria. This document details the endpoints, authentication mechanisms, request/response payloads, code examples, webhook handling, and security best practices for integrating Katpay services.

---

## 📋 Table of Contents
1. [Authentication & Base URL](#-authentication--base-url)
2. [Virtual Accounts](#-virtual-accounts)
   - [Create Virtual Account](#1-create-virtual-account)
3. [Payouts & Wallet Balance](#-payouts--wallet-balance)
   - [Create Payout](#1-create-payout)
   - [Get Payout Status](#2-get-payout-status)
   - [Check Balance](#3-check-balance)
4. [Pay with Transfer](#-pay-with-transfer)
   - [Initiate Transfer Payment](#1-initiate-transfer-payment)
   - [Hosted Checkout](#2-hosted-checkout)
   - [Get Transfer Payment Status by UUID](#3-get-transfer-payment-status-by-uuid)
   - [Verify Transfer Payment by Merchant Reference](#4-verify-transfer-payment-by-merchant-reference)
   - [Cancel Transfer Payment](#5-cancel-transfer-payment)
   - [List Transfer Payments](#6-list-transfer-payments)
5. [Bank Codes](#-bank-codes)
   - [Get Bank List](#1-get-bank-list)
6. [Webhooks & Callbacks](#-webhooks--callbacks)
   - [Webhook & Callback Events](#1-webhook--callback-events)
   - [Callback Notifications](#2-callback-notifications)
   - [Verifying Signatures](#3-verifying-signatures)
7. [Security Best Practices](#-security-best-practices)

---

## 🔑 Authentication & Base URL

### Base URL
```
https://api.katpay.co/v1
```

### Required Headers
All API requests require authentication using your API keys. Never expose secret keys in client-side code.

| Header | Description | Required |
| :--- | :--- | :--- |
| `Authorization` | `Bearer {secret_key}` — Bearer token with your API secret | Yes |
| `api-key` | `{public_key}` — Your public API key | Yes |
| `Content-Type` | `application/json` — Request payload format | Yes |

#### Authentication Header Examples

##### JavaScript
```javascript
const headers = {
  'Authorization': `Bearer ${apiSecret}`,
  'Content-Type': 'application/json',
  'api-key': apiKey
};
```

##### PHP
```php
$headers = [
    'Authorization: Bearer ' . $apiSecret,
    'Content-Type: application/json',
    'api-key: ' . $apiKey
];
```

##### Python
```python
headers = {
    'Authorization': f'Bearer {api_secret}',
    'Content-Type': 'application/json',
    'api-key': api_key
}
```

---

## 💳 Virtual Accounts

Create static virtual accounts tied to your merchant account to receive customer payments.

### Supported Banks
| Bank Code | Bank Name | Status |
| :--- | :--- | :--- |
| `WEMA` | Wema Bank | Active |
| `TITAN` | Paystack-Titan | Active |

---

### 1. Create Virtual Account
Creates a static virtual account for a customer. Calling this endpoint again for the same customer returns the existing account numbers with `"already_exists": true` (HTTP 200).

* **Endpoint:** `POST /virtual-accounts`
* **Full URL:** `https://api.katpay.co/v1/virtual-accounts`

#### Request Body Parameters
| Parameter | Type | Description | Required |
| :--- | :--- | :--- | :--- |
| `email` | `string` | Customer's email address | Yes |
| `name` | `string` | Customer's full name | Yes |
| `phoneNumber` | `string` | Customer's phone number | Yes |
| `bankCode` | `array` | Array of bank codes (e.g., `["WEMA", "TITAN"]`). KatPay issues account numbers for each specified partner bank. | Yes |
| `merchantID` | `string` | Your Katpay Merchant ID | Yes |
| `bvn` | `string` | Customer's 11-digit Bank Verification Number (must be sent as a string to preserve leading zeros). | Yes |

> ⚠️ **Identity Verification Note:** If an invalid or missing BVN is sent, the request is rejected with HTTP `422`:
> ```json
> {
>   "success": false,
>   "message": "The customer's BVN is required.",
>   "errors": { "bvn": ["The customer's BVN is required."] }
> }
> ```

#### Request Examples

##### JavaScript (Axios)
```javascript
const axios = require('axios');

const apiKey = 'your-public-key';
const apiSecret = 'your-api-secret';
const merchantID = 'your-merchant-id';
const headers = {
  'Authorization': `Bearer ${apiSecret}`,
  'Content-Type': 'application/json',
  'api-key': apiKey
};

const data = {
  email: 'customer@example.com',
  name: 'Sadeeq Doe',
  phoneNumber: '+2349055439338',
  bankCode: ['WEMA', 'TITAN'],
  merchantID: merchantID,
  bvn: '12345678901'
};

axios.post('https://api.katpay.co/v1/virtual-accounts', data, { headers })
  .then(response => {
    console.log('Virtual account created:', response.data);
  })
  .catch(error => {
    console.error('Error:', error.response.data);
  });
```

##### PHP (cURL)
```php
<?php
$apiKey = 'your-public-key';
$apiSecret = 'your-api-secret';
$merchantID = 'your-merchant-id';

$data = [
    'email' => 'customer@example.com',
    'name' => 'Sadeeq Doe',
    'phoneNumber' => '+2349055439338',
    'bankCode' => ['WEMA', 'TITAN'],
    'merchantID' => $merchantID,
    'bvn' => '12345678901'
];

$headers = [
    'Authorization: Bearer ' . $apiSecret,
    'Content-Type: application/json',
    'api-key: ' . $apiKey
];

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, 'https://api.katpay.co/v1/virtual-accounts');
curl_setopt($ch, CURLOPT_POST, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($data));
curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);

$response = curl_exec($ch);
curl_close($ch);

echo $response;
?>
```

##### Python (Requests)
```python
import requests
import json

api_key = 'your-public-key'
api_secret = 'your-api-secret'
merchant_id = 'your-merchant-id'
headers = {
    'Authorization': f'Bearer {api_secret}',
    'Content-Type': 'application/json',
    'api-key': api_key
}

data = {
    'email': 'customer@example.com',
    'name': 'Sadeeq Doe',
    'phoneNumber': '+2349055439338',
    'bankCode': ['WEMA', 'TITAN'],
    'merchantID': merchant_id,
    'bvn': '12345678901'
}

response = requests.post(
    'https://api.katpay.co/v1/virtual-accounts',
    headers=headers,
    data=json.dumps(data)
)

print(response.json())
```

#### Success Response (`201 Created` / `200 OK`)
```json
{
  "success": true,
  "message": "Virtual account created successfully",
  "data": {
    "account_number": "9930012345",
    "account_name": "KATPAY/DOE SADEEQ",
    "bank_name": "Wema Bank",
    "provider_reference": "PSDVA_123456",
    "status": "active",
    "total_received": "0.00",
    "transaction_count": 0,
    "last_transaction_at": null,
    "created_at": "2026-10-14T10:00:00.000000Z",
    "already_exists": false,
    "accounts": [
      {
        "account_number": "9930012345",
        "account_name": "KATPAY/DOE SADEEQ",
        "bank_name": "Wema Bank",
        "provider_reference": "PSDVA_123456",
        "status": "active"
      },
      {
        "account_number": "9881234567",
        "account_name": "KATPAY/DOE SADEEQ",
        "bank_name": "Paystack-Titan",
        "provider_reference": "PSDVA_123457",
        "status": "active"
      }
    ]
  }
}
```

---

## 💸 Payouts & Wallet Balance

Programmatically transfer funds to bank accounts in Nigeria. Payouts must be enabled in your Katpay Merchant Settings.

### 1. Create Payout
Send funds to a target Nigerian bank account.

* **Endpoint:** `POST /payouts`
* **Full URL:** `https://api.katpay.co/v1/payouts`

#### Request Body Parameters
| Parameter | Type | Description | Required |
| :--- | :--- | :--- | :--- |
| `amount` | `number` | Amount to transfer in Naira (min: 100) | Yes |
| `bank_code` | `string` | 6-digit bank code from the [Bank Codes](#-bank-codes) endpoint (e.g. `000013` for GTBank) | Yes |
| `account_number` | `string` | 10-digit NUBAN account number | Yes |
| `account_name` | `string` | Name of the account holder. Verified against bank records; mismatched payouts are rejected and refunded. | Yes |
| `description` | `string` | Narration for the payout transaction | No |
| `reference` | `string` | Unique reference string (letters, numbers, `-`, `_`, max 100). Reusing a reference returns HTTP 409. Auto-generated if omitted. | No |

#### Request Examples

##### JavaScript (Fetch)
```javascript
const payout = await fetch('https://api.katpay.co/v1/payouts', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_SECRET_KEY',
    'Content-Type': 'application/json',
    'api-key': 'YOUR_PUBLIC_KEY'
  },
  body: JSON.stringify({
    amount: 5000,
    bank_code: "000013",
    account_number: "0123456789",
    account_name: "Sadeeq Doe",
    description: "Salary Payment",
    reference: "PAY_123456789"
  })
});

const data = await payout.json();
console.log(data);
```

##### PHP (cURL)
```php
<?php
$curl = curl_init();

curl_setopt_array($curl, [
  CURLOPT_URL => "https://api.katpay.co/v1/payouts",
  CURLOPT_RETURNTRANSFER => true,
  CURLOPT_POST => true,
  CURLOPT_POSTFIELDS => json_encode([
    "amount" => 5000,
    "bank_code" => "000013",
    "account_number" => "0123456789",
    "account_name" => "Sadeeq Doe",
    "description" => "Salary Payment",
    "reference" => "PAY_123456789"
  ]),
  CURLOPT_HTTPHEADER => [
    "Authorization: Bearer YOUR_SECRET_KEY",
    "Content-Type: application/json",
    "api-key: YOUR_PUBLIC_KEY"
  ],
]);

$response = curl_exec($curl);
$err = curl_error($curl);

curl_close($curl);

if ($err) {
  echo "cURL Error: " . $err;
} else {
  echo $response;
}
?>
```

#### Response Handling & Status Codes
Amount plus payout fee (`total_deducted`) is reserved immediately upon acceptance. Most requests return `202 Accepted` with status `processing`.

* **`200 OK`**: Payout completed immediately (`status: completed`).
* **`202 Accepted`**: Payout accepted and processing — check status using `GET /v1/payouts/{reference}`.
* **`400 Bad Request`**: Payout failed and balance refunded, or insufficient withdrawable balance.
* **`403 Forbidden`**: Payouts not enabled or locked for account.
* **`409 Conflict`**: Duplicate reference reused.
* **`422 Validation Error`**: Invalid input data.
* **`429 Too Many Requests`**: Exceeded rate limit (max 5 payouts per minute).
* **`503 Service Unavailable`**: Payouts temporarily paused.

##### Response Example — `202 Accepted (Processing)`
```json
{
  "success": true,
  "status": "processing",
  "message": "Payout is processing. The final status will be confirmed shortly.",
  "data": {
    "reference": "PAY_123456789",
    "status": "processing"
  }
}
```

##### Response Example — `400 Bad Request (Failed & Refunded)`
```json
{
  "success": false,
  "message": "Payout could not be processed. Your balance has been refunded.",
  "data": {
    "reference": "PAY_123456789",
    "status": "failed"
  }
}
```

---

### 2. Get Payout Status
Query the state of a payout transaction by reference (`processing`, `completed`, or `failed`).

* **Endpoint:** `GET /payouts/{reference}`
* **Full URL:** `https://api.katpay.co/v1/payouts/{reference}`

#### Response Example (`200 OK`)
```json
{
  "success": true,
  "data": {
    "reference": "PAY_123456789",
    "status": "completed",
    "amount": 5000,
    "fee": 20,
    "total_deducted": 5020,
    "currency": "NGN",
    "recipient": {
      "bank_code": "000013",
      "bank_name": "N/A",
      "account_number": "******6789",
      "account_name": "SADEEQ DOE"
    },
    "description": "Salary Payment",
    "created_at": "2026-10-14T10:00:00+01:00",
    "processed_at": "2026-10-14T10:00:01+01:00",
    "completed_at": "2026-10-14T10:02:30+01:00"
  }
}
```

---

### 3. Check Balance
Retrieves current wallet balance. Use `available` balance to verify funds before triggering payouts.

* **Endpoint:** `GET /balance`
* **Full URL:** `https://api.katpay.co/v1/balance`

#### Response Example (`200 OK`)
```json
{
  "success": true,
  "data": {
    "merchant_id": "MERCH-123456",
    "business_name": "My Business",
    "balance": 150000,
    "frozen": 0,
    "available": 42500,
    "currency": "NGN",
    "payout_enabled": true,
    "payout_locked": false,
    "timestamp": "2026-10-14T10:00:00+01:00"
  }
}
```

---

## 🔄 Pay with Transfer

Accept one-time bank transfer payments from customers. Each request creates a unique payment order with bank transfer details and a hosted checkout URL.

### How It Works
1. Call `POST /v1/transfer-payments` with transaction & customer details.
2. Receive dedicated bank account details and `checkout_url`.
3. Direct customer to `checkout_url` or display `payment_account` in custom UI.
4. Customer performs bank transfer.
5. KatPay confirms transfer and dispatches callback to `callback_url` & webhook URL.
6. Verify signature and mark order as complete.

---

### 1. Initiate Transfer Payment
* **Endpoint:** `POST /transfer-payments`
* **Full URL:** `https://api.katpay.co/v1/transfer-payments`

#### Request Parameters
| Parameter | Type | Description | Required |
| :--- | :--- | :--- | :--- |
| `amount` | `number` | Amount in NGN (min: 100, max: 10,000,000) | Yes |
| `customer_name` | `string` | Customer's full name | Yes |
| `customer_email` | `string` | Customer's email address | Yes |
| `callback_url` | `string` | HTTPS URL to receive webhook/callback payment confirmation | Yes |
| `merchant_reference` | `string` | Unique order/transaction reference | Yes |
| `customer_phone` | `string` | Customer's phone number | No |
| `description` | `string` | Payment description (max 500 chars) | No |
| `success_url` | `string` | URL to redirect customer after successful checkout payment | No |
| `metadata` | `object` | Custom key-value dictionary returned in callbacks | No |
| `expires_in` | `integer` | Expiration time in minutes (default: 30, range: 15 to 480) | No |
| `currency` | `string` | Currency code (default: `NGN`) | No |

#### Request Examples

##### JavaScript (Node.js / Axios)
```javascript
const axios = require('axios');
const SECRET_KEY = 'live_your_secret_key';

const response = await axios.post(
  'https://api.katpay.co/v1/transfer-payments',
  {
    amount: 5000,
    customer_name: 'Sadeeq dev',
    customer_email: 'Sadeeq@example.com',
    customer_phone: '+2349055439338',
    callback_url: 'https://yourdomainsite.com/payment/callback',
    merchant_reference: 'ORDER_001',
    description: 'Payment for Order #001',
    metadata: {
      order_id: '001',
      product: 'Premium Plan'
    },
    expires_in: 30
  },
  {
    headers: {
      'Authorization': `Bearer ${SECRET_KEY}`,
      'Content-Type': 'application/json'
    }
  }
);

console.log('Payment account:', response.data.data.payment_account);
```

##### PHP
```php
<?php
$secretKey = 'your_secret_key';

$curl = curl_init();
curl_setopt_array($curl, [
    CURLOPT_URL => 'https://api.katpay.co/v1/transfer-payments',
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_POST => true,
    CURLOPT_POSTFIELDS => json_encode([
        'amount' => 5000,
        'customer_name' => 'sadeeq dev',
        'customer_email' => 'kathost@gmail.com',
        'customer_phone' => '+2349055439338',
        'callback_url' => 'https://yoursite.com/payment/callback',
        'merchant_reference' => 'ORDER_001',
        'description' => 'Payment for Order #001',
        'metadata' => [
            'order_id' => '001',
            'product' => 'Premium Plan'
        ],
        'expires_in' => 30
    ]),
    CURLOPT_HTTPHEADER => [
        'Authorization: Bearer ' . $secretKey,
        'Content-Type: application/json'
    ]
]);

$response = curl_exec($curl);
curl_close($curl);

$result = json_decode($response, true);
?>
```

##### Python
```python
import requests

SECRET_KEY = 'your_secret_key'

response = requests.post(
    'https://api.katpay.co/v1/transfer-payments',
    json={
        'amount': 5000,
        'customer_name': 'sadeeq dev',
        'customer_email': 'kathost@gmail.com',
        'customer_phone': '+2349055439338',
        'callback_url': 'https://yoursite.com/payment/callback',
        'merchant_reference': 'ORDER_001',
        'description': 'Payment for Order #001',
        'metadata': {
            'order_id': '001',
            'product': 'Premium Plan'
        },
        'expires_in': 30
    },
    headers={
        'Authorization': f'Bearer {SECRET_KEY}',
        'Content-Type': 'application/json'
    }
)

result = response.json()
```

#### Success Response (`201 Created`)
```json
{
  "success": true,
  "message": "Transfer payment initiated successfully",
  "data": {
    "uuid": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
    "merchant_reference": "ORDER_001",
    "internal_reference": "TRF_ABCDEFGHIJ1234567890_1711000000",
    "status": "pending",
    "amount": 5000.00,
    "fee_amount": 75.00,
    "net_amount": 4925.00,
    "currency": "NGN",
    "payment_account": {
      "account_number": "9887985018",
      "account_name": "PAYSTACK CHECKOUT",
      "bank_name": "Paystack-Titan"
    },
    "checkout_url": "https://checkout.katpay.co/pay/transfer/a1b2c3d4-e5f6-7890-abcd-ef1234567890",
    "customer": {
      "name": "sadeeq dev",
      "email": "kathost@gmail.com"
    },
    "expires_at": "2026-03-21T10:30:00+00:00",
    "created_at": "2026-03-21T10:00:00+00:00"
  }
}
```

---

### 2. Hosted Checkout
Redirect customers directly to `checkout_url` (`https://checkout.katpay.co/pay/transfer/{uuid}`). Katpay hosts the checkout page featuring:
* One-click account number copying.
* Countdown timer for payment expiry.
* Real-time payment detection (~12s active check, 4s polling fallback).
* "I have made this transfer" instant verification button.
* Auto-redirect to `success_url` upon completion.

---

### 3. Get Transfer Payment Status by UUID
Poll transfer status using the payment UUID.

* **Endpoint:** `GET /transfer-payments/{uuid}`
* **Full URL:** `https://api.katpay.co/v1/transfer-payments/{uuid}`

#### Possible Payment Statuses
| Status | Description |
| :--- | :--- |
| `pending` | Waiting for customer transfer |
| `processing` | Transfer detected; payment being confirmed |
| `completed` | Payment confirmed; merchant wallet credited |
| `expired` | Window timed out with no transfer received |
| `cancelled` | Payment cancelled by merchant |
| `failed` | Error occurred during payment processing |

---

### 4. Verify Transfer Payment by Merchant Reference
Recommended server-side verification endpoint to confirm payment status using your own `merchant_reference`.

* **Endpoint:** `GET /transfer-payments/verify/{merchant_reference}`
* **Full URL:** `https://api.katpay.co/v1/transfer-payments/verify/{merchant_reference}`

#### Response Key Fields
* `amount`: Expected order amount.
* `amount_received`: Actual transferred amount.
* `status`: Current status (`completed`, `pending`, etc.).

---

### 5. Cancel Transfer Payment
Cancels an active transfer payment (only permitted if status is `pending`).

* **Endpoint:** `POST /transfer-payments/{uuid}/cancel`
* **Full URL:** `https://api.katpay.co/v1/transfer-payments/{uuid}/cancel`

---

### 6. List Transfer Payments
Retrieve transfer payment records with pagination and status filters.

* **Endpoint:** `GET /transfer-payments`
* **Full URL:** `https://api.katpay.co/v1/transfer-payments`

#### Query Parameters
| Parameter | Type | Description |
| :--- | :--- | :--- |
| `status` | `string` | Filter by status (`pending`, `completed`, `expired`, `cancelled`) |
| `merchant_reference` | `string` | Filter by merchant reference |
| `per_page` | `integer` | Records per page (default: 20, max: 100) |

---

## 🏦 Bank Codes

Fetch bank codes needed for conducting payouts.

### 1. Get Bank List
Returns all supported Nigerian bank names and 6-digit bank codes.

* **Endpoint:** `GET /api/bank-list`
* **Full URL:** `https://api.katpay.co/api/bank-list`

#### Response Fields
| Field | Type | Description |
| :--- | :--- | :--- |
| `bankCode` | `string` | Unique bank code (e.g. `000013` for GTBank, `000014` for Access Bank) |
| `bankName` | `string` | Official name of the financial institution |

---

## 🔔 Webhooks & Callbacks

### 1. Webhook & Callback Events
| Event Type | Description |
| :--- | :--- |
| `virtual_account.payment_received` | Fired when funds arrive into a virtual account |
| `transfer_payment.completed` | Fired when a pay-with-transfer payment is confirmed |

> Note: Payout outcomes are checked via `GET /v1/payouts/{reference}`, not via webhooks.

---

### 2. Callback Notifications
When a pay-with-transfer transaction completes, Katpay posts a payload to both your `callback_url` and dashboard Webhook URL.

#### Headers Sent with Callback
| Header | Description |
| :--- | :--- |
| `X-KatPay-Signature` | HMAC-SHA256 signature string |
| `X-KatPay-Timestamp` | Unix timestamp of dispatch |
| `X-KatPay-Event` | Event type (`transfer_payment.completed`) |
| `X-KatPay-Delivery-Id` | Unique delivery ID for idempotency tracking |

#### Callback Payload Example
```json
{
  "event": "transfer_payment.completed",
  "data": {
    "uuid": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
    "merchant_reference": "ORDER_001",
    "internal_reference": "TRF_ABCDEFGHIJ1234567890_1711000000",
    "status": "completed",
    "amount": 5000.00,
    "amount_received": 5000.00,
    "fee_amount": 75.00,
    "net_amount": 4925.00,
    "currency": "NGN",
    "customer_name": "sadeeq dev",
    "customer_email": "kathost@gmail.com",
    "description": "Payment for Order #001",
    "metadata": {
      "order_id": "001",
      "product": "Premium Plan"
    },
    "paid_at": "2026-03-21T10:05:00+00:00",
    "completed_at": "2026-03-21T10:05:01+00:00",
    "payer_account_name": "sadeeq dev",
    "payer_bank_name": "GTBank"
  }
}
```

---

### 3. Verifying Signatures

Always verify the signature before processing webhooks/callbacks:
$$\text{Expected Signature} = \text{HMAC-SHA256}(\text{timestamp} + "." + \text{raw\_payload}, \text{secret\_key})$$

#### Node.js (Express)
```javascript
const express = require('express');
const crypto = require('crypto');

const app = express();
const SECRET_KEY = 'your_secret_key';

app.post('/payment/callback', express.raw({ type: 'application/json' }), (req, res) => {
  const signature = req.headers['x-katpay-signature'] || req.headers['x-katpay-signature'];
  const timestamp = req.headers['x-katpay-timestamp'] || req.headers['x-katpay-timestamp'];
  const payload = req.body.toString();

  // 1. Verify signature
  const expectedSignature = crypto
    .createHmac('sha256', SECRET_KEY)
    .update(`${timestamp}.${payload}`)
    .digest('hex');

  if (!crypto.timingSafeEqual(Buffer.from(signature), Buffer.from(expectedSignature))) {
    return res.status(401).json({ error: 'Invalid signature' });
  }

  // 2. Reject if older than 5 minutes (prevent replay attack)
  if (Math.abs(Date.now() / 1000 - parseInt(timestamp)) > 300) {
    return res.status(401).json({ error: 'Timestamp too old' });
  }

  // 3. Process payload
  const data = JSON.parse(payload);
  if (data.event === 'transfer_payment.completed' || data.event_type === 'virtual_account.payment_received') {
    console.log('Valid notification received:', data);
  }

  res.status(200).json({ received: true });
});

app.listen(3000);
```

#### PHP
```php
<?php
$SECRET_KEY = 'your_secret_key';

$payload = file_get_contents('php://input');
$signature = $_SERVER['HTTP_X_KATPAY_SIGNATURE'] ?? $_SERVER['HTTP_X_KATPAY_SIGNATURE'] ?? '';
$timestamp = $_SERVER['HTTP_X_KATPAY_TIMESTAMP'] ?? $_SERVER['HTTP_X_KATPAY_TIMESTAMP'] ?? '';

// 1. Verify signature
$expectedSignature = hash_hmac('sha256', $timestamp . '.' . $payload, $SECRET_KEY);

if (!hash_equals($expectedSignature, $signature)) {
    http_response_code(401);
    echo json_encode(['error' => 'Invalid signature']);
    exit;
}

// 2. Prevent replay attacks (>5 minutes)
if (abs(time() - (int)$timestamp) > 300) {
    http_response_code(401);
    echo json_encode(['error' => 'Timestamp too old']);
    exit;
}

http_response_code(200);
echo json_encode(['received' => true]);
?>
```

#### Python (Flask)
```python
import hmac
import hashlib
import time
import json
from flask import Flask, request, jsonify

app = Flask(__name__)
SECRET_KEY = 'your_secret_key'

@app.route('/payment/callback', methods=['POST'])
def payment_callback():
    signature = request.headers.get('X-KatPay-Signature', request.headers.get('X-Katpay-Signature', ''))
    timestamp = request.headers.get('X-KatPay-Timestamp', request.headers.get('X-Katpay-Timestamp', ''))
    payload = request.get_data(as_text=True)

    # 1. Verify signature
    expected = hmac.new(
        SECRET_KEY.encode(),
        f'{timestamp}.{payload}'.encode(),
        hashlib.sha256
    ).hexdigest()

    if not hmac.compare_digest(expected, signature):
        return jsonify({'error': 'Invalid signature'}), 401

    # 2. Prevent replay attacks
    if abs(time.time() - int(timestamp)) > 300:
        return jsonify({'error': 'Timestamp too old'}), 401

    return jsonify({'received': True}), 200
```

---

## 🔒 Security Best Practices

1. **Protect Your API Keys**: Always store API keys in environment variables or secure backend vaults. Never leak secret keys in client applications, HTML files, or public git repositories.
2. **Enforce HTTPS**: All incoming and outgoing requests must use TLS/HTTPS encryption.
3. **Verify Webhook Signatures**: Always validate the `X-KatPay-Signature` / `X-Katpay-Signature` HMAC SHA-256 header before executing business logic or updating databases.
4. **Validate Timestamps**: Discard webhook payloads older than 5 minutes (300 seconds) to defend against replay attacks.
5. **Idempotency**: Use `X-KatPay-Delivery-Id` or `merchant_reference` to prevent processing duplicate event dispatches.
