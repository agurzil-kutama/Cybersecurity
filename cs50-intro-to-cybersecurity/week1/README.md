# CS50 — Introduction to Cybersecurity

## Week 1 — Securing Data

> **Course:** CS50's Introduction to Cybersecurity
> **Instructor:** David J. Malan (Harvard University)
> **Topic:** Hashing, salting, cryptography, symmetric vs asymmetric encryption, RSA, Diffie-Hellman, digital signatures, passkeys, encryption in transit and at rest, quantum threat

---

### Why this week matters

Last week was about accounts. This week is about what sits behind them: the data. Passwords, messages, files, sessions, disk contents. Everything that moves between you and a server, and everything that sits still on your device. If Week 0 was about the lock, Week 1 is about the safe.

---

### Plaintext Passwords — The Original Sin

The simplest way a server stores your password is literally your password. A text file like:

```text
alice:apple
bob:banana
```

This is how early Unix systems stored credentials. One breach and every credential is exposed.

**Why it's catastrophic:**
- Instant credential stuffing against other services.
- No cost to the attacker.
- Cascades across every account the user reused the password on.

**Real world:** The 2009 RockYou breach stored 32 million passwords in plaintext. It produced `rockyou.txt` — still used today.

---

### Hashing — One-Way Transformation

A hash function takes an arbitrary-length input and produces a fixed-length output.

```text
hash("apple")  →  ..ekWXa83dhiA
hash("banana") →  xQ72Lm9pRfBc1
hash("cherry") →  bZ91Kj4nWqXe7
```

**Properties:**
- Deterministic — same input, same output.
- Fixed output length.
- One-way — cannot be reversed.
- Avalanche effect — small change flips most bits.

**Why servers store hashes:** When Alice logs in, the server hashes what she typed and compares. The server never needs the real password.

**Real world:** Linux `/etc/shadow`. Windows Active Directory NTLM hashes.

---

### Cracking Hashes — Dictionary, Brute Force, Rainbow Tables

Hashing raises the bar. It does not eliminate the threat.

- **Dictionary attack** — hash every word in a list, compare.
- **Brute force** — try every combination.
- **Rainbow table** — precomputed lookup hash → password.

**Tools:**
- Hashcat — GPU-accelerated, 300+ hash types.
- John the Ripper — CPU, best for Unix shadow files.
- CrackStation — online rainbow tables.

**Real world:** The 2012 LinkedIn breach exposed 6.5M unsalted SHA-1 hashes. Cracked within days.

---

### Salting — Defeating Precomputation

A salt is random data added to each password before hashing.

```text
hash("cherry" + "50") → 50..ekWXa83dhiA
hash("cherry" + "49") → 49xQ72Lm9pRfBc1
```

Same password, different hashes. Rainbow tables become useless.

**Storage convention:** The salt is stored alongside the hash, usually as a prefix (e.g., `$2b$12$...` in bcrypt).

**Real world:** NIST SP 800-63B mandates salted hashing. Modern best practice is bcrypt, scrypt, or Argon2.

---

### Password Hashing vs General Hashing

| Use case | Correct choice |
|---|---|
| File integrity | SHA-256, SHA-3 |
| Message authentication | HMAC-SHA256 |
| Password storage | bcrypt, scrypt, Argon2 |
| Digital signatures | SHA-256 + RSA/ECDSA |

**Why SHA-256 is wrong for passwords:** It's designed to be fast. Fast is the enemy. bcrypt/Argon2 are deliberately slow and tunable.

---

### Cryptographic Hash Functions — Beyond Passwords

Three uses beyond password storage:

1. **Integrity verification** — `sha256sum file.iso`.
2. **Digital signatures** — sign the hash, not the document.
3. **Message authentication codes** — HMAC.

**Properties required:**
- Preimage resistance.
- Second preimage resistance.
- Collision resistance.

**Real world:** Git uses SHA-1 (migrating to SHA-256). Software signing uses SHA-256. TLS certificates use SHA-256.

