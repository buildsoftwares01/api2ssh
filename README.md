# 🚀 API2SSH

No-code Network Automation starts here! API2SSH is the first building block in your network automation workflow. API2SSH easily connected to your favorite no-code automation platforms such as **n8n** to automate SSH command execution across your network devices and servers — for **health checks, backups, provisioning, configuration audits and more** — effortlessly.  

Want to build **network operation workflows** on platforms like **n8n** using triggers from **Zabbix, Grafana, Nagios, Cacti, or PRTG** — but need interactive SSH support? **API2SSH** is your bridge.

**API2SSH** is a REST-to-SSH server that turns HTTP requests into **real, interactive SSH sessions**. Connect your automation platform directly to CLI-based devices — no complex session handling required.  

---

## 🚀 Choose Your Edition

| Feature | Community | Pro |
|----------|------------|-------------|
| Device Limit | 3 devices | Unlimited |
| Intended Use | Testing & small networks | Production-scale networks |
| Interactive SSH | ✅ | ✅ |
| Workflow Automation | ✅ | ✅ |

### ⚡ Community Edition (Free)
Ideal for evaluation, labs, and small production setups.

### 💼 Pro Edition
Purchase on [KoFi](https://ko-fi.com/s/1eb20d3533) (Preferred) or on [Gumroad](https://buildsoftwares01.gumroad.com/l/api2ssh).

For queries or custom workflows, contact the **[developers](mailto:buildsoftwares01@gmail.com)**.

## ✨ Features
- ✅ **Direct SSH channel control** (no Netmiko dependency) for finer-grained session handling and custom logic
- ✅ **Prompt-aware** command completion detection
- ✅ **Sequential** command execution  
- ✅ **Per-command** timeout protection  
- ✅ **Supports** both key-based and password-based SSH authentication 

---

## 🔌 Projects Powered by API2SSH

### 1. AI-based Interface Configuration Audit:

Let AI automatically check all your interface configurations and alert you in case of anomalies.

![Bundle- Ai-based Network Audit](https://github.com/user-attachments/assets/bb47f0be-5aad-4601-ae7d-f9a00ed75420)

### 2. Daily Automatic Device Backup:

Schedule a daily automatic backup of all routers and switches.

<img width="1497" height="747" alt="Automatic Backup" src="https://github.com/user-attachments/assets/fea9a885-e917-449a-9add-d01d88661e1f" />

### 3. IP Address Management (IPAM):

Obtain better visibility on your complete network.

<img width="903" height="450" alt="Bundle- NetSearch Homepage" src="https://github.com/user-attachments/assets/56199d96-8c39-45c4-870b-8a4c7912742e" />

Search any resource (e.g search for IP addresses):

<img width="1402" height="991" alt="Bundle- NetSearch IPaddress Results" src="https://github.com/user-attachments/assets/bddb6a39-93fe-47c4-addf-8d1b6a633d35" />

View complete network's resources in a single table:

<img width="1872" height="851" alt="Bundle- IPAM" src="https://github.com/user-attachments/assets/3058e07a-97bc-427d-8f6a-62179cfbbe4d" />

View all Interfaces' state:

<img width="1868" height="861" alt="Bundle- Interface Status" src="https://github.com/user-attachments/assets/c010f713-7aba-4ac8-8a3c-89e885538b9b" />


## Disclaimer

The workflows for all the projects listed below are available in the **`Workflow`** directory within this repository.

These workflows are currently configured for a **specific device model** that has been tested.

If you intend to use them with **other device models**, you will need to make the following adjustments:

- **Update the Webhook nodes** that call **API2SSH** to modify the commands according to your device model.
- **Modify the JavaScript Code nodes** to adapt the response parsing logic so it matches your device’s output format.

> ⚠️ **Note:** Command syntax and output structures vary between vendors and device models, so some customization will be required.

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

