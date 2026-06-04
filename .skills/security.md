# Antigravity Skill: Security & Zero-Trust
# Priority: CRITICAL | Impact: 10/10 | Rating: ⭐⭐⭐⭐⭐

## ACTIVATION
Load ALWAYS — every project, every layer, no exceptions.
Security is not a feature added at the end. It is baked in from line 1.

---

## CORE RULE
> Assume breach. Design from the outside in.
> Every input is hostile. Every token is a target. Every secret is a liability.

---

## AUTHENTICATION

### OAuth 2.1 / OpenID Connect (Standard)
```
✅ Use Auth.js v5 (Next.js) or Lucia Auth (framework-agnostic)
✅ PKCE flow for ALL OAuth code exchanges — no implicit flow
✅ Store JWT in HttpOnly + Secure + SameSite=Strict cookies ONLY
✅ Access token expiry: 15 minutes
✅ Refresh token expiry: 7-30 days with rotation
✅ Refresh token stored in database (allow revocation)

❌ NEVER store JWT in localStorage
❌ NEVER store JWT in sessionStorage
❌ NEVER use implicit OAuth flow
❌ NEVER use client_secret in frontend code
```

### Session Security
```
✅ Regenerate session ID on every privilege escalation
✅ Bind session to IP + User-Agent fingerprint
✅ Absolute session timeout: 24 hours (regardless of activity)
✅ Concurrent session limit with notification to user
✅ "Log out all devices" functionality required
```

---

## AUTHORIZATION

### RBAC / ABAC Pattern
```typescript
// Define permissions clearly
const permissions = {
  'document:read':   ['viewer', 'editor', 'admin'],
  'document:write':  ['editor', 'admin'],
  'document:delete': ['admin'],
  'billing:manage':  ['admin'],
} as const;

// Enforce at EVERY layer:
// 1. Middleware (route protection)
// 2. Service layer (business logic)
// 3. Database (Row-Level Security)
// ← All 3 required. Never just one.
```

### Row-Level Security (Postgres)
```sql
-- Enable RLS on all tenant tables
ALTER TABLE documents ENABLE ROW LEVEL SECURITY;

-- Policy: users only see their organization's data
CREATE POLICY org_isolation ON documents
  USING (org_id = current_setting('app.current_org_id')::uuid);
```

---

## SECRET MANAGEMENT
```
✅ Vault / AWS Secrets Manager / Doppler for production secrets
✅ .env.example committed with empty values (documentation only)
✅ .env files in .gitignore — verified on every commit
✅ Secrets rotated automatically every 90 days
✅ Secrets never logged — scrub logs before writing
✅ Different secrets per environment (dev / staging / prod)

❌ NEVER hardcode secrets in source code
❌ NEVER commit .env files with real values
❌ NEVER use the same secret across environments
❌ NEVER log request bodies that may contain secrets
```

---

## INPUT VALIDATION & INJECTION PREVENTION
```typescript
// ALL inputs validated with Zod before processing
const CreateUserSchema = z.object({
  email: z.string().email().max(254),
  name: z.string().min(1).max(100).trim(),
  role: z.enum(['user', 'admin']),
});

// Database queries ALWAYS parameterized
// Good:
await db.query('SELECT * FROM users WHERE id = $1', [userId]);

// Never:
await db.query(`SELECT * FROM users WHERE id = '${userId}'`);
```

```
✅ Validate ALL user inputs before any processing (Zod schemas)
✅ Parameterize ALL database queries
✅ Sanitize HTML output (DOMPurify for user-generated content)
✅ Content Security Policy headers on all responses
✅ File upload: validate MIME type + extension + max size + antivirus scan

❌ NEVER trust client-provided IDs without ownership verification
❌ NEVER reflect user input back without escaping
❌ NEVER use eval() or new Function() with user data
```

---

## HTTP SECURITY HEADERS
```typescript
// Required on every response (use Helmet.js for Node.js)
{
  'Strict-Transport-Security': 'max-age=31536000; includeSubDomains; preload',
  'X-Content-Type-Options': 'nosniff',
  'X-Frame-Options': 'DENY',
  'X-XSS-Protection': '0',  // Disabled — use CSP instead
  'Referrer-Policy': 'strict-origin-when-cross-origin',
  'Content-Security-Policy': "default-src 'self'; ...",
  'Permissions-Policy': 'camera=(), microphone=(), geolocation=()',
}
```

---

## RATE LIMITING
```
✅ API Gateway: global rate limit (e.g., 1000 req/min per IP)
✅ Auth endpoints: strict limit (e.g., 5 attempts/15 min per IP)
✅ Per-user token bucket: burst + sustained rate limits
✅ Return 429 with Retry-After header
✅ Lockout + CAPTCHA after 5 failed auth attempts
```

---

## AUDIT LOGGING
```
Log ALL:
  ✅ Authentication events (login, logout, failed attempts)
  ✅ Authorization failures
  ✅ Admin actions (any action by admin role)
  ✅ Data mutations on sensitive entities (user, payment, config)
  ✅ Secret access events

Log format (structured JSON):
  { timestamp, userId, action, resource, resourceId, ip, userAgent, result }

Storage:
  ✅ Immutable append-only log (cannot be deleted by application)
  ✅ Retained for minimum 1 year
  ✅ Alerting on suspicious patterns
```

---

## DEPENDENCY SECURITY
```
✅ Snyk or Dependabot scanning on every commit
✅ npm audit in CI/CD pipeline (fail on high severity)
✅ Lock files committed (package-lock.json / pnpm-lock.yaml)
✅ Automated PRs for security updates
✅ Zero known high-severity vulnerabilities to deploy
```

---

## WORLD-CLASS REFERENCES
- authjs: https://github.com/nextauthjs/next-auth (⭐ 25k)
- helmet: https://github.com/helmetjs/helmet (⭐ 10k)
- casl: https://github.com/stalniy/casl (⭐ 6k)
- lucia: https://github.com/lucia-auth/lucia (⭐ 10k)
- zod: https://github.com/colinhacks/zod (⭐ 36k)
- owasp-cheat-sheets: https://github.com/OWASP/CheatSheetSeries (⭐ 29k)
