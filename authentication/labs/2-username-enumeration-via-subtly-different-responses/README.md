# Username Enumeration via Subtly Different Responses

**Lab Link:** https://portswigger.net/web-security/authentication/password-based/lab-username-enumeration-via-subtly-different-responses

**Difficulty:** Practitioner

**Topic:** Authentication Vulnerabilities, Username Enumeration

**Status:** ✅ Solved

**Date Completed:** 7 Jul 2026

## Lab Objective

This lab is subtly vulnerable to username enumeration and password brute-force attacks. The goal is to:

1. Enumerate a valid username from the candidate list
2. Brute-force the password for that user
3. Access the user's account page

## Key Vulnerability: Subtle Differences

**Difference from Lab 1:**
Unlike the previous lab with obvious error message differences ("Invalid username" vs "Incorrect password"), this lab has **subtle differences** that are harder to spot:

- Generic error message: "Incorrect username or password"
- All responses appear identical at first glance
- **Subtle variations** exist:
  - Extra whitespace/padding
  - Different HTML formatting
  - Slightly different message wording
  - Different response lengths (by a few bytes)
  - Subtle timing differences

## Solution Steps

### Step 1: Investigate the Login Page

- Navigate to the lab's login page
- Note that error messages are now generic
- Submit an invalid username and observe the response
- All error responses appear identical

### Step 2: Capture the Login Request

**Request Format:**
```
POST /login HTTP/2
Content-Type: application/x-www-form-urlencoded

username=app01&password=iloveyou
```

**Response:**
- Status: 200 OK
- Message: "Incorrect username or password"

### Step 3: Enumerate Valid Usernames (Advanced Technique)

**Using Burp Intruder with Response Analysis:**

1. Send login request to Intruder
2. Mark username as payload position:
   ```
   username=§invalid-username§&password=test
   ```
3. Load candidate usernames
4. Start Sniper attack

**Finding the Subtle Difference:**

Since error messages appear identical, look for subtle indicators:

**Method 1: Response Length Variation**
- Sort by "Length" column
- Look for **slight variations** (even 1-2 bytes)
- Valid username response might be:
  - 2-3 bytes longer (extra space in message)
  - 2-3 bytes shorter (slightly different formatting)
  - Example:
    ```
    Payload: admin         Length: 2847
    Payload: root          Length: 2847
    Payload: app01         Length: 2850  ← Subtle difference!
    Payload: user          Length: 2847
    ```

**Method 2: Response Content Comparison**
1. Click on rows to compare responses side-by-side
2. Valid username response shows subtle differences:
   - Extra space before/after message
   - Different HTML comment placement
   - Extra newline character
   - Bold/italic HTML tag (minimal)

**Method 3: Burp Compare Feature**
1. Right-click two responses
2. Select "Compare"
3. Highlight differences
4. Look for subtle text/formatting variations

**Result in this lab:**
```
Valid Username: app01
Subtle Difference: Response length differs by 3 bytes
Indicator: Slightly longer response (extra space/formatting)
```

### Step 4: Brute-force the Password

**Using Burp Intruder:**

1. Clear previous attack
2. Set identified username:
   ```
   username=app01&password=§iloveyou§
   ```
3. Load candidate passwords
4. Start Sniper attack

**Finding Correct Password:**

- Monitor Status column
- Most responses: **200** (login failed)
- Successful login: **302** (redirect to account page)
  ```
  Payload: 123456       Status: 200
  Payload: password     Status: 200
  Payload: iloveyou     Status: 302  ← SUCCESS!
  Payload: letmein      Status: 200
  ```

**Result in this lab:**
```
Correct Password: iloveyou
Status Code: 302 (redirect)
```

### Step 5: Access the Account

- Log in using credentials: `app01:iloveyou`
- Successfully access the user's account page
- Lab is solved! ✅

## Key Learnings

### 1. Subtle Enumeration Techniques

**The Challenge:**
- Generic error messages hide username validity
- Need careful analysis to find subtle differences
- Response length variations (even 1-2 bytes) matter

**Real-World Application:**
- Developers often think generic messages are enough
- Subtle differences still leak information
- Advanced tools can detect minimal variations

### 2. Advanced Detection Methods

| Method | Detection Level | Difficulty |
|--------|-----------------|-------------|
| Message Content | Obvious | Very Easy |
| Response Length | Subtle | Easy |
| HTML Formatting | Very Subtle | Medium |
| Timing Analysis | Timing-based | Hard |
| Byte-level Comparison | Very Detailed | Hard |

### 3. Burp Intruder Techniques

**Sniper Attack** (used here):
- Single payload position
- Efficient for finding valid username
- Then switch to brute-force password

**Cluster Bomb** (not used):
- Multiple payload positions
- Tests all combinations (slower)
- Only useful if both username AND password needed

### 4. Attack Indicators Refined

