# Datacom Cyber Security Operations (Forage)

## Objective
To simulate the responsibilities of a Cybersecurity Analyst Graduate. This involved investigating a live ransomware incident and conducting a comprehensive 5x5 risk assessment for a mid-sized enterprise, focusing on both technical vulnerabilities and business impact.

## 1. Incident Response: Orion Health Services
**Scenario:** A mid-sized healthcare provider suffered a ransomware attack via a phishing email, leading to the encryption of file servers and the exfiltration of sensitive data.

**My Task:** Acted as the incident responder to analyze the attack vector and write an executive-level briefing.

**Key Technical Concepts Applied:**
*   **Initial Access:** Phishing email with a malicious Excel attachment.
*   **Credential Access:** Identified the use of **Mimikatz** to dump credentials from Windows LSASS memory, allowing lateral movement.
*   **Indicators of Compromise (IOCs):** `.orionlock` file extensions, suspicious overseas IP logins.
*   **Business Impact:** Addressed regulatory fines, reputational damage, and physical risks to employees due to exposed payroll/PII data.

[📄 View the Full Incident Response Report](/cybersecurity-operations/datacom-forage-simulation/Orion_Health_Incident_Report.pdf)

---

## 2. Risk Assessment: RetailNova Pty Ltd
**Scenario:** A national retail company (1,200 staff, 85 stores) with a history of security incidents and a reliance on AWS, POS systems, and third-party vendors.

**My Task:** Conducted a formal risk assessment using a 5x5 Risk Matrix to identify, score (Likelihood x Consequence), and mitigate three major risks.

**The 3 Risks Identified:**
1.  **Ransomware Encryption of POS & AWS (Inherent Risk: HIGH)**
    *   *Mitigation:* Network Segmentation (VLANs), EDR implementation, and offline immutable backups.
2.  **Phishing & Credential Compromise (Inherent Risk: HIGH)**
    *   *Mitigation:* Phishing-resistant MFA, Mobile Device Management (MDM) for BYOD, and continuous security awareness training.
3.  **Third-Party Vendor Data Leak (Inherent Risk: HIGH)**
    *   *Mitigation:* Vendor Risk Management (VRM), requiring SOC 2 Type II reports, and Data Loss Prevention (DLP) tools.

[📄 View the Full Risk Assessment Report](/cybersecurity-operations/datacom-forage-simulation/RetailNova_Risk_Assessment.pdf)

---
## Key Takeaways
*   **Business Context Matters:** A breach is not just a technical failure; it is a financial and reputational crisis. 
*   **Defense in Depth:** Basic antivirus is insufficient. Modern defense requires EDR, network segmentation, and strict vendor management.
*   **Risk Quantification:** Understanding how to use a 5x5 matrix to prioritize risks and communicate them to executive leadership.
