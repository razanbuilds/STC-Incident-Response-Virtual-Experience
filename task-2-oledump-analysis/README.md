# Task 2 — Oledump Malware Analysis

## Objective

Analyse a suspicious email attachment — a macro-enabled Excel file (`PaySlip.xlsm`) — using oledump.py to identify malicious VBA macro streams and produce a formal cyber incident report.

---

## Tool Used

**oledump.py** (by Didier Stevens)  
A Python-based tool for analysing OLE (Object Linking and Embedding) files, which are commonly used in Microsoft Office documents. It allows analysts to inspect embedded streams and identify malicious macros.

---

## Scenario

Employees at STC received a phishing email impersonating a trusted colleague (Professor Fahad). The email contained an attachment named `PaySlip.xlsm`. The task was to determine whether the file contained malicious content.

---

## What I Did

### 1. Initial File Analysis

Ran oledump.py against the suspicious file to list all embedded OLE streams:

```bash
oledump.py PaySlip.xlsm
```

The tool returned a full list of streams inside the file, including:

```
A1:  xl/vbaProject.bin
A1:     428  'PROJECT'
A2:      62  'PROJECTwm'
A3: m   1166  'VBA/Sheet1'
A4: M   2196  'VBA/ThisWorkbook'
A5:     2989  'VBA/__VBA_PROJECT'
A6:     1727  'VBA/_SRP_0'
A7:      142  'VBA/_SRP_1'
A8:      688  'VBA/_SRP_2'
A9:      103  'VBA/_SRP_3'
A10:     376  'VBA/_SRP_4'
A11:      66  'VBA/_SRP_5'
A12:     528  'VBA/dir'
```

Streams marked with **M** or **m** indicate the presence of VBA macros — these are the suspicious streams.

### 2. Inspecting the Suspicious Stream

Extracted and decompressed the macro content from the flagged stream:

```bash
oledump.py -s A3 -v PaySlip.xlsm
```

Output revealed VBA attribute metadata, confirming an active macro embedded in the document:

```
Attribute VB_Name = "Sheet1"
Attribute VB_Base = "0{00020820-0000-0000-C000-000000000046}"
Attribute VB_GlobalNameSpace = False
Attribute VB_Creatable = False
Attribute VB_PredeclaredId = True
Attribute VB_Exposed = True
Attribute VB_TemplateDerived = False
Attribute VB_Customizable = True
```

### 3. Key Findings

- The file `PaySlip.xlsm` contains **embedded VBA macros** in multiple streams
- Streams `A3` (Sheet1) and `A4` (ThisWorkbook) both contain macro code
- The macros are set to auto-execute (`VB_Exposed = True`, `VB_PredeclaredId = True`)
- This is consistent with a **macro-based phishing attack** where malicious code executes when the victim opens the file and enables macros

---

## Incident Report Summary

| Field | Value |
|-------|-------|
| Title | Attempt to access suspicious websites via malicious attachment |
| Category | Policy and system violation |
| Date of Incident | 16-11-2021 |
| Type | Incident |
| Source | External |
| Summary | Professor Fahad's identity was impersonated to send phishing emails containing `PaySlip.xlsm` — a macro-enabled Excel file with embedded malicious VBA code. Several employees interacted with the email. |

### Associated Risks
- Execution of the macro may trigger data exfiltration
- Full system damage is possible upon macro execution
- Malicious software may be silently installed on the employee's device
- Attackers may gain remote access to the machine and sensitive data

---

## What I Learned

- How to use oledump.py to analyse OLE-structured Office files
- How to identify macro-containing streams (flagged with M/m)
- How to extract and inspect VBA macro content
- How macro-based phishing attacks operate in real environments
- How to write a structured cybersecurity incident report
