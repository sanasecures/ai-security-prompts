# Lovable-Specific Security Prompts

> Prompts designed specifically for apps built with [Lovable](https://lovable.dev) + Supabase.
> Based on real findings from She Builds Season 2.

---

## 🚀 The First Prompt (Add This to Every New Project)

Add this one-liner at the end of your very first Lovable prompt:

```
Build this application with RLS enabled on all Supabase tables, all secret keys stored in environment variables (never in client-side code), and server-side authorization checks on every route.
```

This seeds your security memory from the start. Lovable will carry it forward in every subsequent prompt.

---

## 🔍 Mid-Build Security Check (Every ~8 Hours)

Run this periodically as you add features:

```
Review the current state of this application for security issues. Specifically check:
1. Are there any API keys or secrets visible in client-side JavaScript?
2. Do all Supabase tables have RLS enabled with appropriate policies?
3. Are there any routes or API endpoints that return data without checking the logged-in user's identity and permissions?
4. Are there any UI elements that are hidden rather than actually restricted by server-side logic?

List all issues found and fix them one by one without breaking existing functionality.
```

---

## 🔐 RLS Check Prompt

Run this in your Supabase SQL editor to verify RLS is enabled on all public tables:

```sql
SELECT
  schemaname,
  tablename,
  rowsecurity
FROM pg_tables
WHERE schemaname = 'public'
ORDER BY tablename;
```

Every table should show `rowsecurity = true`. If any show `false`, run:

```
Enable RLS on all public Supabase tables that currently have it disabled, and add appropriate row-level security policies so users can only read and write their own data.
```

---

## 🔑 Secrets Audit Prompt

```
Audit this entire codebase for secrets exposure. Check:
1. Are any API keys, tokens, or credentials hardcoded anywhere in the code?
2. Are any environment variables being used client-side that should only be server-side?
3. Are any secrets being logged to the console or returned in error messages?
4. Are all third-party API calls going through Supabase Edge Functions rather than the browser?

Fix all issues found and confirm where each secret is now being stored.
```

---

## 🚪 Authorization Audit Prompt

```
Review all API routes and data fetching in this application for authorization issues.

For every endpoint that returns or modifies user data:
1. Does it verify the user is authenticated?
2. Does it verify the user is authorized to access THAT SPECIFIC resource (not just any resource)?
3. Is there any data accessible by changing an ID in a URL or request body?
4. Are there any UI elements that are hidden on the frontend but not actually restricted on the backend?

Fix all issues and add server-side authorization checks wherever they're missing.
```

---

## 🔄 Remix Security Check

If you're remixing someone else's project, run this immediately after:

```
I just remixed a Lovable project. Before I build anything new:
1. Run a full security scan of the existing code
2. Identify any hardcoded secrets or placeholders that need to be replaced
3. Check that RLS is enabled on all tables
4. List any security issues I should fix before adding new features
```

---

## ✅ Pre-Publish Checklist Prompts

Run these before going live:

### Authentication
```
Check this application's authentication implementation:
- Are all protected routes actually checking for a valid session?
- Is there any way to access user data without logging in?
- Test: what happens if someone navigates directly to /dashboard or /profile without being logged in?
Fix any issues found.
```

### Input Validation
```
Review all user input fields and API endpoints in this application.
- Is input being validated on the server, not just the frontend form?
- Are there any fields where a user could submit unexpected data types, lengths, or characters?
- Are there any SQL injection or script injection risks?
Add server-side validation wherever it's missing.
```

### Rate Limiting
```
Add rate limiting to the login, signup, and any password reset endpoints in this application to prevent brute force attacks and abuse.
```

---

## 📋 Pre-Ship Security Checklist

Before you publish, confirm:

- [ ] Ran Lovable Basic Security Scan (auto-runs on publish)
- [ ] Ran Lovable **Deep Security Scan** (2–4 min, run manually before going live)
- [ ] Auto Fix Security Issues is **ON** in project settings
- [ ] All **critical** and **error** findings resolved
- [ ] SQL query confirms RLS enabled on all public tables
- [ ] No API keys visible in browser DevTools → Network tab
- [ ] Logged out and tested: can't access protected pages directly
- [ ] Rate limiting added to login/signup endpoints
- [ ] All user inputs validated server-side

---

## 🔗 Resources

- [Lovable Security Docs](https://docs.lovable.dev) — security defaults and built-in features
- [Supabase RLS Guide](https://supabase.com/docs/guides/database/row-level-security)
- [OWASP Top 10](https://owasp.org/www-project-top-ten/) — the vulnerability list these prompts target
- [VibeAppScanner](https://vibeappscanner.com) — RLS audit tool for vibe-coded apps
- [Aikido](https://aikido.dev) — penetration testing connector available in Lovable

---

*Built from real experience in She Builds Season 2. Watch the full webinar: [YouTube](https://www.youtube.com/live/bV8u9a5lwLs?is=Mc6aXZb0WlmIGfAi)*
