# 🚀 API2SSH

API2SSH is a CLI-based server that turns HTTP requests into real interactive SSH sessions for network devices and servers.  

Most automation platforms handle only non-interactive SSH, but many operations require prompt awareness, timing control, and interactive session handling. API2SSH bridges that gap.  

**Turn API calls into deterministic SSH automation.**  

This **GitHub demo** is limited to **3 devices** for small production networks, testing and evaluation.  

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

## 📦 API Structure

### API Request JSON

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
### 🧠 Parameters

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

### 📤 API Response Format
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

## 🔌 How To Set Up
- **Step 1** → Download the latest version of API2SSH Demo for your OS from: https://github.com/buildsoftwares01/api2ssh/releases
- **Step 2** → Run the program and wait a few seconds for it to boot up.
- **Step 3** → Set the initialization parameters in the CLI-based server application. Refer to below screenshot:

<img width="618" height="451" alt="Screenshot 2026-02-17 at 17 59 41" src="https://github.com/user-attachments/assets/ebb03f2f-e110-4fff-960d-6154d9ba6514" />


- **Step 4** → Run your API POST Request to run commands to your device. Here I am using Postman to send an API Request:

<img width="602" height="550" alt="Screenshot 2026-02-17 at 18 04 24" src="https://github.com/user-attachments/assets/a3739c11-cbb7-4969-b7c9-6a4cc05fb6e0" />

- **Step 5** → The API Request will start and SSH Shell session on the input IP address and execute the series of commands input. You will obtain an API response with the commands' output:

<img width="605" height="410" alt="Screenshot 2026-02-17 at 18 06 22" src="https://github.com/user-attachments/assets/ea837c0a-d612-411c-ab0c-a9bf9fb89b72" />


## 🔌 Gallery


### API2SSH-based n8n Workflow to run multiline script (uploaded to GitHub repository):

<img width="873" height="207" alt="Screenshot 2026-02-16 at 22 08 09" src="https://github.com/user-attachments/assets/0c132015-2f9f-42cc-a878-493dca26ecaf" />

### API2SSH-based n8n Workflow to run daily automatic backup of routers (available in commercial package):

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
