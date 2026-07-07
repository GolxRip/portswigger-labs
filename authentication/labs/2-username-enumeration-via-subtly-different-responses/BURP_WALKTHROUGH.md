# Burp Intruder & Comparer Walkthrough

## Part 1: Enumerate Valid Username

### Step 1: Send Request to Intruder

**Captured Request:**
```
POST /login HTTP/2
Host: 0a5b00120380fad880da8a7800580011.web-security-academy.net
Content-Type: application/x-www-form-urlencoded

username=app01&password=iloveyou
```

**Action:**
- Right-click the POST /login request
- Select "Send to Intruder"

---

### Step 2: Configure Intruder - Positions Tab

**Clear existing markers:**
1. Click "Clear §" button

**Set username as payload position:**
1. Select the username value (e.g., `app01`)
2. Click "Add §" button

**Result:**
```
username=§app01§&password=iloveyou
```

**Verify Attack Type:**
- Select: **Sniper** (from dropdown)
- Sniper = One payload position

---

### Step 3: Configure Payloads - Payloads Tab

**Payload Type:**
- Select "Simple list"

**Load candidate usernames:**
```
Username List:
admin
root
test
app01         ← This one will have subtle difference
user
[... more usernames ...]
```

---

### Step 4: Start Attack

**Action:**
- Click "Start attack" button
- New window opens with results

---

## Part 2: Analyze Results for Subtle Differences

### Method 1: Response Length Analysis

**Step 1: Sort by Length**
1. Click the "Length" column header
2. Click again to reverse sort
3. Look for slight variations (even 1-2 bytes)

**Example Results:**
```
Payload        Status   Length   Payload
test           200      2847
root           200      2847
admin          200      2847
app01          200      2850     ← Subtle difference! (3 bytes longer)
user           200      2847
[... more ...]
```

**Why the difference?**
- Valid username might trigger different code path
- Error message formatting differs slightly
- Extra space, newline, or HTML tag
- Cache behavior differs

**Note:** Difference is subtle (only 3 bytes out of 2850)

---

### Method 2: Use Burp Comparer

**This is more reliable for finding subtle differences:**

**Step 1: Select Two Responses**
1. Click on a row with standard length (e.g., `test`)
2. Hold Ctrl and click on `app01` row
3. Both rows should be highlighted

**Step 2: Open Comparer**
1. Right-click on selected rows
2. Select "Send to Comparer"

**Step 3: Analyze Differences**
1. Comparer window opens
2. Shows both responses side-by-side
3. Differences are highlighted

**Example Differences:**
```
Invalid Username Response:
...
<div class="login-error">
  Incorrect username or password
</div>
...

Valid Username Response:
...
<div class="login-error">
  Incorrect username or password  ← Extra space here!
</div>
...
```

**Or it could be:**
- Extra newline character
- Different HTML formatting
- Comment placement
- CSS class variation

---

### Method 3: Manual Response Inspection

**Click on suspicious row:**
1. Click on `app01` row in Intruder results
2. Response appears in bottom panel
3. Inspect the HTML source
4. Compare with other responses
5. Look for subtle formatting differences

---

## Part 3: Brute-force Password

### Step 1: Clear Payload Positions

**Back in Intruder tab:**
1. Click "Clear §" button
2. Remove the old username payload position

---

### Step 2: Set New Payload Position

**Set identified username as static:**
```
username=app01&password=iloveyou
```

**Add password as payload position:**
1. Select password value (e.g., `iloveyou`)
2. Click "Add §" button

**Result:**
```
username=app01&password=§iloveyou§
```

---

### Step 3: Configure Payloads

**Payload Type:**
- Select "Simple list"

**Load candidate passwords:**
```
Password List:
123456
password
letmein
iloveyou      ← This one will succeed
sunshine
[... more passwords ...]
```

---

### Step 4: Start Attack

**Action:**
- Click "Start attack"
- New window opens

---

## Part 4: Analyze Password Results

### Look at Status Column

**Most responses:**
- Status: **200** (login form returned)
- Indicates: Failed login

**One response:**
- Status: **302** (redirect)
- Indicates: Successful login! ✅

**Example Results:**
```
Payload        Status   Length
123456         200      2847
password       200      2847
letmein        200      2847
iloveyou       302      400    ← SUCCESS! (302 redirect)
sunshine       200      2847
```

**Why 302?**
- 302 = HTTP Redirect (temporary)
- After successful login, server redirects to account page
- Failed logins return 200 with login form

---

## Part 5: Verify Credentials

### Final Test

1. **Navigate to login page**
2. **Enter credentials:**
   - Username: `app01`
   - Password: `iloveyou`
3. **Click Login**
4. **Verify:** Should redirect to account page
5. **Confirm:** Lab solved! ✅

---

## Troubleshooting

### Issue: Can't find length difference

**Solution:**
- Try Burp Comparer instead
- Look for other indicators (timing, HTML formatting)
- Check if difference is in HTTP headers (not just body)

### Issue: No 302 response for any password

**Solution:**
- Check if username is correct
- Verify passwords are from correct wordlist
- Try manual login with discovered username
- Successful response might be 200 (not all are 302)

### Issue: Getting rate limited

**Solution:**
- Reduce Intruder threads (Options > Request Engine > Number of threads)
- Add delay between requests (Options > Request Engine > Throttle)
- Use shorter wordlists for testing

### Issue: Can't see response content

**Solution:**
- Click on row in results table
- Response appears in bottom panel
- Right-click > View in Browser (if needed)
- Use Comparer for side-by-side comparison

---

## Tips & Tricks

1. **Sort Multiple Ways:**
   - By Length (find subtle differences)
   - By Status (find successful login)
   - By Time (find timing differences)

2. **Use Comparer Frequently:**
   - Great for finding subtle differences
   - Better than eyeballing responses
   - Can compare headers too

3. **Filter Results:**
   - Use search box to filter payloads
   - Useful for large wordlists

4. **Export Results:**
   - Right-click > Save as CSV
   - Analyze in spreadsheet
   - Useful for statistical analysis

5. **Response Extraction:**
   - Use in Extractor to pull specific values
   - Can find patterns automatically

---

## Key Differences from Lab 1

| Aspect | Lab 1 | Lab 2 |
|--------|-------|-------|
| Error Messages | Obvious difference | Generic message |
| Detection Method | String matching | Length analysis |
| Difficulty | Very Easy | Medium |
| Tool Needed | Basic Intruder | Intruder + Comparer |
| Skill Required | Beginner | Intermediate |

---

## Summary

**Enumeration Phase:**
1. Send login request to Intruder
2. Set username as payload position
3. Load username wordlist
4. Run Sniper attack
5. Analyze responses for subtle length differences
6. Use Comparer to confirm
7. Note valid username: `app01`

**Brute-force Phase:**
1. Set app01 as username
2. Set password as payload position
3. Load password wordlist
4. Run Sniper attack
5. Look for 302 status code
6. Note correct password: `iloveyou`

**Verification:**
1. Log in with app01:iloveyou
2. Access account page
3. Lab solved! ✅
