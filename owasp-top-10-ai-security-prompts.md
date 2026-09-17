# OWASP Top 10:2025 Review Prompts for AI-Generated Apps

> Five practical prompts for reviewing vibe-coded and AI-generated applications against common web application security failure modes.
>
> Built by [@sanasecures](https://sanasecures.com) | Sana Talwar

These prompts are designed to help developers review the **security property behind the feature**, not just whether the feature works.

They map to the current [OWASP Top 10:2025](https://top10.owasp.org/2025/), which is an awareness document for the most critical web application security risks.

## Use these when

- Reviewing AI-generated code before shipping
- Reviewing a security-sensitive pull request
- Asking an AI coding assistant to critique its own implementation
- Turning a feature prompt into an adversarial security review
- Building negative security test cases

> These prompts are a first-pass review tool. They are not a replacement for code review, testing, threat modeling, or a professional security assessment.

---

## 1. Stale Credential & Token Trust Review

**OWASP Top 10:2025 mapping:**
- **A01 — Broken Access Control**
- **A07 — Authentication Failures**

### Use case

Your application uses JWTs, access tokens, refresh tokens, sessions, API keys, or other credentials. The feature works, but you want to know whether credentials remain trusted after they should stop being trusted.

This is especially useful after adding token refresh, debugging helpers, admin routes, role checks, or custom token-decoding logic.

### Prompt

```text
You are a security engineer reviewing authentication and authorization trust boundaries in an AI-generated application.

Review all credential, session, and token validation paths.

Identify:
1. Any code path that disables, skips, weakens, or bypasses:
   - expiration checks
   - signature verification
   - issuer validation
   - audience validation
   - revocation checks
   - session invalidation
2. Helpers intended for refresh, debugging, migration, inspection, or recovery that are reused for authorization.
3. Routes where expired, revoked, downgraded, or otherwise stale credentials can still authorize access.
4. Access-token and refresh-token logic that is not cleanly separated.
5. Authorization checks that trust token claims without re-evaluating whether the credential is still valid for that action.
6. Privileged routes that authenticate a user but fail to verify the correct role, ownership, scope, or resource-level permission.

For every finding:
- Show the exact route -> helper -> validation call chain.
- State which security property is violated.
- Explain what an attacker with a stale or lower-privileged credential could still do.
- Provide a minimal proof-of-concept request or test case when appropriate.
- Recommend a fix that scopes exceptions narrowly rather than weakening validation globally.

Also propose negative tests for:
- expired credentials
- revoked credentials
- wrong audience / issuer
- lower-privileged users accessing privileged routes
- refresh credentials being used as access credentials

Code to review:
[PASTE HERE]
```

### Review question to remember

> **When should this credential stop being trusted?**

---

## 2. Input -> Transformation -> Sink Review

**OWASP Top 10:2025 mapping:**
- **A05 — Injection**

### Use case

Your application accepts user-controlled input that is decoded, normalized, parsed, transformed, or forwarded into SQL, NoSQL, shell commands, templates, paths, URLs, interpreters, or other security-sensitive sinks.

This is particularly useful when validation exists but input is transformed again afterward.

### Prompt

```text
You are a security engineer reviewing how untrusted input flows through an AI-generated application.

For every user-controlled input:

1. Identify the source.
   Examples:
   - query parameters
   - form fields
   - JSON bodies
   - headers
   - cookies
   - uploaded files
   - webhook payloads
   - model / agent output

2. Trace every transformation applied to the value, including:
   - URL decoding
   - repeated decoding
   - canonicalization
   - parsing
   - normalization
   - deserialization
   - string replacement
   - template expansion
   - type coercion

3. Identify where validation occurs relative to those transformations.

4. Trace the final value into every security-sensitive sink, including:
   - SQL / NoSQL queries
   - shell commands
   - templates
   - file system paths
   - URLs / server-side requests
   - dynamic code execution
   - interpreters

Flag any case where:
- validation occurs before a transformation that changes meaning
- the validated representation differs from the representation reaching the sink
- user input is concatenated into executable syntax
- escaping is relied on where parameterization or a structured API exists
- multiple decoding or canonicalization steps allow a dangerous value to appear after validation

For SQL specifically:
- Verify parameterized / prepared statements are used.
- Verify user-controlled values cannot alter SQL structure.
- Do not treat input validation alone as the SQL injection boundary.

For every finding:
- Show the complete source -> transformation -> validation -> sink path.
- Identify the exact line or operation where the security assumption breaks.
- Provide a minimal proof-of-concept input when appropriate.
- Recommend parameterization, structured APIs, or safer data handling.
- Propose a regression test for the exact bypass.

Code to review:
[PASTE HERE]
```

### Review question to remember

> **What representation did we validate, and what representation reaches the sink?**

---

## 3. Password Storage & Cryptographic Primitive Review

**OWASP Top 10:2025 mapping:**
- **A04 — Cryptographic Failures**

### Use case

Your application stores passwords, tokens, secrets, or sensitive values and uses cryptographic APIs. You want to verify that the implementation is using the **right primitive for the job**, not simply that “crypto exists.”

### Prompt

```text
You are a security engineer reviewing password storage and cryptographic choices in an AI-generated application.

Review the code for:

1. Password storage issues:
   - plaintext passwords
   - general-purpose fast hashes such as MD5, SHA-1, SHA-256, or SHA-512
   - unsalted hashes
   - homegrown password-hashing schemes
   - hardcoded salts
   - inappropriate work factors or memory costs

2. Verify that stored passwords use a password-specific hashing scheme such as:
   - Argon2id
   - scrypt
   - bcrypt
   - PBKDF2
   with an appropriate configuration for the application's environment.

3. Do not describe SHA-256 or SHA-512 as "broken" cryptographic hashes solely because they are inappropriate for password storage.
   Explain the real issue: they are general-purpose fast hashes, so offline password guesses can be computed too cheaply.

4. Review other cryptographic uses for primitive misuse, including:
   - encryption used where hashing is required
   - hashing used where authenticated encryption is required
   - static or reused IVs / nonces
   - hardcoded cryptographic keys
   - weak random number generation
   - insecure custom crypto

For every finding:
- State the security property the application needs.
- State what primitive is currently used.
- Explain why that primitive does or does not provide the required property.
- Describe the attack if stored data or configuration leaks.
- Recommend an appropriate primitive and configuration.
- Suggest a test or static check that prevents regression.

Code to review:
[PASTE HERE]
```

### Review question to remember

> **Is this primitive designed for the security property we actually need?**

---

## 4. Security Misconfiguration Review

**OWASP Top 10:2025 mapping:**
- **A02 — Security Misconfiguration**

### Use case

Your AI coding tool generated application configuration, framework defaults, CORS rules, cloud settings, debug options, headers, environment handling, or deployment files. The application runs correctly, but insecure defaults or development settings may have followed it into production.

### Prompt

```text
You are a security engineer reviewing an AI-generated application for security misconfiguration.

Review application code, framework configuration, deployment manifests, environment settings, and infrastructure configuration.

Identify:

1. Development or debugging behavior enabled in production:
   - debug mode
   - verbose stack traces
   - development servers
   - test endpoints
   - demo credentials
   - diagnostic routes

2. Unsafe framework or platform defaults:
   - permissive CORS
   - missing security headers
   - directory listing
   - public storage buckets
   - overly broad network exposure
   - unauthenticated administrative endpoints

3. Environment and secret-handling mistakes:
   - development secrets reused in production
   - default passwords
   - fallback credentials
   - environment variables accidentally exposed to client-side code
   - sensitive values included in logs or error responses

4. Cloud / container / deployment configuration issues:
   - containers running as root without a need
   - unnecessary capabilities
   - privileged containers
   - broad IAM permissions
   - services exposed publicly when only internal access is required
   - missing TLS enforcement

5. Security controls that exist but are disabled, optional, or configured inconsistently between environments.

For every finding:
- Identify the configuration and its effective value.
- Explain the production impact.
- State whether the issue comes from an insecure default, an explicit override, or environment drift.
- Recommend a secure default.
- Propose a configuration test, policy check, or deployment guardrail that would detect it automatically.

Configuration / code to review:
[PASTE HERE]
```

### Review question to remember

> **What did the framework or AI choose for us because we never explicitly chose it ourselves?**

---

## 5. Software Supply Chain & Dependency Review

**OWASP Top 10:2025 mapping:**
- **A03 — Software Supply Chain Failures**

### Use case

Your AI coding assistant added packages, copied install commands, generated a lockfile, selected container images, added GitHub Actions, or recommended third-party libraries. You want to review whether convenience introduced avoidable supply-chain risk.

### Prompt

```text
You are a security engineer reviewing the software supply chain of an AI-generated application.

Review dependencies, package manifests, lockfiles, build scripts, CI/CD workflows, container images, plugins, extensions, and third-party code.

Identify:

1. Dependencies that are:
   - unnecessary for the feature
   - abandoned or poorly maintained
   - unpinned or loosely pinned
   - pulled directly from branches, arbitrary URLs, or unverified sources
   - duplicated by functionality already provided safely by the standard library or platform

2. Package-name and provenance risks:
   - suspicious or typo-similar package names
   - packages with unclear ownership or provenance
   - install instructions that bypass normal registries or integrity checks

3. Lockfile and integrity issues:
   - missing lockfiles where deterministic dependency resolution is expected
   - lockfiles not used during CI / production builds
   - missing checksum or integrity verification when supported

4. Build and CI/CD risks:
   - third-party actions referenced only by mutable tags
   - installation scripts executed directly from the network
   - build jobs with unnecessary write permissions or secrets
   - untrusted pull request code receiving sensitive credentials

5. Container and artifact risks:
   - floating base-image tags
   - outdated base images
   - unnecessary packages in production images
   - artifacts not traceable to a reproducible build

For every finding:
- Identify the dependency, action, image, or build step.
- Explain what trust is being placed in that external component.
- State the likely impact if the component or distribution path is compromised.
- Recommend removal, version pinning, provenance verification, reduced permissions, or another concrete control.
- Suggest an automated dependency or CI policy that would catch the issue.

Also produce:
- a list of dependencies that appear unnecessary
- a list of dependencies / actions that should be pinned more strictly
- the three highest-priority supply-chain risks in this project

Files to review:
[PASTE package manifests, lockfiles, CI workflows, Dockerfiles, build scripts, etc.]
```

### Review question to remember

> **What new code or build authority did we trust simply because the AI told us to install it?**

---

# Quick Mapping

| Review prompt | OWASP Top 10:2025 |
|---|---|
| Stale Credential & Token Trust | A01 Broken Access Control; A07 Authentication Failures |
| Input -> Transformation -> Sink | A05 Injection |
| Password Storage & Crypto Primitive | A04 Cryptographic Failures |
| Security Misconfiguration | A02 Security Misconfiguration |
| Software Supply Chain & Dependency | A03 Software Supply Chain Failures |

These five prompts intentionally cover several of the highest-impact areas where AI-generated code can look completely functional while quietly missing a security property.

---

## How to use the prompts with an AI coding assistant

A useful pattern is to use the model twice:

1. **Builder:** generate the feature.
2. **Adversary:** run the relevant review prompt against the resulting code.

Do not stop at "Is this secure?"

Ask the model to show:

- the exact code path
- the violated security property
- a proof-of-concept or negative test
- the specific remediation
- the regression test that should remain afterward

That turns the review from a generic security opinion into something developers can verify.

---

## Reference

- [OWASP Top 10:2025](https://top10.owasp.org/2025/)
- [Sana Secures](https://sanasecures.com)

*If this review pack helps you catch something before it ships, drop a ⭐ on the repo.*