---

### Codes vs Ciphers

**Codes** map words or phrases to other words. Cumbersome. Physical books. Compromised if stolen.

**Ciphers** operate algorithmically on letters or bits. Scalable. Reproducible. Modern ciphers are pure math.

**Vocabulary:**
- Encode / Decode — codes.
- Encipher / Decipher — ciphers.
- Encrypt / Decrypt — modern synonyms.
- Plaintext — readable input.
- Ciphertext — scrambled output.

---

### The Caesar Cipher and ROT13

Julius Caesar's cipher: shift each letter by a key.

```text
Plaintext:  A B C D E
Key:        1
Ciphertext: B C D E F
```

**ROT13** is the same cipher with key 13.

**ROT26** is a joke — `13 × 2 = 26`, so it "doubles security." Actually 26 shifts return the plaintext.

**Why it's broken:** Only 25 possible keys. Broken by hand in minutes.

---

### Symmetric Encryption — AES and 3DES

Same key encrypts and decrypts. Fast. Efficient.

```text
ciphertext = encrypt(plaintext, key)
plaintext  = decrypt(ciphertext, key)
```

**Standards today:**
- AES — 128/192/256-bit keys. TLS, disk encryption, VPNs, Wi-Fi.
- 3DES — deprecated, 168-bit effective. Legacy banking.

**The key distribution problem:** How do Alice and Bob agree on a key if they've never met? Asymmetric encryption solves this.

---

### Asymmetric Encryption — RSA and Public Key Crypto

Two keys per person. Public encrypts, private decrypts.

```text
ciphertext = encrypt(plaintext, recipient_public_key)
plaintext  = decrypt(ciphertext, recipient_private_key)
```

**RSA in one paragraph:** Pick two huge primes `p` and `q`. Multiply → `n`. Pick public exponent `e`. Compute private exponent `d`. Encryption is `c = m^e mod n`. Decryption is `m = c^d mod n`. Breaking RSA requires factoring `n` back into `p × q`.

**Key size today:**
- RSA 2048-bit minimum. 4096 preferred.
- ECDSA / Ed25519 — smaller keys, equivalent security.

**Real world:** Every HTTPS connection. Every SSH session. Every signed software update.

---

### Diffie-Hellman Key Exchange

Solves the shared-secret problem without sending the secret over the wire.

**Setup:**
- Public: generator `g`, prime `p`.
- Alice picks private `a`, sends `g^a mod p`.
- Bob picks private `b`, sends `g^b mod p`.
- Alice computes `(g^b)^a mod p`.
- Bob computes `(g^a)^b mod p`.
- Both arrive at `g^(ab) mod p`.

**Real world:** Used in TLS handshakes. Often combined with ECDHE for forward secrecy.

---

### Digital Signatures — Proving Authenticity

**Signing:**
1. Hash the document.
2. Encrypt the hash with your private key → signature.
3. Send document + signature.

**Verifying:**
1. Hash the received document.
2. Decrypt the signature with the signer's public key.
3. Compare the two hashes. If equal, valid.

**Real world:** Code signing (Windows Authenticode, Apple notarization), TLS certificates, email signing, document signing.

**Famous failure:** SolarWinds 2020 — attackers signed a malicious update with a stolen code-signing certificate.

---

### Passkeys — The Passwordless Future

Passkeys replace passwords with per-site public/private key pairs.

**Registration:**
1. Device generates key pair for that site.
2. Public key + user ID sent to server.
3. Private key stays on device, protected by biometric.

**Login:**
1. Server sends a random challenge.
2. Device signs the challenge with the private key.
3. Server verifies with the stored public key.

**Why they're better:**
- Phishing-resistant — key bound to site origin.
- No shared secret to leak.
- No reuse across sites.

**Standards:** FIDO2, WebAuthn, CTAP2.

**Real world:** Google, Apple, Microsoft, and password managers all support passkeys.

---

### Encryption in Transit vs At Rest

