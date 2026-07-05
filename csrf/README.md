# Cross-Site Request Forgery (CSRF) Learning Path

**Status:** ✅ Completed (49/49)
**Completed:** 21 Jun 2026

## Overview

This learning path covers CSRF (Cross-Site Request Forgery). You'll learn about common CSRF vulnerabilities and how to prevent them.

## Topics Covered

### What is CSRF?
- [x] What is CSRF?

### Impact and Mechanics
- [x] What is the impact of a CSRF attack?
- [x] How does CSRF work? (4/4)

### CSRF Attack Construction
- [x] How to construct a CSRF attack (2/2)
- [x] How to deliver a CSRF exploit

### Common Defenses
- [x] Common defences against CSRF

### CSRF Tokens
- [x] What is a CSRF token? (2/2)
- [x] Common flaws in CSRF token validation (12/12)

### SameSite Cookie Restrictions
- [x] Bypassing SameSite cookie restrictions
- [x] What is a site in the context of SameSite cookies? (2/2)
- [x] How does SameSite work? (6/6)
- [x] Bypassing SameSite Lax restrictions using GET requests (3/3)
- [x] Bypassing SameSite restrictions using on-site gadgets (3/3)
- [x] Bypassing SameSite restrictions via vulnerable sibling domains (2/2)
- [x] Bypassing SameSite Lax restrictions with newly issued cookies (3/3)

### Referer-based Defenses
- [x] Bypassing referer-based CSRF defenses
- [x] Validation of Referer depends on header being present (2/2)
- [x] Validation of Referer can be circumvented (2/2)

## Key Takeaways

- CSRF can be used to perform unauthorized actions on behalf of users
- Proper token implementation is critical
- SameSite cookies provide strong protection
- Combine multiple defense mechanisms
- Test for CSRF in all state-changing operations
- Understand the difference between Strict, Lax, and None SameSite values

## Labs Completed

All 49 modules completed successfully!

## Resources

- [PortSwigger CSRF Learning Path](https://portswigger.net/web-security/csrf)
- [OWASP CSRF Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html)
