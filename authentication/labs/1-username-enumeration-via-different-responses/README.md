# Username Enumeration via Different Responses

**Lab Link:** https://portswigger.net/web-security/authentication/password-based/lab-username-enumeration-via-different-responses

**Difficulty:** Apprentice

**Topic:** Authentication Vulnerabilities, Username Enumeration

**Status:** ✅ Solved

**Date Completed:** 5 Jul 2026

## Lab Objective

This lab is vulnerable to username enumeration and password brute-force attacks. The goal is to:

1. Enumerate a valid username from the candidate list
2. Brute-force the password for that user
3. Access the user's account page

## Key Vulnerability

**Response-based Username Enumeration:** The application returns different error messages depending on whether the username is valid:
- Invalid username: "Invalid username" message
- Valid username with wrong password: "Incorrect password" message

This difference allows us to enumerate valid usernames.

## Solution Steps

### Step 1: Investigate the Login Page

- Navigate to the lab's login page
- Submit an invalid username and password to observe the response
- Note the error message format

### Step 2: Capture the Login Request

- With Burp Suite running, intercept the POST /login request
- Observe the request parameters:
  ```
  username=[username]
  password=[password]
  csrf=[csrf_token]
  ```

### Step 3: Enumerate Valid Usernames

**Using Burp Intruder:**

1. Send the login request to Intruder
2. Set the username parameter as payload position: `username=§invalid-username§`
3. Keep password as static value (any password)
4. Select Sniper attack mode
5. Load the candidate usernames list as payload
6. Start the attack

**Identifying Valid Username:**

- Examine the **Length** column in results
- Valid usernames will have different response length (usually longer)
- Response contains: "Incorrect password" instead of "Invalid username"
- Note the valid username found

**Result in this lab:**
```
Valid Username: ai
Response Length: Different from others (indicates valid user)
```

### Step 4: Brute-force the Password

**Using Burp Intruder:**

1. Clear the previous attack
2. Set the identified username as static value: `username=ai`
3. Add payload position to password: `password=§invalid-password§`
4. Replace payloads with candidate passwords list
5. Start the attack

**Identifying Correct Password:**

- Monitor the **Status** column
- Most failed attempts return **200** status (login form)
- Successful login returns **302** status (redirect to account page)
- Note the password that returns 302

**Result in this lab:**
```
Correct Password: Found in the candidate passwords list
Status Code: 302 (redirect)
```

### Step 5: Access the Account

- Log in using the discovered credentials
- Successfully access the user's account page
- Lab is solved! ✅

## Key Learnings

### 1. Response-based Enumeration
- **Vulnerability:** Different error messages leak username validity
- **Impact:** Attackers can enumerate valid usernames efficiently
- **Detection:** Monitor response length, error messages, timing

### 2. Burp Intruder Techniques
- **Sniper Mode:** Single parameter variation (one payload position)
- **Cluster Bomb Mode:** Multiple parameter variation (multiple payload positions)
- For this lab, Sniper is more efficient than Cluster Bomb

### 3. Attack Indicators

| Indicator | Meaning |
|-----------|----------|
| Response Length | Different = Valid username found |
| Error Message | "Incorrect password" = Valid user |
| Status Code | 302 = Successful login |
| Status Code | 200 = Failed login |

### 4. Brute-force Strategy

1. **First:** Enumerate valid username (faster)
2. **Then:** Brute-force password for known user (targeted)
3. **Avoid:** Cluster bomb on all usernames + passwords (slower, noisier)

## Defense Mechanisms

### Against Username Enumeration:

1. **Generic Error Messages**
   ```
   ❌ "Invalid username" or "Incorrect password"
   ✅ "Invalid username or password"
   ```

2. **Consistent Response Length**
   - Ensure all error responses are same length
   - Add padding if necessary

3. **Consistent Response Time**
   - Avoid timing-based enumeration
   - Use consistent database query time

4. **Rate Limiting**
   - Limit login attempts per IP
   - Add CAPTCHA after multiple failures

5. **Account Lockout**
   - Lock account after failed attempts
   - Notify user of lockout

### Against Password Brute-force:

1. **Strong Account Lockout Policy**
   - Lock after 5-10 failed attempts
   - Temporary or permanent lock

2. **Progressive Delays**
   - Increase delay between failed attempts
   - Exponential backoff

3. **CAPTCHA**
   - Show after multiple failures
   - Prevents automated attacks

4. **IP-based Rate Limiting**
   - Block IP after too many attempts
   - Temporary ban

5. **Multi-factor Authentication**
   - Adds additional layer of security
   - Password alone is insufficient

## Tools Used

- **Burp Suite Community Edition**
  - Proxy: Intercept requests
  - Intruder: Automate brute-force attacks
  - Repeater: Test individual requests

## Attack Timeline

```
1. Investigate login page
   ↓
2. Capture login request
   ↓
3. Enumerate usernames (Sniper attack)
   ↓
4. Identify valid username from response length
   ↓
5. Brute-force password for valid user (Sniper attack)
   ↓
6. Identify password from 302 status code
   ↓
7. Log in successfully
   ↓
8. Lab solved! ✅
```

## Common Pitfalls

1. ❌ Using Cluster Bomb instead of Sniper (slower)
2. ❌ Not checking response length differences
3. ❌ Ignoring HTTP status codes (302 vs 200)
4. ❌ Not rate limiting or throttling requests
5. ❌ Getting blocked by rate limiting before finding credentials

## Similar Labs

- Lab: Username enumeration via subtly different responses
- Lab: Username enumeration via response timing
- Lab: Broken brute-force protection, IP block
- Lab: Username enumeration via account lock

## References

- [PortSwigger: Authentication](https://portswigger.net/web-security/authentication)
- [PortSwigger: Username Enumeration](https://portswigger.net/web-security/authentication/password-based)
- [OWASP: Authentication Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html)

---

**Lab Status:** ✅ Solved
**Difficulty:** Apprentice (Easy)
**Techniques:** Username Enumeration, Burp Intruder, Response Analysis
