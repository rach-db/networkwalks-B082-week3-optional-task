# HexStrike MCP + AI-Assisted JTR Password Cracking Lab

A hands-on cybersecurity lab completed in Kali Linux using **Claude Desktop, HexStrike-AI MCP, and John the Ripper (JTR)**.

This project was completed as part of a practical cybersecurity exercise from Networkwalks Academy. The work was divided into two connected parts: first setting up HexStrike MCP with Claude Desktop, and then using that environment for a controlled PDF password-cracking exercise.

> **Note:** This project was performed in a controlled lab environment for educational and cybersecurity training purposes.

---

## Project Overview

The project demonstrates how an AI assistant can interact with local cybersecurity tools through the **Model Context Protocol (MCP)**.

The overall workflow was:

```text
Kali Linux
   ↓
Claude Desktop
   ↓
HexStrike-AI MCP Server
   ↓
John the Ripper
   ↓
PDF Hash Extraction
   ↓
rockyou.txt
   ↓
Password Recovery
```

The two lab manuals are directly connected. The first establishes the HexStrike MCP environment, while the second uses that environment for the JTR exercise.

---

## Objectives

- Set up Claude Desktop on Kali Linux.
- Install and configure HexStrike-AI MCP.
- Connect HexStrike-AI to Claude Desktop.
- Verify that the MCP server is operational.
- Check the John the Ripper installation.
- Work with a password-protected PDF in the lab environment.
- Use a standard password wordlist with JTR.
- Observe how Claude can interact with the available cybersecurity tools through HexStrike MCP.
- Document the successful password recovery.

---

## Lab Environment

| Component | Details |
|---|---|
| Operating System | Kali Linux |
| AI Assistant | Claude Desktop |
| MCP Server | HexStrike-AI |
| Password Cracking Tool | John the Ripper |
| Wordlist | rockyou.txt |
| Target | Password-protected PDF |
| MCP Endpoint | `http://localhost:8888` |

---

## Part 1 — HexStrike MCP Setup

The first part of the lab focused on setting up the HexStrike MCP server and connecting it to Claude Desktop.

### 1. Clone HexStrike-AI

```bash
git clone https://github.com/0x4m4/hexstrike-ai.git
cd hexstrike-ai
```

### 2. Create the Python virtual environment

```bash
python3 -m venv hexstrike-env
source hexstrike-env/bin/activate
```

### 3. Install dependencies

```bash
pip3 install -r requirements.txt
```

During the initial setup, the dependency installation encountered a temporary-space issue while building `angr`/`pyvex`. The Kali VM itself had sufficient disk space, so the build was redirected to a directory on the main filesystem.

```bash
mkdir -p ~/pip-tmp
export TMPDIR=$HOME/pip-tmp
```

The dependencies were then installed successfully.

### 4. Start the HexStrike server

```bash
cd ~/hexstrike-ai
source hexstrike-env/bin/activate
python3 hexstrike_server.py
```

The server was successfully started and made available locally.

---

## Part 2 — Claude Desktop MCP Configuration

Claude Desktop was configured to communicate with HexStrike using the following MCP configuration:

```json
{
  "mcpServers": {
    "hexstrike-ai": {
      "command": "/home/kali/hexstrike-ai/hexstrike-env/bin/python",
      "args": [
        "/home/kali/hexstrike-ai/hexstrike_mcp.py",
        "--server",
        "http://localhost:8888"
      ]
    }
  }
}
```

The configuration was placed in:

```text
~/.config/Claude/claude_desktop_config.json
```

After restarting Claude Desktop, HexStrike became available as a local MCP server.

---

## Verification

A health check was performed through Claude Desktop.

The HexStrike MCP server reported:

- Status: **Healthy and operational**
- Essential tools: **8/8 available**
- Version: **6.0.0**
- Tools available: **60/127**
- Password category: **4/5 tools available**

This confirmed that Claude Desktop was successfully communicating with the HexStrike MCP server.

---

## Part 3 — John the Ripper Verification

John the Ripper was checked directly from Kali Linux.

```bash
john
```

The installed version was:

```text
John the Ripper 1.9.0-jumbo-1+bleeding-aec1328d6c
```

This confirmed that JTR was installed and executable.

---

## Part 4 — Target PDF

The target PDF used in the exercise was:

