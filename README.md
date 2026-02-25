# 💻 DVWA Medium-Level SQL Injection Assessment  

**Controlled Security Review – Web Application Penetration Testing**  

---

## 📌 Project Overview

This project documents a hands-on security assessment of **Damn Vulnerable Web Application (DVWA)** at the **Medium security level**, conducted in a controlled lab environment. The goal was to evaluate whether DVWA’s medium-level defenses against SQL injection were effective and to demonstrate techniques for exploiting residual vulnerabilities.

**Key Outcome:**  
Despite input escaping, POST-only requests, and dropdown input controls, SQL injection was successfully executed, allowing extraction of sensitive data such as user credentials and password hashes.  

- **CVSS 3.1 Score:** 9.8 (Critical)  
- **OWASP Category:** A03:2021 – Injection  
- **Authorization:** AltSchool Africa Internal Training  
- **Testing Date:** February 2, 2026  

---

## 🏗 Lab Environment

| Component | Details |
|-----------|---------|
| Target Application | DVWA (Medium security) |
| Platform | Kali Linux VM (isolated lab) |
| Module Tested | SQL Injection |
| Database Connection | Verified functional |
| Exploitation Tool | Manual HTTP requests, browser, Burp Suite (for observation) |

---

## 🔹 Medium-Level Defense Analysis

**Defenses Introduced at Medium Level**:

1. **Input Escaping** – `mysql_real_escape_string()` prevents quote-based payloads.  
2. **POST Requests Only** – URL manipulation is blocked.  
3. **Dropdown Input Control** – Limits user IDs visible on the client side (1–5).  

**Why Basic SQL Injection Fails:**  
- Classic payloads like `' OR '1'='1` fail due to quote escaping.  

**Critical Developer Mistake:**  
- User input is directly concatenated into numeric SQL queries without type validation.  
- Escaping is ineffective against numeric boolean-based expressions.  

---

## 🔹 Exploitation & Discovery

**Failed Attempt (Expected):**
```sql
Payload: 1' OR '1'='1
Result: Blocked by escaping
```

**Successful Bypass (Key Insight):**
```sql
Payload: 1 OR 1
Result: All user records returned
```

**Explanation:**  
- Input treated as a number, not a string  
- Escaping ineffective in numeric context  
- Client-side dropdown controls bypassed easily  

**Data Extraction via UNION Injection:**  
- Column count determined using UNION injection  
- Controlled SELECT statements extracted sensitive data:  
  - 5 user accounts  
  - Administrator account credentials (hashed)  

📸 Evidence: UNION-based data extraction output

---

## ⚠ Evidence & Impact

**Compromised Data:**  
- Full read access to the `users` table  
- Administrator account compromised  
- Password hashes available for offline cracking  

**Reason for Bypass:**  
- Numeric input not validated  
- Escaping ineffective for logical expressions  
- Client-side controls insufficient for security  

---

## 🔹 Defense & Remediation

**Why Medium Protection Is Insufficient:**  
- Relies on input filtering rather than secure design  
- Fails to separate SQL code from user input  
- No use of prepared statements or parameterized queries  

**Exact Coding Mistake:**
```php
$query = "SELECT first_name, last_name FROM users WHERE user_id = $id";
```

**Recommended Best Practices:**  
- Use **prepared statements** and **parameterized queries**  
- Enforce **strict server-side input validation**  
- Never trust **client-side controls**  
- Replace MD5 password hashing with **Argon2id**  
- Secure design from the start is essential  

---

## 🔹 Key Lessons Learned

- Escaping input alone is **insufficient** against SQL injection  
- Numeric input may bypass string-based defenses  
- Secure design, parameterized queries, and server-side validation are critical to application security  

---

## 🔹 Skills Demonstrated

- Web application penetration testing  
- SQL injection analysis (boolean-based, UNION-based)  
- Input validation evaluation  
- Secure coding recommendations  
- Academic and corporate-style security reporting  

---

## ⚖ Ethical Statement

All testing was conducted in a **controlled lab environment** using simulated accounts. No real user data was accessed or compromised. AI assistance (ChatGPT) was used **only for report polishing and structuring**, not for generating findings.
