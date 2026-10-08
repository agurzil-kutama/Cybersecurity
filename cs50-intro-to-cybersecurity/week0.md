# CS50 — Introduction to Cybersecurity

A living record of my progress through CS50's Introduction to Cybersecurity. These notes are written for revision, reference, and proof of understanding — not just transcriptions of the lecture.

---

## Week 0 — Securing Accounts

### Why this week matters

Every account you own is a door. Authentication is the lock. Authorization is the list of rooms that lock opens. Most breaches don't break the lock — they steal the key. This week is about understanding how keys are stolen and how to make that theft expensive, slow, and unlikely.

---

### Authentication vs Authorization

Two different questions. Never confuse them.

- **Authentication** — Who are you? Proving identity. Username + password, biometrics, hardware keys.
- **Authorization** — What are you allowed to do? Permissions, roles, access control.

A stolen key authenticates the thief. It does not authorize them — unless the system is badly designed and gives every authenticated user full access. That's how privilege escalation starts.

**Real world:** OWASP Top 10 A07:2021 — Identification and Authentication Failures.

---

### How Passwords Fail

A password is a shared secret between you and a system. The moment it leaks, the system is compromised.

- **Too short** — search space collapses.
- **Predictable** — appears in every breach corpus.
- **Reused** — one breach cascades into dozens.
- **Personal** — extractable from OSINT.
- **Stored badly** — plaintext, unsalted hashes, or on a Post-it note.

---

### Brute Force — The Math of Guessing

A brute force attack tries every possible combination.

- **4-digit PIN** → 10⁴ = 10,000 possibilities → cracked in under a second.
- **4 mixed-case letters** → 52⁴ = ~7.3 million → seconds.
- **4 ASCII printable** → 94⁴ = ~78 million → under a minute.
- **8 ASCII printable** → 94⁸ ≈ 6.1 × 10¹⁵ → years on consumer hardware.
- **12 ASCII printable** → 94¹² ≈ 4.7 × 10²³ → infeasible with current tech.

**The lesson:** Length multiplies the search space. Every character adds a factor of the charset size.

**Tools:** Hashcat (GPU), John the Ripper (CPU), Hydra and Medusa (online).

---

### Dictionary Attacks

Instead of guessing every combination, the attacker uses a wordlist.

- **rockyou.txt** — 14 million passwords, from the 2009 RockYou breach.
- **SecLists** — curated by Daniel Miessler.
- **CrackStation** — 1.5 billion human-only passwords.
- **HIBP** — aggregated from thousands of breaches.

Variants: rule-based mutations (`password` → `P@ssw0rd1`), combinator attacks, mask attacks.

---

### Entropy — Measuring Real Strength
Entropy = length × log₂(charset_size)

text

- `1234` → ~13 bits → instant.
- `password` → ~37 bits → minutes.
- `Tr0ub4dor&3` → ~72 bits → years.
- `correct horse battery staple` → ~131 bits → effectively permanent.

**Passphrases win.** More entropy, easier to remember. XKCD #936. NIST agrees.

---

### NIST SP 800-63B

- Minimum 8 characters. Maximum at least 64. ASCII printable, space, Unicode.
- No forced periodic rotation.
- No composition rules.
- No password hints or knowledge-based questions.
- Reject passwords found in breach corpora.
- Rate limit authentication attempts.

**Real world:** Microsoft removed password expiry from Windows Server 2019+ default policy.

---

### Rate Limiting and Lockouts

iOS and Android escalate lockouts: 6 → 1 min, 7 → 5 min, 8 → 15, 9 → 60, 10 → device wipe.

Server-side: token bucket, exponential backoff, CAPTCHA, temporary lockout, IP throttling.

**Bypasses:** distributed brute force, credential stuffing, password spraying.

---

### Credential Stuffing

Using leaked credentials from one site to log into others. ~65% of people reuse passwords.

Notable incidents: Nintendo (160K), Zoom (500K), Twitter (5.4M), 23andMe (6.9M), Roku (576K).

**Defense:** Never reuse. Password managers. MFA.

---

### Multi-Factor Authentication

- **Knowledge** — something you know.
- **Possession** — something you have.
- **Inherence** — something you are.

**2-step ≠ 2FA.** Real 2FA mixes categories.

**Ranking, weakest to strongest:**
1. SMS OTP
2. Email OTP
3. Voice call OTP
4. TOTP app
5. Push with number matching
6. Hardware key (YubiKey, FIDO2)
7. Passkeys

**Real world:** Microsoft — MFA blocks 99.9% of compromise. Google — 100% of automated bots.

---

### OTP and SIM Swapping

TOTP uses a shared secret and time counter (RFC 6238). SMS OTP is weaker because numbers can be hijacked.

**SIM swap:** attacker gathers PII, calls carrier, ports your number. All SMS OTPs route to them.

**Defense:** Avoid SMS. Use TOTP or hardware keys. Carrier PIN / port freeze.

---

### Malware and Keyloggers

Variants: userland, kernel, hardware inline, firmware. Families: Agent Tesla, HawkEye, FinFisher.

**Even MFA fails** if a keylogger captures the OTP before you use it.

**Defense:** Only trusted devices. EDR tools. Hardware keys.

---

### Social Engineering

Manipulating humans, not machines.

Techniques: pretexting, baiting, tailgating, quid pro quo, vishing, smishing, whaling.

**Real case:** Twitter 2020 — attackers posed as IT, got credentials, hijacked 130 accounts.

**Defense:** Zero trust. Verify through a second channel. Never share credentials.

---

### Phishing

Technical social engineering.

Types: mass, spear, whaling, clone, angler, quishing.

