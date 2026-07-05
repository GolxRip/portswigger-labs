# Authentication Vulnerabilities Learning Path

**Status:** 🔄 In Progress (13/55)

## Overview

This learning path explores authentication vulnerabilities, which have a critical impact on security. You'll learn about common mechanisms and vulnerabilities, and strategies for robust authentication.

## Topics Covered

### Fundamentals
- [x] What is authentication?
- [x] What is the difference between authentication and authorization?
- [x] How do authentication vulnerabilities arise?
- [x] What is the impact of vulnerable authentication? (2/2)

### Vulnerabilities in Password-based Login (8/19)
- [x] Brute-force attacks
- [x] Brute-forcing usernames
- [x] Brute-forcing passwords
- [x] Brute-forcing passwords - Continued
- [x] Username enumeration
- [x] Username enumeration - Continued
- [x] Lab: Username enumeration via different responses
- [x] Lab: Username enumeration via subtly different responses
- [ ] Lab: Username enumeration via response timing
- [ ] Flawed brute-force protection
- [ ] Lab: Broken brute-force protection, IP block
- [ ] Account locking
- [ ] Account locking - Continued
- [ ] Account locking - Continued
- [ ] Lab: Username enumeration via account lock
- [ ] User rate limiting
- [ ] HTTP basic authentication
- [ ] HTTP basic authentication - Continued

### Vulnerabilities in Multi-factor Authentication (0/8)
- [ ] Two-factor authentication
- [ ] Bypassing two-factor authentication
- [ ] Lab: 2FA simple bypass
- [ ] Lab: 2FA broken logic
- [ ] Lab: 2FA bypass using a brute-force attack
- [ ] Vulnerabilities in other authentication mechanisms
- [ ] Lab: Offline password cracking
- [ ] Lab: Password reset broken logic

### Vulnerabilities in Other Authentication Mechanisms (0/15)
- [ ] OAuth 2.0 authentication
- [ ] OpenID Connect
- [ ] SAML authentication
- [ ] LDAP injection
- [ ] Lab: SAML-based SSO
- [ ] Lab: SAML-based SSO with weak signatures
- [ ] Lab: OAuth implicit flow
- [ ] Lab: OAuth authorization code flow
- [ ] Lab: OAuth OpenID Connect bypass
- [ ] Lab: Stealing OAuth access tokens via proxy
- [ ] Lab: SAML-based login
- [ ] Lab: Insecure direct object references
- [ ] Lab: Horizontal privilege escalation
- [ ] Lab: Vertical privilege escalation
- [ ] Lab: Vertical privilege escalation with user-supplied admin URL

### Preventing Attacks on Your Own Authentication Mechanisms (0/8)
- [ ] Authentication best practices
- [ ] Implementing secure password reset
- [ ] Implementing secure two-factor authentication
- [ ] Lab: Secure password reset implementation
- [ ] Lab: Secure 2FA implementation
- [ ] Lab: Secure OAuth implementation
- [ ] Lab: Secure SAML implementation
- [ ] Lab: Security misconfiguration

## Progress Summary

✅ **Completed:** 13 topics
⏳ **Remaining:** 42 topics

## Key Concepts Learned

- How to identify and enumerate valid usernames
- Different approaches to brute-force attacks
- Timing-based username enumeration
- Account locking mechanisms
- Rate limiting strategies

## Next Steps

1. Complete remaining labs in password-based login section
2. Move on to multi-factor authentication vulnerabilities
3. Study OAuth 2.0 and OpenID Connect flows
4. Explore SAML authentication vulnerabilities

## Resources

- [PortSwigger Authentication Learning Path](https://portswigger.net/web-security/authentication)
- [OWASP Authentication Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html)
