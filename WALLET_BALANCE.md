# 💳 Wallet Balance API Documentation

Check the live wallet balance of the API account the key belongs to. This is the exact balance every paid endpoint charges against, so you can use it to guard a batch job before it starts, or to alert yourself before you run dry. The call is free — nothing is deducted and no history row is written.

---

## 🚀 Endpoint
* **URL:** `https://dataverify.com.ng/api/developers/balance.php`
* **Method:** `GET` or `POST`

---

## 🔑 Authentication
Unlike the other endpoints, this one prefers your key in a header so it never lands in server logs or browser history. Any one of the three options works (the first is recommended):

| Method | How to send | Notes |
| :--- | :--- | :--- |
| **Bearer token** | `Authorization: Bearer YOUR_API_KEY` | Recommended. Works with `GET` or `POST`. |
| **API key header** | `X-API-Key: YOUR_API_KEY` | Works with `GET` or `POST`. |
| **JSON body** | `{"api_key": "YOUR_API_KEY"}` | `POST` only. Matches the style of other endpoints. |

> ⚠️ **Note:** The key is never accepted in the query string — sending `?api_key=` returns `400 Bad Request`.

### 🚦 Rate Limits
* **Per IP:** 60 requests per minute
* **Per Key:** 300 requests per 5 minutes

---

## ⚙️ Optional Parameters

| Parameter | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `include_prices` | boolean | No | Defaults to `false`. Set to `true` (or `1` in query string) to also get your price for each service, so you can work out how many requests your balance covers. Accepted in the query string (`?include_prices=1`) or in the JSON body. |

---

## 📦 Response Fields

| Field | Type | Description |
| :--- | :--- | :--- |
| `balance` | number | Your current wallet balance in Naira (NGN). |
| `user_balance` | number | Alias of `balance`, containing the same value. |
| `currency` | string | Always `NGN`. |
| `email` | string | Partly masked account email, so you can confirm which account the key belongs to. |
| `developer_status` | string | Your account tier: `starter` or `premium`. |
| `low_balance_threshold` | number \| null | The level at which low-balance alerts are sent to you. `null` if not set. |
| `last_transaction` | string \| null | Timestamp of the last movement on the wallet. `null` if there has been none. |

---

## 🚦 HTTP Status Codes

* **200 OK** - Success - Balance returned.
* **400 Bad Request** - API key was sent in the query string.
* **401 Unauthorized** - Missing or invalid API key.
* **405 Method Not Allowed** - Use `GET` or `POST`.
* **429 Too Many Requests** - Rate limit exceeded, see `Retry-After` header.

---

## 📖 API Request/Response Examples

### 1. Basic Balance Check (No Prices)
#### Request (GET)
```http
GET https://dataverify.com.ng/api/developers/balance.php
Authorization: Bearer YOUR_API_KEY_HERE
```

#### Request (POST)
```http
POST https://dataverify.com.ng/api/developers/balance.php
Content-Type: application/json

{
    "api_key": "YOUR_API_KEY_HERE"
}
```

#### Success Response
```json
{
    "status": true,
    "message": "Balance retrieved successfully",
    "email": "joh***@gmail.com",
    "developer_status": "premium",
    "balance": 52633.96,
    "user_balance": 52633.96,
    "currency": "NGN",
    "low_balance_threshold": 5000,
    "last_transaction": "2026-08-11 19:42:03",
    "timestamp": "2026-08-12 00:32:46"
}
```

---

### 2. Balance Check (With Prices)
#### Request (GET)
```http
GET https://dataverify.com.ng/api/developers/balance.php?include_prices=1
Authorization: Bearer YOUR_API_KEY_HERE
```

#### Success Response
```json
{
    "status": true,
    "message": "Balance retrieved successfully",
    "balance": 9775.09,
    "user_balance": 9775.09,
    "currency": "NGN",
    "prices": {
        "nin_slip": 130,
        "nin_slip_demographic": 200,
        "nin_slip_demographic_regular": 200,
        "vnin_slip": 130,
        "bvn_slip": 200,
        "bank_account_verify": 50,
        "ipe_clearance": 300,
        "nin_validation": 450,
        "personalization": 120,
        "_meta": {
            "currency": "NGN",
            "custom_pricing": true,
            "null_means": "Not configured for your account - contact support before calling that service.",
            "nin_slip_covers": "premium, standard, regular and basic slips, by NIN or by phone",
            "bvn_slip_covers": "bvn_premium and bvn_standard"
        }
    }
}
```

#### 💡 Notes on the Price List:
* These are **YOUR** prices — the exact figures deducted when you call each service, not a public price list.
* `"nin_slip"` is one price for every NIN slip tier (premium, standard, regular, basic) whether you look up by NIN or by phone.
* A `null` price means that service is not configured on your account yet and will be refused until support sets it. IPE, validation, and personalization always return a figure because they fall back to the standard rate.

---

### 3. Error Responses

#### Missing API Key (401 Unauthorized)
```json
{
    "status": false,
    "message": "Missing API key. Send it as \"Authorization: Bearer YOUR_API_KEY\", an X-API-Key header, or api_key in a JSON body."
}
```

#### Invalid API Key (401 Unauthorized)
```json
{
    "status": false,
    "message": "Invalid API key"
}
```

#### API Key Sent in the URL (400 Bad Request)
```json
{
    "status": false,
    "message": "Do not send the API key in the query string. Use the Authorization: Bearer header, the X-API-Key header, or a JSON body."
}
```

#### Rate Limit Exceeded (429 Too Many Requests)
```json
{
    "status": false,
    "message": "Rate limit exceeded. Please try again later."
}
```

---

## 💻 Code Examples

### 🐘 PHP
```php
<?php
$apiKey = 'YOUR_API_KEY_HERE';

$ch = curl_init('https://dataverify.com.ng/api/developers/balance.php');
curl_setopt_array($ch, [
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_HTTPHEADER     => ['Authorization: Bearer ' . $apiKey],
    CURLOPT_TIMEOUT        => 30
]);
$response = curl_exec($ch);
$httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
curl_close($ch);

$result = json_decode($response, true);

if ($httpCode === 200 && !empty($result['status'])) {
    echo "Balance: NGN " . number_format($result['balance'], 2) . "\n";

    // Guard a batch before you start spending
    $costPerCall = 100;
    $pending     = 40;
    if ($result['balance'] < $costPerCall * $pending) {
        echo "Not enough balance for {$pending} requests -- top up first.\n";
    }
} else {
    echo "Error: " . ($result['message'] ?? 'Unknown error') . "\n";
}
```

### 🟨 JavaScript (Node.js / Browser)
```javascript
const apiKey = 'YOUR_API_KEY_HERE';

const res = await fetch('https://dataverify.com.ng/api/developers/balance.php', {
    headers: { 'Authorization': `Bearer ${apiKey}` }
});

const data = await res.json();

if (data.status) {
    console.log(`Balance: NGN ${data.balance.toFixed(2)}`);
} else {
    console.error(data.message);
}
```

### 🐍 Python
```python
import requests

api_key = 'YOUR_API_KEY_HERE'

response = requests.get(
    'https://dataverify.com.ng/api/developers/balance.php',
    headers={'Authorization': f'Bearer {api_key}'},
    timeout=30
)

data = response.json()

if data.get('status'):
    print(f"Balance: NGN {data['balance']:,.2f}")
else:
    print(f"Error: {data.get('message')}")
```
