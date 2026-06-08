# 🤖 AI-Powered Penetration Testing Agent

An autonomous multi-agent penetration testing system built on top of **OpenCode** with a custom **Ubuntu Terminal MCP Server**. The system uses free AI APIs (Groq + Gemini) to orchestrate intelligent, automated pentesting across recon, web vulnerability scanning, exploit research, and report generation.

> ⚠️ **For authorized penetration testing only.** Only use on systems you own or have explicit written permission to test.

---

## 🏗️ Architecture

```
You (natural language)
        ↓
  OpenCode (AI Brain)
  Groq Llama 3.1 70B / Gemini 1.5 Flash
        ↓
  ubuntu-terminal MCP Server
  (Custom Python MCP — 13 tools)
        ↓
  Ubuntu Terminal
  nmap · sqlmap · dalfox · ffuf · subfinder · whois · dig
        ↓
┌─────────────────────────────────────────┐
│         Python Specialist Agents        │
├─────────────────────────────────────────┤
│ 🔍 Recon Agent    → Llama 3.1 70B/Groq │
│ 🌐 Web Agent      → Llama 3.1 70B/Groq │
│ 💥 Exploit Agent  → Llama 3.1 70B/Groq │
│ 📝 Report Agent   → Gemini 1.5 Pro     │
└─────────────────────────────────────────┘
```

---

## 🧠 How It Works

This project is built around two core components:

### 1. Ubuntu Terminal MCP Server
A custom Python **Model Context Protocol (MCP)** server that gives OpenCode direct access to the Ubuntu terminal. OpenCode uses it to run pentesting tools in real time, read output, and chain commands intelligently.

**13 tools exposed via MCP:**
- `run_command` — execute any shell command
- `read_file` / `write_file` — file I/O
- `list_directory` / `find_files` — filesystem navigation
- `system_info` / `resource_usage` — system monitoring
- `list_processes` / `kill_process` — process management
- `make_directory` / `delete_path` / `copy_or_move` — file ops
- `get_env` — environment variables

### 2. Multi-Agent Python System
A Python-based agent system with a master orchestrator and four specialist agents, each powered by a different AI model and focused on a single phase of the pentest:

| Agent | Model | Specialization |
|---|---|---|
| 🧠 Orchestrator | Gemini 1.5 Pro | Planning, delegation, decision making |
| 🔍 Recon Agent | Llama 3.1 70B (Groq) | nmap, whois, DNS, subdomains |
| 🌐 Web Agent | Llama 3.1 70B (Groq) | XSS, SQLi, IDOR, headers, dirs |
| 💥 Exploit Agent | Llama 3.1 70B (Groq) | CVEs, exploits, payloads |
| 📝 Report Agent | Gemini 1.5 Pro | Professional pentest reports |

---

## 🚀 Features

- **Fully autonomous** — give it a target, it plans and executes the full pentest
- **Multi-agent orchestration** — each agent specializes in one phase
- **Real tool execution** — runs actual nmap, sqlmap, dalfox, ffuf on your machine
- **Structured findings** — all results saved as JSON between phases
- **Professional reports** — auto-generated markdown reports with severity ratings
- **100% free APIs** — Groq (unlimited free) + Gemini (free tier)
- **Privacy first** — all execution is local, nothing leaves your machine
- **OpenCode integration** — use natural language to drive the entire pentest

---

## 📁 Project Structure

```
pentest-agent/
├── main.py                  ← entry point, conversation loop
├── orchestrator.py          ← Gemini brain, master planner
├── agents/
│   ├── __init__.py
│   ├── recon_agent.py       ← Llama 70B, network recon
│   ├── web_agent.py         ← Llama 70B, web vuln scanning
│   ├── exploit_agent.py     ← Llama 70B, CVE & exploit research
│   └── report_agent.py      ← Gemini, report generation
├── tools/
│   └── __init__.py          ← placeholder for custom tools
├── memory/
│   ├── findings.json        ← live findings during engagement
│   └── playbook.md          ← custom techniques & methodology
└── .env                     ← API keys (never commit this)

mcp-servers/ubuntu-terminal/
├── server.py                ← MCP server (13 terminal tools)
├── install.sh               ← one-click installer
└── opencode.jsonc           ← OpenCode config template
```

---

## ⚙️ Requirements

