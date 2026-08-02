# 📘 DataVerify API Documentation

Welcome to the **DataVerify API Documentation**. This document provides comprehensive reference material for developers integrating with our identity verification APIs, covering NIN verification slips (by NIN, phone, or demographics), BVN verification slips, bank account verification, IPE clearance, and NIN validation workflows.

---

## 🔑 Authentication

All API requests require authentication using an API key. This key must be included in the request payload as a parameter.

*   **Parameter Key:** `api_key`
*   **Parameter Value:** Your custom DataVerify API Key.

### 💻 Quickstart Authentication Examples

==== PHP Example ====
```php
$apiKey = 'YOUR_API_KEY_HERE';
$url = 'https://dataverify.com.ng/developers/nin_slips/nin_premium';

$payload = json_encode([
    'api_key' => $apiKey,
    'nin' => '12345678901'
]);
```

==== JavaScript Example ====
```javascript
const apiKey = 'YOUR_API_KEY_HERE';
const url = 'https://dataverify.com.ng/developers/nin_slips/nin_premium';

const payload = {
    api_key: apiKey,
    nin: '12345678901'
};
```

==== Python Example ====
```python
import requests
import json

api_key = 'YOUR_API_KEY_HERE'
url = 'https://dataverify.com.ng/developers/nin_slips/nin_premium'

payload = {
    'api_key': api_key,
    'nin': '12345678901'
}
```

---

## 📂 API Table of Contents

