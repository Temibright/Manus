# 📘 ROBOST TECH / IPE Clearance API Documentation

Welcome to the **ROBOST TECH / IPE Clearance API** documentation. This document details the endpoints, authentication mechanisms, payloads, and response structures for the systems provided in this API collection.

---

## 🔑 Authentication
The endpoints require an API Key to be passed via headers for validation and secure access.
*   **Header Key:** `api-key`
*   **Header Value:** Check individual sections for the specific `api-key` values used (e.g., base64-encoded strings or SHA-256 hashes as shown in the examples).

---

## 📂 API Summary & Base URL
*   **Base URL:** `https://robosttech.com` (unless configured dynamically via dynamic environments).
*   **Content-Type:** `application/json` for all requests.

The API suite comprises 4 distinct flows:
1.  **Validation Flow:** Submit a National Identification Number (NIN) for validation and query its progress status.
2.  **Personalization Flow:** Submit personalization requests via Tracking IDs and check their completion status to retrieve profile info and documents.
3.  **NIN Identity Verification & Search:** Search and retrieve user verification data by NIN Number, associated Phone Number, or Demographic Data.
4.  **IPE Clearance Flow:** Submit and re-attempt internal clearance requests using unique Tracking IDs, and retrieve processing/approval statuses.

---

## 1. 🔍 Validation Flow

This flow allows you to validate an individual's identity by submitting their National Identification Number (NIN), and then checking the submission progress.

### 1.1 Submit NIN for Validation
Submit a NIN to trigger the validation workflow.

*   **Endpoint:** `POST /api/validation`
*   **Headers:**
    *   `Content-Type: application/json`
    *   `api-key: 6ebdc3cc61d82916a04758c3c6abe0105aa96ef9fe05d48b5013dd9677809cfe`
*   **Request Body:**
    ```json
    {
      "nin": " 18855414402"
    }
    ```
*   **Success Response (Code: `200 OK`):**
    ```json
    {
      "message": "Validation Submission Successfull",
      "approved": true,
      "category": "new",
      "success": true,
      "nin": " 18855414402"
    }
    ```

---

### 1.2 Check Validation Status
Checks the current progress state of a submitted validation.

*   **Endpoint:** `POST /api/validation_status`
*   **Headers:**
    *   `Content-Type: application/json`
    *   `api-key: 6ebdc3cc61d82916a04758c3c6abe0105aa96ef9fe05d48b5013dd9677809cfe`
*   **Request Body:**
    ```json
    {
      "nin": " 18855414402"
    }
    ```
*   **Response - Sent / Uploaded (Code: `200 OK`):**
    ```json
    {
      "message": "Uploaded",
      "status": "sent",
      "success": false,
      "in-progress": true
    }
    ```
*   **Response - Processing (Code: `200 OK`):**
    ```json
    {
      "message": "Uploaded",
      "status": "processing",
      "success": false,
      "in-progress": true
    }
    ```

---

## 2. 🆔 Personalization Flow

Simulate submitting and retrieving personalized credentials based on a tracking ID.

### 2.1 Submit Personalization
Triggers personalization processing using a unique Tracking ID.

*   **Endpoint:** `POST /api/personalization`
*   **Headers:**
    *   `Content-Type: application/json`
    *   `api-key: 6ebdc3cc61d82916a04758c3c6abe0105aa96ef9fe05d48b5013dd9677809cfe`
*   **Request Body:**
    ```json
    {
      "tracking_id": " CKW49TGENXXXXXX"
    }
    ```
*   **Success Response (Code: `200 OK`):**
    ```json
    {
      "message": "Personalization Submission Successfull",
      "approved": true,
      "category": "to_get_slip",
      "success": true,
      "tracking_id": " CKW49TGENXXXXXX"
    }
    ```

---

### 2.2 Check Personalization Status
Polls the backend to determine if personalization has been completed and, if so, returns demographic data and the user's base64 encoded photo.

*   **Endpoint:** `POST /api/personalization_status`
*   **Headers:**
    *   `Content-Type: application/json`
    *   `api-key: 6ebdc3cc61d82916a04758c3c6abe0105aa96ef9fe05d48b5013dd9677809cfe`
*   **Request Body:**
    ```json
    {
      "tracking_id": "0SXXXXXXXXXXX"
    }
    ```
*   **Response - Processing State (Code: `200 OK`):**
    ```json
    {
      "message": "processing",
      "status": "picked",
      "success": false,
      "in-progress": true
    }
    ```
