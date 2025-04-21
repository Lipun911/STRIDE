# 🔐 STRIDE Threat Modeling Project

## 🧩 Overview

This project demonstrates how to apply the **STRIDE threat modeling framework** to a basic web application. It walks through identifying potential threats, mapping them to the system's architecture, and suggesting mitigations for each risk.

- **Model Used:** STRIDE (Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service, Elevation of Privilege)
- **Target System:** Simple web application with login functionality (e.g., blog or e-commerce site)
- **Goal:** Identify and document threats early in the development process

---

## 🧠 What is STRIDE?

| Letter | Threat Type              | Description                             |
|--------|--------------------------|-----------------------------------------|
| S      | **Spoofing**             | Impersonating something or someone else |
| T      | **Tampering**            | Modifying data or code                  |
| R      | **Repudiation**          | Denying performing an action            |
| I      | **Information Disclosure**| Exposing information to unauthorized users |
| D      | **Denial of Service**    | Making the system unavailable           |
| E      | **Elevation of Privilege**| Gaining unauthorized access levels      |

---

## 📌 System Architecture (DFD - Data Flow Diagram)

The system consists of:
- **User (External Entity)**
- **Web Client (Browser)**
- **Web Server (Flask or Node.js App)**
- **Database Server (MySQL/Postgres)**
- **Authentication Module**

### Data Flow Diagram 
![Diagram](https://github.com/user-attachments/assets/e5055ac0-1bfd-4593-893e-5665afcafbab)

---

## 🔍 STRIDE Threat Modeling Table

| System Component | STRIDE Category | Threat Description | Suggested Mitigation |
|------------------|------------------|---------------------|-----------------------|
| Login Page       | Spoofing          | Attacker logs in using stolen credentials | Implement MFA, rate limiting, and account lockout |
| Web Server       | Tampering         | Altered request parameters (e.g., tampered session token) | Use signed tokens, input validation |
| Web Server       | Repudiation       | User denies performing a transaction | Enable secure audit logging and non-repudiation mechanisms |
| Database         | Information Disclosure | Sensitive user info stored unencrypted | Encrypt data at rest, use hashed passwords (bcrypt) |
| Application      | Denial of Service | Login form can be brute-forced | Implement rate-limiting, WAF, CAPTCHA |
| Backend APIs     | Elevation of Privilege | Attacker accesses admin-only features | Enforce role-based access control, validate JWT claims |

---

## 🛡️ Security Recommendations

- 🔒 **Use HTTPS** for all data in transit.
- 👥 **Implement MFA** to protect against spoofing attacks.
- 🧩 **Secure Sessions** with signed and encrypted tokens.
- 📜 **Enable Logging** for all sensitive operations.
- ⛔ **Rate Limiting** on login and critical API endpoints.
- 🔐 **Encrypt PII and sensitive data** in the database.

---

## 📁 Project Structure

```bash
📁 stride-threat-modeling/
├── README.md
├── dfd-diagram.png
├── stride-threat-table.md
└── report.pdf (optional)
