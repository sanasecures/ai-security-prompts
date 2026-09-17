# Sana Secures — AI App Security Prompt Library
> Practical security prompts for reviewing AI-generated applications.  
> Built by [@sanasecures](https://sanasecures.com) | Sana Talwar — AI Security Practitioner & Speaker
---
## Why This Exists
AI coding tools like Lovable, Cursor, and GitHub Copilot can generate working applications fast — but they often miss critical security controls. These prompts help you catch what the AI forgot.
**Use these when:**
- Reviewing an AI-generated codebase before shipping
- Doing a quick self-audit of a vibe-coded project
- Onboarding a team to secure AI development practices
---
## Prompts
### 1. 🔍 Prompt Injection Review
```
You are a security engineer reviewing an AI application for prompt injection vulnerabilities.
Review the following [system prompt / agent instructions / user-facing input fields] and identify:
1. Places where untrusted user input is passed directly into a prompt without sanitization
2. Instructions that could be overridden or hijacked by a malicious user input
3. Multi-agent or tool-calling flows where one agent's output becomes another's input without validation
4. Any indirect prompt injection risks (e.g., content fetched from external URLs, emails, or documents inserted into the prompt)
For each finding:
- Describe the vulnerability
- Explain how an attacker could exploit it
- Suggest a specific remediation
Code/Prompt to review:
[PASTE HERE]
```
---
### 2. 🔐 Authentication Review
```
You are a security engineer reviewing authentication implementation in an AI-generated application.
Review the following code and identify:
1. Missing or broken authentication on routes/endpoints that should require a logged-in user
2. Insecure session management (e.g., tokens stored in localStorage, no expiry, no rotation on login)
3. Password storage issues (plaintext, weak hashing algorithms like MD5 or SHA1)
4. Missing MFA support on sensitive actions
5. OAuth or SSO misconfigurations
6. Account enumeration risks (e.g., different error messages for "user not found" vs "wrong password")
For each finding:
- Describe the vulnerability
- Severity: Critical / High / Medium / Low
- Specific remediation with code example if possible
Code to review:
[PASTE HERE]
```
---
### 3. 🚪 Authorization Review
```
You are a security engineer reviewing authorization controls in an AI-generated application.
This is consistently the most overlooked issue in AI-generated code. Review the following and identify:
1. Missing authorization checks — routes or functions that authenticate the user but don't verify they're allowed to access that specific resource
2. Insecure Direct Object Reference (IDOR) — e.g., /api/user/123/data where changing 123 returns another user's data
3. Privilege escalation paths — can a regular user perform admin actions by manipulating parameters?
4. Missing row-level security — does the database query filter by the logged-in user's ID?
5. Frontend-only authorization (hiding buttons but not protecting the API endpoint)
For each finding:
- Describe the vulnerability
- Provide a proof-of-concept attack scenario
- Suggest a specific remediation
Code to review:
[PASTE HERE]
```
---
### 4. 🔑 Secrets Detection Review
```
You are a security engineer reviewing an AI-generated codebase for secrets exposure.
Review the following code and identify:
1. Hardcoded API keys, tokens, or credentials in source code
2. Secrets passed as environment variables but exposed client-side (e.g., NEXT_PUBLIC_ prefix in Next.js, Lovable/Supabase client-side code)
3. Secrets committed in configuration files (.env files, config.js, etc.)
4. API keys or tokens logged to console or error messages
5. Secrets exposed in client-side JavaScript bundles
6. Database connection strings or credentials in code
For each finding:
- Location of the exposed secret (file, line if visible)
- What service/system is at risk
- Immediate remediation steps
- How to rotate the credential
Code/config to review:
[PASTE HERE]
```
---
### 5. 🗺️ AI Application Threat Model
```
You are a security architect helping threat model an AI-powered application.
Application description:
[DESCRIBE YOUR APP — what it does, who uses it, what data it handles]
AI components:
[LIST — e.g., LLM for chat, RAG pipeline, AI agents, tool calling, MCP servers]
Based on this, generate a threat model covering:
1. STRIDE Analysis
  - Spoofing: Can an attacker impersonate a user or the AI system?
  - Tampering: Can inputs or outputs be manipulated?
  - Repudiation: Are AI actions logged and attributable?
  - Information Disclosure: What sensitive data could leak (via prompts, RAG, logs)?
  - Denial of Service: Can the AI be made unavailable or unusably slow (e.g., token exhaustion)?
  - Elevation of Privilege: Can a user gain more access via the AI than they should have?
2. AI-Specific Threats
  - Prompt injection (direct and indirect)
  - RAG data poisoning
  - Agent permission abuse
  - Tool call manipulation
  - Context window attacks
3. Top 5 Risks — ranked by likelihood × impact
4. Recommended security controls for each risk
Be specific to the application described. Do not give generic advice.
```
---
## OWASP Top 10 Review Pack
🆕 See **[OWASP Top 10:2025 Review Prompts for AI-Generated Apps](./owasp-top-10-ai-security-prompts.md)** for five deeper review prompts covering stale credential trust, input-to-sink injection, password/crypto primitive misuse, security misconfiguration, and software supply chain failures.

## Building with Lovable?
🆕 See **[Lovable-Specific Security Prompts](./lovable-security-prompts.md)** for platform-specific checks including the first-prompt one-liner, RLS SQL audit, mid-build review, and a full pre-ship checklist.
---
## How to Use These Prompts
1. Copy the prompt
2. Paste your code, system prompt, or app description where indicated
3. Run in ChatGPT, Claude, or your preferred LLM
4. Review findings — treat output as a starting point, not a final audit
> ⚠️ These prompts are a first-pass review tool, not a replacement for professional security assessment.
---
## About
**Sana Talwar** is a Product Security Engineer, AI security practitioner, and speaker.  
She speaks on prompt injection, secure vibe coding, and AI application security at conferences including NDC Oslo, WiCyS, and Cyberjutsu Unplugged.
🌐 [sanasecures.com](https://sanasecures.com)  
💼 [LinkedIn](https://www.linkedin.com/in/sana-talwar/)
### 🎥 See It In Action
Watch the live webinar where these prompts were demonstrated — *AI Application Security for Vibe Coders* — built from real experience shipping a full-stack app in 48 hours during She Builds Season 2.
▶️ [Watch on YouTube](https://www.youtube.com/live/bV8u9a5lwLs?is=Mc6aXZb0WlmIGfAi)
---
*If these prompts helped you ship something more securely, drop a ⭐ on this repo.*
