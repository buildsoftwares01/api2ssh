# 🚀 API2SSH

**Turn API calls into deterministic SSH automation for platforms like n8n, Zapier, and Make.**

API2SSH is a REST-to-SSH bridge server that turns HTTP requests into real, interactive SSH sessions for network devices and servers. Connect automation platforms directly to CLI-based devices without dealing with complex SSH session management.  

Most automation platforms handle only non-interactive SSH, but many operations require **prompt awareness**, **timing control**, and **interactive session handling**. API2SSH solves this problem.

> **Community Edition**: Limited to 3 devices for small production networks, testing and evaluation.  

👉 Contact **buildsoftwares01@gmail.com** for the **full multi-device Enterprise version**.  
👉 Enterprise sales, consulting, and custom solutions are also available via a university-affiliated program. Reach out at **buildsoftwares01@gmail.com**.

---

## ✨ Features

- ✅ **Prompt-aware** command completion detection  
- ✅ **Sequential** command execution  
- ✅ **Per-command** timeout protection  
- ✅ **REST-to-CLI** bridge for automation platforms  
- ✅ **Fernet-based** password encryption

## 🏗️ Architecture

<img width="604" height="359" alt="Screenshot 2026-02-17 at 20 58 44" src="https://github.com/user-attachments/assets/7dfcd6d2-db8d-4626-8457-d22f929ceca8" />


**Flow:**
1. Client sends HTTP POST with commands and router IP
2. API2SSH validates request and decrypts password (if encrypted)
3. Opens interactive SSH session to router
4. Executes commands sequentially with prompt detection
5. Returns structured JSON with outputs

---

## 🚀 Quick Start

