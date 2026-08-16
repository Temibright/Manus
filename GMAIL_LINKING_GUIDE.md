# 📧 Gmail Connection & Identity Linking Guide

This guide explains how to connect Gmail successfully to your application for identity verification, NIN credential retrieval, and user account linking, based on the workflow depicted in the identity verification portal interface.

---

## 📌 Overview

When performing a NIN search or credential lookup in identity apps (e.g., DataVerify / NIMC identity portals), the system checks for credentials stored against the specified National Identification Number (NIN).

If the system returns:
> **`NO Credentials FOUND FOR <NIN_NUMBER>`**

It indicates that the NIN record is not yet linked to an authorized Google/Gmail profile, or the app requires OAuth authorization to fetch or sync identity tokens/credentials associated with the user's Gmail account.

---

## 📬 Will I Have Access to User Messages Once Gmail is Linked?

**Short Answer:** **Not by default.** Access to user emails depends entirely on the **OAuth Scopes** requested during authorization.

### 1. Basic Identity Linking (Default Setup)
By default, standard account linking requests basic profile scopes:
- `openid`
- `https://www.googleapis.com/auth/userinfo.email`
- `https://www.googleapis.com/auth/userinfo.profile`

**What you CAN access:**
- User's primary Gmail address.
- Google User ID.
- Profile name and profile photo.

**What you CANNOT access:**
- User's inbox or messages.
- Email contents, attachments, or search filters.

---

### 2. Reading Gmail Messages (Special Scope Setup)
If your app specifically needs to read incoming emails (e.g., searching for NIMC slip PDF attachments, verification codes, or official credential emails), you must explicitly request the Gmail API scope:
- `https://www.googleapis.com/auth/gmail.readonly` (Read-only access to emails)

#### Google Security & Verification Requirements for Gmail Scopes
Google classifies `gmail.readonly` as a **Restricted Scope**. To access user emails in production:
1. **OAuth Verification**: You must submit your app to Google for OAuth Verification via Google Cloud Console.
2. **CASA Security Assessment**: Google requires apps using restricted Gmail scopes to undergo an annual independent security audit (Cloud Application Security Assessment - CASA).
3. **Limited Use Compliance**: You must strictly adhere to Google's API Services User Data Policy (no selling email data, no human reading of emails except with consent for security/support).

#### Code Example: Requesting Gmail Read Scope
```javascript
const scopes = [
    "https://www.googleapis.com/auth/userinfo.email",
    "https://www.googleapis.com/auth/userinfo.profile",
    "https://www.googleapis.com/auth/gmail.readonly" // <--- Explicit scope for reading emails
].join(" ");
```

#### Code Example: Searching User Gmail Messages for Verification Emails (Node.js)
```javascript
const { google } = require('googleapis');

async function searchUserVerificationEmails(accessToken) {
    const auth = new google.auth.OAuth2();
    auth.setCredentials({ access_token: accessToken });

    const gmail = google.gmail({ version: 'v1', auth });

    // Search for emails matching query (e.g., from NIMC or containing NIN slip)
    const response = await gmail.users.messages.list({
        userId: 'me',
        q: 'from:nimc.gov.ng OR "NIN Slip" OR "Identity Clearance"'
    });

    const messages = response.data.messages || [];
    console.log(`Found ${messages.length} matching messages.`);

    if (messages.length > 0) {
        // Retrieve full message content for the first match
        const msg = await gmail.users.messages.get({
            userId: 'me',
            id: messages[0].id
        });
        return msg.data;
    }

    return null;
}
```

---

## 🔘 UI Action Buttons Explained

| Button | Technical Mechanism | Primary Use Case |
| :--- | :--- | :--- |
| **Link Gmail** | Standard Web OAuth 2.0 Authorization Flow | Redirects user to Google OAuth login in browser to grant account permissions. |
| **Link Gmail from My Device** | Native Google Credential Manager / One Tap SDK | Selects an existing Google/Gmail account logged into the user's mobile device without re-entering credentials. |
| **Copy Link** | Copies Generated Authorization URL to Clipboard | Useful for cross-device authentication, sharing to external browser, or in-app WebViews. |

---

## 🚀 Step-by-Step Implementation Guide

