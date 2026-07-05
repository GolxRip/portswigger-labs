# PortSwigger Web Security Academy Labs

Comprehensive repository documenting all solutions and progress through PortSwigger Web Security Academy learning paths.

## 📊 Progress Overview

### Completed Paths
- ✅ **SQL Injection** - 51/51 (Completed on 25 Apr 2026)
- ✅ **Cross-Site Request Forgery (CSRF)** - 49/49 (Completed on 21 Jun 2026)

### In Progress
- 🔄 **Authentication Vulnerabilities** - 13/55
- 🔄 **CORS** - 5/21

### Not Started
- ⭕ **Server-side Request Forgery (SSRF)** - 0/23
- ⭕ **Web Cache Deception** - 0/36
- ⭕ **WebSockets Vulnerabilities** - 0/19
- ⭕ **Prototype Pollution** - 0/65
- ⭕ **Clickjacking (UI Redressing)** - 0/29
- ⭕ **GraphQL API Vulnerabilities** - 0/19
- ⭕ **Path Traversal** - 0/14
- ⭕ **NoSQL Injection** - 0/24
- ⭕ **Race Conditions** - 0/29
- ⭕ **File Upload Vulnerabilities** - 0/35
- ⭕ **API Testing** - 0/29
- ⭕ **Server-side Vulnerabilities** - 0/52
- ⭕ **Web LLM Attacks** - 0/30

## 📁 Directory Structure

```
├── sql-injection/
├── csrf/
├── authentication/
├── cors/
├── ssrf/
├── web-cache-deception/
├── websockets/
├── prototype-pollution/
├── clickjacking/
├── graphql-api/
├── path-traversal/
├── nosql-injection/
├── race-conditions/
├── file-upload/
├── api-testing/
├── server-side-vulnerabilities/
└── web-llm-attacks/
```

## 📝 File Organization

Each lab solution follows this structure:

```
vulnerability-type/
├── lab-name/
│   ├── README.md          # Lab description and objectives
│   ├── solution.md        # Detailed writeup of the solution
│   ├── payload.txt        # The actual payload/exploit
│   ├── notes.md           # Key learnings and techniques
│   └── screenshots/       # Screenshots of the exploit
```

## 🎯 Solution Template

For each lab, create a `solution.md` with:

```markdown
# Lab Name

## Objective
What the lab is trying to teach

## Vulnerability Type
The type of vulnerability being exploited

## Solution Steps
1. Step 1
2. Step 2
3. Step 3

## Key Payload
```
[Your payload here]
```

## Explanation
Why this works

## Defense/Prevention
How to prevent this vulnerability

## References
- Link to lab
- Related concepts
```

## 🚀 Usage

1. **For each lab you complete:**
   - Create a folder with the lab name
   - Add `solution.md` with your writeup
   - Include `payload.txt` with the exploit
   - Add screenshots to `screenshots/` folder
   - Commit daily to maintain GitHub activity

2. **Commit Message Format:**
   ```
   [VULNERABILITY_TYPE] Lab: Lab Name - Completed
   
   Description of what was learned
   ```

3. **Example:**
   ```
   [SQL_INJECTION] Lab: Retrieving Hidden Data - Completed
   
   Learned how to use UNION-based SQL injection to extract data
   from multiple tables by determining column count and data types.
   ```

## 📚 Learning Path Categories

### Beginner (Apprentice)
- Server-side Vulnerabilities

### Intermediate (Practitioner)
- SQL Injection
- Cross-Site Scripting (XSS)
- CSRF
- XXE
- Path Traversal
- SSRF
- Authentication
- CORS
- And more...

## 💡 Tips

- Document as you learn
- Update README.md with progress
- Commit at least once daily
- Screenshot your successful exploits
- Write clear, reusable payloads

---

**Last Updated:** 5 Jul 2026
**Status:** Active Development
