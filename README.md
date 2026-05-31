# 🔥 Evil Scan - Web Security Framework

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.8+-blue.svg" alt="Python">
  <img src="https://img.shields.io/badge/License-MIT-green.svg" alt="License">
  <img src="https://img.shields.io/badge/Status-Active-brightgreen.svg" alt="Status">
  <img src="https://img.shields.io/badge/Platform-Kali%20Linux-lightgrey.svg" alt="Platform">
</p>

<p align="center">
  <img src="" alt="Evil Scan Banner" width="600">
</p>

<p align="center">
  <strong>Advanced web security testing and exploitation framework</strong> for security professionals and penetration testers.
</p>

<p align="center">
  <a href="#-features">Features</a> •
  <a href="#-installation">Installation</a> •
  <a href="#-quick-start">Quick Start</a> •
  <a href="#-reports">Reports</a> •
  <a href="#-contributing">Contributing</a>
</p>

---

## 📋 Table of Contents

- [Description](#-description)
- [Features](#-features)
- [Prerequisites](#-prerequisites)
- [Installation](#-installation)
- [Quick Start](#-quick-start)
- [Main Menu](#-main-menu)
- [Reports](#-reports)
- [Advanced Configuration](#-advanced-configuration)
- [Troubleshooting](#-troubleshooting)
- [Contributing](#-contributing)
- [License](#-license)
- [Disclaimer](#-️-disclaimer)

---

## 📝 Description

**Evil Scan** is an **advanced and interactive** web penetration testing tool that implements best practices for web security testing and [OWASP API Top 10](https://owasp.org/API-Security/).

Designed for bug bounty hunters and penetration testers, it automates common reconnaissance, analysis, and web penetration testing tasks:
- 🕷️ Complete and exhaustive web application mapping (web spidering with form and input detection)
- 🔬 **Source code analysis** of exposed pages and JS scripts (credentials, API keys, JWT, PEM keys, sensitive comments)
- 🛡️ Vulnerability analysis with **Nuclei** (10,000+ templates)
- 🔍 Fast directory fuzzing with **ffuf** (wordlist pre-filtering + anti-false positive baseline)
- 💉 Advanced injection testing (SQLi, XSS, LFI, RCE, Open Redirect)
- 🔌 API detection and testing (IDOR, Mass Assignment, GraphQL, JWT, CORS)
- 🔍 **Port scanning with Nmap** (`-sV` for service and version detection)
- 🌐 **Subdomain fuzzing (vhost)** with `ffuf` and `Content-Length` baseline
- 🧩 **WordPress / WPScan** – user enumeration, vulnerable plugin/theme detection and targeted attacks
- 🏛️ **Active Directory Penetration Testing** – Kerbrute, LDAP, NetExec (nxc) and Impacket (AS-REP Roasting and Kerberoasting)
- 👤 User and email enumeration
- 🔐 Brute force with **hydra** + CSRF-aware fallback and **automatic error message detection**
- 📊 Reports in **TXT, JSON, Markdown and HTML** (SaaS dashboard with light/dark theme, exportable to PDF)

---

## ⭐ Features

### 🔐 Authentication
- **Pre-authentication** – Automatic login with credentials (Basic Auth or form)
- **Persistent session** – All subsequent tests use the authenticated session
- Cookie handling and hidden fields (CSRF tokens)

### 🔎 Reconnaissance
- **General information** – Server, headers, cookies, SSL/TLS, HTTP methods, robots.txt / sitemap.xml
- **Technology detection** – Integration with `whatweb` (auto-installation) with header fallback
- **Security header analysis** – HSTS, CSP, X-Frame-Options, X-Content-Type-Options, Referrer-Policy
- **Cookie security** – Flags `Secure`, `HttpOnly`, `SameSite`
- **Advanced CORS** – Wildcard + Credentials, reflected origin, preflight with malicious origins

### 🔍 Port Scanning with Nmap
- Executes `nmap -sV` on the target host (extracted from URL)
- **Auto-installation of nmap** via `apt` if not present
- Robust **XML** parsing (`-oX -`): extracts port, protocol, state, service, product, version and `extrainfo`
- Visual table with colors by state (open / open|filtered) on scan completion
- Each open port is registered in `FINDINGS` with `[PORT]` prefix and grouped in dedicated category in reports
- Configurable timeout (600s by default), interruptible with Ctrl+C

### 🛡️ Vulnerability Analysis with Nuclei
- **Auto-installation of Nuclei** via `apt` if not present
- Support for `-jsonl-export` (current format) with `-json-export` fallback
- **Automatic deduplication** by `(template_id, url, severity)`
- **Summary table** by severity and **list of relevant findings** (critical/high/medium/low)
- Interactsh backend noise (`Could not unmarshal interaction data`) filtered
- Results integrated directly in reports (TXT/JSON/HTML)

### 🕷️ Web Spidering
- Configurable BFS crawling (depth, number of pages, robots.txt respect)
- **Real form and input detection** (excludes submit/button/file)
- Deduplication by `(action, method, inputs)` – doesn't inflate with navbar login form
- Robust redirect handling (`TooManyRedirects` doesn't abort phase)
- Results reused by injection tests and source code analysis

### 🔬 Source Code Analysis
- **Reuses URLs discovered by spider** (or launches quick spider if no data)
- Downloads HTML from each page and resources linked from same domain: **JS, JSON, source maps (`.map`), CSS, YAML, XML, `.env`**
- **2 MB per file cap** and `stream=True` to avoid massive downloads
- 15 pattern catalogs with weighted severity:
  - **Critical** – private PEM keys, DB connection strings with embedded credentials
  - **High** – AWS Access Key/Secret, Google API Key, GitHub/Slack/Stripe tokens, JWT, generic hardcoded credentials (`password=`, `api_key=`, `bearer=`, …)
  - **Medium** – sensitive HTML comments (`TODO password`, `FIXME admin`, …), Basic Auth in URL, exposed source maps, private IPs (10/8, 172.16/12, 192.168/16)
  - **Low** – internal paths (`/admin`, `/.git`, `/.env`, `/actuator`, …), exposed emails
- **Critical/high findings dumped to `FINDINGS`** with `[CODE:SEV]` prefix
- **Context snippet (±30 characters)** around each match, global deduplication by `(type, value, url)`
- Summary by severity, visual tables, export to TXT/JSON/MD/HTML (dedicated card in report)

### 🌐 Subdomain Fuzzing (VHost)
- **VHost detection** with `ffuf` sending `Host: FUZZ.<domain>`
- **Content-Length baseline** – sends invalid Host (`defnotvalid<rnd>.<domain>`) to get base size and filter with `-fs` all matching responses
- **Automatic base domain detection** if target is FQDN; asks manually if IP
- Default wordlist: `Discovery/DNS/namelist.txt` (SecLists)
- Fallback to internal multithreading method if no `ffuf`

### 🔀 Directory Fuzzing & Enumeration
- **Directory fuzzing** with `ffuf` (ultra-fast) or internal multithreading method
- **Wordlist pre-filtering** – discards comments (`#`), empty lines and entries with spaces
- **`-fs` filter by baseline** – discards wildcard pages (apps returning 200 with index for any path)
- **Auto-calibration** (`-ac`) enabled
- Default wordlist: `raft-small-directories.txt` (SecLists)
- Table with dynamic widths and separation by status code

### 💉 Injection Testing
- **SQLi** – Error-based, time-based blind, boolean-based
- **XSS** – Reflected with contextual analysis
- **Path Traversal / LFI** – `/etc/passwd`, `win.ini`, encodings and bypass
- **Command Injection** – Linux and Windows
- **Open Redirect** – Detection of redirects to arbitrary hosts
- Reuses forms and inputs detected by spider (efficient)

### 🐘 WordPress Enumeration and Attacks
- Enumerates users and login paths
- Detects core version, installed plugins and themes
- Searches for known vulnerabilities (CVE) in plugins and themes
- Performs login brute force with wordlists

### 🏛️ Active Directory Penetration Testing
Dedicated module (menu option **13**) that orchestrates standard Kali AD tools. Works in two modes: **without credentials** (enumeration only) or **authenticated** (user/password for deeper attacks).

- **User enumeration** with `kerbrute userenum` from wordlist
- **LDAP queries** (`ldapsearch`) to list domain users, groups and computers
- **SMB with NetExec** (`nxc smb`) for enumeration and password spraying / brute force
- **AS-REP Roasting** with `impacket-GetNPUsers` on users without Kerberos pre-authentication
- **Kerberoasting** with `impacket-GetUserSPNs -request` to extract service account hashes
- All obtained hashes and credentials integrated in reports (*Active Directory* section)

> **Recommended tools:** `kerbrute`, `ldap-utils`, `netexec`/`nxc` and `impacket-scripts`.

### 🔌 API Testing (OWASP API Top 10)
- **Endpoint discovery** (`/api`, `/swagger`, `/graphql`, `/actuator`, etc.) and OpenAPI parsing
- **IDOR / BOLA (API1)** – Modification of numeric IDs, UUIDs and parameters
- **JWT (API2)** – Detection, `alg` analysis, privilege claims, expiration
- **Rate Limiting (API4)** – Check with 20 consecutive requests
- **Auth Bypass (API5)** – Headers `X-Original-URL`, `X-Forwarded-For`, etc.
- **Mass Assignment (API6)** – Injection of `is_admin`, `role`, `privilege`
- **Verbose Errors (API7)** – Stack trace and internal path detection
- **CORS / GraphQL (API8)** – Introspection enabled, user enumeration

### 👥 Enumeration & Brute Force
- **User enumeration** – From APIs (`/api/users`, etc.) and differential forms
- **Password brute force** – Supports POST forms and Basic Auth
- **`hydra` integration** (`-t 4 -I -u` for reliability and deduplication)
- **CSRF-aware fallback** to internal method with `requests.Session` (maintains cookies and hidden fields)
- **Automatic login error message detection** – Sends impossible credentials, extracts candidate phrases from HTML and proposes them for confirmation
- **Strict heuristics** (≥2 positive signals) when no confirmed error message, to avoid false positives
- Customizable wordlists, supports SecLists

### 🎨 User Experience
- **Interactive menu** with path autocompletion (Tab) on Kali
- **Visually separated phases** with `[INFO] ======= ... =======` headers
- **Unified box-drawing tables** with dynamic widths and colors by severity/status
- **Progress bars** for spidering, fuzzing and brute force (tqdm)
- **Robust Ctrl+C handling** – Any phase interrupts cleanly, saving partial findings

---

## 🔧 Prerequisites

| Requirement | Version | Required |
|-----------|---------|----------|
| Python | 3.8+ | ✅ Yes |
| pip | Latest | ✅ Yes |
| nmap | Latest | ❌ Optional (auto-installable, required for port scanning) |
| nuclei | 3.x | ❌ Optional (auto-installable) |
| ffuf | Latest | ❌ Optional (improves fuzzing) |
| hydra | Latest | ❌ Optional (improves brute force) |
| whatweb | Latest | ❌ Optional (improves fingerprinting) |
| wpscan | Latest | ❌ Optional (WordPress enumeration and attacks) |
| kerbrute, ldap-utils, netexec/nxc, impacket-scripts | Latest | ❌ Optional (Active Directory module) |
| SecLists | Latest | ❌ Optional (wordlists) |

### System Requirements
- **OS**: Kali Linux (recommended) or any Debian/Ubuntu with SecLists installed
- **RAM**: 512 MB minimum, 2 GB recommended
- **Storage**: ~500 MB for dependencies and wordlists
- **Network**: Connection to target (internal or internet)

---

## 📦 Installation

### 1️⃣ Quick Installation (Kali Linux)

```bash
git clone https://github.com/blackevil518/evil-scan.git
cd evil-scan

# Create virtual environment (recommended)
python3 -m venv venv
source venv/bin/activate

# Python dependencies
pip install -r requirements.txt

# Run
python3 evil-scan.py
```

### 2️⃣ Installation with Optional Tools (recommended for maximum coverage)

```bash
# After quick installation steps:
sudo apt update
sudo apt install -y nmap ffuf hydra whatweb seclists wpscan

# Nuclei: use official binaries (more recent than apt)
GO111MODULE=on go install -v github.com/projectdiscovery/nuclei/v3/cmd/nuclei@latest
# Or:
sudo apt install -y nuclei
nuclei -update-templates
```

> The script will offer to install Nuclei, WhatWeb, SecLists and WPScan automatically via `apt` if not found.

---

## 🚀 Quick Start

### Interactive Mode

```bash
python3 evil-scan.py
```

Will prompt for target URL and display main menu.

### CLI Arguments Mode

```bash
python3 evil-scan.py --url https://example.com --output report.html --threads 10 --timeout 15
```

| Argument | Description |
|---|---|
| `--url, -u` | Target URL (omit for interactive mode) |
| `--output, -o` | Report base path (generates TXT/JSON/HTML) |
| `--threads, -t` | Number of threads (default: 5) |
| `--timeout` | Timeout per request in seconds (default: 10) |
| `--delay, -d` | Delay between requests for evasion (default: 0) |
| `--insecure, -k` | Disables TLS verification (use in labs / self-signed certs) |
| `--no-color` | Disables ANSI colors |
| `--version, -V` | Scanner version |

### Pre-Authentication

```bash
python3 evil-scan.py
# Menu → 1. Configure authentication (login)
# Enter username, password and login URL
# Subsequent tests will use authenticated session
```

---

## 📋 Main Menu

```
 ███████╗██╗   ██╗██╗██╗         ███████╗ ██████╗ █████╗ ██╗   ██╗
 ██╔════╝██║   ██║██║██║         ██╔════╝██╔════╝██╔══██╗██║   ██║
 █████╗  ██║   ██║██║██║         ███████╗██║     ███████║██║   ██║
 ██╔══╝  ╚██╗ ██╔╝██║██║         ╚════██║██║     ██╔══██║██║   ██║
 ███████╗ ╚████╔╝ ██║███████╗    ███████║╚██████╗██║  ██║╚██████╔╝
 ╚══════╝  ╚═══╝  ╚═╝╚══════╝    ╚══════╝ ╚═════╝╚═╝  ╚═╝ ╚═════╝

Evil Scan - Web Security Framework
Developed by @blackevil518 - Telegram: @blackevil518

====================================================
  EVIL SCAN v2.0.0 - BlackEvil Edition
====================================================
 1. Configure authentication (login)
 2. General information and enumeration
 3. Port scanning with Nmap (-sV + targeted NSE)
 4. Vulnerability analysis with Nuclei
 5. Subdomain fuzzing (vhost) with ffuf
 6. Directory fuzzing (uses ffuf if installed)
 7. Web Spidering / Complete site mapping
 8. Source code analysis (credentials/secrets in HTML and JS)
 9. Injection testing (SQLi, XSS, Path Traversal, Command Injection)
10. API testing (discovery, IDOR, mass assignment)
11. User/email enumeration and password brute force
12. WordPress enumeration and attacks (WPScan)
13. Active Directory Penetration Testing (Kerbrute/LDAP/NXC)
14. COMPLETE PENTESTING (runs all tests above)
15. Show Markdown summary                (only after scanning)
16. Show results tables                  (only after scanning)
17. Exit
====================================================
Select an option:
```

**How to read the menu:**

- **Options 2–13** execute each phase independently. You can launch them in any order; results accumulate in same session.
- **Option 1** configures login once: afterwards, all phases reuse authenticated session (cookies and headers).
- **Option 14 — Complete Pentesting:** automatically chains information → Nmap → Nuclei → vhost → directories → spidering → source code → injection → API → WordPress → brute force
- **Options 15 and 16** only appear when scan data exists. Used to review results without re-scanning: **15** prints Markdown summary and **16** reprints tables

---

## 📊 Reports

Reports are automatically generated in `reports/<host>/<host>.{txt,json,html,md}` with four formats:

| Format | Content |
|---|---|
| `*.txt` | Plain summary + sections by category (general, vhost, spider, source code analysis, API, directories, credentials, findings, Nuclei) |
| `*.json` | Complete serialized data (ideal for integration with other tools) |
| `*.html` | **SaaS Dashboard** in single file: light/dark theme, collapsible sidebar, table search and PDF export |
| `*.md`  | Complete summary in **standard Markdown** — copy/paste directly in GitBook, GitHub or Obsidian |

### The HTML Dashboard

HTML report is a **professional SaaS-style interactive dashboard**:

- 🎨 **Light/dark theme** with system auto-detection and persistent toggle.
- 🧭 **Collapsible sidebar** with navigation and active section indicator on scroll.
- 🔎 **Live search** filtering all tables (host, port, CVE, hash…).
- 📊 **Visual summary** with KPI cards (linked to sections) and risk gauge by severity.
- 🖨️ **PDF export** optimized (light palette, margins and no page breaks).
- 🧩 **Only sections with data are shown.**

---

## ⚙️ Advanced Configuration

### Customize Wordlists

Edit constants in `evil-scan.py`:

```python
SECLISTS_SMALL     = "/usr/share/seclists/Discovery/Web-Content/raft-small-directories.txt"
SECLISTS_MEDIUM    = "/usr/share/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt"
SECLISTS_PASSWORDS = "/usr/share/seclists/Passwords/xato-net-10-million-passwords-10000.txt"
```

### Network and Concurrency Parameters

```python
DEFAULT_TIMEOUT = 10   # seconds per request
MAX_REDIRECTS   = 10   # maximum redirects to follow
THREADS         = 5    # concurrent threads
REQUEST_DELAY   = 0.0  # delay between requests
```

### Use Burp Suite as Proxy

```bash
export HTTP_PROXY=http://127.0.0.1:8080
export HTTPS_PROXY=http://127.0.0.1:8080
python3 evil-scan.py
```

---

## 🐛 Troubleshooting

### `ModuleNotFoundError: No module named 'requests'`
```bash
pip install -r requirements.txt
```

### `ffuf: command not found`
```bash
sudo apt install -y ffuf
```

### Nuclei hangs or emits many Interactsh errors
This is backend noise. The script filters `Could not unmarshal interaction data`. To disable Interactsh completely, edit the Nuclei call and add `-ni`.

### Brute force doesn't find certain credentials
Hydra doesn't handle CSRF tokens or sessions; the script will detect pending users and fallback to internal method (CSRF-aware with `requests.Session`). If still not found, check account lockout or rate limiting.

### Spidering stops at few pages
If you see `Exceeded N redirects`, target has long redirect chains. Increase `MAX_REDIRECTS` in script.

---

## 📄 License

This project is under **MIT** license. See [LICENSE](LICENSE) for details.

---

## 👨‍💻 Author

**@blackevil518** – Security Researcher | Penetration Tester

- 📱 Telegram: [@blackevil518](https://t.me/blackevil518)
- 🔧 Evil Scan Framework Developer

---

## ⚠️ Disclaimer

**IMPORTANT**: This tool should only be used on systems where you have explicit permission to perform security testing.

- ❌ **Unauthorized use is ILLEGAL**
- ❌ The author is **NOT responsible** for misuse
- ⚠️ Respect local and international laws
- ✅ Always get written consent before testing

```
"This tool is for authorized security testing only.
Unauthorized access to computer systems is illegal."
```

---

<div align="center">

⭐ If you find it useful, give it a star! ⭐

</div>
