# Chapter 19 Secrets & Authentication

Your app needs to know who the user is (authentication) and what they're allowed to do (authorisation). It also handles sensitive credentials: API keys, database passwords, OAuth tokens. Getting any of this wrong has immediate consequences: data breaches, financial loss, and broken trust.

## Key Terms

| Term                | What it means                                                            |
| ------------------- | ------------------------------------------------------------------------ |
| **Authentication**  | Verifying identity: who are you?                                         |
| **Authorisation**   | Verifying permission: what can you do?                                   |
| **OAuth 2.0**       | The standard protocol for "Login with Google/GitHub/Apple"               |
| **OpenID Connect**  | An identity layer built on top of OAuth 2.0                              |
| **Session**         | The server's record that a specific user is currently logged in          |
| **JWT**             | JSON Web Token: a signed, self-contained token carrying user information |
| **HttpOnly cookie** | A cookie that JavaScript cannot read (only the server can)               |
| **.env file**       | A file containing environment-specific secrets, never committed to Git   |

## OAuth 2.0: How "Login with Google" Works

Most apps don't manage passwords. They delegate authentication to Google, GitHub, or Apple through OAuth 2.0. The flow:

1. User clicks "Login with Google"
2. Your app redirects the user to Google's consent page
3. Google shows: "This app wants to access your email and profile. Allow?"
4. User clicks "Allow"
5. Google redirects back to your app with an authorisation code
6. Your backend exchanges the code for an access token (server-to-server, never exposed to the browser)
7. Your backend uses the token to get the user's profile from Google
8. Your app creates a session for the user

The user never gives your app their Google password. Google handles the authentication. Your app receives a token that proves the user is who they claim to be.

### How JobPilot Handles OAuth: chrome.identity

JobPilot uses Google OAuth, but the flow is slightly different from a web app. Chrome extensions can't do a normal browser redirect (the extension doesn't have a URL that Google can redirect back to). Instead, Chrome provides `chrome.identity.getAuthToken()`, which handles the entire OAuth flow inside a popup window that Chrome controls.

Here's what you'll ask AI to generate:

```javascript
// In the extension's service worker or popup
const token = await chrome.identity.getAuthToken({ interactive: true });
// Google shows the consent screen, user clicks Allow,
// and Chrome hands you the token directly.

// Use the token to get the user's profile
const response = await fetch("https://www.googleapis.com/oauth2/v1/userinfo", {
  headers: { Authorization: `Bearer ${token.token}` },
});
const user = await response.json();
// { email: 'sofia@gmail.com', name: 'Sofia', picture: '...' }
```

Two lines to authenticate. Chrome manages the redirect, the consent screen, and the token storage. This only works because the extension is registered in the Chrome Web Store and has a stable extension ID that Google trusts.

If you're building a web app instead, you'd use a library like NextAuth.js or Supabase Auth. The concept is the same (delegate to Google), but the redirect flow is different.

## Sessions: Staying Logged In

After authentication, the user needs to stay logged in as they move through your app. Two approaches:

### Server-Side Sessions

The server creates a random session ID, stores it in a database with the user's information, and sends it to the browser as a cookie.

```
Set-Cookie: sessionId=a3f8b2c9d4e5; HttpOnly; Secure; SameSite=Strict; Path=/; Max-Age=86400
```

On every subsequent request, the browser automatically sends the cookie. The server looks up the session ID in the database and knows who the user is.

**HttpOnly** means JavaScript cannot read this cookie (protects against XSS attacks stealing the session). **Secure** means it's only sent over HTTPS. **SameSite=Strict** prevents it from being sent on cross-site requests (protects against CSRF).

### JWT (JSON Web Tokens)

A JWT is a signed token that contains the user's information. The server doesn't need to look it up; it verifies the signature.

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.
eyJ1c2VySWQiOiI0MiIsImVtYWlsIjoic29maWFAZXhhbXBsZS5jb20iLCJyb2xlIjoiYWRtaW4ifQ.
SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

Three parts, base64-encoded:

1. **Header:** Algorithm and token type
2. **Payload:** User data (userId, email, role, expiry time)
3. **Signature:** Proof the token wasn't tampered with

