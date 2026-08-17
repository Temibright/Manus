# 📘 DataVerify - IPE Clearance & NIN Validation API Documentation

This document provides complete, structured documentation for the **IPE Clearance** and **NIN Validation** services offered by DataVerify, including all request endpoints, parameters, status checks, and response examples across all processing stages and HTTP status codes.

---

## 🔑 Base URL & Authentication

*   **Base URL:** `https://dataverify.com.ng`
*   **Content-Type:** `application/json`
*   **Authentication Methods:**
    *   **JSON Body:** Include `"api_key": "YOUR_API_KEY"` in the request body (Recommended for POST requests).
    *   **Authorization Header:** `Authorization: Bearer YOUR_API_KEY`
    *   **X-API-Key Header:** `X-API-Key: YOUR_API_KEY`

---

## 📋 Table of Contents

1. [IPE Clearance API](#1-ipe-clearance-api)
    * [1.1 Submit IPE Clearance Request](#11-submit-ipe-clearance-request)
    * [1.2 Check IPE Clearance Status](#12-check-ipe-clearance-status)
2. [NIN Validation API](#2-nin-validation-api)
    * [2.1 Submit NIN Validation Request](#21-submit-nin-validation-request)
    * [2.2 Check NIN Validation Status](#22-check-nin-validation-status)
3. [Summary of Request Statuses & HTTP Error Codes](#3-summary-of-request-statuses--http-error-codes)

---

## 1. 📂 IPE Clearance API

The **IPE Clearance API** allows developers to submit an IPE Clearance request using an alphanumeric tracking ID and poll for status/results. Requests are processed asynchronously.

---

### 1.1 Submit IPE Clearance Request

Submits an IPE Clearance request. The account balance is deducted at submission time.

*   **Endpoint:** `POST /api/developers/ipe.php` (or `/api/developers/ipe2.php`)
*   **Headers:**
    *   `Content-Type: application/json`

#### Request Body Parameters
| Parameter | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `api_key` | `string` | **Yes** | Your API authentication key (max 50 chars). |
| `trackingID` | `string` | **Yes** | Alphanumeric tracking ID to clear (max 20 chars). |

#### Request Example (cURL / JSON)
```json
POST /api/developers/ipe.php HTTP/1.1
Host: dataverify.com.ng
Content-Type: application/json

{
    "api_key": "YOUR_API_KEY_HERE",
    "trackingID": "ABC12345XYZ"
}
```

#### Responses for Submission

##### 🟢 200 OK — Request Submitted (Pending)
```json
HTTP/1.1 200 OK
Content-Type: application/json

{
    "status": true,
    "message": "IPE Clearance request submitted successfully. Your request is being processed.",
    "trackingID": "ABC12345XYZ",
    "transaction_id": "a1b2c3d4e5f6a7b8c9d0e1f2a3",
    "price": 1500,
    "balance_before": 5000,
    "balance_after": 3500,
    "request_status": "pending"
}
```

##### 🔴 400 Bad Request — Missing Parameters
```json
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
    "message": "api_key and trackingID are required."
}
```

##### 🔴 400 Bad Request — Invalid Format
```json
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
    "message": "Invalid tracking ID format."
}
```

##### 🔴 400 Bad Request — Insufficient Balance
```json
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
    "status": false,
    "message": "Insufficient balance"
}
```

##### 🟠 409 Conflict — Duplicate Request
```json
HTTP/1.1 409 Conflict
Content-Type: application/json

{
    "status": false,
    "message": "This record already exists as pending"
}
```

##### 🔒 401 Unauthorized — Invalid API Key
```json
HTTP/1.1 401 Unauthorized
Content-Type: application/json

{
    "status": false,
    "message": "Invalid API key"
}
```

##### ⛔ 403 Forbidden — Blocked IP
```json
HTTP/1.1 403 Forbidden
Content-Type: application/json

{
    "message": "Your IP address is blocked due to suspicious activity."
}
```

##### ⏳ 429 Too Many Requests — Rate Limited
```json
HTTP/1.1 429 Too Many Requests
Content-Type: application/json

{
    "message": "Rate limit exceeded. Please try again later."
}
```

---

### 1.2 Check IPE Clearance Status

Polls for the processing outcome of a previously submitted IPE Clearance request. Status checks are free and do not deduct balance.

*   **Endpoint:** `POST /api/developers/ipe_status.php` (or `/api/developers/ipe_status2.php`)
*   **Headers:**
    *   `Content-Type: application/json`

#### Request Body Parameters
| Parameter | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `api_key` | `string` | **Yes** | Your API authentication key. |
| `trackingID` | `string` | **Yes** | The tracking ID submitted during request creation. |

#### Request Example (cURL / JSON)
```json
POST /api/developers/ipe_status.php HTTP/1.1
Host: dataverify.com.ng
Content-Type: application/json

{
    "api_key": "YOUR_API_KEY_HERE",
    "trackingID": "ABC12345XYZ"
}
```

#### Responses for Status Check

##### 🟢 200 OK — Request Completed Successfully
```json
HTTP/1.1 200 OK
Content-Type: application/json

{
    "status": true,
    "message": "IPE Clearance completed successfully.",
    "request_status": "completed",
    "trackingID": "ABC12345XYZ",
    "newNIN": "12345678901",
    "newTrackingID": "NID987654321",
    "date": "2026-03-05 14:30:00"
}
```

##### 🟡 200 OK — Request Pending
```json
HTTP/1.1 200 OK
Content-Type: application/json

{
    "status": true,
    "message": "Your IPE Clearance request is currently pending. Please check back later.",
    "request_status": "pending",
    "trackingID": "ABC12345XYZ"
}
```

##### 🔵 200 OK — Request Processing / Inprogress
```json
HTTP/1.1 200 OK
Content-Type: application/json

{
    "status": true,
    "message": "Your IPE Clearance request is currently processing. Please check back later.",
    "request_status": "processing",
    "trackingID": "ABC12345XYZ"
}
```

*(Note: `request_status` may also return `"inprogress"` depending on internal state).*

##### 🔴 200 OK — Request Failed / Rejected
```json
HTTP/1.1 200 OK
Content-Type: application/json

{
    "status": false,
    "message": "Your IPE Clearance request has failed.",
    "request_status": "failed",
    "trackingID": "ABC12345XYZ",
    "error_detail": "Insufficient fingerprint data on file"
}
```
*(Other possible failure values for `request_status`: `"blocked"`, `"incompleted"`, `"insufficient_fingerprint"`, or `"abis"`).*

##### ❓ 404 Not Found — Record Does Not Exist
```json
HTTP/1.1 404 Not Found
Content-Type: application/json

{
    "status": false,
    "message": "This tracking ID does not exist on our server."
}
```

##### 🔴 400 Bad Request — Missing Parameters
```json
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
    "message": "Missing required parameters: api_key and trackingID"
}
```

---

#### Code Examples: IPE Clearance Request & Status Check

<details>
<summary><b>PHP Example</b></summary>

```php
<?php
$apiKey = "YOUR_API_KEY_HERE";
$trackingID = "ABC12345XYZ";

// 1. Submit IPE Clearance Request
$submitUrl = 'https://dataverify.com.ng/api/developers/ipe.php';
$payload = json_encode([
    'api_key'    => $apiKey,
    'trackingID' => $trackingID
]);

$ch = curl_init($submitUrl);
curl_setopt_array($ch, [
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_POST           => true,
    CURLOPT_POSTFIELDS     => $payload,
    CURLOPT_HTTPHEADER     => ['Content-Type: application/json']
]);

$response = curl_exec($ch);
$httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
curl_close($ch);

$result = json_decode($response, true);

if ($httpCode === 200 && ($result['status'] ?? false) === true) {
    echo "Request submitted successfully! Status: " . $result['request_status'] . "\n";
} else {
    echo "Submission failed ({$httpCode}): " . ($result['message'] ?? 'Error') . "\n";
    exit;
}

// 2. Poll Status Check
$statusUrl = 'https://dataverify.com.ng/api/developers/ipe_status.php';

$ch = curl_init($statusUrl);
curl_setopt_array($ch, [
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_POST           => true,
    CURLOPT_POSTFIELDS     => $payload,
    CURLOPT_HTTPHEADER     => ['Content-Type: application/json']
]);

$statusResponse = curl_exec($ch);
$statusHttpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
curl_close($ch);

$statusResult = json_decode($statusResponse, true);

switch ($statusResult['request_status'] ?? '') {
    case 'completed':
        echo "Clearance Completed!\n";
        echo "New NIN: " . ($statusResult['newNIN'] ?? 'N/A') . "\n";
        echo "New Tracking ID: " . ($statusResult['newTrackingID'] ?? 'N/A') . "\n";
        break;
    case 'pending':
    case 'processing':
    case 'inprogress':
        echo "Status is currently: " . $statusResult['request_status'] . ". Poll again shortly.\n";
        break;
    case 'failed':
    case 'blocked':
    case 'incompleted':
    case 'insufficient_fingerprint':
    case 'abis':
        echo "Request failed: " . ($statusResult['error_detail'] ?? 'Unknown failure') . "\n";
        break;
    default:
        if ($statusHttpCode === 404) {
            echo "404 Not Found: Tracking ID does not exist.\n";
        } else {
            echo "Error ({$statusHttpCode}): " . ($statusResult['message'] ?? 'Unknown');
        }
}
?>
```
</details>

<details>
<summary><b>JavaScript / Node.js Example</b></summary>

```javascript
const apiKey = 'YOUR_API_KEY_HERE';
const trackingID = 'ABC12345XYZ';

async function submitAndCheckIPE() {
    // 1. Submit Request
    const submitRes = await fetch('https://dataverify.com.ng/api/developers/ipe.php', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ api_key: apiKey, trackingID })
    });
    const submitData = await submitRes.json();
    console.log('Submission Response:', submitData);

    if (!submitRes.ok || !submitData.status) {
        console.error('Submission Failed:', submitData.message);
        return;
    }

    // 2. Poll Status Check
    const statusRes = await fetch('https://dataverify.com.ng/api/developers/ipe_status.php', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ api_key: apiKey, trackingID })
    });
    const statusData = await statusRes.json();
    console.log('Status Check Response:', statusData);

    switch (statusData.request_status) {
        case 'completed':
            console.log(`Completed! New NIN: ${statusData.newNIN}, New Tracking ID: ${statusData.newTrackingID}`);
            break;
        case 'pending':
        case 'processing':
        case 'inprogress':
            console.log(`Current Status: ${statusData.request_status}. Please poll again.`);
            break;
        default:
            console.error(`Failed/Error: ${statusData.error_detail || statusData.message}`);
    }
}

submitAndCheckIPE();
```
</details>

<details>
<summary><b>Python Example</b></summary>

```python
import requests

api_key = 'YOUR_API_KEY_HERE'
tracking_id = 'ABC12345XYZ'

# 1. Submit IPE Clearance Request
submit_url = 'https://dataverify.com.ng/api/developers/ipe.php'
payload = {'api_key': api_key, 'trackingID': tracking_id}

response = requests.post(submit_url, json=payload, timeout=30)
submit_data = response.json()

print(f"Submit HTTP {response.status_code}:", submit_data)

if response.status_code == 200 and submit_data.get('status'):
    # 2. Check Status
    status_url = 'https://dataverify.com.ng/api/developers/ipe_status.php'
    status_resp = requests.post(status_url, json=payload, timeout=30)
    status_data = status_resp.json()

    print(f"Status HTTP {status_resp.status_code}:", status_data)
    req_status = status_data.get('request_status')

    if req_status == 'completed':
        print(f"Completed! New NIN: {status_data.get('newNIN')}")
    elif req_status in ['pending', 'processing', 'inprogress']:
        print(f"Request is {req_status}. Poll again later.")
    else:
        print("Failed or Not Found:", status_data.get('message') or status_data.get('error_detail'))
```
</details>

---

## 2. 📂 NIN Validation API

The **NIN Validation API** allows developers to submit a National Identification Number (NIN) for validation and poll for validation results.

---

### 2.1 Submit NIN Validation Request

Submits a NIN for validation processing. Balance is deducted upon submission.

*   **Endpoint:** `POST /api/developers/validation.php`
*   **Headers:**
    *   `Content-Type: application/json`

#### Request Body Parameters
| Parameter | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `api_key` | `string` | **Yes** | Your API authentication key (max 50 chars). |
| `nin` | `string` | **Yes** | Exactly 11-digit NIN number to validate. |
| `validation_type` | `string` | **No** | Type of validation (Default: `no_record_found`). |

#### Request Example (cURL / JSON)
```json
POST /api/developers/validation.php HTTP/1.1
Host: dataverify.com.ng
Content-Type: application/json

{
    "api_key": "YOUR_API_KEY_HERE",
    "nin": "12345678901",
    "validation_type": "no_record_found"
}
```

#### Responses for Submission

##### 🟢 200 OK — Request Submitted (Pending)
```json
HTTP/1.1 200 OK
Content-Type: application/json

{
    "status": true,
    "message": "NIN Validation request submitted successfully. Your request is being processed.",
    "nin": "12345678901",
    "validation_type": "no_record_found",
    "transaction_id": "a1b2c3d4e5f6a7b8c9d0e1f2a3",
    "price": 800,
    "balance_before": 5000,
    "balance_after": 4200,
    "request_status": "pending"
}
```

##### 🔴 400 Bad Request — Invalid NIN Format
```json
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
    "status": false,
    "message": "Invalid NIN format, must be exactly 11 digits."
}
```

##### 🔴 400 Bad Request — Insufficient Balance
```json
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
    "status": false,
    "message": "Insufficient balance",
    "price": 800,
    "balance": 120
}
```

##### 🟠 409 Conflict — Already Exists
```json
HTTP/1.1 409 Conflict
Content-Type: application/json

{
    "status": false,
    "message": "This NIN already exists on our server as pending"
}
```

##### 🔒 401 Unauthorized — Invalid API Key
```json
HTTP/1.1 401 Unauthorized
Content-Type: application/json

{
    "status": false,
    "message": "Invalid API key"
}
```

##### ⛔ 403 Forbidden — Blocked IP
```json
HTTP/1.1 403 Forbidden
Content-Type: application/json

{
    "status": false,
    "message": "Your IP address is blocked due to suspicious activity."
}
```

##### ⏳ 429 Too Many Requests — Rate Limited
```json
HTTP/1.1 429 Too Many Requests
Content-Type: application/json

{
    "status": false,
    "message": "Rate limit exceeded. Please try again later."
}
```

---

### 2.2 Check NIN Validation Status

Retrieves the validation result using either the returned `transaction_id` or the `nin`. Status checks are free.

*   **Endpoint:** `POST /api/developers/validation_status.php`
*   **Headers:**
    *   `Content-Type: application/json`

#### Request Body Parameters
| Parameter | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `api_key` | `string` | **Yes** | Your API authentication key. |
| `transaction_id` | `string` | **Either** | Transaction ID returned during submission (Preferred). |
| `nin` | `string` | **Either** | 11-digit NIN (Retrieves most recent submission for this NIN). |

#### Request Example (cURL / JSON)
```json
POST /api/developers/validation_status.php HTTP/1.1
Host: dataverify.com.ng
Content-Type: application/json

{
    "api_key": "YOUR_API_KEY_HERE",
    "transaction_id": "a1b2c3d4e5f6a7b8c9d0e1f2a3"
}
```

#### Responses for Status Check

##### 🟢 200 OK — Validated (Completed)
```json
HTTP/1.1 200 OK
Content-Type: application/json

{
    "status": true,
    "message": "NIN Validation completed successfully.",
    "request_status": "validated",
    "nin": "12345678901",
    "validation_type": "no_record_found",
    "transaction_id": "a1b2c3d4e5f6a7b8c9d0e1f2a3",
    "price": 800,
    "response": "Record found and validated successfully.",
    "date": "2026-08-02 09:14:22",
    "completed_at": "2026-08-02 11:40:05"
}
```

##### 🟡 200 OK — Pending
```json
HTTP/1.1 200 OK
Content-Type: application/json

{
    "status": true,
    "message": "Your NIN Validation request is currently pending. Please check back later.",
    "request_status": "pending",
    "nin": "12345678901",
    "validation_type": "no_record_found",
    "transaction_id": "a1b2c3d4e5f6a7b8c9d0e1f2a3",
    "price": 800,
    "date": "2026-08-02 09:14:22"
}
```

##### 🔵 200 OK — Processing
```json
HTTP/1.1 200 OK
Content-Type: application/json

{
    "status": true,
    "message": "Your NIN Validation request is currently processing. Please check back later.",
    "request_status": "processing",
    "nin": "12345678901",
    "validation_type": "no_record_found",
    "transaction_id": "a1b2c3d4e5f6a7b8c9d0e1f2a3",
    "price": 800,
    "date": "2026-08-02 09:14:22"
}
```

##### 🔴 200 OK — Validation Failed
```json
HTTP/1.1 200 OK
Content-Type: application/json

{
    "status": false,
    "message": "Your NIN Validation request has failed.",
    "request_status": "failed",
    "nin": "12345678901",
    "validation_type": "no_record_found",
    "transaction_id": "a1b2c3d4e5f6a7b8c9d0e1f2a3",
    "price": 800,
    "error_detail": "No record found at NIMC for this NIN.",
    "date": "2026-08-02 09:14:22"
}
```

##### ❓ 404 Not Found — Reference Not Found
```json
HTTP/1.1 404 Not Found
Content-Type: application/json

{
    "status": false,
    "message": "No validation request found for this reference on your account."
}
```

---

#### Code Examples: NIN Validation Request & Status Check

<details>
<summary><b>PHP Example</b></summary>

```php
<?php
$apiKey = "YOUR_API_KEY_HERE";
$nin = "12345678901";

// 1. Submit NIN Validation
$submitUrl = 'https://dataverify.com.ng/api/developers/validation.php';
$payload = [
    'api_key'         => $apiKey,
    'nin'             => $nin,
    'validation_type' => 'no_record_found'
];

$ch = curl_init($submitUrl);
curl_setopt_array($ch, [
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_POST           => true,
    CURLOPT_POSTFIELDS     => json_encode($payload),
    CURLOPT_HTTPHEADER     => ['Content-Type: application/json']
]);

$response = curl_exec($ch);
$httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
curl_close($ch);

$result = json_decode($response, true);

if ($httpCode === 200 && ($result['status'] ?? false) === true) {
    $txnId = $result['transaction_id'];
    echo "Validation submitted. Transaction ID: {$txnId}\n";
} else {
    echo "Submission failed ({$httpCode}): " . ($result['message'] ?? 'Error') . "\n";
    exit;
}

// 2. Poll Status Check
$statusUrl = 'https://dataverify.com.ng/api/developers/validation_status.php';
$statusPayload = [
    'api_key'        => $apiKey,
    'transaction_id' => $txnId
];

$ch = curl_init($statusUrl);
curl_setopt_array($ch, [
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_POST           => true,
    CURLOPT_POSTFIELDS     => json_encode($statusPayload),
    CURLOPT_HTTPHEADER     => ['Content-Type: application/json']
]);

$statusResponse = curl_exec($ch);
$statusHttpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
curl_close($ch);

$statusResult = json_decode($statusResponse, true);

switch ($statusResult['request_status'] ?? '') {
    case 'validated':
        echo "Validation Completed Successfully!\n";
        echo "Response Details: " . ($statusResult['response'] ?? 'Validated') . "\n";
        break;

    case 'pending':
    case 'processing':
        echo "Status is {$statusResult['request_status']}. Poll again in a few minutes.\n";
        break;

    case 'failed':
        echo "Validation Failed: " . ($statusResult['error_detail'] ?? 'Reason unspecified') . "\n";
        break;

    default:
        if ($statusHttpCode === 404) {
            echo "404 Not Found: Validation record not found.\n";
        } else {
            echo "Error ({$statusHttpCode}): " . ($statusResult['message'] ?? 'Unknown');
        }
}
?>
```
</details>

<details>
<summary><b>JavaScript / Node.js Example</b></summary>

```javascript
const apiKey = 'YOUR_API_KEY_HERE';
const nin = '12345678901';

async function validateNIN() {
    // 1. Submit Validation
    const submitRes = await fetch('https://dataverify.com.ng/api/developers/validation.php', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
            api_key: apiKey,
            nin,
            validation_type: 'no_record_found'
        })
    });

    const submitData = await submitRes.json();
    if (!submitRes.ok || !submitData.status) {
        console.error('Validation Submission Error:', submitData.message);
        return;
    }

    const transactionId = submitData.transaction_id;
    console.log(`Submitted successfully. Transaction ID: ${transactionId}`);

    // 2. Poll Validation Status
    const statusRes = await fetch('https://dataverify.com.ng/api/developers/validation_status.php', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
            api_key: apiKey,
            transaction_id: transactionId
        })
    });

    const statusData = await statusRes.json();
    console.log('Status Result:', statusData);

    switch (statusData.request_status) {
        case 'validated':
            console.log('NIN Validated!', statusData.response);
            break;
        case 'pending':
        case 'processing':
            console.log(`Request currently ${statusData.request_status}. Please retry later.`);
            break;
        case 'failed':
            console.error('Validation Failed:', statusData.error_detail);
            break;
        default:
            console.error(statusData.message || 'Unknown status');
    }
}

validateNIN();
```
</details>

<details>
<summary><b>Python Example</b></summary>

```python
import requests

api_key = 'YOUR_API_KEY_HERE'
nin = '12345678901'

# 1. Submit Validation Request
submit_url = 'https://dataverify.com.ng/api/developers/validation.php'
payload = {
    'api_key': api_key,
    'nin': nin,
    'validation_type': 'no_record_found'
}

res = requests.post(submit_url, json=payload, timeout=30)
data = res.json()

if res.status_code == 200 and data.get('status'):
    txn_id = data.get('transaction_id')
    print(f"Validation Submitted. Transaction ID: {txn_id}")

    # 2. Check Status
    status_url = 'https://dataverify.com.ng/api/developers/validation_status.php'
    status_payload = {'api_key': api_key, 'transaction_id': txn_id}

    status_res = requests.post(status_url, json=status_payload, timeout=30)
    status_data = status_res.json()

    req_status = status_data.get('request_status')
    if req_status == 'validated':
        print("Validated successfully:", status_data.get('response'))
    elif req_status in ['pending', 'processing']:
        print(f"Validation in progress ({req_status}). Check back later.")
    elif req_status == 'failed':
        print("Validation failed:", status_data.get('error_detail'))
    else:
        print("Error/Status:", status_data.get('message'))
else:
    print("Submission Error:", data.get('message'))
```
</details>

---

## 3. 📊 Summary of Request Statuses & HTTP Error Codes

### Request Status Field (`request_status`) Reference Table

| Service | `request_status` Value | Status Boolean | Meaning / Description |
| :--- | :--- | :--- | :--- |
| **IPE Clearance** | `pending` | `true` | Request received and queued for clearing. |
| **IPE Clearance** | `processing` / `inprogress` | `true` | Request is actively being processed by team/system. |
| **IPE Clearance** | `completed` | `true` | Clearance finished. Returns `newNIN`, `newTrackingID`, and timestamp. |
| **IPE Clearance** | `failed` / `blocked` / `incompleted` / `insufficient_fingerprint` / `abis` | `false` | Request failed or was rejected. Failure details provided in `error_detail`. |
| **NIN Validation** | `pending` | `true` | Validation request received and queued. |
| **NIN Validation** | `processing` | `true` | Validation team/system is verifying record. |
| **NIN Validation** | `validated` | `true` | Validation successful. Outcome available in `response` field. |
| **NIN Validation** | `failed` | `false` | Validation failed (e.g. no record found at NIMC). Error details in `error_detail`. |

---

### HTTP Status Codes Reference Table

| Code | Label | Typical Scenarios & Messages |
| :--- | :--- | :--- |
| **200** | `OK` | Request queued, completed, validated, pending, processing, or failed status retrieved. |
| **400** | `Bad Request` | Missing parameters (`api_key`, `trackingID`, `nin`), invalid tracking ID format, invalid 11-digit NIN format, or insufficient balance. |
| **401** | `Unauthorized` | Missing or invalid `api_key`. |
| **403** | `Forbidden` | IP address blocked due to suspicious activity. |
| **404** | `Not Found` | Tracking ID or transaction reference does not exist on the server. |
| **409** | `Conflict` | Request already exists as pending or processing for the given tracking ID / NIN. |
| **429** | `Too Many Requests` | Rate limit exceeded (100 req/hour per key, 60 req/min per IP). |
