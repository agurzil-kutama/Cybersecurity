# 🎯 Lab Assessment: Dancing (SMB Enumeration & Leakage)

*   **Platform:** Hack The Box
*   **Target IP:** `10.129.X.X`
*   **Difficulty:** Tier 0 (Sanity Check)
*   **Core Protocol Focus:** SMB (Port 445)

---

## 1. Reconnaissance (Discovery)

### Port Scanning Execution
```bash
nmap -p 445 10.129.X.X
```

### Discovered Services

| Port / Protocol | Active Service | Software Version | Status |
| :--- | :--- | :--- | :--- |
| 445/tcp | microsoft-ds | Windows SMB (Modern) | Open / Guest Access Allowed |

**Why it matters:** The Server Message Block (SMB) protocol is heavily utilized for network file sharing across enterprise infrastructures. Spotting port 445 open without explicit network authentication exposed the system to immediate, unauthenticated corporate directory data leakage.

---

## 2. Exploitation (Access Path)

### Active Session Establishment
```bash
# Phase 1: Enumerate available network shares
smbclient -L 10.129.X.X

# Phase 2: Connect directly to the unprotected custom share
smbclient //10.129.X.X/WorkShares
```

### Exploit Breakdown
*   **Vulnerability Type:** Misconfigured Network Share Access (Broken Access Control).
*   **The Flaw:** The Windows server was deployed with a custom share named `WorkShares` that permitted guest/null session connections, bypassing mandatory password enforcement.
*   **Exfiltration Step:** Navigated the remote user directories inside the active share instance to locate and download the proof-of-concept flag file to the local Kali machine.

```ftp
# Executed within the interactive smbclient prompt:
smb: \> ls
smb: \> cd James.P
smb: \James.P\> ls
smb: \James.P\> get flag.txt
smb: \James.P\> exit
```

---

## 3. Defense & Hardening (Remediation)

To secure this asset against the demonstrated attack path, the following system-hardening steps must be applied:
*   **Disable Null Sessions:** Restrict anonymous access permissions through Local Security Policies to block unauthenticated network users from listing or viewing shares.
*   **Enforce Principle of Least Privilege:** Explicitly strip out "Everyone" or "Guest" group permissions from the access control lists (ACLs) of sensitive corporate data shares, requiring explicit account credentials.
*   **Harden Transport Protocols:** Ensure legacy, vulnerable SMBv1 features are entirely uninstalled, and mandate SMBv3 usage with packet encryption enabled to mitigate internal LAN sniffing threats.
