# 🔐 STRIDE Threat Table – OWASP Juice Shop

This table applies the STRIDE framework to OWASP Juice Shop, a deliberately insecure web application used for security training and testing.

| System Component     | STRIDE Category       | Threat Description                                                                 | Suggested Mitigation                                                                 |
|----------------------|------------------------|-------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|
| Login Page           | Spoofing               | Attacker uses default credentials or brute-force login                             | MFA, account lockout, CAPTCHA, strong password policies                                |
| User Profile         | Tampering              | Authenticated user modifies other users' data using IDOR                           | Implement object-level access control, validate user ownership                         |
| Order History Page   | Repudiation            | User deletes or alters order history logs to hide actions                          | Use secure audit logs with integrity checks                                            |
| Product Reviews      | Information Disclosure | Application leaks admin email addresses or sensitive comments                      | Input validation, moderate/display controls, avoid exposing sensitive metadata         |
| Feedback Form        | Denial of Service      | Abuser automates form submissions, flooding the system                             | CAPTCHA, rate-limiting, IP throttling                                                  |
| Admin Panel Access   | Elevation of Privilege | Unauthorized users access admin dashboard via hidden path or tampered token        | Role-based access control, secure token handling, hide admin routes                    |
| REST API (Products)  | Tampering              | Client modifies product prices or discounts via API manipulation                   | Input validation, server-side enforcement of business logic                            |
| Login Endpoint       | Information Disclosure | Application reveals valid usernames during login errors                            | Generic error messages, timing attack mitigations                                      |

