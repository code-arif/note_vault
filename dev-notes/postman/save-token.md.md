# Postman — Save Token Automatically

## Purpose

This note explains how to automatically save an authentication **token** from an API response in Postman.

When login is successful, the token will be saved in a Postman variable so it can be reused in other API requests.

This script should be placed inside the **Tests tab** of the login request.

---

# Example Login Response Format

Your API response **must follow this structure** for the script to work.

```json
{
    "success": true,
    "message": "Login successful",
    "data": {
        "tenant": {
            "id": 17,
            "email": "ab1@gmail.com",
            "status": "active"
        },
        "token": "your_generated_jwt_token_here",
        "token_type": "Bearer"
    },
    "code": 200
}
```

# 1️⃣ Save Token in Environment Variable

### Use this if your project has multiple environments such as

- local
- staging
- production

```json
let res = pm.response.json();

if (res.success === true && res.data && res.data.token) {
    let token = res.data.token;
    pm.environment.set("token", token);
    console.log("Token saved to environment:", token);
} else {
    console.log("Token not found in response");
}
```

# 2️⃣ Save Token in Collection Variable (Recommended for API Projects)

### If API requests are organized inside a Postman Collection, this is the best option.

```json
let res = pm.response.json();

if (res.success === true && res.data && res.data.token) {
    let token = res.data.token;
    pm.collectionVariables.set("token", token);
    console.log("Token saved to collection:", token);
} else {
    console.log("Token not found in response");
}
```

# 3️⃣ Save Token in Global Variable (Not Recommended)

### Global variables are shared across all workspaces and collections, so they may cause conflicts.
### Use only for quick testing.

```json
let res = pm.response.json();

if (res.success === true && res.data && res.data.token) {
    let token = res.data.token;
    pm.globals.set("token", token);
    console.log("Token saved to globals:", token);
} else {
    console.log("Token not found in response");
}
```