JWTs are useful for stateless APIs (the server doesn't store sessions) but have drawbacks: they can't be revoked before expiry unless you maintain a blocklist (which defeats the stateless benefit).

**The practical choice:** Use server-side sessions stored in HttpOnly cookies for most web applications. Use JWTs for API-to-API communication between services.

## Cookie Security Flags

Cookies carry your authentication. Their security flags matter:

| Flag                | What it does                      | Always use?              |
| ------------------- | --------------------------------- | ------------------------ |
| **HttpOnly**        | JavaScript can't read it          | Yes                      |
| **Secure**          | Only sent over HTTPS              | Yes                      |
| **SameSite=Strict** | Not sent on cross-site requests   | Yes (or Lax)             |
| **Max-Age**         | When the cookie expires           | Set appropriate duration |
| **Path=/**          | Cookie applies to the entire site | Usually                  |

Missing any of these flags weakens your authentication. HttpOnly prevents XSS from stealing sessions. Secure prevents interception on HTTP. SameSite prevents CSRF attacks.

## API Keys and Secrets

Every external service gives you credentials. Managing them correctly is non-negotiable.

### Stripe

Stripe provides two types of keys:

- **Publishable key** (`pk_live_...`): Safe for client-side code. Can initiate payments but cannot capture them.
- **Secret key** (`sk_live_...`): Server only. Can capture payments, issue refunds, and access all account data.

If your secret key appears in client code, anyone can charge and refund on your account.

### OpenAI / Anthropic / Gemini

API keys for AI services must be server-side only. They carry billing implications. If exposed, someone else runs queries on your account.

```javascript
// Wrong: API key in frontend code
const response = await fetch("https://api.openai.com/v1/chat/completions", {
  headers: { Authorization: `Bearer sk-abc123...` }, // Visible to anyone
});

// Correct: frontend calls your backend, backend calls OpenAI
// Frontend
const response = await fetch("/api/summarise", {
  method: "POST",
  body: JSON.stringify({ text: reviewText }),
});

// Backend
app.post("/api/summarise", authenticate, async (req, res) => {
  const result = await openai.chat.completions.create({
    model: "gpt-4o-mini",
    messages: [{ role: "user", content: req.body.text }],
  });
  res.json({ summary: result.choices[0].message.content });
});
```

### .env Files

Secrets live in `.env` files that are never committed to Git.

```bash
# .env (in .gitignore, never committed)
STRIPE_SECRET_KEY=sk_live_abc123
OPENAI_API_KEY=sk-xyz789
SUPABASE_URL=https://abc.supabase.co
SUPABASE_SERVICE_ROLE_KEY=eyJhbG...
DATABASE_URL=postgresql://user:pass@host/db
```

Your code reads them through environment variables:

```javascript
const stripe = new Stripe(process.env.STRIPE_SECRET_KEY);
const openai = new OpenAI({ apiKey: process.env.OPENAI_API_KEY });
```

In production (Vercel, Render, Netlify), you set these same variables in the platform's dashboard. Different values for development and production.

> **Try It:** Create a `.env` file in any project folder. Add `SECRET_KEY=test123`. Now run `git status`. If `.env` appears in the output, you haven't added it to `.gitignore` yet. Do that now. Run `git status` again. The file should be gone from the list. This is how you keep secrets out of version control.

### Firebase and Supabase: The Confusing Keys

Firebase and Supabase give you client-side keys that look like they should be secret but aren't. The Firebase `apiKey` and Supabase `anon key` are designed to be public. They identify your project but don't grant special access.

The actual security comes from:

- **Firebase Security Rules:** database-level rules that control who can read/write what
- **Supabase Row-Level Security (RLS):** PostgreSQL policies that restrict data access per user

If you disable these rules (common during development), your database is open to anyone with the project URL. This is the most common Firebase/Supabase security mistake.

## Common Beginner Mistake

**Storing tokens in localStorage.** It feels natural: save the token, read it on every request. But localStorage is accessible from any JavaScript on the page. An XSS vulnerability lets an attacker steal every token stored in localStorage. HttpOnly cookies are invisible to JavaScript and automatically sent with requests. Use them for session management.

## Checklist

- [ ] I understand OAuth 2.0: user authenticates with Google, my app gets a token
- [ ] I use HttpOnly, Secure, SameSite cookies for session management
- [ ] I never store authentication tokens in localStorage
- [ ] My .env file is in .gitignore and never committed
- [ ] I know which keys are client-safe (Stripe publishable, Firebase apiKey) and which are server-only (Stripe secret, OpenAI key)
- [ ] If using Firebase/Supabase, security rules / RLS are enabled and configured

## Summary

Authentication verifies identity; authorisation verifies permission. OAuth 2.0 delegates authentication to trusted providers (Google, GitHub). Sessions keep users logged in, stored as HttpOnly cookies on the browser and session records on the server. API keys must be separated: client-safe keys in frontend code, secret keys on the server only. The .env file holds secrets locally and must never be committed to Git. Firebase/Supabase client keys are designed to be public, but security rules must be enabled to control data access.

## Challenge

1. Create a `.env` file in your project. Add `SECRET_KEY=test123`. Read it in your code with `process.env.SECRET_KEY`. Confirm `.env` is in `.gitignore`. Run `git status`. The `.env` file should not appear.

2. Inspect the cookies on a website where you're logged in (DevTools → Application → Cookies). Find the session cookie. Check its flags: is it HttpOnly? Secure? SameSite? Compare with a site that uses localStorage for authentication (check DevTools → Application → Local Storage).