```text
/home/kali/Desktop/My Locked PDF1.pdf
```

The file was confirmed to exist and was readable:

```text
Size: 66K
```

The filename differs from the filename used in the original lab manual, so the actual local file path was used during the exercise.

---

## Part 5 — Wordlist

Kali contained the RockYou wordlist in compressed form:

```text
/usr/share/wordlists/rockyou.txt.gz
```

The wordlist was extracted for the lab:

```text
/usr/share/wordlists/rockyou.txt
```

This was the dictionary specified by the Networkwalks JTR exercise.

---

## Part 6 — Password Recovery

The PDF hash was extracted and John the Ripper was run using the RockYou wordlist through the HexStrike MCP workflow.

The successful result reported by Claude was:

```text
Hash extracted successfully.
Now running John the Ripper against it with rockyou.txt.

Cracked it

Password: password1
```

### Result

**Recovered password: `password1`**

This confirmed that the complete workflow was functioning:

```text
Claude Desktop
      ↓
HexStrike MCP
      ↓
PDF hash extraction
      ↓
John the Ripper
      ↓
rockyou.txt
      ↓
Password recovered
```

---

## Evidence

Recommended screenshots for the project repository:

### 1. HexStrike server running
Shows the HexStrike server successfully starting on Kali Linux.

### 2. Claude MCP configuration
Shows `hexstrike-ai` configured under Claude Desktop's local MCP servers.

### 3. HexStrike health check
Shows that the MCP server is healthy and that tools are available.

### 4. John the Ripper version
Shows the installed JTR version.

### 5. Target PDF
Shows the password-protected lab PDF in the Kali Desktop.

### 6. Successful password recovery
Shows the final Claude response confirming that JTR cracked the PDF password.

---

## What I Learned

This lab helped me understand how **AI, MCP, and traditional cybersecurity tools can work together**.

Some of the main things I learned were:

- How to set up an MCP server in Kali Linux.
- How Claude Desktop can communicate with local tools through MCP.
- How HexStrike acts as a bridge between the AI assistant and cybersecurity utilities.
- How John the Ripper is used for password recovery in a controlled environment.
- How wordlists such as RockYou are used during dictionary-based password testing.
- Why environment configuration and dependency management matter when setting up security tools.
- How to troubleshoot installation problems involving disk and temporary build space.

---

## Challenges Faced

### 1. Dependency installation failed

The initial installation of the HexStrike dependencies failed with:

```text
ERROR: [Errno 28] No space left on device
```

The error occurred while building dependencies related to `angr` and `pyvex`.

Although the main Kali filesystem had plenty of free space, `/tmp` was mounted as a small `tmpfs`. The build was therefore redirected to a directory on the main filesystem using:

```bash
mkdir -p ~/pip-tmp
export TMPDIR=$HOME/pip-tmp
```

This allowed the installation to complete.

### 2. Flask was initially missing

Because the dependency installation had stopped early, starting the server produced:

```text
ModuleNotFoundError: No module named 'flask'
```

After successfully completing the dependency installation, this issue was resolved.

### 3. Claude initially showed no MCP servers

The MCP configuration was initially placed in the wrong Claude configuration file. The correct MCP configuration file was identified as:

```text
~/.config/Claude/claude_desktop_config.json
```

After correcting the configuration and restarting Claude Desktop, HexStrike was successfully detected.

---

## Responsible Use

This project was performed as a controlled cybersecurity training exercise.

Password-cracking tools should only be used on systems, files, accounts, or data that you own or have explicit authorization to test. The techniques demonstrated here are intended for cybersecurity education, testing, and authorized security assessments.

---

## References

- Networkwalks Academy — *How to setup HexStrike MCP Server (Practice Lab)*
- Networkwalks Academy — *JTR Password Cracking Lab Module (AI-version)*
- HexStrike-AI project: `https://github.com/0x4m4/hexstrike-ai`

---

## Final Outcome

**HexStrike MCP + Claude Desktop + John the Ripper successfully worked together in the Kali Linux lab environment.**

The final test successfully recovered the password of the supplied lab PDF:

```text
password1
```

This project gave me practical experience with MCP-based AI tool integration, Linux troubleshooting, and password-security testing in a controlled environment.
---
## Author
Rachel Debbarma
