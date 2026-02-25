# 💻 Medium-Level SQL Injection Defense Analysis  

**Web Application Security Assessment – SQL Injection Bypass**

---

## 📌 Project Overview

This project demonstrates a hands-on security assessment of a web application implementing **medium-level defenses against SQL injection**. The focus was to analyze why common SQL injection payloads fail and identify logical vulnerabilities that still allow attackers to bypass input filtering.

Objectives:

- Evaluate effectiveness of medium-level defenses  
- Test numeric and string input handling  
- Demonstrate bypass techniques using boolean-based and UNION-based SQL injection  
- Highlight proper secure coding practices and mitigation strategies  

All testing was performed in a controlled lab environment using simulated accounts and test data.

---

## 🏗 Environment & Defenses Tested

| Security Level | Defenses Introduced |
|----------------|------------------|
| Medium | - Input escaping using `mysql_real_escape_string()`<br>- POST requests only (prevents URL manipulation)<br>- Dropdown input control limiting user IDs (1–5) |

**Key Observation:**  
While basic SQL injection payloads (e.g., `' OR '1'='1`) were blocked by escaping, the numeric input context remained vulnerable.

---

## 🔹 Exploitation & Discovery

### 1️⃣ Failed Attempt (Expected)
```sql
Payload: 1' OR '1'='1
Result: Blocked by escaping quotes
```

### 2️⃣ Successful Bypass
```sql
Payload: 1 OR 1
Result: All user records returned
```

**Insights:**

- Numeric inputs were not validated or type-checked  
- Escaping is ineffective in numeric contexts  
- Client-side controls (dropdown) are easily bypassed  
- Boolean-based SQL logic injection works without special characters  

---

## 🔹 Data Extraction

- **Column count determined using UNION injection**  
- **Database content retrieved via controlled SELECT statements**  
- Extracted sensitive information:
  - 5 user accounts  
  - Administrator account credentials (hashed passwords)  

📸 Evidence: UNION-based data extraction output

---

## ⚠ Impact Assessment

- Full read access to the `users` table  
- Exposure of all user credentials  
- Administrator account compromised  
- Weak defenses mislead developers into a false sense of security  

---

## 🔹 Defense & Remediation

### Why Medium Protection is Insufficient

- Relies solely on input escaping  
- Concatenates user input directly into SQL queries  
- Fails to separate SQL code from user data  
- Trusts client-side controls  

### Coding Mistake Example
```php
$query = "SELECT first_name, last_name FROM users WHERE user_id = $id";
```

### Recommended Best Practices

- Use **prepared statements** and **parameterized queries**  
- Enforce strict **server-side input validation**  
- Never rely on client-side controls for security  
- Use modern password hashing (e.g., **Argon2id**) instead of MD5  
- Adopt secure coding design from the start  

---

## 🔹 Key Lessons Learned

- Escaping input is **not sufficient** to prevent SQL injection  
- Numeric input can bypass escaping if type validation is missing  
- Proper mitigation requires **secure design, parameterized queries, and server-side enforcement**  

---

## 🔹 Skills Demonstrated

- Web application penetration testing  
- SQL injection analysis (boolean-based, UNION-based)  
- Input validation and numeric context evaluation  
- Secure coding recommendations  
- Academic and corporate-style security reporting  

---

## ⚖ Ethical Statement

All testing was conducted in a **controlled lab environment** using simulated accounts. No real user data was accessed or compromised. AI assistance (ChatGPT) was used **only for report polishing and structuring**, not for generating findings.
