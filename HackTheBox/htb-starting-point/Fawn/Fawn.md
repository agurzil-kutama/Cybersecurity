# 🎯 Lab Assessment: Fawn (FTP Information Disclosure)

*   **Platform:** Hack The Box
*   **Target IP:** `10.129.X.X`
*   **Difficulty:** Tier 0 (Sanity Check)
*   **Core Protocol Focus:** FTP (Port 21)

---

## 1. Reconnaissance (Discovery)

### Port Scanning Execution
```bash
nmap -sV 10.129.X.X
```

### Discovered Services

| Port / Protocol | Active Service | Software Version | Status |
| :--- | :--- | :--- | :--- |
| 21/tcp | ftp | vsftpd 3.0.3 | Open / Anonymous Enabled |

**Why it matters:** Service version fingerprinting (`-sV`) identifies the exact software running. Discovering an open FTP port requires testing for misconfigured authentication parameters, such as anonymous access.

---

## 2. Exploitation (Access Path)

### Active Session Establishment
```bash
ftp 10.129.X.X
```

### Exploit Breakdown
*   **Vulnerability Type:** Information Disclosure via Misconfigured Anonymous Login.
*   **The Flaw:** The FTP server is configured to allow unauthenticated users to log in using generic credentials, exposing the internal file system structure.
*   **Exfiltration Step:** Connected to the service using the username `anonymous` and leaving the password field completely blank. Navigated the remote directory to download the proof-of-concept flag to the local attacking machine.

```ftp
# Executed within the active FTP prompt:
ftp> ls
ftp> get flag.txt
ftp> exit
```

---

## 3. Defense & Hardening (Remediation)

To secure this asset against the demonstrated attack path, the following system-hardening steps must be applied:
*   **Disable Anonymous Access:** Modify the FTP server configuration file (e.g., `/etc/vsftpd.conf`) and ensure the directive `anonymous_enable=NO` is enforced.
*   **Implement Robust Access Control:** Restrict file system access to explicitly authorized users who possess strong, unique passwords.
*   **Enforce Encrypted Transport Layers:** Transition the service from standard cleartext FTP to SFTP (SSH File Transfer Protocol) over port 22 or FTPS (FTP over TLS) to safeguard credentials and data in transit.