- Ubuntu 20.04+
- Python 3.10+
- OpenCode installed
- Go 1.23+ (for Go-based tools)
- Free API keys: [Groq](https://console.groq.com) + [Google Gemini](https://aistudio.google.com)

### Pentesting Tools
```bash
# Core
sudo apt-get install -y nmap whois dnsutils sqlmap

# Go-based (install after Go 1.23+)
go install github.com/projectdiscovery/subfinder/v2/cmd/subfinder@latest
go install github.com/projectdiscovery/httpx/cmd/httpx@latest
go install github.com/hahwul/dalfox/v2@latest
go install github.com/ffuf/ffuf/v2@latest
```

---

## 🔧 Installation

### 1. Clone the repo
```bash
git clone https://github.com/yourusername/pentest-agent.git
cd pentest-agent
```

### 2. Install Python dependencies
```bash
pip3 install groq google-generativeai rich python-dotenv requests
```

### 3. Set up API keys
```bash
cp .env.example .env
nano .env
```
```env
GROQ_API_KEY=your_groq_key_here
GEMINI_API_KEY=your_gemini_key_here
```

### 4. Set up the MCP server
```bash
cd mcp-servers/ubuntu-terminal
bash install.sh
```

### 5. Connect to OpenCode
```bash
# Add to ~/.config/opencode/opencode.jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "ubuntu-terminal": {
      "type": "local",
      "command": ["python3", "/absolute/path/to/mcp-servers/ubuntu-terminal/server.py"],
      "enabled": true,
      "environment": {}
    }
  }
}
```

Then inside OpenCode:
```
/connect → Groq → paste your key
/model   → groq/llama-3.1-70b-versatile
```

---

## 💬 Usage

### Option A — Via OpenCode (Recommended)
```bash
cd ~/pentest-agent
opencode
```
Then prompt it naturally:
```
You are an elite pentester with terminal access.
Target: testphp.vulnweb.com

PHASE 1 - RECON:
Run nmap -sV -T4 testphp.vulnweb.com and whois testphp.vulnweb.com

Tell me everything you find.
```

### Option B — Via Python Agent Directly
```bash
cd ~/pentest-agent
python3 main.py
```
```
[pentest-agent]> pentest testphp.vulnweb.com
[pentest-agent]> recon testphp.vulnweb.com
[pentest-agent]> webscan testphp.vulnweb.com
[pentest-agent]> report
[pentest-agent]> findings
```

---

## 🔒 Safety & Ethics

- Only test systems you **own** or have **written permission** to test
- The MCP server blocks dangerous commands (`rm -rf /`, fork bombs, disk wipes)
- Protected system paths cannot be deleted via the MCP
- All findings are stored locally — nothing is sent to external servers
- API calls go only to Groq and Google (model inference only, not your target data)

---

## 🗺️ Roadmap

- [ ] Fine-tune Llama 8B on real XSS findings for ultra-specialized agent
- [ ] Add IDOR specialist agent
- [ ] Add SSRF specialist agent
- [ ] RAG system with CVE database
- [ ] Automatic screenshot capture of findings
- [ ] HTML report generation with charts
- [ ] Slack/Discord notifications for critical findings
- [ ] Docker container for portable deployment

---

## 🧰 Tech Stack

| Component | Technology |
|---|---|
| AI Orchestrator | Google Gemini 1.5 Pro |
| Specialist Agents | Groq Llama 3.1 70B |
| MCP Protocol | Python MCP SDK |
| Terminal Agent | OpenCode |
| Port Scanning | nmap |
| XSS Scanning | dalfox |
| SQLi Testing | sqlmap |
| Dir Brute Force | ffuf |
| Subdomain Enum | subfinder |
| HTTP Probing | httpx |
| Report Format | Markdown |

---

## 📖 Learning Resources

- [PortSwigger Web Security Academy](https://portswigger.net/web-security) — labs used to test this agent
- [MCP Protocol Docs](https://modelcontextprotocol.io) — how the terminal MCP works
- [OpenCode Docs](https://opencode.ai/docs) — the AI agent platform used
- [Groq API Docs](https://console.groq.com/docs) — free LLM inference
- [OWASP Testing Guide](https://owasp.org/www-project-web-security-testing-guide/) — methodology reference

---

## ⚠️ Disclaimer

This tool is developed for **educational purposes** and **authorized penetration testing** only. The author is not responsible for any misuse or damage caused by this tool. Always obtain proper written authorization before testing any system you do not own.
