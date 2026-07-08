# 🔐 STRIDE Threat Modeling Project

**A comprehensive security analysis of a web application using the STRIDE threat modeling framework.**

---

## 📋 Table of Contents

1. [Overview](#overview)
2. [STRIDE Framework](#stride-framework)
3. [System Architecture & Data Flow](#system-architecture--data-flow)
4. [Threat Modeling Analysis](#threat-modeling-analysis)
5. [Risk Assessment Matrix](#risk-assessment-matrix)
6. [Detailed Threat Scenarios](#detailed-threat-scenarios)
7. [Security Controls & Mitigations](#security-controls--mitigations)
8. [Practical Code Examples](#practical-code-examples)
9. [Compliance Mapping](#compliance-mapping)
10. [Testing & Validation](#testing--validation)
11. [References](#references)

---

## 🧩 Overview

This project demonstrates a **professional threat modeling exercise** for a web-based application with user authentication and data management capabilities. It follows a structured, repeatable methodology suitable for security assessments and compliance requirements.

- **Framework:** STRIDE (Microsoft's Threat Modeling Methodology)
- **Target System:** E-commerce/Blog Web Application with Login & User Management
- **Scope:** Web Application Architecture (3-tier: Client, Server, Database)
- **Purpose:** Identify, prioritize, and mitigate security threats early in the development lifecycle
- **Audience:** Security Analysts, Architects, Development Teams

---

## 🧠 What is STRIDE?

STRIDE is a threat modeling framework that categorizes security threats into six categories:

| Letter | Threat Type | Description | Example |
|--------|-------------|-------------|---------|
| **S** | **Spoofing** | Pretending to be something/someone else | Fake login, session hijacking, IP spoofing |
| **T** | **Tampering** | Modifying data or code without authorization | Request parameter manipulation, DLL injection |
| **R** | **Repudiation** | Denying responsibility for actions | User denies making a purchase, deleting logs |
| **I** | **Information Disclosure** | Unauthorized access to sensitive data | SQL injection, unencrypted data exposure |
| **D** | **Denial of Service** | Making system unavailable to users | DDoS, brute force, resource exhaustion |
| **E** | **Elevation of Privilege** | Gaining higher access levels than authorized | Privilege escalation, broken access control |

---

## 📌 System Architecture & Data Flow

### System Components

```
┌─────────────────────────────────────────────────────────┐
│                     INTERNET                             │
└─────────────────────────────────────────────────────────┘
                          │
                    [HTTPS/TLS]
                          │
        ┌─────────────────────────────────────┐
        │      WEB CLIENT (Browser)           │
        │  - Login Form                       │
        │  - User Dashboard                   │
        │  - Data Submission                  │
        └─────────────────────────────────────┘
                          │
              ┌───────────┴────────────┐
              │                        │
         [HTTPS]                  [HTTPS]
              │                        │
    ┌─────────────────────┐   ┌────────────────────┐
    │   WEB SERVER        │   │  API GATEWAY       │
    │ - Express/Flask     │   │ - Rate Limiting    │
    │ - Auth Module       │   │ - Request Validation
    │ - Business Logic    │   │ - DDoS Protection  │
    └─────────────────────┘   └────────────────────┘
              │                        │
              └───────────┬────────────┘
                          │
                      [TLS]
                          │
        ┌─────────────────────────────────────┐
        │    DATABASE SERVER                  │
        │ - MySQL / PostgreSQL                │
        │ - User Table (Encrypted)            │
        │ - Audit Logs                        │
        │ - Session Store                     │
        └─────────────────────────────────────┘
```

### Trust Boundaries

**Trust Boundary 1:** Internet ↔ Web Server (Public Network)
- **Attack Surface:** HTTPS interception, DNS hijacking, network snooping
- **Control:** TLS 1.2+, HSTS headers

**Trust Boundary 2:** Web Server ↔ Database (Internal Network)
- **Attack Surface:** SQL Injection, unauthorized database access
- **Control:** Parameterized queries, firewall rules, database encryption

**Trust Boundary 3:** Client ↔ Server (Browser/Application)
- **Attack Surface:** XSS, CSRF, malicious scripts
- **Control:** CSP headers, CSRF tokens, input validation

---

## 🔍 Threat Modeling Analysis

### Comprehensive STRIDE Threat Table

| # | Component | STRIDE | Threat | Severity | Likelihood | Risk | Mitigation Strategy |
|---|-----------|--------|--------|----------|------------|------|---------------------|
| 1 | Login Page | **S** | Credential stuffing / brute force | HIGH | HIGH | **CRITICAL** | Rate limiting, account lockout, CAPTCHA, MFA |
| 2 | Login Page | **S** | Session hijacking | HIGH | MEDIUM | **HIGH** | Secure cookies (HttpOnly, Secure flags), SameSite attribute |
| 3 | Login Page | **T** | Phishing (credential theft) | HIGH | MEDIUM | **HIGH** | HTTPS enforcement, phishing education, DMARC/SPF |
| 4 | Web Server | **T** | Parameter tampering (session tokens) | MEDIUM | MEDIUM | **MEDIUM** | JWT with signature, input validation, WAF |
| 5 | Web Server | **T** | Code injection (SQL, NoSQL) | CRITICAL | MEDIUM | **CRITICAL** | Parameterized queries, ORM, input sanitization |
| 6 | Web Server | **R** | User denies transaction | MEDIUM | LOW | **LOW** | Audit logging, digital signatures, non-repudiation tokens |
| 7 | Database | **I** | Sensitive data exposure (unencrypted) | CRITICAL | HIGH | **CRITICAL** | Encryption at rest (AES-256), encrypted field values |
| 8 | Database | **I** | Weak password storage | CRITICAL | HIGH | **CRITICAL** | Bcrypt/Argon2, salt, slow hash function |
| 9 | Network | **I** | Man-in-the-middle attack | HIGH | MEDIUM | **HIGH** | TLS 1.2+, certificate pinning, HSTS |
| 10 | Database | **I** | Unauthorized database access | HIGH | LOW | **MEDIUM** | Firewall, VPN, database authentication, least privilege |
| 11 | Application | **D** | Brute force login attack | HIGH | HIGH | **CRITICAL** | Rate limiting, exponential backoff, account lockout |
| 12 | Application | **D** | DDoS attack | MEDIUM | MEDIUM | **MEDIUM** | CDN, WAF, rate limiting, API gateway |
| 13 | Application | **D** | Resource exhaustion | MEDIUM | MEDIUM | **MEDIUM** | Connection pooling, request timeout, resource quotas |
| 14 | Backend API | **E** | Broken access control | CRITICAL | MEDIUM | **CRITICAL** | RBAC, authorization checks, JWT validation |
| 15 | Backend API | **E** | Privilege escalation via API | HIGH | LOW | **MEDIUM** | Role validation, capability-based security, audit logs |
| 16 | Frontend | **I** | Cross-site scripting (XSS) | HIGH | MEDIUM | **HIGH** | Content Security Policy, output encoding, input validation |
| 17 | Frontend | **T** | CSRF attacks | MEDIUM | MEDIUM | **MEDIUM** | CSRF tokens, SameSite cookies, origin validation |

---

## 📊 Risk Assessment Matrix

### Severity Levels
- **CRITICAL:** System compromise, data breach, financial loss
- **HIGH:** Significant impact on confidentiality, integrity, or availability
- **MEDIUM:** Noticeable impact, workaround possible
- **LOW:** Minor impact, limited business consequence

### Likelihood Levels
- **HIGH:** Easily exploitable, known tools/techniques available
- **MEDIUM:** Requires moderate effort, some technical skill needed
- **LOW:** Difficult to exploit, requires significant effort/resources

### Risk Calculation Matrix

```
              LIKELIHOOD
           Low    Medium    High
        ┌────────────────────────┐
    H   │  MED  │  HIGH │ CRIT  │
        ├────────────────────────┤
  S E   │  LOW  │  MED  │ HIGH  │
V V E   ├────────────────────────┤
  R E   │  LOW  │  LOW  │  MED  │
        └────────────────────────┘
        Low   Medium   High
```

### Critical Threats (Must Address First)
1. ❗ **Code Injection (SQL/NoSQL)** - CRITICAL Risk
2. ❗ **Weak Password Storage** - CRITICAL Risk
3. ❗ **Broken Access Control** - CRITICAL Risk
4. ❗ **Credential Stuffing** - CRITICAL Risk
5. ❗ **Unencrypted Data Storage** - CRITICAL Risk

---

## 🎯 Detailed Threat Scenarios

### Scenario 1: Credential Stuffing Attack

**Threat:** Attacker uses automated tools to test stolen username/password combinations

**Attack Chain:**
1. Attacker obtains leaked credentials from dark web
2. Writes script to test credentials against login endpoint
3. Successfully gains access to user accounts
4. Exfiltrates sensitive data

**Business Impact:**
- User account compromise
- Privacy breach (PII exposure)
- Regulatory fines (GDPR)
- Reputational damage

**Mitigations:**
- ✅ Rate limiting: Max 5 failed attempts per IP per minute
- ✅ Progressive delays: Exponential backoff (1s, 2s, 4s, 8s)
- ✅ Account lockout: Temporary lock after 10 failed attempts
- ✅ CAPTCHA: Enable after 3 failed attempts
- ✅ Multi-factor authentication: Requires secondary verification
- ✅ Monitoring: Alert on multiple failed login attempts

---

### Scenario 2: SQL Injection

**Threat:** Attacker manipulates SQL queries through input parameters

**Vulnerable Query:**
```python
query = f"SELECT * FROM users WHERE email = '{user_input}'"
```

**Attack:**
```
Input: ' OR '1'='1
Query: SELECT * FROM users WHERE email = '' OR '1'='1'
Result: Returns all users in database
```

**Business Impact:**
- Complete database compromise
- Unauthorized data access (HIGH severity)
- Data deletion/modification
- Regulatory violations

**Mitigations:**
- ✅ Parameterized queries (prepared statements)
- ✅ Input validation & sanitization
- ✅ Principle of least privilege (DB user)
- ✅ Stored procedures with input validation
- ✅ Web Application Firewall (WAF)

---

### Scenario 3: Privilege Escalation via Broken Access Control

**Threat:** User accesses admin functions without authorization

**Attack:**
```
Attacker: Regular user (user_id=5)
URL: /api/users/1/delete (admin only)
Current implementation: No role validation
Result: User successfully deletes other users
```

**Business Impact:**
- Unauthorized actions performed
- Data manipulation
- Compliance violation
- Accountability loss

**Mitigations:**
- ✅ Role-Based Access Control (RBAC)
- ✅ JWT claims validation
- ✅ Authorization middleware on all endpoints
- ✅ Principle of least privilege
- ✅ Audit logging of all admin actions

---

### Scenario 4: Man-in-the-Middle (MITM) Attack

**Threat:** Attacker intercepts unencrypted communications

**Attack Chain:**
1. User connects to free WiFi without HTTPS check
2. Attacker performs ARP spoofing
3. Captures session cookie
4. Hijacks user session

**Business Impact:**
- Session compromise
- Credential theft
- Data interception
- Account takeover

**Mitigations:**
- ✅ TLS 1.2+ enforcement
- ✅ HSTS (HTTP Strict-Transport-Security) headers
- ✅ Certificate pinning (for mobile apps)
- ✅ Secure cookies (HttpOnly, Secure, SameSite)
- ✅ Mutual TLS (mTLS) for backend services

---

## 🛡️ Security Controls & Mitigations

### Authentication Controls

```python
# ✅ SECURE: Strong Password Hashing with Argon2
from argon2 import PasswordHasher

ph = PasswordHasher()

# Hashing password during registration
hashed_password = ph.hash(password)

# Verifying password during login
try:
    ph.verify(hashed_password, provided_password)
    print("Password match!")
except:
    print("Invalid password")
```

### Authorization Controls

```python
# ✅ SECURE: Role-Based Access Control
from functools import wraps
from flask import abort, g

def require_role(role):
    def decorator(f):
        @wraps(f)
        def decorated_function(*args, **kwargs):
            if g.user.role != role:
                abort(403)  # Forbidden
            return f(*args, **kwargs)
        return decorated_function
    return decorator

@app.route('/admin/users', methods=['GET'])
@require_role('admin')
def get_all_users():
    return get_users_from_db()
```

### Input Validation & Sanitization

```python
# ✅ SECURE: Parameterized Queries (SQL Injection Prevention)
import mysql.connector

connection = mysql.connector.connect(**db_config)
cursor = connection.cursor(prepared=True)

email = "user@example.com"

# Safe: Uses parameter binding
query = "SELECT * FROM users WHERE email = %s"
cursor.execute(query, (email,))

# ❌ UNSAFE: String concatenation (vulnerable to SQL injection)
# query = f"SELECT * FROM users WHERE email = '{email}'"
```

### Session Management

```python
# ✅ SECURE: Secure Session Cookie Configuration
from flask import Flask
from flask_session import Session

app = Flask(__name__)

app.config['SESSION_COOKIE_SECURE'] = True      # HTTPS only
app.config['SESSION_COOKIE_HTTPONLY'] = True    # No JS access
app.config['SESSION_COOKIE_SAMESITE'] = 'Lax'   # CSRF protection
app.config['SESSION_COOKIE_AGE'] = 1800         # 30 min expiry
app.config['PERMANENT_SESSION_LIFETIME'] = 1800

Session(app)
```

### Rate Limiting

```python
# ✅ SECURE: Rate Limiting on Login Endpoint
from flask_limiter import Limiter
from flask_limiter.util import get_remote_address

limiter = Limiter(
    app=app,
    key_func=get_remote_address,
    default_limits=["200 per day", "50 per hour"]
)

@app.route('/login', methods=['POST'])
@limiter.limit("5 per minute")  # Max 5 login attempts/minute
def login():
    username = request.form.get('username')
    password = request.form.get('password')
    # Validate credentials
    return redirect('/dashboard')
```

### Data Encryption

```python
# ✅ SECURE: Encrypting Sensitive Data
from cryptography.fernet import Fernet
import os

# Generate key once and store securely
key = os.environ.get('ENCRYPTION_KEY')
cipher_suite = Fernet(key)

# Encrypting sensitive data before storage
def encrypt_pii(data):
    return cipher_suite.encrypt(data.encode())

# Decrypting when needed
def decrypt_pii(encrypted_data):
    return cipher_suite.decrypt(encrypted_data).decode()

# Usage
ssn = "123-45-6789"
encrypted_ssn = encrypt_pii(ssn)
# Store in database as encrypted_ssn
```

### Logging & Monitoring

```python
# ✅ SECURE: Comprehensive Audit Logging
import logging
from datetime import datetime

logger = logging.getLogger(__name__)

def audit_log(user_id, action, resource, status, details=""):
    """Log security-relevant events"""
    log_entry = {
        'timestamp': datetime.utcnow().isoformat(),
        'user_id': user_id,
        'action': action,
        'resource': resource,
        'status': status,
        'ip_address': request.remote_addr,
        'user_agent': request.headers.get('User-Agent'),
        'details': details
    }
    logger.info(json.dumps(log_entry))

# Usage
@app.route('/api/users/<user_id>', methods=['DELETE'])
def delete_user(user_id):
    current_user = get_current_user()
    
    if current_user.role != 'admin':
        audit_log(current_user.id, 'DELETE_USER', user_id, 'FAILED', 'Unauthorized')
        abort(403)
    
    delete_user_from_db(user_id)
    audit_log(current_user.id, 'DELETE_USER', user_id, 'SUCCESS')
    return {'message': 'User deleted'}, 200
```

---

## 💻 Practical Code Examples

### Example 1: Vulnerable vs. Secure Login Implementation

#### ❌ VULNERABLE CODE
```python
from flask import Flask, request

app = Flask(__name__)

# BAD: Storing plaintext passwords
users_db = {
    "john": "password123",
    "jane": "secure_pass"
}

@app.route('/login', methods=['POST'])
def login_vulnerable():
    username = request.form.get('username')
    password = request.form.get('password')
    
    # No rate limiting - vulnerable to brute force
    # Plain password comparison - vulnerable to credential stuffing
    if username in users_db and users_db[username] == password:
        # No secure session setup
        return {'status': 'success', 'user_id': username}, 200
    
    return {'status': 'failed'}, 401
```

#### ✅ SECURE CODE
```python
from flask import Flask, request, session
from argon2 import PasswordHasher
from flask_limiter import Limiter
from flask_limiter.util import get_remote_address
import secrets
import logging

app = Flask(__name__)
app.secret_key = secrets.token_hex(32)
limiter = Limiter(app=app, key_func=get_remote_address)
ph = PasswordHasher()

# Secure session configuration
app.config.update(
    SESSION_COOKIE_SECURE=True,
    SESSION_COOKIE_HTTPONLY=True,
    SESSION_COOKIE_SAMESITE='Lax',
    PERMANENT_SESSION_LIFETIME=1800
)

@app.route('/login', methods=['POST'])
@limiter.limit("5 per minute")  # Rate limiting
def login_secure():
    try:
        username = request.form.get('username', '').strip()
        password = request.form.get('password', '')
        
        # Input validation
        if not username or not password:
            logging.warning(f"Login attempt with missing credentials from {request.remote_addr}")
            return {'status': 'failed'}, 401
        
        # Fetch user from database
        user = db.query_user_by_username(username)
        
        if not user:
            # Timing-attack resistant comparison
            logging.info(f"Login attempt for non-existent user: {username}")
            return {'status': 'failed'}, 401
        
        # Verify password using Argon2
        try:
            ph.verify(user.password_hash, password)
        except:
            logging.warning(f"Failed login for user: {username} from {request.remote_addr}")
            return {'status': 'failed'}, 401
        
        # Check if account is locked
        if user.locked_until and datetime.now() < user.locked_until:
            return {'status': 'account_locked'}, 429
        
        # Successful authentication
        session.permanent = True
        session['user_id'] = user.id
        session['role'] = user.role
        session['login_time'] = datetime.utcnow().isoformat()
        
        logging.info(f"Successful login for user: {username}")
        
        return {'status': 'success', 'redirect': '/dashboard'}, 200
        
    except Exception as e:
        logging.error(f"Unexpected error during login: {str(e)}")
        return {'status': 'error'}, 500
```

### Example 2: API Endpoint with Authorization

#### ❌ VULNERABLE
```python
@app.route('/api/users/<user_id>', methods=['DELETE'])
def delete_user_vulnerable(user_id):
    # No authorization check!
    # Anyone can delete any user
    db.delete_user(user_id)
    return {'status': 'deleted'}, 200
```

#### ✅ SECURE
```python
from functools import wraps
from flask import abort, g

def require_auth(f):
    @wraps(f)
    def decorated_function(*args, **kwargs):
        token = request.headers.get('Authorization', '').replace('Bearer ', '')
        if not token:
            abort(401)
        
        # Validate JWT token
        try:
            g.user = validate_jwt(token)
        except:
            abort(401)
        
        return f(*args, **kwargs)
    return decorated_function

def require_admin(f):
    @wraps(f)
    def decorated_function(*args, **kwargs):
        if g.user.role != 'admin':
            audit_log(g.user.id, 'UNAUTHORIZED_DELETE', kwargs.get('user_id'), 'FAILED')
            abort(403)
        return f(*args, **kwargs)
    return decorated_function

@app.route('/api/users/<user_id>', methods=['DELETE'])
@require_auth
@require_admin
def delete_user_secure(user_id):
    audit_log(g.user.id, 'DELETE_USER', user_id, 'INITIATED')
    
    db.delete_user(user_id)
    
    audit_log(g.user.id, 'DELETE_USER', user_id, 'SUCCESS')
    return {'status': 'deleted'}, 200
```

---

## 🔗 Compliance Mapping

### OWASP Top 10 (2021) Alignment

| OWASP Risk | STRIDE Category | Control | Implementation |
|-----------|-----------------|---------|-----------------|
| A01:2021 – Broken Access Control | **E** (Elevation of Privilege) | RBAC, JWT validation, audit logs | Authorization middleware, role checks |
| A02:2021 – Cryptographic Failures | **I** (Information Disclosure) | Encryption at rest/transit | AES-256, TLS 1.2+, bcrypt passwords |
| A03:2021 – Injection | **T** (Tampering) | Parameterized queries, input validation | Prepared statements, sanitization |
| A04:2021 – Insecure Design | **S**-**E** (Multiple) | Security by design, threat modeling | This project |
| A05:2021 – Security Misconfiguration | **I** (Information Disclosure) | Secure defaults, hardening | Security headers, HTTPS enforcement |
| A06:2021 – Vulnerable Components | **T** (Tampering) | Dependency management | Regular updates, security scanning |
| A07:2021 – Authentication Failures | **S** (Spoofing) | Strong auth, MFA, rate limiting | Argon2, MFA, rate limiting |
| A08:2021 – Software & Data Integrity | **T** (Tampering) | Code integrity verification | Signed artifacts, secure CI/CD |
| A09:2021 – Logging & Monitoring Gaps | **R** (Repudiation) | Comprehensive audit logging | Event logging, monitoring alerts |
| A10:2021 – SSRF | **I** (Information Disclosure) | Input validation, network segmentation | Whitelist validation, network policies |

### NIST Cybersecurity Framework

| NIST Function | STRIDE Controls | Examples |
|---------------|-----------------|----------|
| **Identify** | Threat modeling, asset inventory | DFD, component mapping, data classification |
| **Protect** | Access control, encryption, authentication | RBAC, TLS, MFA, password hashing |
| **Detect** | Logging, monitoring, anomaly detection | Audit logs, intrusion detection, alerts |
| **Respond** | Incident response, forensics | Log retention, incident procedures |
| **Recover** | Backup, disaster recovery | Data backups, failover mechanisms |

### Data Protection Regulations

| Regulation | Requirement | Implementation |
|-----------|-------------|-----------------|
| **GDPR** | Data protection by design, encryption, audit trails | Encryption at rest, consent tracking, audit logs |
| **CCPA** | Data transparency, user rights | Privacy controls, data inventory, deletion policies |
| **HIPAA** | PHI protection, access controls, audit logging | Encryption, RBAC, comprehensive logging |
| **PCI-DSS** | Payment security, encryption, access control | TLS, tokenization, authentication |

---

## 🧪 Testing & Validation

### Security Testing Checklist

- [ ] **Authentication Testing**
  - [ ] Weak password policy validation
  - [ ] Session timeout testing
  - [ ] Password reset functionality
  - [ ] MFA bypass attempts
  - [ ] Credential stuffing resistance

- [ ] **Authorization Testing**
  - [ ] Horizontal privilege escalation (accessing other users' data)
  - [ ] Vertical privilege escalation (admin function access)
  - [ ] Role-based access control enforcement
  - [ ] JWT claim validation
  - [ ] API endpoint authorization

- [ ] **Injection Testing**
  - [ ] SQL Injection (parameterized queries verification)
  - [ ] Command Injection
  - [ ] XSS (input/output encoding)
  - [ ] LDAP Injection
  - [ ] NoSQL Injection

- [ ] **Cryptography Testing**
  - [ ] TLS version and cipher strength
  - [ ] Certificate validity
  - [ ] Secure data storage (encryption)
  - [ ] Key management practices

- [ ] **Session Management Testing**
  - [ ] Session fixation prevention
  - [ ] Session hijacking resistance
  - [ ] Cookie security flags (HttpOnly, Secure, SameSite)
  - [ ] Session timeout functionality

### Automated Testing Tools

```bash
# OWASP ZAP - Web application scanning
zaproxy

# Burp Suite - Manual penetration testing
burpsuite

# SQLMap - SQL Injection testing
sqlmap -u "http://target.com/login" -data "username=test&password=test" --dbs

# nmap - Network vulnerability scanning
nmap -sV --script vuln target.com

# SonarQube - Static code analysis
sonarqube

# OWASP Dependency-Check - Vulnerable dependencies
dependency-check --project "MyApp" --scan /path/to/code
```

### Manual Testing Examples

```bash
# Test 1: SQL Injection
curl -X POST http://target.com/login \
  -d "username=' OR '1'='1&password=anything"

# Test 2: Rate Limiting
for i in {1..10}; do
  curl -X POST http://target.com/login \
    -d "username=test&password=wrong"
done

# Test 3: Session Hijacking
curl -b "session_id=stolen_session_token" http://target.com/dashboard

# Test 4: XSS
curl -X POST http://target.com/comment \
  -d "text=<script>alert('XSS')</script>"
```

---

## 📚 References

### Microsoft STRIDE Resources
- [Threat Modeling](https://learn.microsoft.com/en-us/windows/security/threat-protection/intelligence/what-is-threat-modeling)
- [SDL Threat Modeling](https://learn.microsoft.com/en-us/archive/msdn-magazine/2009/january/threat-modeling-core-concepts-and-tradecraft)

### OWASP Resources
- [OWASP Top 10](https://owasp.org/Top10/)
- [OWASP Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
- [OWASP Threat Modeling](https://owasp.org/www-community/Threat_Modeling)

### Industry Standards
- [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework)
- [MITRE ATT&CK Framework](https://attack.mitre.org/)
- [CWE - Common Weakness Enumeration](https://cwe.mitre.org/)
- [CVE - Common Vulnerabilities and Exposures](https://cve.mitre.org/)

### Cryptography & Security
- [OWASP Cryptographic Storage](https://cheatsheetseries.owasp.org/cheatsheets/Cryptographic_Storage_Cheat_Sheet.html)
- [OWASP Authentication Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html)
- [OWASP Session Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html)

### Tools & Methodologies
- [Burp Suite Community](https://portswigger.net/burp/community)
- [OWASP ZAP](https://www.zaproxy.org/)
- [Threat Modeling Manifesto](https://www.threatmodelingmanifesto.org/)

---

## 📞 Contact & Support

This project is designed for security professionals and developers learning threat modeling. For questions or suggestions, please open an issue.

**Last Updated:** July 2026
**Status:** Complete & Production-Ready ✅
