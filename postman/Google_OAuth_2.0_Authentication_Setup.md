# Google OAuth 2.0 Authentication Setup (Laravel + Postman)

## Overview

This document explains how to configure Google OAuth 2.0 authentication for API testing using Postman and Google Cloud Console.

---

## Prerequisites

* Google Cloud Account
* Laravel Application
* Postman Desktop App
* OAuth 2.0 Client Credentials

---

## Step 1: Create OAuth Credentials

1. Open Google Cloud Console.
2. Navigate to:

```text
APIs & Services → Credentials
```

3. Click:

```text
Create Credentials → OAuth Client ID
```

4. Select:

```text
Web Application
```

5. Provide an application name.

---

## Step 2: Configure Authorized Redirect URI

When using Postman OAuth Authentication, add the following redirect URI:

```text
https://oauth.pstmn.io/v1/browser-callback
```

Go to:

```text
Google Cloud Console
→ APIs & Services
→ Credentials
→ OAuth Client
→ Authorized Redirect URIs
```

Add:

```text
https://oauth.pstmn.io/v1/browser-callback
```

⚠️ Important:

Google requires an exact match.

The following are considered different:

```text
https://oauth.pstmn.io/v1/browser-callback
https://oauth.pstmn.io/v1/callback
```

A mismatch will cause:

```text
Error 400: redirect_uri_mismatch
```

---

## Step 3: Configure OAuth Consent Screen

Navigate to:

```text
APIs & Services → OAuth Consent Screen
```

Configure:

* Application Name
* Support Email
* Developer Contact Email

Add scopes:

```text
openid
email
profile
```

Publish the application if required.

---

## Step 4: Configure Postman

Open Authorization tab.

Configuration:

```text
Grant Type:
Authorization Code

Callback URL:
https://oauth.pstmn.io/v1/browser-callback

Auth URL:
https://accounts.google.com/o/oauth2/v2/auth

Access Token URL:
https://oauth2.googleapis.com/token

Scope:
openid email profile

Client Authentication:
Send as Basic Auth Header
```

Provide:

```text
Client ID
Client Secret
```

---

## Step 5: Request Access Token

Click:

```text
Get New Access Token
```

Authenticate using your Google account.

Upon successful authentication:

```text
Access Token Generated Successfully
```

Use:

```text
Authorization: Bearer {access_token}
```

for API requests.

---

## Common Errors

### Error 400: redirect_uri_mismatch

Cause:

```text
Configured redirect URI does not match
the URI sent by Postman.
```

Solution:

Verify that Google Cloud Console contains:

```text
https://oauth.pstmn.io/v1/browser-callback
```

exactly.

---

### Access Blocked

Cause:

```text
OAuth Consent Screen is incomplete
or application is not configured properly.
```

Solution:

Complete the OAuth Consent Screen setup and verify test users.

---

## Testing

Successful flow:

```text
Postman
→ Google Login
→ Consent Screen
→ Access Token
→ API Request
→ Authorized Response
```

---

## References

Google OAuth 2.0 Documentation:
https://developers.google.com/identity/protocols/oauth2

Postman OAuth Documentation:
https://learning.postman.com/docs/sending-requests/authorization/oauth-20/
