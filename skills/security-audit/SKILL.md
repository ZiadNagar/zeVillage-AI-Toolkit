---
name: security-audit
description: "Use this skill when the user asks to review code for security vulnerabilities, harden an application, audit authentication or authorization logic, check for OWASP Top 10 issues, review secrets management, or assess dependency security. Triggers for 'is this secure', 'security review', 'find vulnerabilities', 'audit this code', 'check for injection', 'harden this', 'review auth logic', or 'check dependencies for CVEs'. Covers web application security, API security, input validation, cryptography usage, and secure configuration."
license: Complete terms in LICENSE.txt
---

# Security Audit

You are performing a security review. Be thorough, specific, and prioritize findings by severity. Every finding must include a concrete fix — never report a problem without a solution.

## Severity Levels

| Level | Label | Definition | Response |
| ----- | ----- | ---------- | -------- |
| P0 | **Critical** | Actively exploitable, data breach risk | Fix immediately, block deployment |
| P1 | **High** | Exploitable with moderate effort | Fix before next release |
| P2 | **Medium** | Defense-in-depth gap, limited impact | Fix within current sprint |
| P3 | **Low** | Best practice violation, minimal risk | Track and fix when convenient |

## Audit Checklist

### 1. Input Validation & Injection

- [ ] All user inputs are validated and sanitized on the server side
- [ ] SQL queries use parameterized statements (no string concatenation)
- [ ] HTML output is escaped to prevent XSS (use framework defaults)
- [ ] File uploads validate type, size, and content (not just extension)
- [ ] URL redirects use allowlists (no open redirects)
- [ ] Command execution avoids shell interpolation (use arrays, not strings)
- [ ] GraphQL queries have depth/complexity limits
- [ ] JSON/XML parsers have entity expansion limits (prevent XXE)

```typescript
// ❌ SQL Injection
const query = `SELECT * FROM users WHERE id = '${userId}'`;

// ✅ Parameterized
const query = `SELECT * FROM users WHERE id = $1`;
await db.query(query, [userId]);
```

```typescript
// ❌ XSS via innerHTML
element.innerHTML = userInput;

// ✅ Safe text content
element.textContent = userInput;
// or use framework escaping (React JSX, Vue templates auto-escape)
```

### 2. Authentication

- [ ] Passwords are hashed with bcrypt/scrypt/argon2 (never MD5/SHA1/SHA256 alone)
- [ ] Session tokens are cryptographically random, >= 128 bits
- [ ] Sessions expire and can be invalidated server-side
- [ ] Login has rate limiting and account lockout
- [ ] Password reset tokens are single-use and time-limited (< 1 hour)
- [ ] Multi-factor authentication is available for sensitive operations
- [ ] OAuth state parameter is validated to prevent CSRF

```python
# ❌ Weak hashing
import hashlib
hashed = hashlib.sha256(password.encode()).hexdigest()

# ✅ Strong hashing
import bcrypt
hashed = bcrypt.hashpw(password.encode(), bcrypt.gensalt(rounds=12))
```

### 3. Authorization

- [ ] Every endpoint checks authorization (no "authentication = authorization")
- [ ] Access control is deny-by-default
- [ ] Object-level authorization (IDOR prevention) — users can only access their own data
- [ ] Role checks happen server-side, not client-side
- [ ] API endpoints enforce the same permissions as the UI
- [ ] Admin functions are in separate routes/controllers with middleware guards

```typescript
// ❌ IDOR — trusting client-provided ID
app.get("/api/orders/:id", async (req, res) => {
  const order = await db.getOrder(req.params.id);
  res.json(order); // Anyone can view any order
});

// ✅ Authorization check
app.get("/api/orders/:id", async (req, res) => {
  const order = await db.getOrder(req.params.id);
  if (order.userId !== req.user.id) return res.status(403).json({ error: "Forbidden" });
  res.json(order);
});
```

### 4. Secrets & Configuration

- [ ] No secrets in source code (API keys, passwords, tokens)
- [ ] `.env` files are in `.gitignore`
- [ ] Secrets are loaded from environment variables or a secrets manager
- [ ] Different secrets for dev/staging/production
- [ ] Database credentials use least-privilege accounts
- [ ] API keys are scoped to minimum required permissions
- [ ] Secrets are rotated on a schedule

