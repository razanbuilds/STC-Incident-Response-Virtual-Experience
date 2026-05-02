# Task 3 — Wireshark Network Traffic Analysis

## Objective

Analyse a suspicious network connection captured in Wireshark to identify malicious activity, determine the nature of the threat, extract Indicators of Compromise (IOCs), and produce a formal cyber incident report.

---

## Tool Used

**Wireshark**  
An industry-standard network protocol analyser used to capture and inspect network packets in real time or from saved capture files.

---

## Scenario

A suspicious outbound network connection was detected on the STC network. The connection was flagged for analysis. After inspecting the captured traffic, it was determined the connection originated from a **data-stealing malware (infostealer)** communicating with an attacker-controlled server.

---

## What I Did

### 1. Traffic Inspection

Opened the packet capture file in Wireshark and examined the HTTP response content from the suspicious connection. The server response revealed the malware's capabilities and targets.

**Connection details:**
- Date/Time: Thu, 23 Mar 2023 10:49:07 GMT
- Server: nginx/1.18.0 (Ubuntu)
- Suspicious IPs communicating with the infected host

### 2. Malware Behaviour Analysis

The captured traffic showed the infostealer was actively attempting to collect and exfiltrate:

**Cryptocurrency wallet data:**
- Exodus, Electrum, Ledger Live, Atomic, Binance, Coinomi, Monero, Guarda, Wasabi, and others

**Browser extension credentials:**
- MetaMask, TronLink, BinanceChain, Coinbase, Trust Wallet, Phantom, Ronin, Brave, and many others

**Communication platform databases:**
- Telegram and Discord local databases

**Desktop files matching extensions:**
- `.txt`, `.dox`, `.pdf`

**Malicious downloads attempted:**
- DLL files: `nss3.dll`, `vcruntime140.dll`, `freebl3.dll`, `sqlite3.dll`, `msvcp140.dll`, `mozglue.dll`, `softokn3.dll`
- Executable files: `Clip1.exe`, `bebra.exe`

### 3. IOC Extraction

Compiled all Indicators of Compromise from the captured traffic:

| # | Type | Value |
|---|------|-------|
| 1 | IP Address | 37.220.87.68 |
| 2 | IP Address | 37.220.87.61 |
| 3 | IP Address | 77.73.134.35 |
| 4 | EXE file | Clip1.exe |
| 5 | EXE file | bebra.exe |
| 6 | DLL file | nss3.dll |
| 7 | DLL file | msvcp140.dll |
| 8 | DLL file | vcruntime140.dll |
| 9 | DLL file | mozglue.dll |
| 10 | DLL file | freebl3.dll |
| 11 | DLL file | softokn3.dll |
| 12 | DLL file | sqlite3.dll |

---

## Incident Report Summary

| Field | Value |
|-------|-------|
| Title | Suspicious network connection attempting to steal information |
| Category | Network hacking |
| Date of Incident | Thu, 23 Mar 2023 10:49:07 GMT |
| Type | Incident |
| Source | Internal |
| Summary | A suspicious connection was detected originating from an infostealer malware. The malware communicated with an attacker's C2 server and attempted to exfiltrate cryptocurrency wallets, browser extension credentials, messaging app databases, and sensitive desktop files. |

### Recommendations
- Block all identified IOCs (IPs, EXE and DLL files) immediately
- Scan the network for any other hosts communicating with the same IOCs
- Report the incident to the relevant authorities, including the Saudi Cybersecurity Authority (NCA)

---

## What I Learned

- How to analyse captured network traffic in Wireshark
- How to identify C2 (Command & Control) communication patterns
- How infostealer malware operates and what data it targets
- How to extract and compile an IOC table from network evidence
- How to write recommendations based on incident findings
