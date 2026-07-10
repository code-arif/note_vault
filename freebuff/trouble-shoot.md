# Freebuff Troubleshooting Guide (Windows)

> A complete troubleshooting guide for installing and running **Freebuff** on Windows.
>
> **Last Updated:** July 10, 2026

---

# Table of Contents

1. Overview
2. Common Issues
   - Issue 1: `spawn EPERM (EPERM)`
   - Issue 2: `TLS certificate verification failed`
3. Full Reset (Nuclear Option)
4. Environment Variables
5. Useful Commands
6. Prevention Tips

---

# Overview

Freebuff downloads its executable (`freebuff.exe`) into the following directory:

```text
C:\Users\<USERNAME>\.config\manicode\
```

Most Windows-related issues fall into one of two categories:

1. **Windows blocks the executable**
   - Windows Defender
   - Antivirus software
   - Permission issues

2. **Network blocks HTTPS requests**
   - Corporate proxy
   - School network
   - Self-signed certificates
   - SSL inspection

---

# Issue 1 — Failed to start Freebuff

## Error

```text
Failed to start freebuff:
spawn EPERM (EPERM)
```

Example:

```text
Download complete! Starting Freebuff...

Failed to start freebuff:
spawn EPERM (EPERM)
```

---

## Cause

This error usually means Windows prevented `freebuff.exe` from running.

Common reasons include:

- Windows Defender blocked the executable
- Antivirus quarantined the file
- The executable is missing
- Insufficient permissions
- Windows SmartScreen blocked execution
- Incorrect binary downloaded

---

## Solution 1 — Delete Cached Binary (Recommended)

Delete the cached executable and let Freebuff download it again.

```powershell
Remove-Item -Recurse -Force "$env:USERPROFILE\.config\manicode" -ErrorAction SilentlyContinue
```

Then run:

```powershell
freebuff
```

---

## Solution 2 — Run PowerShell as Administrator

1. Close PowerShell
2. Right-click **PowerShell**
3. Select **Run as Administrator**
4. Run:

```powershell
freebuff
```

---

## Solution 3 — Unblock the Executable

Navigate to:

```text
C:\Users\<USERNAME>\.config\manicode\
```

Then:

1. Right-click **freebuff.exe**
2. Click **Properties**
3. If an **Unblock** checkbox appears:
   - Check it
   - Click **Apply**
   - Click **OK**

---

## Solution 4 — Add Windows Defender Exclusion

Open:

```
Windows Security
```

Navigate to:

```
Virus & threat protection
    ↓
Manage settings
    ↓
Exclusions
    ↓
Add or remove exclusions
```

Add this folder:

```text
C:\Users\<USERNAME>\.config\manicode
```

This prevents Defender from quarantining `freebuff.exe`.

---

## Solution 5 — Use the Baseline Binary

If your CPU does not support the required instruction set, force Freebuff to use the baseline binary.

```powershell
$env:FREEBUFF_BINARY_TARGET="win32-x64-baseline"
```

Then run:

```powershell
freebuff
```

---

# Issue 2 — TLS Certificate Verification Failed

## Error

```text
TLS certificate verification failed
```

or

```text
self signed certificate in certificate chain
```

Example:

```text
POST https://freebuff.com/api/auth/cli/code

TLS certificate verification failed

self signed certificate in certificate chain
```

---

## Cause

Your network is intercepting HTTPS traffic.

This is common on:

- Company laptops
- Corporate VPNs
- School networks
- Enterprise firewalls
- SSL-inspecting proxies

Node.js does not trust the proxy's self-signed certificate by default.

---

## Solution 1 — Temporary Workaround

Disable TLS verification for the current PowerShell session.

```powershell
$env:NODE_TLS_REJECT_UNAUTHORIZED="0"
```

Then run:

```powershell
freebuff
```

> **Warning**
>
> This disables TLS verification.
>
> Use it only for testing or temporary troubleshooting.

---

## Solution 2 — Install the Corporate Root Certificate (Recommended)

### Step 1

Open:

```
https://freebuff.com
```

using Edge or Chrome.

