# Chapter 9 Data & State: Where Things Live and Why

You've built apps that display information and respond to clicks. Now comes a question that trips up every beginner: where does the data go? A user fills in a form. They close the browser. They come back tomorrow. Is the data still there? The answer depends entirely on where you stored it.

## Key Terms

| Term                         | What it means                                                                               |
| ---------------------------- | ------------------------------------------------------------------------------------------- |
| **State**                    | The data your app knows right now (what's on screen, who's logged in, what's in the basket) |
| **Local state**              | Data stored in a JavaScript variable (dies when the page refreshes)                         |
| **Persistent state**         | Data saved somewhere that survives page refreshes and browser closures                      |
| **CRUD**                     | Create, Read, Update, Delete: the four operations you perform on any data                   |
| **Firestore**                | Firebase's NoSQL document database                                                          |
| **Supabase**                 | An open-source alternative to Firebase, built on PostgreSQL                                 |
| **Row-Level Security (RLS)** | Database rules that control which users can access which rows                               |

## The Disappearing Data Problem

Every beginner encounters this. You set a variable. You use it. Everything works. You refresh the page. The variable is gone. The data vanished. This isn't a bug. It's how browsers work.

```javascript
// This variable exists only while the page is open
let reviewCount = 0;

document.querySelector("#submit").addEventListener("click", () => {
  reviewCount++;
  console.log(`Reviews: ${reviewCount}`);
});

// Refresh the page. reviewCount is 0 again.
```

JavaScript variables live in memory. Memory gets cleared when the page reloads. To make data survive, you need to store it somewhere persistent.

## Five Places Data Can Live

### 1. JavaScript Variable (Volatile)

The code above. Dies on refresh. Use it for temporary things: the current tab, whether a dropdown is open, which item the user is hovering over.

### 2. localStorage (Browser, Persistent)

Survives refreshes and browser restarts. Limited to about 5 MB. Only accessible from the same domain.

```javascript
// Save
localStorage.setItem("theme", "dark");
localStorage.setItem(
  "draft",
  JSON.stringify({ title: "My post", body: "..." }),
);

// Load
const theme = localStorage.getItem("theme"); // "dark"
const draft = JSON.parse(localStorage.getItem("draft"));

// Delete
localStorage.removeItem("draft");
```

Good for: user preferences, unfinished form drafts, cached data that doesn't need to be secure.

Bad for: anything sensitive (anyone with DevTools can read it), anything that needs to sync across devices.

> **Try It:** Open any webpage. Open DevTools → Console. Type `localStorage.setItem("test", "hello")` and press Enter. Close the tab. Open it again. Type `localStorage.getItem("test")`. It's still there. That's persistence. Now type `localStorage.removeItem("test")` and it's gone.

### Chrome Extensions: Where JobPilot's Data Lives (chrome.storage)

Here's where JobPilot stores the user's profile. Chrome extensions have a storage option you won't find in normal web apps. `chrome.storage.local` works like localStorage but with two superpowers: it's accessible from the extension's popup, service worker, and content scripts (they don't share the same localStorage), and `chrome.storage.sync` mirrors data across every Chrome instance the user is signed into.

JobPilot stores the user's entire professional profile (name, email, phone, work history, education, skills) in `chrome.storage.local`. When a user logs into Chrome on a new laptop, their profile is already there. No database call. No login screen. It's also safer than regular localStorage, because page-level JavaScript on the ATS website can't read it. Only the extension's own code has access.

If you're building a web app instead, you'd use localStorage for similar data (user preferences, drafts) or a database like Supabase for anything that needs a server.

```javascript
// Save a profile in a Chrome extension
await chrome.storage.local.set({
  profile: {
    name: "Sofia",
    email: "sofia@email.com",
    phone: "+351 912 345 678",
  },
});

// Load it from anywhere in the extension
const result = await chrome.storage.local.get("profile");
const profile = result.profile;
```

### 3. Cookies (Browser, Sent to Server)

Small (about 4 KB), but unlike localStorage, cookies are sent to the server with every HTTP request. This makes them useful for authentication (Chapter 19 explains authentication in detail).

```javascript
// Set a cookie (from JavaScript)
document.cookie = "language=en; max-age=31536000; path=/";

// Server-set HttpOnly cookie (can't be read from JavaScript)
// Set-Cookie: session=abc123; HttpOnly; Secure; SameSite=Strict
```

Good for: session tokens, authentication. Bad for: storing large amounts of data.

