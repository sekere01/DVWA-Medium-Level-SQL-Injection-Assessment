<div align="center">

# 💉 DVWA Medium-Level SQL Injection Assessment

![Type](https://img.shields.io/badge/Type-Web%20App%20Pentest-red?style=for-the-badge)
![CVSS](https://img.shields.io/badge/CVSS%203.1-9.8%20Critical-critical?style=for-the-badge)
![OWASP](https://img.shields.io/badge/OWASP-A03%3A2021%20Injection-orange?style=for-the-badge)
![Auth](https://img.shields.io/badge/Authorization-AltSchool%20Africa-blue?style=for-the-badge)

> A hands-on security assessment of DVWA at Medium security level, demonstrating that input escaping alone is insufficient to prevent SQL injection — resulting in full extraction of user credentials and password hashes.

</div>

---

## 📌 Project Overview

This project documents a controlled penetration test of **Damn Vulnerable Web Application (DVWA)** at the **Medium security level**. The goal was to evaluate whether DVWA's medium-level defenses against SQL injection were effective and to demonstrate techniques for exploiting residual vulnerabilities.

**Key Outcome:** Despite input escaping, POST-only requests, and dropdown input controls, SQL injection was successfully executed — allowing full extraction of user credentials and password hashes.

| Field | Details |
|---|---|
| **CVSS 3.1 Score** | 9.8 — Critical |
| **OWASP Category** | A03:2021 – Injection |
| **Authorization** | AltSchool Africa Internal Training |
| **Testing Date** | February 2, 2026 |
| **Tester** | sekere01 |

---

## 🏗️ Lab Environment

| Component | Details |
|---|---|
| **Target Application** | DVWA (Medium security level) |
| **Platform** | Kali Linux VM — fully isolated lab |
| **Module Tested** | SQL Injection |
| **Exploitation Method** | Manual HTTP requests, browser intercept |
| **Observation Tool** | Burp Suite |

---

## 🛡️ Medium-Level Defense Analysis

DVWA's medium security level introduces three defenses:

| Defense | Implementation |
|---|---|
| Input Escaping | `mysql_real_escape_string()` — blocks quote-based payloads |
| POST Requests Only | URL manipulation blocked |
| Dropdown Input Control | Limits visible user IDs to 1–5 on the client side |

**Why these defenses fail:**
- Quote escaping is **ineffective against numeric boolean expressions**
- User input is **directly concatenated** into SQL queries without type validation
- Client-side dropdown controls are **trivially bypassed** via modified HTTP requests

---

## 🔬 Exploitation & Discovery

### Failed Attempt (Expected)
```sql
Payload: 1' OR '1'='1
Result:  Blocked — quote escaping prevents string-based injection
```

### Successful Bypass
```sql
Payload: 1 OR 1=1
Result:  All user records returned
```

**Why it works:** The input is treated as a numeric value, not a string. Escaping has no effect on logical expressions — the vulnerable query concatenates directly:

```php
// Vulnerable code
$query = "SELECT first_name, last_name FROM users WHERE user_id = $id";
```

### Data Extraction via UNION Injection
- Column count determined using `ORDER BY` and `UNION SELECT` probing
- Controlled `SELECT` statements extracted:
  - 5 user accounts
  - Administrator credentials (MD5-hashed)
  - Full contents of the `users` table

---

## ⚠️ Impact

| Impact | Details |
|---|---|
| **Data Exposure** | Full read access to the `users` table |
| **Account Compromise** | Administrator account credentials extracted |
| **Password Hashes** | MD5 hashes available for offline cracking |
| **Auth Bypass** | Client-side controls entirely circumvented |

---

## 🔒 Remediation

**Root Cause:** Reliance on input filtering rather than secure query design.

### Vulnerable Code
```php
// ❌ Direct concatenation — never do this
$query = "SELECT first_name, last_name FROM users WHERE user_id = $id";
```

### Secure Fix
```php
// ✅ Parameterized query
$stmt = $pdo->prepare("SELECT first_name, last_name FROM users WHERE user_id = ?");
$stmt->execute([$id]);
```

### Recommended Best Practices
- Use **prepared statements** and **parameterized queries** — not input escaping
- Enforce **strict server-side type validation** on all inputs
- Never rely on **client-side controls** as a security mechanism
- Replace **MD5** password hashing with **Argon2id**
- Apply **principle of least privilege** to database accounts

---

## 📘 Key Lessons

- Input escaping alone is **not sufficient** against SQL injection
- Numeric inputs can **bypass string-based defenses** entirely
- Secure design must be built in from the start — it cannot be patched on top

---

## 🧠 Skills Demonstrated

- Web application penetration testing
- SQL injection — boolean-based and UNION-based techniques
- HTTP request manipulation and client-side control bypass
- Input validation evaluation
- Secure coding recommendations (PHP / PDO)
- Corporate-style security reporting with CVSS scoring

---

## 📄 Report

The full assessment report is available here:

| File | Description |
|---|---|
| [`DVWA_report.pdf`](DVWA_report.pdf) | Complete penetration test report with evidence and findings |

> **Note:** PDF files will download rather than preview directly on GitHub.

---

## ⚖️ Ethical Statement

> All testing was conducted in a **controlled lab environment** using a deliberately vulnerable application (DVWA) with simulated accounts. No real user data was accessed or compromised.
>
> This assessment was authorized as part of **AltSchool Africa** internal security training.
>
> **Do not replicate these techniques against any system you do not own or have explicit written permission to test.** Unauthorized access is illegal under the CFAA and equivalent legislation worldwide.