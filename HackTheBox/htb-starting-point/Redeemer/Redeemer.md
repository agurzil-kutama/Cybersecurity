# 🎯 Lab Assessment: Redeemer (Redis Database Leak)

*   **Platform:** Hack The Box
*   **Target IP:** `10.129.X.X`
*   **Difficulty:** Tier 0 (Sanity Check)
*   **Core Protocol Focus:** Redis (Port 6379)

---

## 1. Reconnaissance (Discovery)

### Port Scanning Execution
```bash
nmap -p 6379 -sV -Pn 10.129.X.X
```

### Discovered Services

| Port / Protocol | Active Service | Software Version | Status |
| :--- | :--- | :--- | :--- |
| 6379/tcp | redis | Redis key-value store 5.0.7 | Open / Password Disabled |

**Why it matters:** Redis is an extremely fast, in-memory database used heavily for active data caching. Discovering it exposed publicly on port 6379 hints that access controls or host network firewalls were omitted entirely during server deployment.

---

## 2. Exploitation (Access Path)

### Active Session Establishment
```bash
redis-cli -h 10.129.X.X
```

### Exploit Breakdown
*   **Vulnerability Type:** Missing Access Authentication Layer.
*   **The Flaw:** The Redis instance was configured without a `requirepass` directive, allowing unauthenticated remote clients to drop into a fully privileged shell immediately upon connection.
*   **Exfiltration Step:** Interrogated the live database keyspace to extract the exact key identifying the flag, then read its string contents directly.

```redis
# Executed within the live interactive redis-cli prompt:
10.129.X.X:6379> info
10.129.X.X:6379> keys *
10.129.X.X:6379> get flag
```

---

## 3. Defense & Hardening (Remediation)

To secure this asset against the demonstrated attack path, the following system-hardening steps must be applied:
*   **Enable Password Authentication:** Update the `redis.conf` configuration file to include the `requirepass` directive followed by a highly secure, high-entropy password string.
*   **Bind to Internal Interfaces Only:** Restrict the server to listen only to local traffic by ensuring `bind 127.0.0.1` is set, blocking it from accepting raw external network interfaces.
*   **Implement Network Filtering:** Apply network-layer firewall restrictions (like `iptables` or cloud security groups) to strictly drop any unauthorized inbound requests targeting port 6379.
