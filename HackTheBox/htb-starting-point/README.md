# 🚀 Hack The Box: Starting Point Ledger

This directory houses the technical walkthroughs and remediation strategies for targets within the Hack The Box Starting Point laboratory vector. The primary operational objective of this track is analyzing structural misconfigurations within fundamental transport layers, network file shares, and database deployments.

---

## 📊 Target Matrix

| Asset | Category | Core Infrastructure Vector | Exploit Type | Impact |
| :--- | :--- | :--- | :--- | :--- |
| **Meow** | Tier 0 | Telnet (Port 23) | Blank Administrator Password | Full Host Access |
| **Fawn** | Tier 0 | FTP (Port 21) | Enabled Anonymous Authentication | Data Exfiltration |
| **Dancing** | Tier 0 | SMB (Port 445) | Public Guest Share Misconfiguration | Share Directory Traversal |
| **Redeemer** | Tier 0 | Redis (Port 6379) | Missing Access Authentication Layer | In-Memory Data Leaks |

---

## 🛠️ Security Engineering Framework

Every asset listed above follows a strict three-phase operational standard:

### 1. Active Mapping
* Executing targeted port mapping commands to discover running network services.
* Service fingerprinting to detect the underlying software deployment and versions.

### 2. Tactical Exploitation
* Interacting directly with the exposed port using native utility tools (`telnet`, `ftp`, `smbclient`, `redis-cli`).
* Traversing system file architectures or database keys to securely extract proof-of-concept indicators (flags).

### 3. Structural Remediation
* Documenting system-hardening baselines required to neutralize the threat vector.
* Recommending architectural updates including local loopback binding, credential enforcement, and secure cryptographic encapsulation.
