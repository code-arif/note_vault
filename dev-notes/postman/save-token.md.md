# Postman — Save Token Automatically

### Purpose
Shortcut script to save the token to the environment if the login is successful.

---

### Script
```javascript
let res = pm.response.json();

if (res.success === true && res.data && res.data.token) {
    let token = res.data.token;
    pm.environment.set("token", token);
    console.log("Token saved to environment:", token);
} else {
    console.log("Token not found in response");
}

```

---

### The response must be in this format.
```json
{
    "success": true,
    "message": "Login successful",
    "data": {
        "tenant": {
            "id": 17,
            "email": "ab1@gmail.com",
            "status": "active",
            "profile": {
                "id": 9,
                "tenant_id": 17,
                "first_name": "John",
                "middle_name": "A.",
                "last_name": "Deo",
                "phone": "1234567890",
                "country_code": null,
                "created_at": "2026-01-08T04:03:17.000000Z",
                "updated_at": "2026-01-08T04:03:17.000000Z"
            }
        },
        "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL29jX3dvcmtmb3JjZV9sYXJhdmVsLnRlc3QvYXBpL3YxL3RlbmFudC9sb2dpbiIsImlhdCI6MTc2Nzg0NTU5NiwiZXhwIjoxNzcwNDM3NTk2LCJuYmYiOjE3Njc4NDU1OTYsImp0aSI6Ilh4VHpvR2lpdGFpNHNZSWoiLCJzdWIiOiIxNyIsInBydiI6IjQzMjYzMzc1ZjdmZmQ2YTJjZTVmMzhiZTkzOGZkMTJlM2YwNzlmYWUifQ.mhkdqcFIAskKPneg6TtZbc2_nvO3HP7wK3EPDaCrY-oOnly of testing !!!",
        "token_type": "Bearer"
    },
    "code": 200
}
```