**In transit:**
- Data moving between you and a server.
- HTTPS / TLS.
- Protects against MITM on the wire.

**At rest:**
- Data sitting on your device.
- FileVault (macOS), BitLocker (Windows), LUKS (Linux).

**End-to-end encryption (E2EE):**
- Data encrypted from sender to recipient.
- Even the service provider cannot read it.
- WhatsApp, Signal, iMessage.

**Critical distinction:** HTTPS to Gmail ≠ Gmail can't read your mail. Only E2EE guarantees the provider can't.

---

### Secure Deletion — Deleting Isn't Deleting

When you delete a file:
1. The filesystem marks the blocks as free.
2. The data stays until overwritten.
3. It might sit there for weeks, months, or years.

**Secure deletion:**
- Overwrite the data with zeros, ones, or random patterns.
- Or use full-disk encryption from day one.

**Tools:** `shred` (Linux), `cipher /w` (Windows).

---

### Full-Disk Encryption — The Real Fix

| OS | Feature |
|---|---|
| macOS | FileVault |
| Windows | BitLocker |
| Linux | LUKS |
| iOS / Android | Automatic on modern devices |

**Why it's crucial:**
- Device theft → data unreadable.
- Device resale → no data leak.

**Trade-off:** Lose the password → lose everything.

---

### Ransomware — Full-Disk Encryption Used Against You

Attackers encrypt your files and demand payment for the key.

**Chain:**
1. Initial access (phishing, RDP, vulnerability).
2. Lateral movement.
3. Exfiltration (double extortion).
4. Encryption of everything.
5. Ransom note.

**Notable incidents:** Colonial Pipeline (2021), REvil/Kaseya (2021), WannaCry (2017), Conti (2020–2022).

**Defense:** Offline backups (3-2-1 rule), EDR, MFA, network segmentation, patching.

---

### Quantum Computing — The Looming Threat

Classical bits are 0 or 1. Qubits can be both simultaneously.

**The impact:**
- 2 qubits → 4 states.
- 3 qubits → 8 states.
- 32 qubits → 4 billion states at once.

**Why it threatens crypto:**
- **Shor's algorithm** — breaks RSA, Diffie-Hellman, ECC.
- **Grover's algorithm** — quadratic speedup for brute force.

**Post-quantum cryptography:**
- NIST standardized CRYSTALS-Kyber and CRYSTALS-Dilithium in 2024.
- Migration is ongoing. "Harvest now, decrypt later" is the immediate threat.

---

### Concepts Cheat Sheet

| Term | Meaning |
|---|---|
| Hashing | One-way transformation, fixed output |
| Salting | Random data added before hashing |
| Rainbow table | Precomputed hash → password lookup |
| Symmetric encryption | Same key to encrypt and decrypt |
| Asymmetric encryption | Public key encrypts, private key decrypts |
| Diffie-Hellman | Key exchange over untrusted channel |
| Digital signature | Private key signs, public key verifies |
| Passkey | Public-key authentication, phishing-resistant |
| E2EE | End-to-end encryption |
| FDE | Full-disk encryption |
| Ransomware | Encryption used for extortion |
| Qubit | Quantum bit, superposed 0 and 1 |

---

### Numbers to Remember

- 18 quintillion — older hash space mentioned in lecture.
- 2^256 ≈ 1.16 × 10^77 — SHA-256 output space.
- RSA 2048-bit — minimum recommended today.
- AES 256-bit — sufficient against classical + Grover.
- bcrypt cost factor 12 — modern default.

---

### Commands for Revision