**How to verify a link:**
1. Hover before clicking.
2. Read the domain right to left — `paypal.com.evil.ru` is not PayPal.
3. HTTPS is necessary but not sufficient.
4. Type the URL manually for anything sensitive.
5. Password managers won't autofill on fake domains.

**Real world:** ~3.4B phishing emails/day. ~36% of breaches involve phishing. $17,700 lost per minute.

**Defense:** DMARC, DKIM, SPF. Email gateways. FIDO2 and passkeys.

---

### Machine-in-the-Middle

Variants: ARP spoofing, DNS spoofing, SSL stripping, evil twin, BGP hijacking.

**Defense:** HTTPS + HSTS. Certificate pinning. VPN. DNS over HTTPS. Cryptography (next lecture).

---

### Single Sign-On

OAuth 2.0 / OpenID Connect.

**How it works:** Click "Sign in with Google." Redirect to Google. Authenticate. Token returned. App never sees password.

**Benefits:** Fewer passwords, centralized MFA, faster onboarding.

**Risks:** Single point of failure. Privacy. Recovery harder. OAuth consent phishing.

---

### Password Managers

Encrypted with AES-256 and a KDF (PBKDF2, Argon2).

**Features:** generator, autofill on matching domain, sync, breach alerts, secure sharing.

**Options:** Bitwarden, KeePassXC, 1Password, Dashlane, Apple/Google/Microsoft built-ins.

**Trade-off:** One master password protects everything. Mitigate with strong passphrase, MFA on vault, printed recovery kit, self-hosted vault.

---

### Passkeys — The Preview

1. Register → device generates key pair.
2. Public key goes to site.
3. Private key stays on device, protected by biometric/PIN.
4. Login → site sends challenge, device signs, site verifies.

**Why better:** Phishing-resistant. No shared secret. No reuse.

Built on FIDO2, WebAuthn, CTAP2.

---

### Concepts Cheat Sheet

| Term | Meaning |
|---|---|
| Authentication | Proving who you are |
| Authorization | What you're allowed to do |
| Brute force | Try every combination |
| Dictionary attack | Try words from a list |
| Credential stuffing | Reuse leaked credentials |
| MFA | Two or more different factor types |
| TOTP | Time-based one-time password |
| SIM swap | Port victim's number to attacker's SIM |
| Keylogger | Records keystrokes |
| Social engineering | Manipulating humans |
| Phishing | Technical social engineering |
| MITM | Attacker between you and the server |
| SSO | One account for many services |
| Password manager | Encrypted vault plus generator |
| Passkey | Public-key auth, phishing-resistant |

---

### Numbers to Remember

- 10⁴ = 10,000 — 4-digit PIN space.
- 94⁸ ≈ 6.1 × 10¹⁵ — 8-character ASCII space.
- 99.9% — Microsoft's MFA block rate.
- 64 characters — NIST minimum maximum password length.
- 8 characters — NIST minimum password length.

---

### Commands for Revision

```bash
# Hashcat — crack NTLM hashes
hashcat -m 1000 -a 0 hashes.txt rockyou.txt

# John — crack /etc/shadow
john --wordlist=rockyou.txt shadow.txt

# Hydra — online SSH brute force
hydra -l admin -P rockyou.txt ssh://target

# Generate TOTP from a secret
oathtool --totp -b JBSWY3DPEHPK3PXP

# Check a password against HIBP
curl -s https://api.pwnedpasswords.com/range/5BAA6 | grep -i <suffix>
```

---

### Seven Rules of Account Security

1. Long beats complex.
2. Unique password for every site.
3. MFA on everything important.
4. Avoid SMS OTP.
5. Use a password manager.
6. Never trust a link — type the URL.
7. Only your own devices for sensitive logins.

---

### References

- [NIST SP 800-63B — Digital Identity Guidelines](https://pages.nist.gov/800-63-3/sp800-63b.html)
- [OWASP Top 10](https://owasp.org/Top10/)
- [OWASP Authentication Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html)
- [FIDO Alliance](https://fidoalliance.org/)
- [RFC 6238 — TOTP](https://datatracker.ietf.org/doc/html/rfc6238)
- [RFC 4226 — HOTP](https://datatracker.ietf.org/doc/html/rfc4226)
- [Have I Been Pwned](https://haveibeenpwned.com/)

---

### Personal Takeaways

- Authentication is a system, not a password. Every layer matters.
- The attacker's job is to find the weakest link — usually the human.
- MFA + password manager + unique passwords block ~99% of account attacks.
- Passkeys are the future. Start using them where supported.
- Never roll your own crypto. Never trust a link you didn't type.
- Security is relativity, not absolutes. Raise the cost to the attacker.

---

### Hands-On — Planned Labs

> Labs I'm working through next. I'll update this section with screenshots and writeups as I complete them.

- [ ] Recreate Malan's Python brute force script
- [ ] Crack a known MD5 hash with Hashcat + `rockyou.txt`
- [ ] Check my email against the HIBP API
- [ ] Compare password entropy scores with zxcvbn
- [ ] Complete TryHackMe rooms related to Week 0

---

## Connect

[![GitHub](https://img.shields.io/badge/GitHub-agurzil--kutama-181717?style=flat&logo=github)](https://github.com/agurzil-kutama)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Adel%20Boutaghane-0A66C2?style=flat&logo=linkedin)](https://linkedin.com/in/adel-boutaghane-54b982386)
[![TryHackMe](https://img.shields.io/badge/TryHackMe-IGILGILI18-212C42?style=flat&logo=tryhackme)](https://tryhackme.com/p/IGILGILI18)

> **Next:** Week 1 — Securing Data.
