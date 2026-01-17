---
layout: post
title:  ZUZE_GAME_OVER
description: This blog presents a detailed static and dynamic malware analysis of the *Zeus Game Over (ZUZE)* sample.  The analysis focuses on PE structure, imported APIs, encryption mechanisms, persistence indicators, and runtime behavior observed in an isolated FLARE-VM environment.
tags: malware_analysis
---
# ZUZE GAME OVER VIRUS  
## Static and Dynamic Malware Analysis Report

---

## STATIC ANALYSIS

### Fingerprinting


<table border="1" cellpadding="6">
  <tr>
    <th>File Name</th>
    <th>Size (bits)</th>
    <th>Meaning</th>
    <th>Architecture</th>
  </tr>
  <tr>
    <td>epig.exe</td>
    <td>319968</td>
    <td>Packed sample</td>
    <td>32-bit</td>
  </tr>
  <tr>
    <td>epig_unpacked.exe</td>
    <td>261120</td>
    <td>Unpacked version</td>
    <td>32-bit</td>
  </tr>
</table>

We will be diving into the **unpacked sample** for analysis.

---

### Hash Analysis

- **SHA-256**  
  `09E9FB8BEB798F2C17A311D59C0A44D9E815D6CAD8EA4FEADD77A66D4D3706B5`

- **MD5**  
  `36ADE9194C6F05CCEE041CEF0A5C7DA7`

- **HEX Header**  
  `4D 5A 90 00 03 00 00 00 04 00 00 00 FF FF 00 00 B8 00 00 00 00 00 00 00 40 00 00 00 00 00 00 00`

---

### PEStudio Observation

The file is identified as a **Windows executable** because it starts with the `MZ` header.

---

### Imported Libraries

<ul>
  <li><b>Secur32.dll</b> – Authentication and security packages</li>
  <li><b>WS2_32.dll</b> – TCP/IP network communication</li>
  <li><b>CRYPT32.dll</b> – Cryptographic operations</li>
  <li><b>WININET.dll</b> – HTTP/FTP communication</li>
  <li><b>NETAPI32.dll</b> – Network and user management</li>
  <li><b>IPHLPAPI.dll</b> – Network configuration details</li>
  <li><b>ADVAPI32.dll</b> – Windows Registry manipulation</li>
</ul>
---

## Functional Breakdown of the Malware

The malware likely performs the following actions:
<ul>
  <li>
    <b>Command & Control (C2):</b>
    Uses socket and HTTP-based communication to contact attacker servers.
  </li>
  <li>
    <b>Data Exfiltration & Encryption:</b>
    Encrypts stolen data using Windows cryptographic APIs.
  </li>
  <li>
    <b>System Profiling:</b>
    Collects network, system, and user information.
  </li>
  <li>
    <b>Persistence & Stealth:</b>
    Modifies registry keys to survive system reboots.
  </li>
</ul>

---

## IMPORTS

### Process Injection
- `VirtualAllocEx`
- `WriteProcessMemory`
- `CreateRemoteThread`
- `SetThreadContext`
- `GetThreadContext`

### Stealth & Hidden Desktop
- `CreateDesktopW`
- `SwitchDesktop`
- `SetThreadDesktop`
- `OpenInputDesktop`

### Privilege Escalation
- `AdjustTokenPrivileges`
- `OpenProcessToken`
- `LookupPrivilegeValueW`
- `CreateProcessAsUserW`

### Persistence
- `RegSetValueExW`
- `RegCreateKeyExW`
- `SetFileAttributesW`

### Information Theft
- `GetKeyboardState`
- `GetClipboardData`
- `GetUserNameExW`

### Network Communication
- `send`
- `recv`
- `WSAEventSelect`
- `getaddrinfo`

### Encryption (C2)
- `EncryptMessage`
- `DecryptMessage`
- `CryptImportKey`
- `CryptVerifySignatureW`

### Anti-Analysis
- `OutputDebugStringA`
- `CreateToolhelp32Snapshot`
- `Process32NextW`