```bash
# Hash a file
sha256sum file.iso

# Verify a checksum
sha256sum -c checksums.txt

# Generate a bcrypt hash
htpasswd -bnBC 12 "" "password" | tr -d ':\n'

# Crack MD5 hashes with Hashcat
hashcat -m 0 -a 0 hashes.txt rockyou.txt

# Crack NTLM hashes (Windows)
hashcat -m 1000 -a 0 hashes.txt rockyou.txt

# John the Ripper on Linux shadow
unshadow /etc/passwd /etc/shadow > combined.txt
john --wordlist=rockyou.txt combined.txt

# Generate RSA key pair
openssl genrsa -out private.pem 4096
openssl rsa -in private.pem -pubout -out public.pem

# Encrypt with public key
openssl rsautl -encrypt -pubin -inkey public.pem -in message.txt -out encrypted.bin

# Sign a file
openssl dgst -sha256 -sign private.pem -out signature.bin file.txt

# Verify a signature
openssl dgst -sha256 -verify public.pem -signature signature.bin file.txt

# Secure delete a file (Linux)
shred -u -n 7 sensitive.txt

# Encrypt a disk with LUKS (Linux)
cryptsetup luksFormat /dev/sdX
cryptsetup luksOpen /dev/sdX encrypted_volume
```

---

### Seven Rules of Data Security

1. Never store plaintext passwords. Hash and salt.
2. Use bcrypt, scrypt, or Argon2 for password storage.
3. Use asymmetric crypto to exchange keys, symmetric to move data.
4. Sign what matters. Verify what you receive.
5. Enable full-disk encryption before you store anything.
6. Prefer E2EE services when the content is sensitive.
7. Assume yesterday's crypto is broken tomorrow. Plan for post-quantum.

---

### References

- [NIST SP 800-63B — Digital Identity Guidelines](https://pages.nist.gov/800-63-3/sp800-63b.html)
- [NIST Post-Quantum Cryptography](https://csrc.nist.gov/projects/post-quantum-cryptography)
- [OWASP Cryptographic Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cryptographic_Storage_Cheat_Sheet.html)
- [FIDO Alliance — Passkeys](https://fidoalliance.org/passkeys/)
- [RFC 8017 — PKCS #1 RSA](https://datatracker.ietf.org/doc/html/rfc8017)
- [RFC 8446 — TLS 1.3](https://datatracker.ietf.org/doc/html/rfc8446)
- [Have I Been Pwned](https://haveibeenpwned.com/)

---

### Personal Takeaways

- Hashing raises the bar. Salting closes the loophole. Together they're the baseline.
- Symmetric crypto is fast but needs a shared key. Asymmetric crypto solves the key exchange — slower, but essential.
- Digital signatures prove who sent what. Code signing is a real attack surface.
- Passkeys are the future. Start using them where supported.
- Encryption in transit protects the wire. E2EE protects from the provider. FDE protects the device.
- Deleting a file doesn't delete the data. Encrypt first.
- Quantum is not a today problem. It's a this-decade problem. Start migrating.

---

### Hands-On — Planned Labs

> Labs I'm working through next. I'll update this section with screenshots and writeups as I complete them.

- [ ] Hash a file with `sha256sum` and verify integrity
- [ ] Crack an MD5 hash with Hashcat + `rockyou.txt`
- [ ] Generate an RSA key pair with OpenSSL
- [ ] Sign and verify a file with OpenSSL
- [ ] Enable FileVault / BitLocker / LUKS on a test device
- [ ] Complete TryHackMe room: "Hashing — Crypto 101"

---

### How This Connects to My Next Steps

> **Next lab:** Set up a local Hashcat environment and crack a known MD5 hash from a CTF challenge.
> **Next week in CS50:** Securing Systems — operating system defenses, malware, sandboxing.

---

## Connect

[![GitHub](https://img.shields.io/badge/GitHub-agurzil--kutama-181717?style=flat&logo=github)](https://github.com/agurzil-kutama)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Adel%20Boutaghane-0A66C2?style=flat&logo=linkedin)](https://linkedin.com/in/adel-boutaghane-54b982386)
[![TryHackMe](https://img.shields.io/badge/TryHackMe-IGILGILI18-212C42?style=flat&logo=tryhackme)](https://tryhackme.com/p/IGILGILI18)

> **Next:** Week 2 — Securing Systems.