1.  **[NIN Verification Slips by NIN Number](#1-nin-verification-slips-by-nin-number)**
2.  **[NIN Verification Slips by Phone Number](#2-nin-verification-slips-by-phone-number)**
3.  **[NIN Verification Slips by Demographic Details](#3-nin-verification-slips-by-demographic-details)**
4.  **[BVN Verification Slips](#4-bvn-verification-slips)**
5.  **[Bank Account Verification](#5-bank-account-verification)**
6.  **[IPE Clearance - Submit Request](#6-ipe-clearance---submit-request)**
7.  **[IPE Clearance - Check Status](#7-ipe-clearance---check-status)**
8.  **[NIN Validation - Submit Request](#8-nin-validation---submit-request)**
9.  **[NIN Validation - Check Status](#9-nin-validation---check-status)**
10. **[Error Handling](#10-error-handling)**

---

## 1. NIN Verification Slips by NIN Number

Generate NIN verification slips using the 11-digit NIN number. Four slip tiers are available: **Premium Slip**, **Regular Slip**, **Standard Slip**, and **VNIN Slip**.

*   **Endpoint:** `POST https://dataverify.com.ng/developers/nin_slips/nin_premium` (Alternative endpoint: `https://dataverify.com.ng/developers/nin_slips/nin_premium.php`)

### Request Parameters

| Parameter | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `api_key` | string | **Yes** | Your API authentication key |
| `nin` | string | **Yes** | 11-digit National Identification Number (NIN) |

### Request Example (JSON)
```json
{
    "api_key": "YOUR_API_KEY_HERE",
    "nin": "12345678901"
}
```

### Response Example (Code `200 OK`)
```json
{
    "status": "success",
    "response_code": "00",
    "user_data": {
        "nin": "12345678901",
        "first_name": "JOHN",
        "last_name": "DOE",
        "middle_name": "MICHAEL",
        "gender": "MALE",
        "date_of_birth": "15-05-1985",
        "phone_number": "08012345678",
        "address": "123 Sample Street, Lagos"
    },
    "message": "PDF generated successfully",
    "pdf_base64": "base64_encoded_pdf_data_here..."
}
```

### Integration Examples

==== PHP cURL Example (Force Download) ====
```php
<?php
$apiKey = "YOUR_API_KEY_HERE";
$nin = "12345678901";

$url = 'https://dataverify.com.ng/developers/nin_slips/nin_premium.php';

$payload = json_encode([
    'api_key' => $apiKey,
    'nin' => $nin
]);

$ch = curl_init($url);
curl_setopt_array($ch, [
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_POST => true,
    CURLOPT_POSTFIELDS => $payload,
    CURLOPT_HTTPHEADER => [
        'Content-Type: application/json',
        'Content-Length: ' . strlen($payload)
    ]
]);

$response = curl_exec($ch);
$httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
$curlError = curl_error($ch);
curl_close($ch);

if ($curlError) {
    echo "API connection failed: " . $curlError;
    exit;
}

$apiResponse = json_decode($response, true);

if ($apiResponse['status'] === 'success' && !empty($apiResponse['pdf_base64'])) {
    $pdfData = base64_decode($apiResponse['pdf_base64']);
    header('Content-Type: application/pdf');
    header('Content-Disposition: attachment; filename="nin_slip_' . $nin . '.pdf"');
    header('Content-Length: ' . strlen($pdfData));
    echo $pdfData;
    exit;
} else {
    echo "Error: " . $apiResponse['message'];
}
?>
```

==== PHP Save to File Example ====
```php
<?php
$apiKey = "YOUR_API_KEY_HERE";
$nin = "12345678901";

$url = 'https://dataverify.com.ng/developers/nin_slips/nin_premium';

$payload = json_encode([
    'api_key' => $apiKey,
    'nin' => $nin
]);

$ch = curl_init($url);
curl_setopt_array($ch, [
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_POST => true,
    CURLOPT_POSTFIELDS => $payload,
    CURLOPT_HTTPHEADER => [
        'Content-Type: application/json',
        'Content-Length: ' . strlen($payload)
    ]
]);

$response = curl_exec($ch);
$curlError = curl_error($ch);
curl_close($ch);

if ($curlError) {
    echo "API connection failed: " . $curlError;
    exit;
}

$apiResponse = json_decode($response, true);

if ($apiResponse['status'] === 'success' && !empty($apiResponse['pdf_base64'])) {
    $pdfData = base64_decode($apiResponse['pdf_base64']);
    $fileName = "nin_slip_" . $nin . ".pdf";
    file_put_contents($fileName, $pdfData);
    echo "PDF saved successfully: " . $fileName;
} else {
    echo "Error: " . $apiResponse['message'];
}
?>
```

---

## 2. NIN Verification Slips by Phone Number

Generate NIN verification slips using a registered phone number. Three slip tiers are available: **Premium Slip**, **Regular Slip**, and **Standard Slip**.

*   **Endpoint:** `POST https://dataverify.com.ng/developers/nin_slips/nin_premium_phone` (Alternative endpoint: `https://dataverify.com.ng/developers/nin_slips/nin_by_phone.php`)

### Request Parameters

| Parameter | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `api_key` | string | **Yes** | Your API authentication key |
| `nin` | string | **Yes** | 11-digit phone number (e.g., `08012345678`) |

### Request Example (JSON)
```json
{
    "api_key": "YOUR_API_KEY_HERE",
    "nin": "08012345678"
}
```

### Response Example (Code `200 OK`)
```json
{
    "status": "success",
    "response_code": "00",
    "user_data": {
        "nin": "12345678901",
        "first_name": "JOHN",
        "last_name": "DOE",
        "middle_name": "MICHAEL",
        "gender": "MALE",
        "date_of_birth": "15-05-1985",
        "phone_number": "08012345678",
        "address": "123 Sample Street, Lagos"
    },
    "message": "PDF generated successfully",
    "pdf_base64": "base64_encoded_pdf_data_here..."
}
```

### Integration Examples

==== PHP cURL Example (Force Download) ====
```php
<?php
$apiKey = "YOUR_API_KEY_HERE";
$phoneNumber = "08012345678";

$url = 'https://dataverify.com.ng/developers/nin_slips/nin_by_phone.php';

$payload = json_encode([
    'api_key' => $apiKey,
    'nin' => $phoneNumber
]);

$ch = curl_init($url);
curl_setopt_array($ch, [
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_POST => true,
    CURLOPT_POSTFIELDS => $payload,
    CURLOPT_HTTPHEADER => [
        'Content-Type: application/json',
        'Content-Length: ' . strlen($payload)
    ]
]);

$response = curl_exec($ch);
$curlError = curl_error($ch);
curl_close($ch);

if ($curlError) {
    echo "API connection failed: " . $curlError;
    exit;
}

$apiResponse = json_decode($response, true);

if ($apiResponse['status'] === 'success' && !empty($apiResponse['pdf_base64'])) {
    $pdfData = base64_decode($apiResponse['pdf_base64']);
    header('Content-Type: application/pdf');
    header('Content-Disposition: attachment; filename="nin_slip_' . $phoneNumber . '.pdf"');
    header('Content-Length: ' . strlen($pdfData));
    echo $pdfData;
    exit;
} else {
    echo "Error: " . $apiResponse['message'];
}
?>
```

---

## 3. NIN Verification Slips by Demographic Details

Generate NIN verification slips using personal demographic information. One slip tier is available: **Premium Slip**.

*   **Endpoint:** `POST https://dataverify.com.ng/developers/nin_slips/nin_premium_demo.php`

### Request Parameters

| Parameter | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `api_key` | string | **Yes** | Your API authentication key |
| `firstname` | string | **Yes** | Individual's first name |
| `lastname` | string | **Yes** | Individual's last name |
| `dob` | string | **Yes** | Date of birth in **DD-MM-YYYY** format |
| `gender` | string | **Yes** | Gender — `m` for Male, `f` for Female, or `male`/`female` (auto-converted) |

### Request Example (JSON)
```json
{
    "api_key": "YOUR_API_KEY_HERE",
    "firstname": "SADEEQ",
    "lastname": "DOE",
    "dob": "15-05-1985",
    "gender": "m"
}
```

### Response Example (Code `200 OK`)
```json
{
    "status": "success",
    "response_code": "00",
    "user_data": {
        "nin": "12345678901",
        "first_name": "JOHN",
        "last_name": "DOE",
        "middle_name": "MICHAEL",
        "gender": "MALE",
        "date_of_birth": "15-05-1985",
        "phone_number": "08012345678",
        "address": "123 Sample Street, Lagos"
    },
    "message": "PDF generated successfully",
    "pdf_base64": "base64_encoded_pdf_data_here..."
}
```

### Integration Examples

==== PHP Implementation Example ====
```php
<?php
$apiKey = "YOUR_API_KEY_HERE";
$firstName = "JOHN";
$lastName = "DOE";
$dob = "1985-05-15"; // yyyy-mm-dd from user form
$genderInput = "male"; // accepts: 'm', 'f', 'male', 'female'

// Convert DOB from yyyy-mm-dd to dd-mm-yyyy (required by API)
$formattedDob = date("d-m-Y", strtotime($dob));

// Normalise gender to m/f
$gender = strtolower($genderInput);
if ($gender === 'male') $gender = 'm';
if ($gender === 'female') $gender = 'f';

$url = 'https://dataverify.com.ng/developers/nin_slips/nin_premium_demo.php';

$payload = json_encode([
    'api_key'   => $apiKey,
    'firstname' => $firstName,
    'lastname'  => $lastName,
    'dob'       => $formattedDob,
    'gender'    => $gender
]);

$ch = curl_init($url);
curl_setopt_array($ch, [
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_POST           => true,
    CURLOPT_POSTFIELDS     => $payload,
    CURLOPT_HTTPHEADER     => [
        'Content-Type: application/json',
        'Content-Length: ' . strlen($payload)
    ],
    CURLOPT_SSL_VERIFYPEER => false,
    CURLOPT_TIMEOUT        => 30
]);

$response  = curl_exec($ch);
$httpCode  = curl_getinfo($ch, CURLINFO_HTTP_CODE);
$curlError = curl_error($ch);
curl_close($ch);

if (!empty($curlError)) {
    echo "cURL Error: $curlError";
} elseif ($httpCode !== 200) {
    $err = json_decode($response, true);
    echo "Error: " . ($err['message'] ?? "HTTP $httpCode");
} else {
    $result = json_decode($response, true);

    if (!empty($result['pdf_base64'])) {
        $pdfData  = base64_decode($result['pdf_base64']);
        $filePath = 'nin_slip_' . time() . '.pdf';
        file_put_contents($filePath, $pdfData);
        echo "PDF saved: $filePath";
    } else {
        echo "Error: " . ($result['message'] ?? 'Unknown error');
    }
}
?>
```

---

## 4. BVN Verification Slips

Generate BVN verification slips using the 11-digit BVN number. Two service tiers are available: **Premium Slip** and **Standard Slip**.

*   **Endpoint:** `POST https://dataverify.com.ng/developers/bvn_slip/bvn_premium.php`

### Request Parameters

| Parameter | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `api_key` | string | **Yes** | Your API authentication key |
| `bvn` | string | **Yes** | 11-digit Bank Verification Number (BVN) |

### Request Example (JSON)
```json
{
    "api_key": "YOUR_API_KEY_HERE",
    "bvn": "12345678901"
}
```

### Response Example (Code `200 OK`)
```json
{
    "status": "success",
    "response_code": "00",
    "user_data": {
        "bvn": "12345678901",
        "first_name": "JOHN",
        "last_name": "DOE",
        "middle_name": "MICHAEL",
        "gender": "MALE",
        "date_of_birth": "15-05-1985",
        "phone_number": "08012345678",
        "address": "123 Sample Street, Lagos"
    },
    "message": "BVN slip generated successfully",
    "pdf_base64": "base64_encoded_pdf_data_here..."
}
```

---

## 5. Bank Account Verification

Verify a Nigerian bank account against a BVN. This is a data lookup endpoint that returns the account holder's name and a name similarity match score (no PDF document is generated). Bank codes use standard CBN/NIBSS institution codes (e.g., `000013` for GTBank).

*   **Endpoint:** `POST https://dataverify.com.ng/developers/nin_slips/bank_account_verify.php`

### Request Parameters

| Parameter | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `api_key` | string | **Yes** | Your API authentication key |
| `bvn` | string | **Yes** | 11-digit BVN (must commence with "22") |
| `bankCode` | string | **Yes** | Bank/institution code (e.g., `000013` for GTBank) |
| `bankAccount` | string | **Yes** | 10-digit NUBAN account number |

### Request Example (JSON)
```json
{
    "api_key": "YOUR_API_KEY_HERE",
    "bvn": "22333444555",
    "bankCode": "000013",
    "bankAccount": "0123456789"
}
```

### Response Example (Code `200 OK`)
```json
{
    "status": "success",
    "message": "Bank account verification completed",
    "transaction_id": "a1b2c3d4e5f6a1b2c3d4e5f6a1b2c3",
    "data": {
        "verifyResult": true,
        "bankAccountName": "JOHN DOE",
        "nameMatchPercentage": 1.0
    }
}
```

### Response Fields Description

| Field | Type | Description |
| :--- | :--- | :--- |
| `verifyResult` | boolean | `true` if the account holder's name matches the BVN |
| `bankAccountName` | string | Registered name on the bank account |
| `nameMatchPercentage` | float | Name similarity score, ranging from `0.0` to `1.0` |

### Integration Examples

==== PHP Implementation Example ====
```php
<?php
$apiKey = "YOUR_API_KEY_HERE";

$url = 'https://dataverify.com.ng/developers/nin_slips/bank_account_verify.php';

$payload = json_encode([
    'api_key'     => $apiKey,
    'bvn'         => '22333444555',
    'bankCode'    => '000013',
    'bankAccount' => '0123456789'
]);

$ch = curl_init($url);
curl_setopt_array($ch, [
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_POST => true,
    CURLOPT_POSTFIELDS => $payload,
    CURLOPT_HTTPHEADER => [
        'Content-Type: application/json',
        'Content-Length: ' . strlen($payload)
    ]
]);

$response = curl_exec($ch);
$curlError = curl_error($ch);
curl_close($ch);

if ($curlError) {
    echo "API connection failed: " . $curlError;
    exit;
}

$apiResponse = json_decode($response, true);

if ($apiResponse['status'] === 'success') {
    $data = $apiResponse['data'];
    echo "Account Name: " . $data['bankAccountName'] . PHP_EOL;
    echo "Verified: " . ($data['verifyResult'] ? 'YES' : 'NO') . PHP_EOL;
    echo "Name Match: " . ($data['nameMatchPercentage'] * 100) . '%' . PHP_EOL;
} else {
    echo "Error: " . $apiResponse['message'];
}
?>
```

---

## 6. IPE Clearance - Submit Request

Submit an IPE clearance request using a valid Tracking ID. The request is queued for processing and results can be checked via the status endpoint. Balance is deducted at submission time.

*   **Endpoint:** `POST https://dataverify.com.ng/api/developers/ipe.php` (Alternative endpoint: `https://dataverify.com.ng/api/developers/ipe2.php`)

### Request Parameters

| Parameter | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `api_key` | string | **Yes** | Your API authentication key (max 50 characters) |
| `trackingID` | string | **Yes** | The IPE Tracking ID to process (alphanumeric, max 20 characters) |

### HTTP Status Codes

*   **`200 OK`**: Success - Request submitted and queued for processing.
*   **`400 Bad Request`**: Missing parameters, invalid format, or insufficient balance.
*   **`401 Unauthorized`**: Invalid API key.
*   **`403 Forbidden`**: IP address blocked.
*   **`409 Conflict`**: Duplicate request (tracking ID already pending/processing).
*   **`429 Too Many Requests`**: Rate limit exceeded (100 requests/hour per key, 60 requests/minute per IP).

### Response Structure - Success

| Field | Type | Description |
| :--- | :--- | :--- |
| `status` | boolean | `true` on success |
| `message` | string | Human-readable result message |
| `trackingID` | string | The tracking ID submitted |
| `transaction_id` | string | Unique transaction reference for this request |
| `price` | number | Amount charged for this request |
| `balance_before` | number | Your wallet balance before the charge |
| `balance_after` | number | Your wallet balance after the charge |
| `request_status` | string | Always `"pending"` at submission |

### Response Structure - Error

| Field | Type | Description |
| :--- | :--- | :--- |
| `status` | boolean | `false` on error |
| `message` | string | Description of what went wrong |

### Response Examples

==== Request JSON ====
```json
{
    "api_key": "YOUR_API_KEY_HERE",
    "trackingID": "ABC12345XYZ"
}
```

==== Response `200 OK` (Success) ====
```json
{
    "status": true,
    "message": "IPE Clearance request submitted successfully. Your request is being processed.",
    "trackingID": "ABC12345XYZ",
    "transaction_id": "a1b2c3d4e5f6...",
    "price": 1500,
    "balance_before": 5000,
    "balance_after": 3500,
    "request_status": "pending"
}
```

==== Response `400 Bad Request` (Missing Parameters) ====
```json
{
    "message": "api_key and trackingID are required."
}
```

==== Response `400 Bad Request` (Invalid Format) ====
```json
{
    "message": "Invalid tracking ID format."
}
```

==== Response `400 Bad Request` (Insufficient Balance) ====
```json
{
    "status": false,
    "message": "Insufficient balance"
}
```

==== Response `409 Conflict` (Duplicate) ====
```json
{
    "status": false,
    "message": "This record already exists as pending"
}
```

==== Response `401 Unauthorized` (Invalid Key) ====
```json
{
    "status": false,
    "message": "Invalid API key"
}
```

==== Response `429 Too Many Requests` (Key Rate Limit) ====
```json
{
    "message": "Rate limit exceeded. Please try again later."
}
```

==== Response `429 Too Many Requests` (IP Rate Limit) ====
```json
{
    "message": "You have exceeded the maximum number of requests. Please try again later."
}
```

==== Response `403 Forbidden` (IP Blocked) ====
```json
{
    "message": "Your IP address is blocked due to suspicious activity."
}
```

### Integration Examples

==== PHP cURL Example ====
```php
<?php
$apiKey = "YOUR_API_KEY_HERE";
$trackingID = "ABC12345XYZ";

$url = 'https://dataverify.com.ng/api/developers/ipe2.php';

$payload = json_encode([
    'api_key' => $apiKey,
    'trackingID' => $trackingID
]);

$ch = curl_init($url);
curl_setopt_array($ch, [
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_POST => true,
    CURLOPT_POSTFIELDS => $payload,
    CURLOPT_HTTPHEADER => [
        'Content-Type: application/json',
        'Content-Length: ' . strlen($payload)
    ]
]);

$response = curl_exec($ch);
$httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
curl_close($ch);

$result = json_decode($response, true);

echo "HTTP Code: $httpCode\n";

if ($httpCode === 200 && $result['status'] === true) {
    echo "Request submitted successfully!\n";
    echo "Tracking ID: " . $result['trackingID'] . "\n";
    echo "Transaction ID: " . $result['transaction_id'] . "\n";
    echo "Price Charged: " . $result['price'] . "\n";
    echo "Balance Before: " . $result['balance_before'] . "\n";
    echo "Balance After: " . $result['balance_after'] . "\n";
    echo "Status: " . $result['request_status'] . "\n";
    echo "\nUse ipe_status2.php to check for results.";
} elseif ($httpCode === 409) {
    echo "Duplicate: " . $result['message'];
} elseif ($httpCode === 400) {
    echo "Bad Request: " . $result['message'];
} elseif ($httpCode === 429) {
    echo "Rate Limited: " . $result['message'];
} else {
    echo "Error ($httpCode): " . ($result['message'] ?? 'Unknown error');
}
?>
```

---

## 7. IPE Clearance - Check Status

Check the processing status of a previously submitted IPE clearance request. Returns the full result when completed, or the current status if still processing. No wallet balance is deducted for status checks.

*   **Endpoint:** `POST https://dataverify.com.ng/api/developers/ipe_status.php` (Alternative endpoint: `https://dataverify.com.ng/api/developers/ipe_status2.php`)

### Request Parameters

| Parameter | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `api_key` | string | **Yes** | Your API authentication key (max 50 characters) |
| `trackingID` | string | **Yes** | The Tracking ID submitted earlier (alphanumeric, max 20 characters) |

### HTTP Status Codes

*   **`200 OK`**: Success - Returns status/result (completed, pending, processing, inprogress, or failed).
*   **`400 Bad Request`**: Missing parameters or invalid tracking ID format.
*   **`401 Unauthorized`**: Invalid API key.
*   **`403 Forbidden`**: IP address blocked.
*   **`404 Not Found`**: Tracking ID does not exist on the server.
*   **`429 Too Many Requests`**: Rate limit exceeded.

### Response Structure - Completed (HTTP `200 OK`)

| Field | Type | Description |
| :--- | :--- | :--- |
| `status` | boolean | `true` - request was successful |
| `message` | string | `"IPE Clearance completed successfully."` |
| `request_status` | string | `"completed"` |
| `trackingID` | string | The tracking ID you submitted |
| `newNIN` | string \| null | The new NIN from the clearance result |
| `newTrackingID` | string \| null | The new tracking ID from the clearance result |
| `date` | string | Date the record was created (Y-m-d H:i:s) |

### Response Structure - Pending / Processing / Inprogress (HTTP `200 OK`)

| Field | Type | Description |
| :--- | :--- | :--- |
| `status` | boolean | `true` |
| `message` | string | `"Your IPE Clearance request is currently {status}. Please check back later."` |
| `request_status` | string | `"pending"`, `"processing"`, or `"inprogress"` |
| `trackingID` | string | The tracking ID you submitted |

### Response Structure - Failed (HTTP `200 OK`)

| Field | Type | Description |
| :--- | :--- | :--- |
| `status` | boolean | `false` |
| `message` | string | `"Your IPE Clearance request has failed."` |
| `request_status` | string | `"failed"`, `"blocked"`, `"incompleted"`, `"insufficient_fingerprint"`, or `"abis"` |
| `trackingID` | string | The tracking ID you submitted |
| `error_detail` | string | Detailed reason for the failure |

### Response Structure - Not Found (HTTP `404 Not Found`)

| Field | Type | Description |
| :--- | :--- | :--- |
| `status` | boolean | `false` |
| `message` | string | `"This tracking ID does not exist on our server."` |

### Response Examples

==== Request JSON ====
```json
{
    "api_key": "YOUR_API_KEY_HERE",
    "trackingID": "ABC12345XYZ"
}
```

==== Response `200 OK` (Completed) ====
```json
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

==== Response `200 OK` (Pending/Processing) ====
```json
{
    "status": true,
    "message": "Your IPE Clearance request is currently pending. Please check back later.",
    "request_status": "pending",
    "trackingID": "ABC12345XYZ"
}
```

==== Response `200 OK` (Failed) ====
```json
{
    "status": false,
    "message": "Your IPE Clearance request has failed.",
    "request_status": "failed",
    "trackingID": "ABC12345XYZ",
    "error_detail": "Insufficient fingerprint data on file"
}
```

==== Response `404 Not Found` ====
```json
{
    "status": false,
    "message": "This tracking ID does not exist on our server."
}
```

==== Response `400 Bad Request` (Missing Parameters) ====
```json
{
    "message": "Missing required parameters: api_key and trackingID"
}
```

==== Response `400 Bad Request` (Invalid Format) ====
```json
{
    "message": "Invalid trackingID format, must be 1 to 20 alphanumeric characters."
}
```

==== Response `401 Unauthorized` (Invalid Key) ====
```json
{
    "status": false,
    "message": "Invalid API key"
}
```

==== Response `429 Too Many Requests` (Rate Limit) ====
```json
{
    "message": "Rate limit exceeded. Please try again later."
}
```

### Integration Examples

==== PHP cURL Example ====
```php
<?php
$apiKey = "YOUR_API_KEY_HERE";
$trackingID = "ABC12345XYZ";

$url = 'https://dataverify.com.ng/api/developers/ipe_status2.php';

$payload = json_encode([
    'api_key' => $apiKey,
    'trackingID' => $trackingID
]);

$ch = curl_init($url);
curl_setopt_array($ch, [
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_POST => true,
    CURLOPT_POSTFIELDS => $payload,
    CURLOPT_HTTPHEADER => [
        'Content-Type: application/json',
        'Content-Length: ' . strlen($payload)
    ]
]);

$response = curl_exec($ch);
$httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
curl_close($ch);

$result = json_decode($response, true);

echo "HTTP Code: $httpCode\n";

if ($httpCode === 404) {
    echo "Not Found: " . $result['message'];
    exit;
}

if ($httpCode !== 200) {
    echo "Error ($httpCode): " . ($result['message'] ?? 'Unknown error');
    exit;
}

// HTTP 200 - check request_status
switch ($result['request_status'] ?? '') {
    case 'completed':
        echo "Clearance completed!\n";
        echo "New NIN: " . ($result['newNIN'] ?? 'N/A') . "\n";
        echo "New Tracking ID: " . ($result['newTrackingID'] ?? 'N/A') . "\n";
        echo "Date: " . $result['date'] . "\n";
        break;

    case 'pending':
    case 'processing':
    case 'inprogress':
        echo "Status: " . $result['request_status'] . "\n";
        echo $result['message'] . "\n";
        echo "Please poll again in a few minutes.\n";
        break;

    case 'failed':
    case 'blocked':
    case 'incompleted':
    case 'insufficient_fingerprint':
    case 'abis':
        echo "Request failed!\n";
        echo "Status: " . $result['request_status'] . "\n";
        echo "Reason: " . ($result['error_detail'] ?? 'No details') . "\n";
        break;

    default:
        echo "Unexpected status: " . ($result['message'] ?? 'Unknown');
}
?>
```

---

## 8. NIN Validation - Submit Request

Submit a NIN for validation. The request is charged immediately, stored on our servers with a pending status, and processed by our validation team. Results are retrieved through the status endpoint — this endpoint never returns a validation result directly.

*   **Endpoint:** `POST https://dataverify.com.ng/api/developers/validation.php`

### Request Parameters

| Parameter | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `api_key` | string | **Yes** | Your API authentication key (max 50 characters) |
| `nin` | string | **Yes** | The NIN to validate. Must be exactly 11 digits |
| `validation_type` | string | No | Type of validation. Currently only `"no_record_found"` is supported (and is the default when omitted) |

### HTTP Status Codes

*   **`200 OK`**: Success - Request submitted and queued for processing.
*   **`400 Bad Request`**: Missing parameters, invalid NIN format, or insufficient balance.
*   **`401 Unauthorized`**: Invalid API key.
*   **`403 Forbidden`**: IP address blocked.
*   **`409 Conflict`**: You already have this NIN pending or processing (You are not charged for duplicates. Poll the status endpoint instead).
*   **`429 Too Many Requests`**: Rate limit exceeded (100 requests/hour per key, 60 requests/minute per IP).

### Success Response Structure

| Field | Type | Description |
| :--- | :--- | :--- |
| `status` | boolean | `true` on success |
| `transaction_id` | string | Reference for this request. Save this as it is the fastest way to poll the status endpoint |
| `price` | number | Amount charged for this request |
| `balance_before` | number | Your API balance before the deduction |
| `balance_after` | number | Your API balance after the deduction |
| `request_status` | string | Always `"pending"` on a fresh submission |

### Response Examples

==== Request JSON ====
```json
{
    "api_key": "YOUR_API_KEY_HERE",
    "nin": "12345678901",
    "validation_type": "no_record_found"
}
```

==== Response `200 OK` (Success) ====
```json
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

==== Response `400 Bad Request` (Invalid NIN Format) ====
```json
{
    "status": false,
    "message": "Invalid NIN format, must be exactly 11 digits."
}
```

==== Response `400 Bad Request` (Insufficient Balance) ====
```json
{
    "status": false,
    "message": "Insufficient balance",
    "price": 800,
    "balance": 120
}
```

==== Response `409 Conflict` (Duplicate Pending) ====
```json
{
    "status": false,
    "message": "This NIN already exists on our server as pending"
}
```

==== Response `401 Unauthorized` ====
```json
{
    "status": false,
    "message": "Invalid API key"
}
```

==== Response `429 Too Many Requests` ====
```json
{
    "status": false,
    "message": "Rate limit exceeded. Please try again later."
}
```

==== Response `403 Forbidden` ====
```json
{
    "status": false,
    "message": "Your IP address is blocked due to suspicious activity."
}
```

### Integration Examples

==== PHP Implementation Example ====
```php
<?php
$url = 'https://dataverify.com.ng/api/developers/validation.php';

$payload = [
    'api_key'         => 'YOUR_API_KEY_HERE',
    'nin'             => '12345678901',
    'validation_type' => 'no_record_found'
];

$ch = curl_init($url);
curl_setopt_array($ch, [
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_POST           => true,
    CURLOPT_POSTFIELDS     => json_encode($payload),
    CURLOPT_HTTPHEADER     => ['Content-Type: application/json']
]);

$response  = curl_exec($ch);
$http_code = curl_getinfo($ch, CURLINFO_HTTP_CODE);
curl_close($ch);

$result = json_decode($response, true);

if ($http_code === 200 && !empty($result['status'])) {
    echo "Submitted. Transaction ID: " . $result['transaction_id'] . "\n";
    echo "Charged: " . $result['price'] . "\n";
    // Store transaction_id, then poll validation_status.php
} elseif ($http_code === 409) {
    echo "Already submitted: " . $result['message'] . "\n";
} else {
    echo "Error: " . ($result['message'] ?? 'Unknown error') . "\n";
}
?>
```

---

## 9. NIN Validation - Check Status

Retrieve the outcome of a validation request. Lookups are scoped to your API key, so you can only query your own submissions. Polling this endpoint is free and does not affect your wallet balance.

*   **Endpoint:** `POST https://dataverify.com.ng/api/developers/validation_status.php`

### Request Parameters

| Parameter | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `api_key` | string | **Yes** | Your API authentication key (max 50 characters) |
| `transaction_id` | string | **Yes (or NIN)** | The transaction ID returned at submission. Preferred as it identifies an exact request |
| `nin` | string | **Yes (or TxID)** | The 11-digit NIN. Returns your most recent submission for that NIN. Ignored if `transaction_id` is supplied |

### Request Statuses Description

| `request_status` | `status` | Meaning |
| :--- | :--- | :--- |
| `"pending"` | `true` | Received and waiting to be picked up. Keep polling. |
| `"processing"` | `true` | Our team is working on it. Keep polling. |
| `"validated"` | `true` | Done. The outcome details are in the `response` field. |
| `"failed"` | `false` | Could not be validated. Reason is in `error_detail`. Refunds (where applicable) are credited back to your API balance. |

### HTTP Status Codes

*   **`200 OK`**: Success - Record found, check `request_status`.
*   **`400 Bad Request`**: Missing or malformed parameters.
*   **`401 Unauthorized`**: Invalid API key.
*   **`404 Not Found`**: No validation request under this reference on your account.

### Response Examples

==== Request JSON (by Transaction ID) ====
```json
{
    "api_key": "YOUR_API_KEY_HERE",
    "transaction_id": "a1b2c3d4e5f6a7b8c9d0e1f2a3"
}
```

==== Request JSON (by NIN) ====
```json
{
    "api_key": "YOUR_API_KEY_HERE",
    "nin": "12345678901"
}
```

==== Response `200 OK` (Pending) ====
```json
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

==== Response `200 OK` (Validated) ====
```json
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

==== Response `200 OK` (Failed) ====
```json
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

==== Response `404 Not Found` ====
```json
{
    "status": false,
    "message": "No validation request found for this reference on your account."
}
```

### Integration Examples

==== PHP Implementation Example ====
```php
<?php
$url = 'https://dataverify.com.ng/api/developers/validation_status.php';

$payload = [
    'api_key'        => 'YOUR_API_KEY_HERE',
    'transaction_id' => 'a1b2c3d4e5f6a7b8c9d0e1f2a3'
];

$ch = curl_init($url);
curl_setopt_array($ch, [
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_POST           => true,
    CURLOPT_POSTFIELDS     => json_encode($payload),
    CURLOPT_HTTPHEADER     => ['Content-Type: application/json']
]);

$response = curl_exec($ch);
close($ch);

$result = json_decode($response, true);

switch ($result['request_status'] ?? '') {
    case 'validated':
        echo "Validated!\n";
        echo "Response: " . ($result['response'] ?? 'No details') . "\n";
        break;

    case 'pending':
    case 'processing':
        echo "Still in progress. Please poll again in a few minutes.\n";
        break;

    case 'failed':
        echo "Request failed!\n";
        echo "Reason: " . ($result['error_detail'] ?? 'No details') . "\n";
        break;

    default:
        echo "Unexpected status: " . ($result['message'] ?? 'Unknown');
}
?>
```

---

## 10. Error Handling

All API responses follow a consistent format. Always inspect the `status` or success indicators in the response payload to determine if an operation was successful.

### HTTP Status Codes

*   **`200 OK`**: PDF generated successfully, lookup completed, or request status fetched.
*   **`400 Bad Request`**: Invalid parameters, insufficient balance, or record not found.
*   **`401 Unauthorized`**: Invalid API key.
*   **`403 Forbidden`**: IP address blocked.
*   **`429 Too Many Requests`**: Rate limit exceeded.

### Response Examples

==== Success Example (HTTP `200 OK`) ====
```json
{
    "status": "success",
    "response_code": "00",
    "user_data": {
        "nin": "12345678901",
        "first_name": "JOHN",
        "last_name": "DOE",
        "middle_name": "MICHAEL",
        "gender": "MALE",
        "date_of_birth": "15-05-1985",
        "phone_number": "08012345678",
        "address": "123 Sample Street, Lagos"
    },
    "message": "PDF generated successfully",
    "pdf_base64": "base64_encoded_data..."
}
```

==== Error Example (HTTP `400 Bad Request`) ====
```json
{
    "status": "error",
    "response_code": "01",
    "message": "Invalid NIN number provided",
    "error_code": "INVALID_NIN"
}
```

==== Record Not Found Example (HTTP `400 Bad Request`) ====
```json
{
    "status": "error",
    "response_code": "02",
    "message": "API Response: Record not found. The ID data you entered does not exist or may be incorrect. Please ensure all details are correct and resubmit"
}
```