---

## STRINGS

### Encryption
- `CryptAcquireContextW`
- `CryptCreateHash`
- `CryptHashData`
- `CryptUnprotectData`
- `PFXImportCertStore`
- `certs\%s\%s_%02u_%02u_%04u.pfx`

### Data Handling
- `CreateFileW`
- `WriteFile`
- `ReadFile`
- `DeleteFileW`
- `MoveFileExW`
- `GetTempPathW`
- `GetTempFileNameW`
- `%s.cab`
- `flashplayer.cab`

### Environment Awareness
- `IsWow64Process`
- `GetNativeSystemInfo`
- `GetVersionExW`
- `GetSystemMetrics`
- `GetDiskFreeSpaceExW`
- `GetAdaptersAddresses`
- `GetDriveTypeW`
- `GlobalMemoryStatusEx`

### Surveillance
- `screenshots\%s\%04x_%08x.jpg`
- `image/jpeg`
- `GDI32.dll`
- `PrintWindow`
- `BitBlt`
- `GetDC`

---

## NOTE (Static Analysis)

Static analysis indicates that the Zeus Game Over sample is a **32-bit Windows PE executable** with extensive capabilities for:

- Process injection  
- Credential theft  
- Encrypted C2 communication  
- Registry-based persistence  
- User surveillance  

The unpacked version exposes clear indicators of malicious intent, warranting further **dynamic and behavioral analysis**.

---

## DYNAMIC ANALYSIS (Unpacked Sample)

### Registry & Persistence Strings
- `SOFTWARE\Microsoft\Windows NT\CurrentVersion`
- `SOFTWARE\Microsoft\Internet Explorer`
- `ProfileList\%s`
- `ProfileImagePath`
- `Start Page`
- `InstallDate`
- `Run`

### C2 Communication Strings
- `HTTP/1.1`
- `POST /write HTTP/1.1`
- `GET /test HTTP/1.1`
- `Host: default`
- `Content-Type: application/x-www-form-urlencoded`
- `Authorization`
- `X-Real-IP`

### Data Theft Indicators
- `username="%s", password="%s"`
- `Account_Name`
- `SMTP_Email_Address`
- `Google Talk`
- `Windows Mail`
- `Mozilla\Firefox`
- `Internet Explorer`

### Process Injection APIs
- `VirtualAllocEx`
- `WriteProcessMemory`
- `CreateRemoteThread`
- `NtCreateThread`
- `NtCreateUserProcess`
- `ReadProcessMemory`
- `VirtualProtectEx`

---

## Execution Overview

The unpacked sample (`epig_unpacked.exe`) was executed in an isolated **FLARE-VM** environment with **INetSim**.

### Observations
- No child processes spawned
- No registry persistence detected
- No files created or modified
- No network traffic observed

---

## Analysis Conclusion (Unpacked Sample)

The unpacked variant showed **minimal runtime behavior** and lacked loader or installation logic.  
It is likely intended for **static analysis or reverse engineering only**.

---

## Analyst Note

The absence of malicious behavior is a valid finding.  
Zeus Game Over commonly relies on **loader-based execution**, which is removed in unpacked samples.

---

## Dynamic Analysis Summary (Packed Sample)

- Sample executed briefly and terminated
- Only standard DLL loading observed
- No persistence, injection, or network activity
- Likely incompatible with modern Windows environments

---

## Final Conclusion

Although full runtime behavior was not observed, **static and partial dynamic analysis confirm the sample as Zeus Game Over malware**.  
Execution failure is attributed to **legacy design and environment incompatibility**, not analysis error.

---

*Source: Original PDF Analysis Report* :[contentReference][con]  

[con]: https://www.linkedin.com/posts/punit-sharma-68b49425a_ana-ugcPost-7417936074376925184-gYhL?utm_source=share&utm_medium=member_desktop&rcm=ACoAAD_Jh8sBXXEttA3W7jcP_TTfG7jIAgtqxd4 