| Indicator | Meaning | Reliability |
|-----------|---------|-------------|
| Response Length | Subtle difference | Medium |
| HTTP Status 302 | Successful login | High |
| Message Comparison | Very subtle | Low |
| Timing Analysis | Consistent delay | Medium |
| Byte-level Diff | Precise | High |

## Comparison: Lab 1 vs Lab 2

| Aspect | Lab 1 (Different) | Lab 2 (Subtle) |
|--------|-----------------|----------------|
| Error Messages | Different text | Generic text |
| Detection | Obvious | Subtle |
| Message Length | Very different | Slightly different |
| Required Technique | String matching | Length analysis |
| Difficulty | Easy | Medium |
| Real-World Relevance | Outdated approach | Modern vulnerability |

## Defense Mechanisms

### Why Subtle Differences Still Fail

**Problem with Subtle Differences:**
```python
# Attempt to hide enumeration
if username not in database:
    # Different code path (timing difference!)
    error_msg = "Incorrect username or password"  # Slightly different
else:
    if password != stored_password:
        error_msg = "Incorrect username or password"  # Same message
```

**Issue:**
- Different code paths cause timing variations
- Response formatting differs slightly
- Cache behavior differs
- String building differs

### True Defense: Cryptographic Comparison

```python
import hashlib
import time
from constant_time_compare import compare

def login(username, password):
    # Get user (or dummy if doesn't exist)
    user = get_user_or_dummy(username)
    
    # Always do password check (even for non-existent users)
    # Use constant-time comparison to prevent timing attacks
    password_correct = compare(
        hashlib.pbkdf2_hmac('sha256', password, user.salt),
        user.password_hash
    )
    
    if password_correct:
        # Always takes same time (uses dummy user for non-existent)
        return "Login successful", 200
    else:
        return "Incorrect username or password", 401
```

**Key Points:**
- `get_user_or_dummy()`: Returns dummy user if username doesn't exist
- `constant_time_compare()`: Takes same time regardless of string content
- All code paths have same duration
- Same error message for all failures

### Complete Defense Checklist

- [x] Generic error message for all failures
- [x] Consistent response length (with padding if needed)
- [x] Constant-time password comparison
- [ ] Rate limiting (should still implement)
- [ ] Account lockout (should still implement)
- [ ] CAPTCHA after multiple failures
- [ ] Multi-factor authentication

## Tools Used

- **Burp Suite Community Edition**
  - Proxy: Intercept requests
  - Intruder: Automate enumeration
  - Comparer: Analyze subtle differences
  - Repeater: Manual testing

## Attack Timeline

```
1. Investigate login page
   ↓
2. Capture login request
   ↓
3. Enumerate usernames using Intruder (Sniper)
   ↓
4. Analyze responses for subtle length differences
   ↓
5. Use Comparer to find exact differences
   ↓
6. Identify valid username: app01
   ↓
7. Brute-force password using Intruder (Sniper)
   ↓
8. Identify successful login (302 status)
   ↓
9. Note correct password: iloveyou
   ↓
10. Log in successfully
    ↓
11. Lab solved! ✅
```

## Common Challenges

### Challenge 1: Finding Subtle Differences

**Problem:** All responses look identical at first glance

**Solution:**
- Sort by response length
- Use Burp Comparer
- Look for 1-2 byte variations
- Compare HTML source (not just rendered)

### Challenge 2: Response Length Varies

**Problem:** Length differences are inconsistent

**Solution:**
- Multiple attacks to verify
- Statistical analysis (average vs outliers)
- Use Response Extraction to normalize

### Challenge 3: Timing-based Detection

**Problem:** Might need timing analysis instead

**Solution:**
- Use Burp's "Response received" time column
- Increase samples for statistical significance
- Add delays in Intruder to see pattern

## Similar Labs

- Lab: Username enumeration via different responses (Lab 1)
- Lab: Username enumeration via response timing (Lab 3)
- Lab: Broken brute-force protection, IP block
- Lab: Username enumeration via account lock

## Key Takeaway

**Generic error messages are NOT enough!**

Developers often think: "If I use generic error messages, enumeration is solved."

**Reality:** Subtle differences in:
- Response length
- Response timing
- HTML formatting
- Code execution path
- Cache behavior

...still leak information that attackers can exploit.

**True solution:** Use constant-time comparison and ensure identical responses for all error cases.

## References

- [PortSwigger: Authentication](https://portswigger.net/web-security/authentication)
- [CWE-203: Observable Discrepancy](https://cwe.mitre.org/data/definitions/203.html)
- [OWASP: User Enumeration](https://owasp.org/www-community/attacks/User_Enumeration)
- [Python: timing attack](https://codahale.com/a-lesson-in-timing-attacks/)

---

**Lab Status:** ✅ Solved
**Difficulty:** Practitioner (Medium)
**Techniques:** Advanced Response Analysis, Burp Comparer, Subtle Enumeration
**Valid Username:** app01
**Valid Password:** iloveyou
