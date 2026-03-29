# Chapter 14 APIs & HTTP: How Your App Talks to the World

Your frontend lives in the browser. Your backend lives on a server. Between them is HTTP: the protocol that carries every request, every response, every piece of data across the internet. Understanding HTTP is the difference between staring at a blank screen and knowing exactly why the data didn't load.

## Key Terms

| Term         | What it means                                                                                    |
| ------------ | ------------------------------------------------------------------------------------------------ |
| **HTTP**     | HyperText Transfer Protocol: the language browsers and servers use to communicate                |
| **API**      | Application Programming Interface: a set of endpoints your code can call to send or receive data |
| **Endpoint** | A specific URL that accepts requests (e.g. `/api/reviews`)                                       |
| **Request**  | What the browser sends (method, URL, headers, body)                                              |
| **Response** | What the server sends back (status code, headers, body)                                          |
| **JSON**     | JavaScript Object Notation: the standard format for data exchange                                |
| **REST**     | A convention for organising API endpoints around resources                                       |

## HTTP Methods: The Four Verbs

HTTP has four methods that cover nearly everything:

| Method               | Purpose     | Example                   |
| -------------------- | ----------- | ------------------------- |
| **GET**              | Read data   | Fetch the list of reviews |
| **POST**             | Create data | Submit a new review       |
| **PUT** or **PATCH** | Update data | Edit an existing review   |
| **DELETE**           | Remove data | Delete a review           |

These map directly to CRUD (Chapter 9, Data & State). GET reads. POST creates. PUT/PATCH updates. DELETE removes.

```javascript
// GET: read all reviews
const response = await fetch("/api/reviews");

// POST: create a new review
const response = await fetch("/api/reviews", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ rating: 5, comment: "Excellent" }),
});

// PUT: update review with id 42
const response = await fetch("/api/reviews/42", {
  method: "PUT",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ rating: 4, comment: "Updated comment" }),
});

// DELETE: remove review with id 42
const response = await fetch("/api/reviews/42", {
  method: "DELETE",
});
```

## Status Codes: What the Server Tells You

Every response includes a three-digit status code. The first digit tells you the category:

| Range   | Meaning                    | Examples                                                                               |
| ------- | -------------------------- | -------------------------------------------------------------------------------------- |
| **2xx** | Success                    | 200 OK, 201 Created, 204 No Content                                                    |
| **3xx** | Redirect                   | 301 Moved Permanently, 302 Found                                                       |
| **4xx** | Client error (your fault)  | 400 Bad Request, 401 Unauthorised, 403 Forbidden, 404 Not Found, 429 Too Many Requests |
| **5xx** | Server error (their fault) | 500 Internal Server Error, 502 Bad Gateway, 503 Service Unavailable                    |

The ones you'll see most often:

- **200**: Everything worked.
- **201**: Created successfully (after a POST).
- **400**: Your request has bad data (missing fields, wrong format).
- **401**: You're not authenticated (no token or expired token). Chapter 19 covers authentication and tokens.
- **403**: You're authenticated but not authorised (you don't have permission).
- **404**: The resource doesn't exist (wrong URL or deleted).
- **429**: You've made too many requests (rate limited).
- **500**: The server crashed (not your fault, usually).

## Headers: Metadata on Every Request

Headers carry information about the request that isn't the data itself. The three you'll use most:

```javascript
const response = await fetch("/api/reviews", {
  method: "POST",
  headers: {
    "Content-Type": "application/json", // "I'm sending JSON"
    Authorization: "Bearer eyJhbGciOi...", // "Here's my auth token"
    Accept: "application/json", // "Send me JSON back"
  },
  body: JSON.stringify({ rating: 5 }),
});
```

**Content-Type** tells the server what format the body is in. Almost always `application/json`.

**Authorization** carries your authentication token. The `Bearer` prefix is convention for token-based auth.

**Accept** tells the server what format you want the response in.

## JSON: The Universal Data Format

JSON (JavaScript Object Notation) is how APIs exchange data. It looks like a JavaScript object, with restrictions: keys must be in double quotes, no trailing commas, no comments.

```json
{
  "reviews": [
    {
      "id": "r1",
      "rating": 5,
      "comment": "Outstanding service",
      "author": {
        "name": "Sofia",
        "email": "sofia@example.com"
      }
    },
    {
      "id": "r2",
      "rating": 4,
      "comment": "Very good",
      "author": {
        "name": "Miguel",
        "email": "miguel@example.com"
      }
    }
  ],
  "totalCount": 2,
  "averageRating": 4.5
}
```

Converting between JSON strings and JavaScript objects:

```javascript
// Object → JSON string (for sending)
const body = JSON.stringify({ rating: 5, comment: "Brilliant" });

// JSON string → Object (for using)
const data = JSON.parse(responseText);
// or, more commonly:
const data = await response.json();
```

Common JSON parsing error: trying to parse something that isn't JSON. If the server returns HTML (an error page) instead of JSON, `response.json()` throws an error. Always check the status code before parsing.

## Building an API: Express.js

Express.js is the most popular Node.js framework for building APIs. A minimal API:

