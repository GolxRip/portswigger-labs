# Cross-Site Request Forgery (CSRF) Learning Path

**Status:** ✅ Completed (49/49)
**Completed:** 21 Jun 2026

## Overview

This learning path covers CSRF (Cross-Site Request Forgery). You'll learn about common CSRF vulnerabilities and how to prevent them.

## Topics Covered

### 1. CSRF Fundamentals
- Understanding CSRF attacks
- How browsers handle cross-site requests
- Attack vectors and impact

### 2. Common CSRF Vulnerabilities
- Unvalidated state-changing requests
- Missing or predictable tokens
- SameSite cookie bypass

### 3. CSRF Attack Techniques
- Token prediction
- Token extraction from responses
- Using GET requests
- Bypassing referer validation

### 4. CSRF Defense Mechanisms
- Anti-CSRF tokens
- SameSite cookies
- Custom request headers
- Referer validation
- Verifying origin

### 5. Prevention Strategies
- Implementing CSRF tokens
- SameSite cookie attribute
- Double-submit cookies
- Custom headers
- Origin validation

## Labs Completed

Add your completed labs here with links to solutions.

## Key Takeaways

- CSRF can be used to perform unauthorized actions on behalf of users
- Proper token implementation is critical
- SameSite cookies provide strong protection
- Combine multiple defense mechanisms
- Test for CSRF in all state-changing operations

## Resources

- [PortSwigger CSRF Learning Path](https://portswigger.net/web-security/csrf)
- [OWASP CSRF Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html)
