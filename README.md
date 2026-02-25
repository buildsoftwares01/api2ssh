# 🚀 API2SSH

## 📑 Quick Links

- [Community Edition](#-community-edition)
- [Enterprise Edition](#-enterprise-edition)
- [Features](#-features)
- [Sample n8n Workflow](#-sample-n8n-workflow-enabled-by-api2ssh)
- [Quick Start](#-quick-start)
- [Architecture](#-architecture)
- [API Endpoint](#-api-endpoint)
- [API Request](#api-request)
- [Request Parameters](#request-parameters)
- [Command Object](#command-object)
- [API Response](#api-response)
- [Security](#-security)

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
- For inquiries and sales, [Contact Sales](mailto:buildsoftwares01@gmail.com)

---

## ✨ Features
- ✅ **Direct SSH channel control** (no Netmiko dependency) for finer-grained session handling and custom logic
- ✅ **Prompt-aware** command completion detection
- ✅ **Sequential** command execution  
- ✅ **Per-command** timeout protection  
- ✅ **Supports** both key-based and password-based SSH authentication 

---

## 🔌 Sample n8n Workflow Enabled By API2SSH

### 1. Run Multiline Scripts On Devices (e.g for provisioning) (uploaded to GitHub repository):

<img width="873" height="207" alt="Screenshot 2026-02-16 at 22 08 09" src="https://github.com/user-attachments/assets/0c132015-2f9f-42cc-a878-493dca26ecaf" />

### 2. Run Daily Automatic Backup of Routers (available in Enterprise package):

<img width="1497" height="747" alt="Automatic Backup" src="https://github.com/user-attachments/assets/fea9a885-e917-449a-9add-d01d88661e1f" />

### 3. Build a live table of network resources (available in Enterprise package):
<img width="1422" height="736" alt="Workflow Webhook- Table" src="https://github.com/user-attachments/assets/f5f73540-0288-414a-a732-cf1f7d123e66" />


Example Resources Table (Interfaces):
<img width="1915" height="963" alt="Webhook- Table" src="https://github.com/user-attachments/assets/b96ce871-40fb-4d1b-bb4d-3e7d65407299" />


---

## 🚀 Quick Start

1. **Download and Run the latest executable** from the [Releases section](https://github.com/buildsoftwares01/api2ssh/releases) and wait a few seconds for startup

2. **Send API requests** to `http://localhost:PORT/api/api2ssh`. I am using Postman for demonstration:

<div align="center">
<img width="541" height="511" alt="Screenshot 2026-02-19 at 12 09 12" src="https://github.com/user-attachments/assets/4c559691-6496-4131-92e6-86f111e494ec" />
</div>

3. **The API Request will start an SSH Shell session** on the specified router IP address and execute the series of commands given. You will obtain an API response with each command's output:

<div align="center">
<img width="533" height="477" alt="Screenshot 2026-02-19 at 12 10 35" src="https://github.com/user-attachments/assets/faf61287-a300-4135-b5e8-882d98ffc003" />
</div>

---

## 🏗️ Architecture

<img width="604" height="359" alt="Screenshot 2026-02-17 at 20 58 44" src="https://github.com/user-attachments/assets/7dfcd6d2-db8d-4626-8457-d22f929ceca8" />


**Flow:**
1. Client sends HTTP POST with commands and router IP
2. Opens interactive SSH session to router
3. Executes commands sequentially with prompt detection
4. Returns structured JSON with outputs

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
      "command_timeout": 10,                 // OPTIONAL per-command timeout (overrides top-level)
      "stop_on_timeout": "yes"             // OPTIONAL: "yes" or "no" (case-insensitive). Default: "yes" (stop on timeout)
    },
    {
      "command": "show interfaces",
      "expected_end": "Router1>",
      "command_timeout": 30,
      "stop_on_timeout": "yes"
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
  "commands": [                              // REQUIRED (should not be empty)
    {
      "command": "terminal length 0",        // REQUIRED
      "expected_end": "Router1>",            // OPTIONAL: prompt indicating completion
      "command_timeout": 10,                 // OPTIONAL per-command timeout (overrides top-level)
      "stop_on_timeout": "yes"             // OPTIONAL: "yes" or "no" (case-insensitive). Default: "yes" (stop on timeout)
    },
    {
      "command": "show interfaces",
      "expected_end": "Router1>",
      "command_timeout": 30,
      "stop_on_timeout": "yes"
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
| `custom_ssh_key_path` | string | optional for key-based auth | Path to a private key file used when `ssh_login_method` is `key-based`. |
| `commands` | array | ✓ | List of commands to execute (see below) |

### Command Object

Each command in the `commands` array has:

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| `command` | string | ✓ | CLI command to execute |
| `expected_end` | string | | Prompt pattern indicating completion (e.g., `Router1>`) |
| `command_timeout` | int | | Per-command timeout |
| `stop_on_timeout` | string | | Abort Execution if failure |

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

### SSH Key-Based Authentication

API2SSH supports secure SSH key-based authentication. Configure key-based access between the machine running API2SSH and the target devices, then set `ssh_login_method` to `key-based` in your API request.

By default, the system key path is used. If you need to specify a different private key, provide `custom_ssh_key_path` with the full path to the key file.

Ensure the corresponding public key is present in the remote device’s `authorized_keys` file with proper permissions, otherwise authentication will fail.

---