### Step 1: Google Cloud Console Configuration

To connect Gmail to your app, you must register your application in the Google Cloud Console.

1. **Create a Project**:
   - Go to [Google Cloud Console](https://console.cloud.google.com/).
   - Click **Select a project** > **New Project**, name it (e.g., `Identity-NIN-Verify`), and click **Create**.

2. **Configure OAuth Consent Screen**:
   - Navigate to **APIs & Services** > **OAuth consent screen**.
   - Select **User Type** (`External` for public users, `Internal` for organization members).
   - Fill in App Information (App Name, User Support Email, Developer Contact Info).
   - Add required Scopes:
     - `openid`
     - `https://www.googleapis.com/auth/userinfo.email`
     - `https://www.googleapis.com/auth/userinfo.profile`
     - *(Optional for email reading)* `https://www.googleapis.com/auth/gmail.readonly`
   - Save and set Publishing Status to **In production** (or add Test Users if in testing phase).

3. **Create Credentials**:
   - Navigate to **APIs & Services** > **Credentials** > **Create Credentials** > **OAuth client ID**.
   - **For Web ("Link Gmail" & "Copy Link")**:
     - Application Type: **Web application**.
     - Authorized JavaScript origins: `https://your-domain.com` (or `http://localhost:3000` for dev).
     - Authorized redirect URIs: `https://your-domain.com/api/auth/google/callback`.
   - **For Mobile/Android ("Link Gmail from My Device")**:
     - Application Type: **Android**.
     - Package Name: e.g. `com.yourcompany.identityapp`.
     - SHA-1 Certificate Fingerprint: Obtained via `./gradlew signingReport` or keytool.

---

### Step 2: Implementing "Link Gmail" (Web OAuth 2.0 Flow)

#### Frontend (JavaScript / React / Web)
Generate the authorization redirect URL when the user clicks **Link Gmail**.

```javascript
const GOOGLE_CLIENT_ID = "YOUR_GOOGLE_CLIENT_ID.apps.googleusercontent.com";
const REDIRECT_URI = "https://your-domain.com/api/auth/google/callback";

function handleLinkGmail(ninNumber) {
    const scopes = [
        "https://www.googleapis.com/auth/userinfo.email",
        "https://www.googleapis.com/auth/userinfo.profile",
        "openid"
    ].join(" ");

    // Include state parameter to retain NIN context across redirect
    const state = encodeURIComponent(JSON.stringify({ nin: ninNumber, action: "link_gmail" }));

    const authUrl = `https://accounts.google.com/o/oauth2/v2/auth?` +
        `client_id=${GOOGLE_CLIENT_ID}` +
        `&redirect_uri=${encodeURIComponent(REDIRECT_URI)}` +
        `&response_type=code` +
        `&scope=${encodeURIComponent(scopes)}` +
        `&state=${state}` +
        `&access_type=offline` +
        `&prompt=consent`;

    window.location.href = authUrl;
}
```

---

### Step 3: Implementing "Link Gmail from My Device" (Native / One Tap)

For seamless device authentication on mobile devices or Google One-Tap enabled web apps, use Google Identity Services (GIS).

#### Web / Android Integration Example (Google Identity Services)
```html
<script src="https://accounts.google.com/gsi/client" async defer></script>

<script>
function initializeGoogleDeviceAuth(ninNumber) {
    google.accounts.id.initialize({
        client_id: "YOUR_GOOGLE_CLIENT_ID.apps.googleusercontent.com",
        callback: (response) => handleCredentialResponse(response, ninNumber),
        auto_select: false
    });

    // Triggers the native Google Account chooser modal on device
    google.accounts.id.prompt((notification) => {
        if (notification.isNotDisplayed() || notification.isSkippedMoment()) {
            // Fallback to standard web redirect if One-Tap is dismissed/unavailable
            handleLinkGmail(ninNumber);
        }
    });
}