### 4. Database (Server, Permanent)

The real solution for user data. Databases live on servers. They can store gigabytes. They have access control. They survive anything short of the server being destroyed.

**Firebase Firestore** (NoSQL, document-based):

```javascript
import { collection, addDoc, getDocs } from "firebase/firestore";

// Create
await addDoc(collection(db, "reviews"), {
  rating: 5,
  comment: "Brilliant service",
  createdAt: new Date(),
});

// Read
const snapshot = await getDocs(collection(db, "reviews"));
snapshot.forEach((doc) => {
  console.log(doc.id, doc.data());
});
```

**Supabase** (PostgreSQL, relational):

```javascript
// Create
await supabase.from("reviews").insert({
  rating: 5,
  comment: "Brilliant service",
});

// Read
const { data } = await supabase
  .from("reviews")
  .select("*")
  .order("created_at", { ascending: false });
```

Both work from the browser. Both handle authentication. The key difference: Firestore is document-based (flexible structure), Supabase is relational (strict tables with columns).

### 5. URL Parameters (Shareable State)

The URL itself can hold state. `example.com/search?q=coffee&page=2` stores the search query and page number. This state is shareable: anyone who opens that URL sees the same results.

```javascript
// Read URL parameters
const params = new URLSearchParams(window.location.search);
const query = params.get("q"); // "coffee"
const page = params.get("page"); // "2"
```

Good for: search queries, filters, pagination, anything you want to be bookmarkable or shareable. Bad for: sensitive data (the URL is visible and stored in browser history).

## CRUD: The Four Operations

Every data interaction is one of four things:

| Operation  | SQL         | Firestore   | Supabase JS | HTTP Method |
| ---------- | ----------- | ----------- | ----------- | ----------- |
| **Create** | INSERT INTO | addDoc()    | .insert()   | POST        |
| **Read**   | SELECT      | getDocs()   | .select()   | GET         |
| **Update** | UPDATE      | updateDoc() | .update()   | PUT/PATCH   |
| **Delete** | DELETE      | deleteDoc() | .delete()   | DELETE      |

Every feature in every application is some combination of these four operations. A to-do list: create tasks, read the list, update completion status, delete finished tasks. A review app: create reviews, read the dashboard, update a review, delete spam.

## The Full Comparison

|                        | Variable      | localStorage | Cookie       | Database      | URL Params      |
| ---------------------- | ------------- | ------------ | ------------ | ------------- | --------------- |
| Survives refresh       | No            | Yes          | Yes          | Yes           | Yes             |
| Survives browser close | No            | Yes          | Until expiry | Yes           | If bookmarked   |
| Shared across devices  | No            | No           | No           | Yes           | If link shared  |
| Size limit             | RAM           | ~5 MB        | ~4 KB        | Unlimited     | ~2,000 chars    |
| Visible to user        | Console       | DevTools     | DevTools     | No (server)   | Address bar     |
| Good for               | Temp UI state | Preferences  | Auth tokens  | All user data | Shareable state |

## Common Beginner Mistake

**Using localStorage for user data that should be in a database.** A sleep tracker that stores entries in localStorage works perfectly on one device. The user gets a new phone. All their data is gone. localStorage is per-browser, per-device. If data matters to the user, it belongs in a database.

## Checklist

- [ ] I know why variables disappear on refresh (they're in memory, not storage)
- [ ] I can use localStorage for preferences and non-sensitive drafts
- [ ] I understand that cookies are sent with every request (good for auth, bad for large data)
- [ ] I can perform CRUD operations on Firebase or Supabase
- [ ] I choose the right storage for the right data (temporary → variable, preferences → localStorage, auth → cookies, user data → database)

## Summary

Data can live in five places: JavaScript variables (temporary), localStorage (persistent in the browser), cookies (sent to the server automatically), databases (permanent, server-side, cross-device), and URL parameters (shareable, bookmarkable). Variables die on refresh. localStorage survives but is device-specific. Cookies are small but travel with every request. Databases are the correct choice for any user data that matters. Every data operation is one of four types: Create, Read, Update, Delete.

## Challenge

1. Build a simple note-taking page. Store notes in localStorage. Close the browser. Reopen. Confirm the notes are still there. Now open the same page in a different browser (or private window). The notes are gone. This is why databases exist.

2. Create a free Supabase project. Create a "notes" table. Modify your page to save and load notes from Supabase instead of localStorage. Confirm: the same notes appear in any browser.
