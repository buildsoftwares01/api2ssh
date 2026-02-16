# 🚀 API2SSH

API2SSH is a CLI-based server that turns HTTP requests into real interactive SSH sessions for network devices and servers.  

Most automation platforms handle only non-interactive SSH, but many operations require prompt awareness, timing control, and interactive session handling. API2SSH bridges that gap.  

**Turn API calls into deterministic SSH automation.**  

This **GitHub demo** is limited to **2 devices** for testing and evaluation.  

👉 Contact **buildsoftwares01@gmail.com** for the **full multi-device production version**.  
👉 Enterprise sales, consulting, and custom solutions are also available via a university-affiliated program. Reach out at **buildsoftwares01@gmail.com**.

---

## 🔥 What Is Included

- ✅ Prompt-aware command completion detection  
- ✅ Sequential command execution  
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

---

## 📦 API Request Structure

### Example JSON

```json
{
"request_id": "optional-unique-id",           // OPTIONAL
"router_ip": "192.168.1.1",                   // REQUIRED
"username": "admin",                          // REQUIRED if ssh_login_method == "2"
"password": "password1",                      // REQUIRED if ssh_login_method == "2"
"ssh_port": 22,                               // OPTIONAL (default: 22)
"disable_password_encryption": false,         // OPTIONAL (default: encryption enabled)
"commands": [                                 // REQUIRED (should not be empty)
{
"command": "terminal length 0",      // REQUIRED
"expected_end": "Router1>",          // OPTIONAL
"command_timeout": 10                // OPTIONAL per-command timeout (overrides top-level)
},
{
"command": "show interfaces",
"expected_end": "Router1>",          // OPTIONAL
"command_timeout": 30                // OPTIONAL per-command timeout
}
]
}
```
## 🧠 Parameters

- **request_id** → Optional tracking identifier
- **router_ip** → Target device IP  
- **ssh_port** → SSH port (default 22)  
- **username / password** →  
  - Either defined globally at startup  
  - Or passed dynamically in each request
- **disable_password_encryption** → Disable password encryption (use for plaintext password in API)
- **commands** → Ordered list of CLI commands with below parameters:
  - command** → Command to be executed  
  - expected_end** → Prompt pattern to detect command completion  
  - commands_timeout** → Timeout per command (seconds)  

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


## 🔌 Gallery

CLI-based server application, waiting for API calls:

<img width="687" height="555" alt="Screenshot 2026-02-16 at 21 27 53" src="https://github.com/user-attachments/assets/8f3e693c-fd37-4a2b-b67f-63ced6a5c2e9" />

API2SSH-based n8n Workflow to run multiline script:

<img width="873" height="207" alt="Screenshot 2026-02-16 at 22 08 09" src="https://github.com/user-attachments/assets/0c132015-2f9f-42cc-a878-493dca26ecaf" />

API2SSH-based n8n Workflow to run daily automatic backup of routers (available in commercial package):

<img width="1497" height="747" alt="Automatic Backup" src="https://github.com/user-attachments/assets/fea9a885-e917-449a-9add-d01d88661e1f" />

## 🔐 Password Encryption & Security

API2SSH supports optional Fernet-based password encryption.

---


### 🔑 How Are Your Passwords Secured

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

## 🎯 Built For

- Network automation workflows  
- REST-based orchestration platforms  
- MSP device fleets  
- Engineers modernizing legacy CLI environments  

---