*   **Response - Completed State (Code: `200 OK`):**
    ```json
    {
      "message": "Personalization Successfull",
      "personalized": true,
      "success": true,
      "status": "completed",
      "data": {
        "firstName": "AMILA",
        "middleName": null,
        "lastName": "ABDULLAHI",
        "dateOfBirth": "01-01-19XX",
        "gender": "FEMALE",
        "idNumber": "35XXXXXXXXX",
        "tracking_id": "XXXXXXXXX",
        "photo": "/9j/4AAQSkZJRgABAQEAYABgAAD//X... [TRUNCATED_BASE64_IMAGE_STRING]",
        "firstname": "JAMILA",
        "surname": "ABDULLAHI",
        "middlename": "",
        "birthdate": "01-01-19XX",
        "residence_lga": "",
        "residence_state": "",
        "residence_AdressLine1": "",
        "residence_addr": "",
        "residence_address": "",
        "residence_Town": "",
        "self_origin_state": "",
        "self_origin_lga": "",
        "telephoneno": "",
        "email": "",
        "maritalstatus": "",
        "religion": "",
        "nin": "35XXXXXXXXX",
        "NIN": "35XXXXXXXXX",
        "birthcountry": "",
        "heigth": "170"
      },
      "tracking_id": "XXXXXXXXX",
      "reply": "Successfull"
    }
    ```

---

## 3. 👤 NIN Identity Verification & Search

A search API suite allowing validation and retrieval of user registry profiles using different parameters (NIN, Phone Number, or Demographics).

### 3.1 Search by NIN Number
Verifies identity using the NIN.

*   **Endpoint:** `POST /api/nin_verify`
*   **Headers:**
    *   `Content-Type: application/json`
    *   `api-key: dd1cd7d9e00b3565cbf8410f4662226d93f71daf18b904811cd98dcfd4296868`
*   **Request Body:**
    ```json
    {
      "nin": " 18855414402"
    }
    ```
*   **Success Response (Code: `200 OK`):**
    ```json
    {
      "message": "Verification Successfull",
      "success": true,
      "data": {
        "batchid": "****",
        "birthcountry": "****",
        "birthdate": "27-11-2007",
        "birthlga": "****",
        "birthstate": "****",
        "cardstatus": "****",
        "centralID": "103084397",
        "educationallevel": "****",
        "email": "****",
        "emplymentstatus": "****",
        "firstname": "ALIYU",
        "gender": "m",
        "heigth": "****",
        "maritalstatus": "***",
        "pmiddlename": "****",
        "profession": "****",
        "psurname": "****",
        "religion": "****",
        "residence_AdressLine1": "KAN KUYAWA WARD MASHI",
        "residence_Town": "MASHI",
        "residence_lga": "Mashi",
        "residence_state": "Katsina",
        "residencestatus": "****",
        "self_origin_state": "Katsina",
        "signature": "***",
        "surname": "BALA",
        "telephoneno": "08032364684",
        "title": "****",
        "trackingId": "CKW49TGEN0001D4"
      }
    }
    ```

---

### 3.2 Search by NIN Phone Number
Verifies identity using the telephone number.

*   **Endpoint:** `POST /api/nin_phone`
*   **Headers:**
    *   `Content-Type: application/json`
    *   `api-key: dd1cd7d9e00b3565cbf8410f4662226d93f71daf18b904811cd98dcfd4296868`
*   **Request Body:**
    ```json
    {
      "phone": "01234567891"
    }
    ```
*   **Success Response (Code: `200 OK`):**
    ```json
    {
      "message": "Verification Successfull",
      "success": true,
      "data": {
        "id": "6857ea799d047676d66e8b4a",
        "city": null,
        "parentId": null,
        "status": "found",
        "reason": null,
        "dataValidation": false,
        "selfieValidation": false,
        "signature": null,
        "birthState": null,
        "nokState": "",
        "birthLGA": null,
        "isConsent": true,
        "businessId": "666904f5b042e67180425d37",
        "type": "nin",
        "mobile": "01234567891",
        "firstName": "JOHN",
        "middleName": "EMEKA",
        "lastName": "OKORO",
        "dateOfBirth": "2009-01-01",
        "gender": "male"
      }
    }
    ```

---

### 3.3 Search by Demographic Data
Verifies identity by combining personal descriptive details.

*   **Endpoint:** `POST /api/nin_demo`
*   **Headers:**
    *   `Content-Type: application/json`
    *   `api-key: dd1cd7d9e00b3565cbf8410f4662226d93f71daf18b904811cd98dcfd4296868`
*   **Request Body:**
    ```json
    {
      "firstname": "JOHN",
      "lastname": "EMEKA",
      "middlename": "",
      "gender": "female",
      "dateOfBirth": "2009-01-01"
    }
    ```
