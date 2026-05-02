# Task 1 — Splunk Log Analysis

## Objective

Investigate suspicious proxy network logs using Splunk SIEM to identify policy violations, detect threat activity, and produce a formal cyber incident report.

---

## Tool Used

**Splunk Enterprise (SIEM)**  
Splunk was used to ingest proxy access log files, run search queries, apply filters, and identify suspicious fields within the log data.

---

## What I Did

### 1. Log Ingestion
Uploaded proxy log files (`logs.csv`) into Splunk and confirmed successful indexing. The logs were sourced from a Blue Coat ProxySG device on the STC network.

### 2. Search & Filtering
Used Splunk's Search Processing Language (SPL) to filter events. Applied the following filter to isolate relevant activity:

```
source="Proxy logs.csv" host="LAPTOP-84857IW" sourcetype="csv" action="blocked"
```

This returned **2 events** flagged as blocked — both originating from the same user on the same date.

### 3. Suspicious Fields Identified

After drilling into the blocked events, the following fields were flagged as suspicious:

| # | Field | Value |
|---|-------|-------|
| 1 | category | Hacking |
| 2 | action | blocked |
| 3 | cs_Referer | http://www.zone-h.org/ |
| 4 | cs_categories | Hacking |
| 5 | cs_host | www.zone-h.org |
| 6 | cs_uri_scheme | http |
| 7 | dest | www.zone-h.org |
| 8 | dest_host | www.zone-h.org |
| 9 | http_referrer | http://www.zone-h.org/ |
| 10 | s_action | TCP_DENIED |
| 11 | sc_filter_result | DENIED |
| 12 | site | www.zone-h.org |
| 13 | url | http://www.zone-h.org/favicon.ico |
| 14 | vendor_action | TCP_DENIED |
| 15 | vendor_categories | Hacking |

### 4. Key Findings

- User **Fahad3315** attempted to access `www.zone-h.org` — a known hacking and website defacement platform
- The proxy categorized the traffic as **Hacking** and the action was **TCP_DENIED / DENIED**
- The source IP was `10.55.66.777` and the proxy server IP was `10.20.30.40`
- The incident occurred on **16 November 2021 at 08:39**
- The incident source was determined to be **External**

---

## Incident Report Summary

| Field | Value |
|-------|-------|
| Title | Attempt to access suspicious websites |
| Category | Policy and system violation |
| Date of Incident | 16-11-2021 |
| Type | Incident |
| Source | External |
| Summary | User Fahad3315 attempted to access `www.zone-h.org`, a hacking platform. The proxy blocked the connection. Phishing emails were also sent impersonating Professor Fahad, containing malicious attachments, and some employees interacted with them. |

### Associated Risks
- Interaction with malicious content may lead to data leakage
- Full system compromise is possible if malware is executed
- Malicious programs may be installed on employee devices
- Remote access to employee machines and sensitive data may be established

---

## What I Learned

- How to ingest and search log files in Splunk
- How to apply SPL filters to isolate suspicious events
- How to identify Indicators of Compromise (IOCs) within proxy logs
- How to document findings in a professional incident report format
