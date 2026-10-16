# 🎯 Lab Assessment: Meow (Telnet Exploitation)

*   **Platform:** Hack The Box
*   **Target IP:** `10.129.X.X`
*   **Difficulty:** Tier 0 (Sanity Check)
*   **Core Protocol Focus:** Telnet (Port 23)

---

## 1. Reconnaissance (Discovery)

### Port Scanning Execution
```bash
nmap 10.129.X.X
```

### Discovered Services

| Port / Protocol | Active Service | Software Version | Status |
| :--- | :--- | :--- | :--- |
| 23/tcp | telnet | Linux telnetd | Open / Unauthenticated |

**Why it matters:** Initial scanning establishes the blueprint of the network architecture. Discovering an active Telnet port reveals that the remote system handles management traffic over an unencrypted channel.

---

## 2. Exploitation (Access Path)

### Active Session Establishment
```bash
telnet 10.129.X.X
```

### Exploit Breakdown
*   **Vulnerability Type:** Weak Credentials / Blank Administrative Password.
*   **The Flaw:** The host machine leaves the super-administrator account accessible from the network without enforcing password validation.
*   **Exfiltration Step:** Authenticated successfully using the user identity `root` and pressing Enter at the password prompt. Located the proof-of-concept flag text file directly inside the administrative home directory.

```bash
# Executed within the active target shell:
cat /root/flag.txt
```

---

## 3. Defense & Hardening (Remediation)

To secure this asset against the demonstrated attack path, the following system-hardening steps must be applied:
*   **Enforce Strong Authentication:** Configure a highly complex password string for the `root` administrative account to prevent brute-force entry.
*   **Deprecate Cleartext Protocols:** Completely disable the Telnet service on port 23. Telnet passes authentication data in plain text, making it vulnerable to packet sniffing.
*   **Migrate to Secure Shell (SSH):** Deploy OpenSSH on port 22. SSH encrypts the entire management session, protecting credentials and commands from local network interception.
