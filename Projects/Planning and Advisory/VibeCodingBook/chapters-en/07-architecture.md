# Chapter 12 The Architecture Map: Frontend, Backend, Database, API

Every web application has four parts. If you put the wrong thing in the wrong part, your app will break, leak secrets, or cost you money. This chapter draws the map so you always know where things belong.

## Key Terms

| Term                 | What it means                                                |
| -------------------- | ------------------------------------------------------------ |
| **Frontend**         | Code that runs in the user's browser (HTML, CSS, JavaScript) |
| **Backend**          | Code that runs on a server (Node.js, Python, Go)             |
| **Database**         | Where data lives permanently (PostgreSQL, MongoDB, Supabase) |
| **API**              | The messenger: how the frontend asks the backend for data    |
| **Server**           | A computer that's always on, waiting for requests            |
| **Client**           | The user's browser or device                                 |
| **Request/Response** | The browser asks (request), the server answers (response)    |

## The Restaurant Analogy

A restaurant has four areas. So does your app.

**The dining room** is the frontend. Customers see it, sit in it, interact with it. The menu is HTML. The decoration is CSS. The waiter taking your order is JavaScript.

**The kitchen** is the backend. Customers never see it. This is where the real work happens: cooking food, checking ingredient stock, handling special requests.

**The pantry** is the database. Raw ingredients stored in an organised way. The kitchen retrieves what it needs, uses it, and puts things back.

**The waiter** is the API. Takes orders from the dining room to the kitchen, brings food from the kitchen to the dining room. The dining room and the kitchen never communicate directly.

### JobPilot's Architecture Through the Restaurant Lens

Let's map JobPilot to the restaurant. The **dining room** is the popup the user clicks to fill a form and the content script that injects into job application pages. The **kitchen** is the service worker running in the background (plus a small API server that talks to OpenAI). The **pantry** is `chrome.storage`, where the user's professional profile lives. And the **waiter**? Chrome's message passing system, which carries messages between the content script, the popup, and the service worker. None of them can talk directly. Every request goes through a message.

There's one extra character that restaurants don't have: a **delivery driver**. The content script is injected into someone else's website (Greenhouse, Lever, Workday). It's code that lives inside a page you don't own. That's unusual. Most web apps only run on their own domain.

If you're building a web app instead of an extension, the mapping is simpler: React frontend = dining room, Express/Node backend = kitchen, database = pantry, API endpoints = waiter. No delivery driver needed.

## Tracing a Request

A user clicks "Submit Review" on the ReviewOrb app. Here's exactly what happens:

1. **Frontend (browser):** JavaScript captures the form data (rating: 5, comment: "Excellent")
2. **API call:** JavaScript sends an HTTP POST request to `https://api.example.com/reviews`
3. **Backend (server):** Receives the request, validates the data, checks authentication (Chapter 19 explains how authentication works)
4. **Database:** Backend inserts a new row: `{user_id: 42, rating: 5, comment: "Excellent", created_at: "2026-01-15"}`
5. **Response:** Backend sends back `{status: "success", id: 123}`
6. **Frontend (browser):** JavaScript shows "Review submitted!" to the user

Six steps. Every web application follows this pattern, from a one-page review app to Google Maps.

## Where Things Belong

This is where beginners make expensive mistakes. The rule is simple: anything that must be secret or trusted goes on the backend.

### Frontend (Browser)

The user can see everything. Open DevTools → Sources → every line of code is visible. Every variable. Every string. Every API key you accidentally left there.

**Put here:**

