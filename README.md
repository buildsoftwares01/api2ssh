# 🚀 API2SSH

Automate SSH command execution across your network devices and servers — for **health checks, backups, provisioning, configuration audits and more** — effortlessly.  

Want to build **network operation workflows** on platforms like **n8n** using triggers from **Zabbix, Grafana, Nagios, Cacti, or PRTG** — but need interactive SSH support? **API2SSH** is your bridge.

**API2SSH** is a REST-to-SSH server that turns HTTP requests into **real, interactive SSH sessions**. Connect your automation platform directly to CLI-based devices — no complex session handling required.  

## ⚡ Community Edition
- Limited to **3 devices**  
- Intended for **testing, evaluation, and small production networks**  

---

## 💼 Enterprise Edition
- **Multi-device support** for large networks  
- Consulting and custom solutions available through a **university-affiliated program**  
- For inquiries and sales, contact: **buildsoftwares01@gmail.com**

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
2. **Configure a port on which API2SSH can run** and enter your Fernet encryption key (optional, more details in the Security section):

<div align="center">
<img width="572" height="395" alt="Screenshot 2026-02-19 at 12 02 37" src="https://github.com/user-attachments/assets/4cca87b1-cea0-4f93-8993-3321932f414c" />
</div>

3. **Send API requests** to `http://localhost:PORT/api/api2ssh`. I am using Postman for demonstration:

<div align="center">
<img width="541" height="511" alt="Screenshot 2026-02-19 at 12 09 12" src="https://github.com/user-attachments/assets/4c559691-6496-4131-92e6-86f111e494ec" />
</div>

4. **The API Request will start an SSH Shell session** on the specified router IP address and execute the series of commands given. You will obtain an API response with each command's output:

<div align="center">
<img width="533" height="477" alt="Screenshot 2026-02-19 at 12 10 35" src="https://github.com/user-attachments/assets/faf61287-a300-4135-b5e8-882d98ffc003" />
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

### API Request
#### Option 1: Using key-based SSH Authentication:

```json
{
  "request_id": "optional-unique-id",        // OPTIONAL
  "router_ip": "192.168.1.1",                // REQUIRED
  "ssh_port": 22,                            // REQUIRED (default: 22)
  "username": "admin",                       // REQUIRED
  "ssh_login_method": "key-based",           // Required: "password-based" or "key-based"
  "custom_ssh_key_path": "/path/to/key",     // OPTIONAL: private key file (for key-based auth)
  "commands": [                              // REQUIRED (should not be empty)
    {
      "command": "terminal length 0",        // REQUIRED
      "expected_end": "Router1>",            // OPTIONAL: prompt indicating completion
      "command_timeout": 10                  // OPTIONAL per-command timeout (overrides top-level)
    },
    {
      "command": "show interfaces",
      "expected_end": "Router1>",
      "command_timeout": 30
    }
  ]
}
```

#### Option 2: Using password-based SSH Authentication:
```json
{
  "request_id": "optional-unique-id",        // OPTIONAL
  "router_ip": "192.168.1.1",                // REQUIRED
  "ssh_port": 22,                            // OPTIONAL (default: 22)
  "username": "admin",                       // REQUIRED
  "ssh_login_method": "password-based",      // REQUIRED: "password-based" or "key-based"
  "password": "password1",                   // REQUIRED for password-based auth
  "password_encryption": "true",             // OPTIONAL: "true" or "false" (applies to password-based auth)
  "commands": [                              // REQUIRED (should not be empty)
    {
      "command": "terminal length 0",        // REQUIRED
      "expected_end": "Router1>",            // OPTIONAL: prompt indicating completion
      "command_timeout": 10                  // OPTIONAL per-command timeout (overrides top-level)
    },
    {
      "command": "show interfaces",
      "expected_end": "Router1>",
      "command_timeout": 30
    }
  ]
}
```


### Request Parameters

| Parameter | Type | Required | Notes |
|-----------|------|----------|-------|
| `request_id` | string | | Optional tracking identifier |
| `router_ip` | string | ✓ | Target device IP address |
| `ssh_port` | int | | SSH port (default: 22) |
| `username` | string | required for password-based auth | SSH username |
| `ssh_login_method` | string | | Authentication method: `password-based` or `key-based` (default: `password-based`) |
| `password` | string | required for password-based auth | SSH password (encrypted or plaintext depending on `password_encryption`) |
| `password_encryption` | string | | When using password-based auth, set to `true` to decrypt the provided password, or `false` to use plaintext. Values are the strings `"true"` or `"false"`. |
| `custom_ssh_key_path` | string | optional for key-based auth | Path to a private key file used when `ssh_login_method` is `key-based`. |
| `commands` | array | ✓ | List of commands to execute (see below) |

### Command Object

Each command in the `commands` array has:

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| `command` | string | ✓ | CLI command to execute |
| `expected_end` | string | | Prompt pattern indicating completion (e.g., `Router1>`) |
| `command_timeout` | int | | Per-command timeout 

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

### Method 1- SSH Key-Based Authentication

API2SSH supports secure SSH key-based authentication. Configure key-based access between the machine running API2SSH and the target devices, then set `ssh_login_method` to `key-based` in your API request.

By default, the system key path is used. If you need to specify a different private key, provide `custom_ssh_key_path` with the full path to the key file.

Ensure the corresponding public key is present in the remote device’s `authorized_keys` file with proper permissions, otherwise authentication will fail.

---

### Method 2-Fernet-Based Password Encryption

We strongly recommend using key-based authentication but also provide an optional Fernet encryption for password-based authentication:

- Passwords are decrypted using a **Fernet key** before SSH connection
- Decryption happens **in memory only**
- Passwords are never logged or stored on disk
- You control the encryption key

### Password Modes

**Encrypted Mode (Recommended)**
- Passwords are encrypted with Fernet
- Provide Fernet key at startup
- Set `password_encryption: false` (default)

**Plaintext Mode**
- Set `password_encryption: true` in request
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

During startup, paste your **Fernet key** when prompted:

The server can now decrypt passwords in-memory before connecting to devices.

---

## 🔄 Workflow Summary

```
1. Generate Key    → CipherGenKey → Fernet Key
                                        ↓
2. Encrypt Password → CipherEncrypter → Encrypted Password
                                        ↓
3. Start App       → API2SSHdemo → Use Key + Encrypted Password
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

One of the main objective of this tool to to enable interactive SSH Shell command execution on automation platforms such as n8n. 
Sample n8n workflows:

### API2SSH-based n8n Workflow to run multiline script (uploaded to GitHub repository):

<img width="873" height="207" alt="Screenshot 2026-02-16 at 22 08 09" src="https://github.com/user-attachments/assets/0c132015-2f9f-42cc-a878-493dca26ecaf" />

### API2SSH-based n8n Workflow to run daily automatic backup of routers (available in commercial package):

<img width="1497" height="747" alt="Automatic Backup" src="https://github.com/user-attachments/assets/fea9a885-e917-449a-9add-d01d88661e1f" />