```javascript
import express from "express";

const app = express();
app.use(express.json()); // Parse JSON bodies

const reviews = [];

// GET /api/reviews
app.get("/api/reviews", (req, res) => {
  res.json(reviews);
});

// POST /api/reviews
app.post("/api/reviews", (req, res) => {
  const { rating, comment } = req.body;

  if (!rating || rating < 1 || rating > 5) {
    return res.status(400).json({ error: "Rating must be 1 to 5" });
  }

  const review = {
    id: crypto.randomUUID(),
    rating,
    comment,
    createdAt: new Date().toISOString(),
  };

  reviews.push(review);
  res.status(201).json(review);
});

// DELETE /api/reviews/:id
app.delete("/api/reviews/:id", (req, res) => {
  const index = reviews.findIndex((r) => r.id === req.params.id);
  if (index === -1) {
    return res.status(404).json({ error: "Review not found" });
  }
  reviews.splice(index, 1);
  res.status(204).send();
});

app.listen(3000, () => {
  console.log("API running on http://localhost:3000");
});
```

Four files could run this: `package.json`, the code above, and a client that calls it. That's a working API.

> **Hands-On:** Open your browser's Console on any page. Type `fetch("https://jsonplaceholder.typicode.com/posts/1").then(r => r.json()).then(console.log)` and press Enter. You just made a GET request to a public API and received JSON back. Every API call your app will ever make follows this same pattern.

## External APIs: They All Work the Same Way

Google Places, OpenAI, Stripe, Supabase, any third-party API. The pattern is identical: HTTP request with headers and body. HTTP response with status code and JSON.

```javascript
// OpenAI: send a prompt, get a response
const response = await fetch("https://api.openai.com/v1/chat/completions", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    Authorization: `Bearer ${process.env.OPENAI_API_KEY}`,
  },
  body: JSON.stringify({
    model: "gpt-4o-mini",
    messages: [{ role: "user", content: "Summarise this review: ..." }],
  }),
});

const data = await response.json();
const summary = data.choices[0].message.content;
```

The URL changes. The authentication method changes. The request body changes. But the structure is always the same: method, URL, headers, body, status code, response.

### JobPilot's Core API Call

This is the heart of what you're building. The extension extracts form field names from a job application page ("first_name", "email", "resume_text") and sends them, along with the user's saved profile, to OpenAI. The response is a JSON object mapping each field to the right profile value.

This call happens in the service worker. Never in the content script, because the content script runs inside someone else's website and API keys must stay hidden (Chapter 18).

Here's the prompt you'd give AI to generate this:

```javascript
// Service worker receives form fields from content script
const response = await fetch("https://api.openai.com/v1/chat/completions", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    Authorization: `Bearer ${apiKey}`, // stored securely, never shipped to the page
  },
  body: JSON.stringify({
    model: "gpt-4o-mini",
    messages: [
      {
        role: "system",
        content:
          "Match user profile data to form field names. Return JSON only.",
      },
      {
        role: "user",
        content: JSON.stringify({ fields: formFields, profile: userProfile }),
      },
    ],
  }),
});

const data = await response.json();
// { "first_name": "Sofia", "email": "sofia@email.com", "phone": "+351 912 345 678" }
```

Same pattern as every other API call: method, URL, headers, body, status code, response. The payload is different. The structure is identical.

## Rate Limits

Every API has limits on how many requests you can make per minute, hour, or day. Exceed them and you get a 429 status code. Your app stops working until the limit resets.

Some APIs return rate limit information in headers:

```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 23
X-RateLimit-Reset: 1705344000
```

Best practices:

- Read the API's rate limit documentation before building
- Implement retry logic with exponential backoff (wait 1 second, then 2, then 4)
- Cache responses when the data doesn't change often
- Use webhooks instead of polling when available

## Common Beginner Mistake

**Not checking the status code before using the response.** If the server returns a 401, the body contains an error message, not your data. Parsing it as if it were data causes confusing bugs downstream.

```javascript
// Wrong: assumes success
const data = await response.json();
displayReviews(data.reviews); // crashes if response was an error

// Correct: check status first
const response = await fetch("/api/reviews");
if (!response.ok) {
  console.error(`Error: ${response.status} ${response.statusText}`);
  return;
}
const data = await response.json();
displayReviews(data.reviews);
```

## Checklist

- [ ] I know the four HTTP methods (GET, POST, PUT/PATCH, DELETE)
- [ ] I can interpret status codes (2xx success, 4xx client error, 5xx server error)
- [ ] I can send and receive JSON data with fetch()
- [ ] I check the response status before parsing the body
- [ ] I understand rate limits and plan for them when using external APIs

## Summary

HTTP is the protocol between browsers and servers. Four methods cover most operations: GET reads, POST creates, PUT updates, DELETE removes. Status codes communicate success (2xx), client errors (4xx), and server errors (5xx). JSON is the standard data format. Headers carry metadata like authentication tokens and content types. External APIs follow the same pattern: request with method, URL, headers, and body; response with status code and JSON. Rate limits exist on every API. Always check the status code before processing a response.

## Challenge

1. Open DevTools on any website. Go to the Network tab. Find a GET request that returns JSON. Read the status code, headers, and response body. Find a POST request (try submitting a form). Compare the two.

2. Build a minimal Express.js API with three endpoints: GET /api/items (list all), POST /api/items (create one), DELETE /api/items/:id (remove one). Test it with fetch() from the browser or with curl from the terminal.