---

### Step 2

Open the website certificate.

```
Lock Icon
    ↓
Connection is secure
    ↓
Certificate
```

---

### Step 3

Export the **Root Certificate**

Save it as:

```
Base-64 encoded X.509 (.CER)
```

---

### Step 4

Install the certificate.

1. Double-click the `.cer` file
2. Click **Install Certificate**
3. Choose:
   - Current User
   - (or Local Machine if Administrator)
4. Select:

```
Trusted Root Certification Authorities
```

5. Finish the installation.

---

### Step 5

Restart PowerShell.

Run:

```powershell
freebuff
```

---

## Solution 3 — Use Another Network

Sometimes the easiest solution is switching networks.

Examples:

- Mobile hotspot
- Home Wi-Fi
- Personal internet connection

---

## Solution 4 — Configure Proxy

If your company requires an HTTP proxy:

```powershell
$env:HTTP_PROXY="http://proxy-ip:port"
$env:HTTPS_PROXY="http://proxy-ip:port"
```

Then run:

```powershell
freebuff
```

---

# Full Reset (Nuclear Option)

If everything is broken, completely reset the local installation.

```powershell
Remove-Item -Recurse -Force "$env:USERPROFILE\.config\manicode" -ErrorAction SilentlyContinue

Remove-Item Env:FREEBUFF_BINARY_TARGET -ErrorAction SilentlyContinue

Remove-Item Env:NODE_TLS_REJECT_UNAUTHORIZED -ErrorAction SilentlyContinue

freebuff
```

This:

- Deletes cached binaries
- Removes custom environment variables
- Downloads a fresh copy

---

# Environment Variables

## Baseline Binary

```powershell
$env:FREEBUFF_BINARY_TARGET="win32-x64-baseline"
```

Purpose:

Force Freebuff to use the baseline binary.

---

## Disable TLS Verification

```powershell
$env:NODE_TLS_REJECT_UNAUTHORIZED="0"
```

Purpose:

Temporarily disable certificate validation.

> **Do not use permanently.**

---

## HTTP Proxy

```powershell
$env:HTTP_PROXY="http://proxy:port"
```

---

## HTTPS Proxy

```powershell
$env:HTTPS_PROXY="http://proxy:port"
```

---

# Useful Commands

## Delete Freebuff Cache

```powershell
Remove-Item -Recurse -Force "$env:USERPROFILE\.config\manicode"
```

---

## Run Freebuff

```powershell
freebuff
```

---

## Run Using Baseline Binary

```powershell
$env:FREEBUFF_BINARY_TARGET="win32-x64-baseline"
freebuff
```

---

## Disable TLS Verification

```powershell
$env:NODE_TLS_REJECT_UNAUTHORIZED="0"
freebuff
```

---

# Prevention Tips

- Keep Windows Defender updated.
- Add the Freebuff cache directory to Defender exclusions if repeated blocks occur.
- Run PowerShell as Administrator when troubleshooting.
- Avoid disabling TLS verification permanently.
- On corporate or school networks, install the organization's trusted root certificate instead of bypassing SSL verification.
- If problems persist, try a different network (e.g., a mobile hotspot) to rule out proxy or firewall issues.

---

# Troubleshooting Checklist

| Problem | Recommended Fix |
|----------|-----------------|
| `spawn EPERM (EPERM)` | Delete cache → Re-download → Run as Administrator |
| Defender blocks executable | Add Defender exclusion |
| SmartScreen blocks app | Unblock `freebuff.exe` in Properties |
| CPU compatibility issue | Use `FREEBUFF_BINARY_TARGET=win32-x64-baseline` |
| TLS verification failed | Install trusted root certificate |
| Self-signed certificate | Configure proxy or install corporate certificate |
| Still not working | Perform the Full Reset (Nuclear Option) |

---

# References

- Windows Security (Microsoft Defender)
- Node.js TLS Documentation
- Freebuff CLI Documentation (if available)

---

**Document Version:** 1.0  
**Created:** July 10, 2026  
**Platform:** Windows 10 / Windows 11  
**Shell:** PowerShell