1. **Download and Run the executable** from the [Releases section](https://github.com/buildsoftwares01/api2ssh/releases) and wait a few seconds for startup
2. **Configure SSH credentials**:
   - Choose between common credentials (for all devices) or per-request credentials (different passwords per API call)
   - Set the initialization parameters in the CLI-based server application:

<div align="center">
<img width="618" height="451" alt="Screenshot 2026-02-17 at 17 59 41" src="https://github.com/user-attachments/assets/ebb03f2f-e110-4fff-960d-6154d9ba6514" />
</div>

3. **Send API requests** to `http://localhost:PORT/api/api2ssh`. I am using Postman here:

<div align="center">
<img width="602" height="550" alt="Screenshot 2026-02-17 at 18 04 24" src="https://github.com/user-attachments/assets/a3739c11-cbb7-4969-b7c9-6a4cc05fb6e0" />
</div>

4. **The API Request will start and SSH Shell session** on the input IP address and execute the series of commands input. You will obtain an API response with the commands' output:

<div align="center">
<img width="605" height="410" alt="Screenshot 2026-02-17 at 18 06 22" src="https://github.com/user-attachments/assets/ea837c0a-d612-411c-ab0c-a9bf9fb89b72" />
</div>

---

## 📡 API Endpoint

```
POST /api/api2ssh
```

The service will:
1. Validate the request
2. Open an interactive SSH shell
3. Execute commands sequentially
4. Detect completion using prompt matching
5. Return structured JSON response

---

## 📋 API Request

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

### Request Parameters

| Parameter | Type | Required | Notes |
|-----------|------|----------|-------|
| `router_ip` | string | ✓ | Target device IP address |
| `commands` | array | ✓ | List of commands to execute (see below) |
| `username` | string | if mode=2 | SSH username (required in dynamic credentials mode) |
| `password` | string | if mode=2 | SSH password (encrypted or plaintext) |
| `ssh_port` | int | | SSH port (default: 22) |
| `request_id` | string | | Optional tracking identifier |
| `disable_password_encryption` | bool | | Skip decryption for this request (default: false) |
| `commands_timeout` | int | | Global timeout per command in seconds (default: 120) |

### Command Object

Each command in the `commands` array has:

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| `command` | string | ✓ | CLI command to execute |
| `expected_end` | string | | Prompt pattern indicating completion (e.g., `Router1>`) |
| `command_timeout` | int | | Per-command timeout (overrides global timeout)  

### API Response

```json
{
  "results": [
    {
      "command": "show ip route",
      "output": "...command output..."
    },
    {
      "command": "show interfaces",
      "output": "...command output..."
    }
  ],
  "router_ip": "192.168.1.1",
  "request_id": "optional-tracking-id"
}
```

---

## 🔐 Security

### Fernet-Based Password Encryption

API2SSH supports optional Fernet encryption for passwords:

- Passwords are decrypted using a **Fernet key** before SSH connection
- Decryption happens **in memory only**
- Passwords are never logged or stored on disk
- You control the encryption key

### Password Modes

**Encrypted Mode (Recommended)**
- Passwords are encrypted with Fernet
- Provide Fernet key at startup
- Set `disable_password_encryption: false` (default)

**Plaintext Mode**
- Set `disable_password_encryption: true` in request
- Password used as-is
- Only for trusted internal environments

### Best Practices

- ✅ Run behind a firewall or reverse proxy
- ✅ Use HTTPS/TLS when exposing externally
- ✅ Enable Fernet encryption in production
- ✅ Protect encryption keys securely
- ✅ Rotate credentials regularly
- ❌ Avoid plaintext mode in production

### Key Generation & Password Encryption

**Step 1: Generate a Fernet Encryption Key**

Run the key generator executable:
```bash
CipherGenKey.exe
```

Output:
```
Your New Fernet Encryption Key :
<your-long-key-string>
```

**Save this key securely** — you'll need it every time you start API2SSH. Do not save on same server where API2SSH will run.

---

**Step 2: Encrypt Your SSH Password**

Run the cipher encrypter executable:
```bash
CipherEncrypter.exe
```

You'll be prompted for:
1. **Encryption Key** → Paste the key generated in Step 1
2. **Secret to Encrypt** → Your SSH password

Output:
```
Encrypted: b'gAAAAABl...<encrypted-password>...'
```

**Copy the entire encrypted string** (excluding the `b'...'` wrapper).

---

**Step 3: Start API2SSH with Encrypted Credentials**

Run the main API2SSH server:
```bash
API2SSH Demo vx- Windows.exe
or
API2SSH Demo vx- MACOS-arm64
```

During startup:
1. Enter the **port number**
2. Choose **SSH Login Method** (1 or 2)
3. Select **"Y"** when asked about encryption
4. Paste your **Fernet key** from Step 1
5. Paste your **encrypted password** from Step 2

The server will now decrypt passwords in-memory before connecting to devices.

---

## 🔄 Workflow Summary

```
1. Generate Key    → CipherGenKey → Fernet Key
                                        ↓
2. Encrypt Password → CipherEncrypter → Encrypted Password
                                        ↓
3. Start App       → api2sshdemo → Use Key + Encrypted Password
                                        ↓
4. Make API Calls  → Send requests with router IP and commands
```

---

## 💡 Use Cases

- **Network Automation**: Automate router/switch configuration
- **Orchestration Platforms**: n8n, Zapier, Make integration
- **MSP Operations**: Manage multiple customer devices
- **Legacy CLI Modernization**: REST API for CLI-only devices


## 🔌 n8n Workflow Templates

One of the main objective of this tool to to enable interactive SSH Shell command execution on n8n. 
Sample n8n workflows:

### API2SSH-based n8n Workflow to run multiline script (uploaded to GitHub repository):

<img width="873" height="207" alt="Screenshot 2026-02-16 at 22 08 09" src="https://github.com/user-attachments/assets/0c132015-2f9f-42cc-a878-493dca26ecaf" />

### API2SSH-based n8n Workflow to run daily automatic backup of routers (available in commercial package):

<img width="1497" height="747" alt="Automatic Backup" src="https://github.com/user-attachments/assets/fea9a885-e917-449a-9add-d01d88661e1f" />