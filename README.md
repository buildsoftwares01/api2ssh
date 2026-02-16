# 🚀 API2SSH

API2SSH is a CLI-based server application that converts structured HTTP requests into real interactive SSH sessions with network devices and servers.

Most automation platforms support **only non-interactive SSH**.  
Most on-device operations require prompt awareness, timing control and interactive session handling.  

API2SSH bridges that gap 🙂  

**Turn structured API calls into deterministic SSH automation.**  

This **GitHub version is a demo build**, limited to **2 devices orchestration**, so you can test the workflow and integration model before moving to production.

👉 The **full multi-device production version** is available on [Gumroad](https://buildsoftwares01.gumroad.com/l/api2ssh).

---

## 🔥 What Is Included

- ✅ Interactive SSH session handling 🙂  
- ✅ Prompt-aware command completion detection  
- ✅ Sequential command execution  
- ✅ Structured JSON responses  
- ✅ Per-command timeout protection  
- ✅ REST-to-CLI bridge for automation platforms
- ✅ Fernet-based Security

---

## 🔌 How It Works

Send a POST request to:
```json
/api/api2ssh
```

The service will:
1. Validate the request  
2. Open an interactive SSH shell  
3. Execute commands sequentially  
4. Detect command completion using prompt matching  
5. Return structured JSON output  

Clean. Deterministic. Automation-ready 🙂

A screenshot of the CLI app running, waiting for API calls:
<img width="687" height="555" alt="Screenshot 2026-02-16 at 21 27 53" src="https://github.com/user-attachments/assets/8f3e693c-fd37-4a2b-b67f-63ced6a5c2e9" />

---

## 📦 API Request Structure

### Example JSON

```json
{
"router_ip": "192.168.1.1",                  // REQUIRED
"commands": [                               // REQUIRED (should not be empty)
{
"command": "show ip route",          // REQUIRED
"expected_end": "Router1>",          // OPTIONAL
"command_timeout": 60                // OPTIONAL per-command timeout (overrides top-level)
},
{
"command": "show interfaces",
"expected_end": "Router1>",          // OPTIONAL
"command_timeout": 30                // OPTIONAL per-command timeout
}
],

"username": "admin",                        // REQUIRED if ssh_login_method == "2"
"password": "password",                     // REQUIRED if ssh_login_method == "2"

"ssh_port": 22,                             // OPTIONAL (default: 22)
"request_id": "optional-unique-id",         // OPTIONAL
"disable_password_encryption": false        // OPTIONAL (default: encryption enabled)
}
```
## 🧠 Parameters

- **router_ip** → Target device IP  
- **ssh_port** → SSH port (default 22)  
- **username / password** →  
  - Either defined globally at startup  
  - Or passed dynamically in each request  
- **commands** → Ordered list of CLI commands with below parameters:
  - command** → Command to be executed  
  - expected_end** → Prompt pattern to detect command completion  
  - commands_timeout** → Timeout per command (seconds)  
- **request_id** → Optional tracking identifier
- **disable_password_encryption** → Disable password encryption (use for plaintext password in API)


## 📤 Response Format
```json
{
  "results": [
    {
      "command": "show ip route",
      "output": "....device output...."
    }
  ],
  "request_id": "optional-tracking-id"
}
```
Each command returns structured output ready for automation workflows 🙂

## 🔐 Password Encryption & Security

API2SSH supports optional Fernet-based password encryption.

---

### 🔑 How It Works

- When encryption is enabled at startup, passwords are decrypted using a **Fernet key** before initiating the SSH session.
- Decryption happens **in memory only**.
- Passwords are never logged or stored on disk.

---

### ⚙️ Modes

#### Encrypted Mode (Recommended)

- Provide encrypted password values
- Provide Fernet key at application startup
- `disable_password_encryption` must be `false` (default)

#### Plaintext Mode

- Set `"disable_password_encryption": true` in the API request
- Password will be used as-is
- Recommended only for trusted internal environments

---

### 🛡 Security Recommendations

- Run API2SSH behind a firewall or reverse proxy
- Use HTTPS (TLS) when exposing the API externally
- Avoid plaintext mode in production
- Protect your Fernet encryption key securely
- Rotate credentials regularly

---
## ⚠️ Demo Limitations

This GitHub edition has all the commercial version's features and i onlys **limited to testing and evaluation** on 2 devices:

👉 Get the commercial version on [Gumroad](https://buildsoftwares01.gumroad.com/l/api2ssh) if you plan to:
- Run parallel automation across routers  
- Integrate into orchestration platforms  
- Deploy in production environments  

---

## 🎯 Built For

- Network automation workflows  
- REST-based orchestration platforms  
- MSP device fleets  
- Engineers modernizing legacy CLI environments  

---

