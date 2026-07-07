# Defense Mechanisms Against Username Enumeration

## Why Username Enumeration is a Problem

**Impact:**
- Reduces attack space (attacker knows valid usernames)
- Enables targeted password brute-force attacks
- Provides reconnaissance for further attacks
- Violates user privacy (username disclosure)

---

## Defense Strategy 1: Generic Error Messages

### ❌ VULNERABLE
```
Username doesn't exist: "Invalid username"
Username exists but wrong password: "Incorrect password"
Username exists but account locked: "Account locked"
```

**Why vulnerable:**
- Each message reveals different information
- Attacker can determine if username exists
- Different message lengths make it obvious

### ✅ SECURE
```
All cases: "Invalid username or password"
```

**Implementation:**
```python
# Bad
if username not in database:
    return "Invalid username"
elif password != stored_password:
    return "Incorrect password"

# Good
if username not in database or password != stored_password:
    return "Invalid username or password"
```

---

## Defense Strategy 2: Consistent Response Length

### Problem
- Different error messages have different lengths
- Attacker can distinguish valid/invalid users by response size

### Solution: Padding

```python
def login(username, password):
    error_msg = ""
    
    if username not in database:
        error_msg = "Invalid username or password"
    elif password != stored_password:
        error_msg = "Invalid username or password"
    elif account_locked:
        error_msg = "Invalid username or password"
    else:
        return login_success()
    
    # Pad to consistent length (e.g., 200 chars)
    error_msg = error_msg.ljust(200, ' ')
    return error_msg
```

**Result:**
- All error responses are exactly 200 bytes
- Attacker can't use response length as indicator

---

## Defense Strategy 3: Consistent Response Time

### Problem
- Valid username lookups might take different time than invalid
- Timing attacks can reveal valid usernames

### Solution: Add Artificial Delay

```python
import time

def login(username, password):
    start_time = time.time()
    
    # Perform authentication
    if username not in database:
        is_valid = False
    else:
        is_valid = (password == stored_password)
    
    # Ensure minimum processing time (e.g., 500ms)
    elapsed = time.time() - start_time
    if elapsed < 0.5:
        time.sleep(0.5 - elapsed)
    
    return error_msg
```

**Result:**
- All login attempts take ~500ms
- Timing-based enumeration is defeated

---

## Defense Strategy 4: Rate Limiting

### IP-based Rate Limiting

```python
from flask import request
from datetime import datetime, timedelta

failed_attempts = {}  # IP -> [(timestamp, username), ...]

def check_rate_limit(ip_address):
    now = datetime.now()
    
    # Clean old attempts (older than 15 minutes)
    if ip_address in failed_attempts:
        failed_attempts[ip_address] = [
            (ts, user) for ts, user in failed_attempts[ip_address]
            if now - ts < timedelta(minutes=15)
        ]
    
    # Check limit: max 5 attempts per 15 minutes
    if len(failed_attempts.get(ip_address, [])) >= 5:
        return False, "Too many login attempts. Please try again later."
    
    return True, "OK"

def login(username, password):
    ip = request.remote_addr
    allowed, msg = check_rate_limit(ip)
    
    if not allowed:
        return msg, 429  # Too Many Requests
    
    if authenticate(username, password):
        # Clear failed attempts
        failed_attempts[ip] = []
        return "Login successful", 200
    else:
        # Record failed attempt
        failed_attempts[ip].append((datetime.now(), username))
        return "Invalid username or password", 401
```

**Result:**
- Max 5 attempts per IP per 15 minutes
- Brute-force attacks are significantly slowed

---

## Defense Strategy 5: Account Lockout

### Progressive Lockout

```python
def check_account_lockout(username):
    user = database.get_user(username)
    
    if not user:
        return None, "User not found"
    
    if user.locked_until and datetime.now() < user.locked_until:
        return None, "Account temporarily locked. Try again later."
    
    return user, "OK"

def login(username, password):
    user, msg = check_account_lockout(username)
    
    if not user:
        return msg, 401
    
    if password != user.password:
        # Increment failed attempts
        user.failed_attempts += 1
        
        if user.failed_attempts >= 5:
            # Lock for 15 minutes
            user.locked_until = datetime.now() + timedelta(minutes=15)
        
        database.update_user(user)
        return "Invalid username or password", 401
    
    # Reset on success
    user.failed_attempts = 0
    user.locked_until = None
    database.update_user(user)
    
    return "Login successful", 200
```

**Result:**
- Account locks after 5 failed attempts
- Temporary lock (15 minutes) prevents brute-force
- Legitimate users might get locked out (UX tradeoff)

---

## Defense Strategy 6: CAPTCHA

### When to Show CAPTCHA

```python
def should_show_captcha(username):
    user = database.get_user(username)
    if not user:
        return True  # Unknown user
    
    # Show CAPTCHA if:
    # - Multiple failed attempts (3+)
    # - Recent failed attempts from same IP
    # - Suspicious pattern detected
    
    failed_count = user.failed_attempts
    if failed_count >= 3:
        return True
    
    return False

def login(username, password, captcha_response=None):
    if should_show_captcha(username):
        if not captcha_response or not verify_captcha(captcha_response):
            return "Please complete CAPTCHA", 400
    
    if authenticate(username, password):
        return "Login successful", 200
    else:
        return "Invalid username or password", 401
```

**Result:**
- CAPTCHA prevents automated attacks
- Manual brute-force becomes impractical
- Slight friction for legitimate users

---

## Defense Strategy 7: Multi-Factor Authentication (MFA)

### Two-Factor Authentication

```python
def login_step1(username, password):
    if authenticate(username, password):
        # Send OTP to email/phone
        otp = generate_otp()
        user = database.get_user(username)
        send_otp_to_email(user.email, otp)
        
        return "OTP sent to your email", 200
    else:
        return "Invalid username or password", 401

def login_step2(username, otp):
    if verify_otp(username, otp):
        # Create session
        session = create_session(username)
        return "Login successful", 200
    else:
        return "Invalid OTP", 401
```

**Result:**
- Even if password is compromised, account is protected
- Second factor required for access
- Much stronger security

---

## Defense Checklist

- [ ] Generic error messages for all login failures
- [ ] Consistent response length (with padding if needed)
- [ ] Consistent response time (artificial delay if needed)
- [ ] IP-based rate limiting (5 attempts / 15 minutes)
- [ ] Account lockout (lock after 5 failed attempts for 15 minutes)
- [ ] CAPTCHA after multiple failures
- [ ] Multi-factor authentication (OTP, authenticator app, etc.)
- [ ] Logging and monitoring of login attempts
- [ ] Security headers (HSTS, CSP, etc.)
- [ ] HTTPS only (no HTTP)

---

## Implementation Priority

**Must Have (Critical):**
1. Generic error messages
2. Rate limiting
3. Account lockout

**Should Have (Important):**
4. Consistent response time/length
5. CAPTCHA

**Nice to Have (Enhanced Security):**
6. Multi-factor authentication
7. Advanced monitoring and alerting

---

## References

- [OWASP: Brute Force Attack](https://owasp.org/www-community/attacks/Brute_force_attack)
- [OWASP: User Enumeration](https://owasp.org/www-community/attacks/User_Enumeration)
- [CWE-640: Weak Password Recovery Mechanism](https://cwe.mitre.org/data/definitions/640.html)
- [PortSwigger: Authentication](https://portswigger.net/web-security/authentication)