```bash
# Check for secrets in git history
git log --all -p | grep -iE "(api_key|secret|password|token)\s*[:=]" | head -20

# Check for .env files tracked in git
git ls-files | grep -i "\.env"
```

### 5. Dependencies

- [ ] Dependencies are pinned to exact versions (lockfile committed)
- [ ] No known critical/high CVEs in dependencies
- [ ] Dependency audit passes (`npm audit`, `pip-audit`, `cargo audit`)
- [ ] Unused dependencies are removed
- [ ] Dependencies are from trusted sources (official registries)

```bash
# Node.js
npm audit --audit-level=high

# Python
pip-audit

# Check for outdated packages
npm outdated
pip list --outdated
```

### 6. HTTP Security Headers

- [ ] `Content-Security-Policy` — restricts script/style/image sources
- [ ] `Strict-Transport-Security` — enforces HTTPS (max-age >= 1 year)
- [ ] `X-Content-Type-Options: nosniff` — prevents MIME sniffing
- [ ] `X-Frame-Options: DENY` or CSP `frame-ancestors` — prevents clickjacking
- [ ] `Referrer-Policy: strict-origin-when-cross-origin` — limits referrer leakage
- [ ] `Permissions-Policy` — disables unused browser features

```typescript
// Next.js security headers (next.config.js)
const securityHeaders = [
  { key: "Strict-Transport-Security", value: "max-age=63072000; includeSubDomains; preload" },
  { key: "X-Content-Type-Options", value: "nosniff" },
  { key: "X-Frame-Options", value: "DENY" },
  { key: "Referrer-Policy", value: "strict-origin-when-cross-origin" },
  { key: "Permissions-Policy", value: "camera=(), microphone=(), geolocation=()" },
];
```

### 7. API Security

- [ ] Rate limiting on all public endpoints
- [ ] Request size limits configured
- [ ] CORS is restrictive (not `*` in production)
- [ ] API versioning prevents breaking changes from exposing data
- [ ] Error responses don't leak internal details (stack traces, SQL errors)
- [ ] Pagination has maximum page size limits

### 8. Data Protection

- [ ] Sensitive data is encrypted at rest (PII, payment data)
- [ ] TLS 1.2+ enforced for data in transit
- [ ] Logs don't contain sensitive data (passwords, tokens, PII)
- [ ] Database backups are encrypted
- [ ] Data retention policies are implemented
- [ ] GDPR/privacy: data export and deletion capabilities exist

### 9. Error Handling & Logging

- [ ] Errors return generic messages to clients (no stack traces in production)
- [ ] Security events are logged (failed logins, permission denials, input validation failures)
- [ ] Log injection is prevented (user input in logs is sanitized)
- [ ] Monitoring/alerting exists for anomalous patterns

## Audit Report Format

After completing a review, produce a report in this format:

```markdown
# Security Audit Report

## Summary
- **Scope:** [what was reviewed]
- **Date:** [date]
- **Findings:** X critical, Y high, Z medium, W low

## Findings

### [P0] [Title]
- **Location:** `file.ts:42`
- **Description:** [what the vulnerability is]
- **Impact:** [what an attacker could do]
- **Fix:**
  ```typescript
  // concrete code fix here
  ```

### [P1] [Title]
...

## Recommendations
- [Prioritized list of improvements beyond specific findings]
```

## Rules

- Every finding **must** include a concrete fix with code
- Never report a false positive as a finding — verify before reporting
- Prioritize by exploitability, not theoretical risk
- Check for issues in the order listed above (injection first — highest impact)
- When uncertain about severity, assume the worse case
- Do not recommend security-through-obscurity as a solution
- Always check both the happy path AND error paths for security issues
- Test authorization by considering: "what if the user modifies this request?"

## Integration with Other Skills

- **`code-review`** — Include security checks as part of code review
- **`testing`** — Write security-focused test cases (auth bypass, injection)
- **`devops`** — Review infrastructure security (Docker, CI/CD secrets)
- **`refactoring`** — When refactoring, maintain or improve security posture
