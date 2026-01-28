---
layout: post
title: "Ransomware TeslaCrypt"
categories: analysis
description: "tatic and dynamic analysis of TeslaCrypt ransomware, including API behavior, string evidence, network communication, and energy consumption impact."
tags: [malware_analysis,]
---
<script>
function filterStrings() {
    let input = document.getElementById('searchBar').value.toLowerCase();
    let cards = document.getElementsByClassName('category-card');
    
    for (let i = 0; i < cards.length; i++) {
        let items = cards[i].getElementsByTagName('li');
        let cardHasMatch = false;
        
        for (let j = 0; j < items.length; j++) {
            if (items[j].innerHTML.toLowerCase().indexOf(input) > -1) {
                items[j].style.display = "";
                cardHasMatch = true;
            } else {
                items[j].style.display = "none";
            }
        }
        // Hide card if no items match
        cards[i].style.display = cardHasMatch ? "" : "none";
    }
}
</script>
<style>
        table {
            width: 100%;
            border-collapse: collapse;
            font-family: Arial, sans-serif;
            margin: 20px 0;
        }
        th {
            background-color: #2c3e50;
            color: white;
            text-align: left;
            padding: 12px;
        }
        td {
            border: 1px solid #ddd;
            padding: 1px;
            vertical-align: top;
        }
        tr:hover {
            background-color:rgb(0, 0, 0);
        }
        .category-cell {
            font-weight: bold;
            color: #c0392b;
            width: 20%;
        }
        dl { background:rgb(0, 0, 0); padding: 20px; border-left: 5px solid #2980b9; border-radius: 4px; }
        dt { font-weight: bold; font-size: 1.2em; color: #2c3e50; margin-top: 15px; }
        dt span { font-size: 0.8em; color: #7f8c8d; font-weight: normal; }
        dd { margin: 5px 0 15px 20px; font-style: italic; }
        .purpose { display: block; font-style: normal; margin-top: 5px; color: #444; }
        .purpose::before { content: "🎯 Purpose: "; font-weight: bold; color: #e67e22; }

        body { font-family: 'Segoe UI', Tahoma, sans-serif; background: var(--bg); color: var(--text); padding: 20px; }
        .header { border-bottom: 2px solid var(--accent); margin-bottom: 20px; padding-bottom: 10px; }
        
        /* Search Bar */
        #searchBar {
            width: 100%;
            padding: 12px;
            margin-bottom: 20px;
            border-radius: 8px;
            border: none;
            background: var(--card-bg);
            color: white;
            font-size: 16px;
            box-shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1);
        }

        /* Grid Layout */
        .grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(350px, 1fr)); gap: 20px; }
        
        .category-card {
            background: var(--card-bg);
            border-radius: 12px;
            padding: 15px;
            border: 1px solid #334155;
        }
        .category-card h3 { 
            margin-top: 0; 
            color: var(--accent); 
            font-size: 1.1em;
            border-bottom: 1px solid #334155;
            padding-bottom: 8px;
        }

        .string-list { list-style: none; padding: 0; margin: 0; }
        .string-list li {
            padding: 6px 8px;
            margin: 4px 0;
            background: #0f172a;
            border-radius: 4px;
            font-family: 'Consolas', monospace;
            font-size: 0.85em;
            word-break: break-all;
            transition: background 0.2s;
        }
        .string-list li:hover { background: #334155; }
        
        .tag-ransom { color: var(--danger); font-weight: bold; }
        .tag-api { color: #fbbf24; }
    </style>
# Ransomware TeslaCrypt – Static & Dynamic Analysis

## Introduction

This blog presents a detailed **static and dynamic analysis of the TeslaCrypt ransomware**.  
The objective is to understand its internal structure, behavior, network communication, and **environmental impact due to energy consumption**.

---

## Static Analysis

### Fingerprinting
<table border="1" cellpadding="6">
  <tr>
    <th>File Name</th>
    <th>Size (bits)</th>
    <th>Meaning</th>
    <th>Architecture</th>
  </tr>
  <tr>
    <td>1</td>
    <td>290,816</td>
    <td>Packed sample</td>
    <td>32-bit</td>
  </tr>
  <tr>
    <td>2</td>
    <td>263,680</td>
    <td>Unpacked version</td>
    <td>32-bit</td>
  </tr>
</table>


We focus on the **unpacked sample** for analysis.

---

### Hash Analysis

- **SHA-256**  
  `3372c1edab46837f1e973164fa2d726c5c5e17bcb888828ccd7c4dfcc234a370`

- **MD5**  
  `209a288c68207d57e0ce6e60ebf60729`

The executable starts with **MZ**, confirming it is a **Windows PE file**.  
Compile timestamps align with known TeslaCrypt campaigns (timestamps may be falsified).

#### Hash & PEStudio Output

<img src="/assets/blog_image/report2/2.JPG" style="width: 800px; height: auto;">

---

<img src="/assets/blog_image/report2/1.JPG" style="width: 800px; height: auto;">

---

## Imported APIs (Malicious Indicators)
<table>
<tr>
    <th>Category</th>
    <th>API Function</th>
    <th>Potential Malicious Purpose</th>
</tr>


<tr>
    <td class="category-cell" rowspan="3">Evasion & Stealth</td>
    <td>IsDebuggerPresent</td>
    <td>Used to change behavior or exit to avoid analysis.</td>
</tr>
<tr>
    <td>Sleep</td>
    <td>Delays execution to "time out" automated sandboxes.</td>
</tr>
<tr>
    <td>VirtualAlloc</td>
    <td>Process Injection or unpacking hidden code.</td>
</tr>
<tr>
    <td class="category-cell" rowspan="2">Data Theft (Stealer)</td>
    <td>OpenClipboard / GetClipboardData</td>
    <td>Clipboard Hijacking</td>
</tr>
<tr>
    <td>GetDIBits / CreateCompatibleBitmap</td>
    <td>Screen Capturing.</td>
</tr>
<tr>
    <td class="category-cell" rowspan="3">Information Gathering</td>
    <td>EnumProcesses</td>
       <td>Lists all running programs to identify security software (Antivirus).</td>
</tr>
<tr>
    <td>GetLogicalDriveStringsW</td>
    <td>Identifies connected drives (USB, Network shares).</td>
</tr>
<tr>
    <td>GetTokenInformation</td>
    <td>Checks user privileges.</td>
</tr>
<tr>
    <td class="category-cell" rowspan="3">Persistence & Execution</td>
    <td>RegSetValueExW</td>
    <td>Writes to the Windows Registry to ensure the malware starts automatically on every reboot.</td>
</tr>
<tr>
    <td>ShellExecuteW / CreateProcessW</td>
    <td>Launches secondary payloads, command scripts, or hidden system tools.</td>
</tr>
<tr>
    <td>CopyFileW</td>
    <td>Moves the malware binary to a "safe" hidden directory (like System32 or AppData).</td>
</tr>
<tr>
    <td class="category-cell" rowspan="3">Network & Exfiltration</td>
    <td>InternetConnectA</td>
    <td>Establishes a connection to a remote Command & Control (C2) server.</td>
</tr>
<tr>
    <td>HttpSendRequestA</td>
    <td>Exfiltration.</td>
</tr>
<tr>
    <td>InternetSetCookieW</td>
    <td>Used to steal or manipulate web session cookies to hijack online accounts.</td>
</tr>
<tr>
    <td class="category-cell">Anti-Forensics</td>
       <td>DeleteFileW</td>
    <td>Used to delete the original installer or temporary "stolen data" files after they are uploaded cover tracks.</td>
</tr>
</table>

#### Imports Table Screenshot

<img src="/assets/blog_image/report2/3.JPG" style="width: 800px; height: auto;">

---

## Library Analysis

| DLL | Risk Level | Role |
|----|-----------|-----|
| WININET.dll | Critical | C2 communication |
| ADVAPI32.dll | High | Privilege & registry |
| PSAPI.dll | High | Process enumeration |
| KERNEL32.dll | Medium | Core execution |
| USER32.dll | Medium | User activity |
| GDI32.dll | Medium | Screen capture |
| SHELL32.dll | Medium | Program execution |

<dl>
    <dt>1. Network & Data Theft <span>(WININET.dll & ole32.dll)</span></dt>
    <dd>
        These libraries allow the malware to act like a web browser.
        <span class="purpose">It uses these to send stolen data (logins, cookies, files) to a remote server. The ole32.dll library helps it interact with browser objects or other COM-based applications (like Outlook or Excel) to extract data.</span>
    </dd>

<dt>2. System Reconnaissance <span>(PSAPI.DLL & KERNEL32.dll)</span></dt>
    <dd>
        These are used to "scout" the infected machine.
        <span class="purpose">PSAPI (Process Status API) is specifically used to see which programs are currently running. Malware uses this to look for tools like Wireshark, Process Hacker, or Windows Defender to avoid being caught.</span>
    </dd>

<dt>3. User Spying <span>(USER32.dll & GDI32.dll)</span></dt>
    <dd>
        These handle the visual and interactive parts of Windows.
        <span class="purpose">Used to "hook" user actions. USER32 monitors typing/clicking, while GDI32 and MSIMG32 "paint" a copy of your screen into a file to be stolen.</span>
    </dd>

<dt>4. Privilege Escalation <span>(ADVAPI32.dll)</span></dt>
    <dd>
        This library manages security and registry access.
        <span class="purpose">The malware checks for "System" or "Admin" rights. If missing, it may trigger UAC prompts or exploit vulnerabilities to gain higher permissions.</span>
    </dd>
</dl>

---

## String Analysis
<div class="header">
    <h1>Malware Analysis: <span class="tag-ransom">CryptoLocker v3</span></h1>
    <p>String Classification & Evidence Overview</p>
</div>

<input type="text" id="searchBar" onkeyup="filterStrings()" placeholder="Search for strings (e.g. '.onion', 'Crypt', 'exe')...">

<div class="grid" id="stringGrid">
<div class="category-card">
    <h3>📄 Binary Artifacts (PE Header)</h3>
    <ul class="string-list">
        <li>This program cannot be run in DOS mode</li>
        <li>RichkM</li>
        <li>.text (Code Section)</li>
        <li>.rdata (Read-only Data)</li>
        <li>.data (Initialized Data)</li>
        <li>.rsrc (Resources)</li>
    </ul>
</div>

<!-- Ransomware Specifics -->
<div class="category-card">
    <h3>💰 Ransomware Evidence</h3>
    <ul class="string-list">
        <li class="tag-ransom">CryptoLocker-v3</li>
        <li>HELP_TO_DECRYPT_YOUR_FILES.txt</li>
        <li>HELP_TO_DECRYPT_YOUR_FILES.bmp</li>
        <li>CryptoLocker.lnk</li>
        <li>key.dat | log.html | help.html</li>
        <li>vssadmin delete shadows /all</li>
    </ul>
</div>

<!-- Network & C2 -->
<div class="category-card">
    <h3>🌐 Network & Exfiltration</h3>
    <ul class="string-list">
        <li>34r6hq26q2h4jkzj.onion</li>
        <li>tor2web.fi | tor2web.org</li>
        <li>50.7.138.132 (C2 IP)</li>
        <li>Subject=Payment&recovery_key=%s...</li>
        <li>Subject=Ping | Subject=Crypted</li>
        <li>WININET.dll</li>
    </ul>
</div>

<!-- Cryptography -->
<div class="category-card">
    <h3>🔐 Cryptographic Operations</h3>
    <ul class="string-list">
        <li>RSA-2048 | SHA-256 | RIPE-MD160</li>
        <li>CryptAcquireContextW</li>
        <li>CryptGenRandom | CryptEncrypt</li>
        <li>SECG curve over a 256 bit prime field</li>
        <li>CAutoBN_CTX : BN_CTX_new()</li>
        <li>EncodeBase58</li>
    </ul>
</div>

<!-- Target File Extensions -->
<div class="category-card">
    <h3>🎯 Encryption Targets</h3>
    <ul class="string-list">
        <li>Documents: .docx, .xlsx, .pptx, .accdb</li>
        <li>Media: .jpeg, .asset, .unity3d</li>
        <li>Databases: .db, .mdbackup</li>
        <li>Games: .mcgame, .wotreplay, .rgss3a</li>
    </ul>
</div>

<!-- GUI / Interaction -->
<div class="category-card">
    <h3>🖥️ GUI / User Interaction</h3>
    <ul class="string-list">
        <li>Your personal files are encrypted!</li>
        <li>Enter Decrypt Key</li>
        <li>Check Payment</li>
        <li>Click to copy Bitcoin address</li>
        <li>mshta.exe (HTML Application Host)</li>
    </ul>
</div>

<!-- Persistence & Anti-Analysis -->
<div class="category-card">
    <h3>🛡️ Persistence & Evasion</h3>
    <ul class="string-list">
        <li>Software\Microsoft\Windows\CurrentVersion\Run</li>
        <li>IsDebuggerPresent</li>
        <li>CreateMutexW</li>
        <li>EnumProcesses | CreateToolhelp32Snapshot</li>
        <li>Process32First / Next</li>
    </ul>
</div>

</div>

Static strings confirm **TeslaCrypt / CryptoLocker family ransomware**. <br>
Static string analysis reveals that the sample is a fully functional ransomware belonging to the
TeslaCrypt/CryptoLocker family. The malware implements strong cryptographic routines, including
RSA-2048 and SHA-256, to encrypt a wide range of user files across multiple file formats. It
enumerates drives and processes, deletes Volume Shadow Copies to prevent recovery, and
establishes persistence via registry Run keys. The presence of Tor and Tor2Web URLs, Bitcoinrelated encoding, and extensive ransom instructions indicates a complete payment and decryption
workflow. These strings strongly confirm ransomware behavior without requiring execution.

---

## Static Analysis Conclusion

Static analysis confirms this sample as a **fully functional TeslaCrypt ransomware**.  
It includes encryption, persistence, network communication, and victim interaction mechanisms.  
Dynamic analysis is required to observe **runtime behavior and system impact**.

---

### Key Evidence

- RSA-2048 & SHA-256 encryption
- Tor & Tor2Web URLs
- Bitcoin payment workflow
- File extension targeting
- Shadow copy deletion
- GUI ransom messages

## Dynamic Analysis

#### Ransom Note & GUI

<img src="/assets/blog_image/report2/s2.JPG" style="width: 800px; height: auto;">
<br>
<img src="/assets/blog_image/report2/s3.JPG" style="width: 800px; height: auto;">

### Process Behavior

- Opens files with write permission
- Overwrites original data
- Deletes original files
- Runs continuously

#### Process Monitoring

<img src="/assets/blog_image/report2/s1.JPG" style="width: 800px; height: auto;">

---

### File System Modifications

- Creation of ransom notes
- Encryption of files (`.jpg`, `.txt`, etc.)
- Renaming encrypted extensions
- Startup persistence enabled

<img src="/assets/blog_image/report2/s5.JPG" style="width: 800px; height: auto;">

---

## Network Behavior Analysis

Network analysis was performed using **INetSim**.

### Observations

- DNS queries to Tor2Web gateways
- Onion service communication attempts
- Bitcoin-related requests
- Payment verification traffic

#### Network Logs

<img src="/assets/blog_image/report2/n1.JPG" style="width: 800px; height: auto;">
<img src="/assets/blog_image/report2/nn1.png" style="width: 800px; height: auto;">

---

## Energy Consumption Analysis


### Baseline System (Clean)

**CPU**
<table >

<tr>
    <th>Performance Counter</th>
    <th>Instance</th>
    <th>Machine</th>
    <th>Mean (%)</th>
    <th>Minimum (%)</th>
    <th>Maximum (%)</th>
</tr>


<tr>
    <td>% C1 Time</td>
    <td><span class="instance-tag">_Total</span></td>
    <td>DESKTOP-42QFN1J</td>
    <td class="mid-val">70</td>
    <td>0</td>
    <td>87</td>
</tr>
<tr>
    <td>% C2 Time</td>
    <td><span class="instance-tag">_Total</span></td>
    <td>DESKTOP-42QFN1J</td>
    <td class="low-val">0</td>
    <td>0</td>
    <td>0</td>
</tr>
<tr>
    <td>% C3 Time</td>
    <td><span class="instance-tag">_Total</span></td>
    <td>DESKTOP-42QFN1J</td>
    <td class="low-val">0</td>
    <td>0</td>
    <td>0</td>
</tr>
<tr>
    <td>% DPC Time</td>
    <td><span class="instance-tag">_Total</span></td>
    <td>DESKTOP-42QFN1J</td>
    <td class="low-val">0.104</td>
    <td>0</td>
    <td>3</td>
</tr>
<tr>
    <td>% Idle Time</td>
    <td><span class="instance-tag">_Total</span></td>
    <td>DESKTOP-42QFN1J</td>
    <td class="mid-val">70</td>
    <td>0</td>
    <td>87</td>
</tr>
<tr>
    <td>% Interrupt Time</td>
    <td><span class="instance-tag">_Total</span></td>
    <td>DESKTOP-42QFN1J</td>
    <td class="low-val">0.13</td>
    <td>0</td>
    <td>3</td>
</tr>
<tr>
    <td>% Privileged Time</td>
    <td><span class="instance-tag">_Total</span></td>
    <td>DESKTOP-42QFN1J</td>
    <td class="low-val">13</td>
    <td>0</td>
    <td>84</td>
</tr>
<tr style="background-color:rgb(0, 0, 0);">
    <td >% Processor Time</td>
    <td><span class="instance-tag">_Total</span></td>
    <td>DESKTOP-42QFN1J</td>
    <td class="low-val">18</td>
    <td>0.458</td>
    <td class="high-val">100</td>
</tr>
<tr>
    <td>% User Time</td>
    <td><span class="instance-tag">_Total</span></td>
    <td>DESKTOP-42QFN1J</td>
    <td class="low-val">5</td>
    <td>0</td>
    <td>40</td>
</tr>

</table>
- Avg Utilization: 18%
- Duration: 300 seconds  
- CPU Load Index: `18 × 300 = 5,400`

**Disk**
<table>
<tr>
    <th>Disk Number</th>
    <th>Reads / sec</th>
    <th>Kb / Read</th>
    <th>Writes / sec</th>
    <th>Kb / Write</th>
</tr>
<tr>
    <td><span class="highlight">0</span></td>
    <td>32.9</td>
    <td>17</td>
    <td>12.5</td>
    <td>224</td>
</tr>
</table>
- Writes/sec: 12.5  
- Disk Load Index: `3,750`
- Data Written: ~820 MB

**Total Baseline Energy Index:**  
`9,150`

---

### Infected System (Ransomware Active)

**CPU**
<table>
<tr>
    <th>Performance Counter</th>
    <th>Mean (%)</th>
    <th>Min (%)</th>
    <th>Max (%)</th>
</tr>
<tr>
    <td>% C1 Time</td>
    <td class="val-mid">52</td>
    <td>1</td>
    <td>94</td>
</tr>
<tr>
    <td>% C2 Time / % C3 Time</td>
    <td>0</td>
    <td>0</td>
    <td>0</td>
</tr>
<tr>
    <td>% DPC Time</td>
    <td>0.893</td>
    <td>0</td>
    <td>7</td>
</tr>
<tr>
    <td>% Idle Time</td>
    <td class="val-mid">52</td>
    <td>1</td>
    <td>94</td>
</tr>
<tr>
    <td>% Interrupt Time</td>
    <td>0.37</td>
    <td>0</td>
    <td>4</td>
</tr>
<tr>
    <td>% Privileged Time</td>
    <td class="val-mid">30</td>
    <td>0</td>
    <td>82</td>
</tr>
<tr style="background-color:rgb(0, 0, 0);">
    <td>% Processor Time</td>
    <td class="val-high">37</td>
    <td>0.691</td>
    <td class="val-high">99</td>
</tr>
<tr>
    <td>% User Time</td>
    <td>7</td>
    <td>0</td>
    <td>45</td>
</tr>
</table>
- Avg Utilization: 37%
- CPU Load Index: `11,100`

**Disk**
 <table>
<tr>
    <th>Disk #</th>
    <th>Reads / sec</th>
    <th>Kb / Read</th>
    <th>Writes / sec</th>
    <th>Kb / Write</th>
</tr>
<tr>
    <td>0</td>
    <td class="highlight">356.7<span class="sub-label">PEAK I/O</span></td>
    <td>22</td>
    <td class="highlight">267.5<span class="sub-label">ENCRYPTION STREAM</span></td>
    <td>64</td>
</tr>
</table>

- Writes/sec: 267.5
- Disk Load Index: `80,250`
- Data Written: ~5.1 GB
<table class="metric-table">
<tr>
    <th>Metric</th>
    <th>Value</th>
</tr>
<tr>
    <td>Writes/sec</td>
    <td>267.5 <span class="unit">ops/s</span></td>
</tr>
<tr>
    <td>KB/Write</td>
    <td>64 <span class="unit">KB</span></td>
</tr>
<tr>
    <td>Duration</td>
    <td>300 <span class="unit">seconds</span></td>
</tr>
<tr>
    <td>Total Data Written</td>
    <td>5,136,000 <span class="unit">KB (~4.9 GB)</span></td>
</tr>
</table>

**Total Infected Energy Index:**  
`91,350`

---

## Baseline vs Infected Comparison

| State | Energy Index |
|-----|-------------|
| Clean | 9,150 |
| Infected | 91,350 |

**≈ 10× increase in energy consumption**

---

## Final Conclusion

TeslaCrypt ransomware not only encrypts user data but also causes **severe CPU and disk stress**, leading to **significant energy waste**.  
This highlights ransomware as both a **cybersecurity threat** and an **environmental concern**, increasing carbon emissions through unnecessary system load.

---

## Disclaimer

This analysis is strictly for **educational and research purposes**.  
No live systems were harmed during testing.<br>
*Source: Original PDF Analysis Report* :[contentReference][con]  

[con]: https://www.linkedin.com/posts/punit-sharma-68b49425a_report2-activity-7421884787394543616--Nca