- User interface (HTML, CSS, visual components)
- Form validation for user experience ("please enter a valid email": catches typos before submitting)
- Non-sensitive logic (sorting a list, showing/hiding elements, animations)
- Public configuration (the app's name, theme colours, feature flags from an API)

**Never put here:**

- API keys with billing implications (OpenAI, Stripe secret key)
- Business logic that determines pricing or access
- Database credentials
- Authentication decisions ("is this user an admin?" decided only in the browser = anyone can be admin)

### Backend (Server)

The user cannot see this code. It runs on your server.

**Put here:**

- All secret keys and credentials
- Authentication logic (verify tokens, check permissions)
- Business rules (pricing calculations, access control)
- Database operations (queries, updates, deletes)
- Data validation that matters (the backend must re-validate everything the frontend validated, because someone can bypass the frontend entirely)

### Database

**Put here:**

- User data (accounts, profiles, preferences)
- Application data (reviews, orders, messages)
- Configuration that changes at runtime (feature flags, settings)

**Never put here:**

- Passwords in plain text (use bcrypt or argon2 hashing)
- Sensitive data without encryption
- Temporary data that belongs in the session or cache

## Anti-Patterns That Will Ruin Your Day

**Billing logic in the browser.** If your frontend calculates the price and sends it to the backend ("charge £19.99"), anyone can change it to £0.01 using DevTools. The backend must calculate prices independently.

**API keys in HTML.** Your Stripe secret key in a `<script>` tag means anyone can make charges on your account. Only publishable keys go in client code.

**Auth only in CSS.** Hiding an admin panel with `display: none` is not security. Removing the CSS shows the panel. The backend must verify permissions on every request.

**Database queries from the browser.** If the frontend talks directly to the database without a backend, there's no access control. User A can read User B's data by guessing the ID.

> **Try It:** Open any web app you use (Twitter, Gmail, a to-do app). Open DevTools → Network tab. Click something that loads data. Find the API request in the list. The page you're looking at is the frontend. The URL in that request is the API. The server that responded is the backend. You just traced the architecture.

## The Code Version

Frontend (React):

```jsx
// Frontend: collects data, sends to backend
async function submitReview(rating, comment) {
  const response = await fetch("https://api.example.com/reviews", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
      Authorization: `Bearer ${userToken}`,
    },
    body: JSON.stringify({ rating, comment }),
  });
  const result = await response.json();
  return result;
}
```

Backend (Node.js/Express):

```javascript
// Backend: validates, authenticates, stores
app.post("/reviews", authenticate, async (req, res) => {
  const { rating, comment } = req.body;

  // Validate on the server (never trust the client)
  if (rating < 1 || rating > 5) {
    return res.status(400).json({ error: "Rating must be 1 to 5" });
  }

  // Store in database
  const review = await db.reviews.create({
    userId: req.user.id,
    rating,
    comment,
    createdAt: new Date(),
  });

  res.json({ status: "success", id: review.id });
});
```

Notice: the frontend sends data. The backend validates it again. The backend accesses the database. The frontend never touches the database directly.

## Common Beginner Mistake

**Skipping the backend entirely.** Supabase and Firebase let the frontend talk to the database directly, which feels like you don't need a backend. You do. The moment you need business logic, data validation beyond basic rules, API integrations with secret keys, or complex queries, you need server-side code. Start with direct database access. Add a backend when complexity demands it.

## Checklist

- [ ] I can name the four parts of a web app (frontend, backend, database, API)
- [ ] I know that users can see all frontend code (DevTools → Sources)
- [ ] I never put secret keys in frontend code
- [ ] I validate data on the backend, not just the frontend
- [ ] I can trace a request from button click to database and back

## Summary

Every web app has four parts: frontend (what the user sees in the browser), backend (code on the server), database (where data lives), and API (the messenger between frontend and backend). Users can see all frontend code, so secrets and business logic belong on the backend. The backend validates all data, even if the frontend validated it first. Common mistakes: putting billing logic in the browser, exposing API keys in HTML, and relying on CSS to hide admin features.

## Challenge

1. Open any web application you use daily. Open DevTools (Network tab). Click around. Watch the requests. For each request, identify: is it going to an API? What method (GET/POST)? What data is in the response?

2. Draw a diagram of a simple app (a to-do list with user accounts). Label where each piece lives: which code runs in the browser, which runs on the server, and what goes in the database. Identify one thing that would be a security mistake if placed in the wrong box.