*   **Success Response (Code: `200 OK`):**
    ```json
    {
      "message": "Verification Successfull",
      "success": true,
      "data": {
        "batchid": "*****",
        "birthcountry": "****",
        "birthdate": "01-01-2009",
        "birthlga": "****",
        "birthstate": "****",
        "cardstatus": "****",
        "centralID": "86315298",
        "educationallevel": "****",
        "email": "****",
        "emplymentstatus": "****",
        "firstname": "WASILA",
        "gender": "f",
        "heigth": "****",
        "maritalstatus": "****",
        "pmiddlename": "****",
        "profession": "****",
        "psurname": "****",
        "religion": "****",
        "residence_AdressLine1": "KAN KUYAWA WARD MASHI",
        "residence_Town": "MASHI",
        "residence_lga": "Mashi",
        "residence_state": "Katsina",
        "residencestatus": "****",
        "self_origin_state": "Katsina",
        "signature": "***",
        "surname": "BALA",
        "telephoneno": "08032364684",
        "title": "****",
        "trackingId": "CKW49TGEN0001D4"
      }
    }
    ```

---

## 4. 📘 IPE Clearance API

Designed to manage internal clearances, handle duplicates, rejection parameters, and verify processing outcomes based on individual unique `tracking_id` strings.

---

### 4.1 Submit IPE Clearance Request
Submits a brand new clearance process.

*   **Endpoint:** `POST /api/clearance`
*   **Headers:**
    *   `Content-Type: application/json`
    *   `api-key: YmJAeW9wbWFpbC5jb206NTY1NTc3ODc4ODg6MTc0NTM0MTU1MA==`
*   **Request Body:**
    ```json
    {
      "tracking_id": "0RQ6C5ASWFS36LR"
    }
    ```
*   **Success Behaviour (Code: `200 OK`):**
    Receives an HTTP 200 indicating receipt. The clearance status can be checked within a few minutes.

---

### 4.2 Submit IPE Request Failed (Duplicate Existing)
Triggered when attempting to submit a duplicate `tracking_id` that is already registered or cleared.

*   **Endpoint:** `POST /api/clearance`
*   **Headers:**
    *   `Content-Type: application/json`
    *   `api-key: YmJAeW9wbWFpbC5jb206NTY1NTc3ODc4ODg6MTc0NTM0MTU1MA==`
*   **Request Body:**
    ```json
    {
      "tracking_id": "0RQ6C5ASWFS36LS"
    }
    ```
*   **Error Response (Code: `400 Bad Request`):**
    ```json
    {
      "success": false,
      "tracking_id": "0RQ6C5ASWFS36LS",
      "message": "Already Exist",
      "exist": true
    }
    ```

---

### 4.3 Submit IPE Request Failed (Previous Rejection)
Triggered when the clearance cannot proceed because a previous reattempt resulted in a rejection.

*   **Endpoint:** `POST /api/clearance`
*   **Headers:**
    *   `Content-Type: application/json`
    *   `api-key: YmJAeW9wbWFpbC5jb206NTY1NTc3ODc4ODg6MTc0NTM0MTU1MA==`
*   **Request Body:**
    ```json
    {
      "tracking_id": "0SN5NFZSGDEGZ0N"
    }
    ```
*   **Error Response (Code: `400 Bad Request`):**
    ```json
    {
      "success": false,
      "tracking_id": "0SN5NFZSGDEGZ0N",
      "message": "Previous Clearance Failed"
    }
    ```

---

### 4.4 Check IPE Clearance Status (Success Case)
Checks the outcome of a successfully processed clearance request.

*   **Endpoint:** `POST /api/clearance_status`
*   **Headers:**
    *   `Content-Type: application/json`
    *   `api-key: YmJAeW9wbWFpbC5jb206NTY1NTc3ODc4ODg6MTc0NTM0MTU1MA==`
*   **Request Body:**
    ```json
    {
      "tracking_id": "0RQ6C5ASWFS36LU"
    }
    ```
*   **Response (Code: `200 OK`):**
    ```json
    {
      "message": "Clearance Successfull",
      "cleared": true,
      "success": true,
      "status": "completed",
      "tracking_id": "0RQ6C5ASWFS36LU",
      "reply": "2GVZ0SI8KO000VK"
    }
    ```

---

### 4.5 Check IPE Clearance Status (Failed / Refunded Case)
Checks status for a request that failed internally, where a refund has already been issued.

*   **Endpoint:** `POST /api/clearance_status`
*   **Headers:**
    *   `Content-Type: application/json`
    *   `api-key: YmJAeW9wbWFpbC5jb206NTY1NTc3ODc4ODg6MTc0NTM0MTU1MA==`
*   **Request Body:**
    ```json
    {
      "tracking_id": "0SN5NFZSGDEGZ0N"
    }
    ```
*   **Response (Code: `200 OK`):**
    ```json
    {
      "message": "Clearance failed",
      "not_cleared": true,
      "success": false,
      "status": "failed",
      "tracking_id": "0SN5NFZSGDEGZ0N",
      "reply": "NO RECORD FOUND AND YOU ARE ALREADY REFUNDED(AND REFUNDED BACK)"
    }
    ```