function handleCredentialResponse(response, ninNumber) {
    // response.credential contains the ID Token (JWT) signed by Google
    fetch('/api/auth/google/native-token', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
            id_token: response.credential,
            nin: ninNumber
        })
    })
    .then(res => res.json())
    .then(data => {
        if (data.success) {
            alert("Gmail successfully linked to NIN profile!");
            window.location.reload();
        } else {
            alert("Linking failed: " + data.message);
        }
    });
}
</script>
```

---

### Step 4: Implementing "Copy Link"

Allow users to copy the generated OAuth authentication link to share or open in an external browser.

```javascript
function handleCopyLink(ninNumber) {
    const scopes = [
        "https://www.googleapis.com/auth/userinfo.email",
        "https://www.googleapis.com/auth/userinfo.profile",
        "openid"
    ].join(" ");

    const state = encodeURIComponent(JSON.stringify({ nin: ninNumber, action: "link_gmail" }));

    const authUrl = `https://accounts.google.com/o/oauth2/v2/auth?` +
        `client_id=${GOOGLE_CLIENT_ID}` +
        `&redirect_uri=${encodeURIComponent(REDIRECT_URI)}` +
        `&response_type=code` +
        `&scope=${encodeURIComponent(scopes)}` +
        `&state=${state}` +
        `&access_type=offline` +
        `&prompt=consent`;

    navigator.clipboard.writeText(authUrl).then(() => {
        alert("Authentication link copied to clipboard!");
    }).catch(err => {
        console.error("Failed to copy link: ", err);
    });
}
```

---

### Step 5: Backend Callback & Account Linking

#### Node.js / Express Callback Handler
```javascript
const express = require('express');
const axios = require('axios');
const app = express();

app.get('/api/auth/google/callback', async (req, res) => {
    const { code, state } = req.query;
    const { nin } = JSON.parse(decodeURIComponent(state || '{}'));

    try {
        // 1. Exchange authorization code for tokens
        const tokenResponse = await axios.post('https://oauth2.googleapis.com/token', {
            code,
            client_id: process.env.GOOGLE_CLIENT_ID,
            client_secret: process.env.GOOGLE_CLIENT_SECRET,
            redirect_uri: process.env.GOOGLE_REDIRECT_URI,
            grant_type: 'authorization_code'
        });

        const { access_token, id_token } = tokenResponse.data;

        // 2. Retrieve user info from Google
        const userResponse = await axios.get('https://www.googleapis.com/oauth2/v2/userinfo', {
            headers: { Authorization: `Bearer ${access_token}` }
        });

        const userEmail = userResponse.data.email;
        const googleUserId = userResponse.data.id;

        // 3. Link Gmail address to NIN profile in Database
        await saveGmailNinAssociation({
            nin: nin,
            email: userEmail,
            googleUserId: googleUserId,
            accessToken: access_token
        });

        // 4. Redirect user back to portal with success parameter
        res.redirect(`/nin-verify?nin=${nin}&gmail_linked=true`);
    } catch (error) {
        console.error("Error linking Gmail:", error.response?.data || error.message);
        res.redirect(`/nin-verify?nin=${nin}&error=gmail_link_failed`);
    }
});
```

---

## 🛠️ Troubleshooting & Frequently Asked Questions

### 1. Why do I see `NO Credentials FOUND FOR <NIN>`?
- The specified NIN has not been attached to a Gmail address in the system database yet.
- Connecting Gmail binds the email identity to the NIN, enabling automated credential generation, verification receipts, and identity document access.

### 2. Error: `redirect_uri_mismatch`
- **Cause**: The `redirect_uri` parameter sent in the OAuth request does not match the exact authorized redirect URI configured in Google Cloud Console.
- **Solution**: Check protocol (`http` vs `https`), host (`localhost` vs domain), port, and trailing slashes. Make sure `https://your-domain.com/api/auth/google/callback` matches character-for-character.

### 3. Error: `Access blocked: Authorization Error / App not verified`
- **Cause**: The app is in "Testing" mode in Google Cloud Console, and the signing user is not added to the list of Test Users.
- **Solution**: Either add the user email under **OAuth consent screen > Test users**, or publish the app to Production mode.

### 4. WebView issues on mobile devices
- Google blocks OAuth requests inside embedded WebViews for security reasons (`disallowed_useragent`).
- Use **Google Sign-In / Credential Manager SDK** natively or launch an external system browser (Custom Tabs) when using "Link Gmail from My Device".
