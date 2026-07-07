# Burp Intruder Walkthrough

## Step-by-Step Configuration

### Step 1: Capture Request in Burp Proxy

**What you should see:**
```
POST /login HTTP/1.1
Host: [LAB_URL]
Content-Type: application/x-www-form-urlencoded

username=invalid-username&password=any-password&csrf=[token]
```

**Action:**
- Right-click the request
- Select "Send to Intruder"

---

### Step 2: Configure Intruder for Username Enumeration

**Tab: Positions**

1. Click "Clear §" button to remove any existing markers
2. Select the username value (e.g., `invalid-username`)
3. Click "Add §" button to mark it as payload position
4. Result should look like:
   ```
   username=§invalid-username§&password=any-password&csrf=[token]
   ```

**Attack Type: Sniper**
- Select from dropdown if not already selected
- Sniper = One payload position

---

### Step 3: Configure Payloads for Username List

**Tab: Payloads**

**Payload Type:**
- Select "Simple list" from dropdown

**Payload Configuration:**
- Paste the candidate usernames list:
  ```
  admin
  root
  test
  user
  ai          ← This one will be found
  [... more usernames]
  ```

**Tab Options:**
- Leave defaults
- Optional: Enable "URL-encode these characters"

---

### Step 4: Start Attack

**Action:**
- Click "Start attack" button
- New window opens with results

---

## Analyzing Results

### Results Table Columns

| Column | What to Look For |
|--------|------------------|
| Payload | The username being tested |
| Status | HTTP response code (should be 200) |
| Length | Response body size in bytes |
| Cookies | Session cookies (track if set) |
| Time | Request processing time |

### Finding Valid Username

**Method 1: Response Length**
1. Click "Length" column header to sort
2. Look for row with different length
3. Valid username responses are usually longer
4. Example:
   ```
   Payload: admin        Length: 2847
   Payload: root         Length: 2847
   Payload: ai           Length: 2856  ← Different! Valid user
   Payload: test         Length: 2847
   ```

**Method 2: Compare Responses**
1. Click on a row to view response
2. Invalid username response:
   ```
   Invalid username
   ```
3. Valid username response:
   ```
   Incorrect password
   ```

**Note the valid username found:** `ai`

---

## Step 5: Configure Intruder for Password Brute-force

**Back to Intruder Tab**

1. Click "Clear §" to clear previous markers
2. Change username to the valid one:
   ```
   username=ai&password=any-password&csrf=[token]
   ```
3. Select password value
4. Click "Add §" to mark as payload:
   ```
   username=ai&password=§any-password§&csrf=[token]
   ```

---

## Step 6: Configure Payloads for Password List

**Tab: Payloads**

1. Clear previous payloads
2. Paste candidate passwords:
   ```
   123456
   password
   letmein
   welcome
   [... more passwords]
   ```

**Start attack**

---

## Finding Correct Password

### Results Table Analysis

**Look at Status Column:**
- Most attempts: **200** (failed login)
- One attempt: **302** (successful login - redirect)

```
Payload: 123456       Status: 200  ← Failed
Payload: password     Status: 200  ← Failed
Payload: sunshine     Status: 302  ← SUCCESS! ✓
Payload: letmein      Status: 200  ← Failed
```

**Note the correct password:** `sunshine`

---

## Final: Log In and Solve Lab

1. Navigate to login page
2. Enter username: `ai`
3. Enter password: `sunshine`
4. Click login
5. Verify you're on account page
6. Lab solved! ✅

---

## Common Issues & Fixes

### Issue: All responses same length
**Solution:** Check the actual response content, not just length
- Look for error message differences
- Check HTTP status codes

### Issue: No 302 response found
**Solution:** Check response content
- Successful login may return 200 with account page content
- Look for login success indicator in response

### Issue: Too slow / Getting rate limited
**Solution:**
- Reduce number of threads (Intruder > Options > Threads)
- Add delay between requests (Options > Request Engine)
- Consider using smaller wordlists for testing

### Issue: CSRF token changes on each request
**Solution:**
- Use Burp "Grep-Match" to extract and update token
- Or use macro to auto-update token
- For this lab, token might not be validated

---

## Tips & Tricks

1. **Sort by Length:** Click column header multiple times to sort ascending/descending
2. **Filter Results:** Use search box to filter results
3. **Export Results:** Right-click to export attack results to file
4. **Highlight:** Right-click row to highlight specific results
5. **Request Preview:** Click payload row to see full response in bottom panel

