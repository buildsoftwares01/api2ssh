🚀 API2SSH

API2SSH is a lightweight REST service that converts structured HTTP requests into real, interactive SSH sessions with network devices.

This GitHub version is a demo build, limited to 1 device orchestration, so you can test the workflow and integration model before moving to production.

👉 The full multi-device production version is available on Gumroad.

🔥 What This Demo Proves

Even in the limited edition, you get:

Interactive SSH session handling 🙂

Prompt-aware command completion detection

Sequential command execution

Structured JSON responses

Per-command timeout protection

REST-to-CLI bridge for automation platforms

This is not simple SSH execution — it handles real interactive sessions (prompts, sudo requests, CLI state changes).

🔌 How It Works

Send a POST request to:

/api/api2ssh


The service will:

Validate the request

Open an interactive SSH shell

Execute commands sequentially

Detect command completion using prompt matching

Return structured JSON output

Clean. Deterministic. Automation-ready 🙂

📦 API Request Structure
Example JSON
{
  "router_ip": "192.168.1.1",
  "ssh_port": 22,
  "username": "admin",
  "password": "password",
  "commands": [
    {
      "command": "show ip route",
      "expected_end": "Router1>"
    },
    {
      "command": "show interfaces",
      "expected_end": "Router1>"
    }
  ],
  "commands_timeout": 120,
  "request_id": "optional-tracking-id"
}

🧠 Parameters

router_ip → Target device IP

ssh_port → SSH port (default 22)

username / password →

Either defined globally at startup

Or passed dynamically in each request

commands → Ordered list of CLI commands

expected_end → Prompt pattern to detect command completion

commands_timeout → Timeout per command (seconds)

request_id → Optional tracking identifier

📤 Response Format
{
  "results": [
    {
      "command": "show ip route",
      "output": "....device output...."
    }
  ],
  "request_id": "optional-tracking-id"
}


Each command returns structured output ready for automation workflows 🙂

⚠️ Demo Limitations

This GitHub edition is restricted to:

✅ 1 allowed device

❌ No multi-device orchestration

❌ No production license management

It is intended for testing and evaluation only.

💼 Full Version (Gumroad)

The production edition includes:

Multi-device orchestration

Scalable device limits

License control system

Production-ready deployment model

Designed for MSPs and enterprise automation

If you plan to:

Run parallel automation across routers

Integrate into orchestration platforms

Deploy in production environments

👉 Get the full version on Gumroad.

🎯 Built For

Network automation workflows

REST-based orchestration platforms

MSP device fleets

Engineers modernizing legacy CLI environments

💡 Why API2SSH?

Most automation platforms support only non-interactive SSH.
Real devices require prompt awareness, timing control, and interactive session handling.

API2SSH bridges that gap predictably 🙂

Turn structured API calls into deterministic SSH automation.
Test it here. Scale it with the full version.
