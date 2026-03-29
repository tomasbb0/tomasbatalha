# Chapter 8 How the Browser Actually Works

The browser is the most important piece of software in web development, and most people treat it as a black box. This chapter opens it up. After reading it, DevTools will stop looking like an alien control panel and start looking like an instrument you know how to use.

## Key Terms

| Term               | What it means                                                                         |
| ------------------ | ------------------------------------------------------------------------------------- |
| **DOM**            | Document Object Model: the browser's representation of your HTML as a tree of objects |
| **CSS Selector**   | A pattern that targets specific HTML elements for styling                             |
| **Specificity**    | The rules CSS uses to decide which style wins when multiple rules apply               |
| **Event**          | Something that happens in the browser (click, scroll, keypress, page load)            |
| **Event listener** | Code that runs when a specific event occurs                                           |
| **fetch()**        | The JavaScript function that makes HTTP requests from the browser                     |
| **DevTools**       | The browser's built-in development toolkit (F12 or Cmd+Option+I)                      |

## The DOM: Your HTML as a Living Tree

You write HTML. The browser reads it and builds a tree structure called the DOM. Every tag becomes a node. Every node can have children. JavaScript can add, remove, and modify any node at any time.

```html
<body>
  <header>
    <h1>Shop</h1>
    <nav>
      <a href="/about">About</a>
      <a href="/contact">Contact</a>
    </nav>
  </header>
  <main>
    <p>Welcome to the shop.</p>
  </main>
</body>
```

The DOM tree:

```
body
├── header
│   ├── h1 ("Shop")
│   └── nav
│       ├── a ("About")
│       └── a ("Contact")
└── main
    └── p ("Welcome to the shop.")
```

JavaScript can change any part of this tree:

```javascript
// Change text
document.querySelector("h1").textContent = "New Shop";

// Add an element
const banner = document.createElement("div");
banner.textContent = "Sale: 20% off everything";
document.querySelector("main").prepend(banner);

// Remove an element
document.querySelector("nav").remove();
```

The page updates instantly. No reload. This is how single-page applications work: JavaScript manipulates the DOM instead of loading new pages.

## CSS: How Styling Actually Works

CSS applies styles to HTML elements through selectors. When multiple rules target the same element, specificity decides which one wins.

```css
/* Tag selector: low specificity */
p {
  color: black;
}

/* Class selector: medium specificity */
.highlight {
  color: blue;
}

/* ID selector: high specificity */
#main-title {
  color: red;
}

/* Inline style: highest specificity (avoid this) */
/* <p style="color: green;"> */
```

If an element matches all four rules, the inline style wins. Then ID. Then class. Then tag. This hierarchy causes most CSS frustration: "I changed the colour but nothing happened" usually means a more specific rule is overriding yours.

**Responsive design** uses media queries:

```css
/* Default: mobile first */
.container {
  padding: 16px;
}

/* Screens wider than 768px */
@media (min-width: 768px) {
  .container {
    padding: 32px;
    max-width: 720px;
    margin: 0 auto;
  }
}
```

Modern CSS layouts use two systems:

- **Flexbox** for one-dimensional layouts (a row of cards, a navigation bar)
- **Grid** for two-dimensional layouts (a dashboard with rows and columns)

## JavaScript in the Browser: Events and Fetch

JavaScript in the browser does two things: responds to events and makes network requests.

### Events

Every interaction is an event. Click, scroll, type, hover, submit, resize, load. You attach code to events using event listeners:

```javascript
// When the button is clicked, show a message
document.querySelector("#submit-btn").addEventListener("click", () => {
  alert("Form submitted!");
});

// When the user types in the search box, filter the list
document.querySelector("#search").addEventListener("input", (event) => {
  const query = event.target.value.toLowerCase();
  filterResults(query);
});

// When the page finishes loading, fetch data from the API
window.addEventListener("DOMContentLoaded", () => {
  loadReviews();
});
```

### fetch(): Talking to the World

`fetch()` makes HTTP requests. It's how your frontend talks to backends and external APIs.

```javascript
// GET: retrieve data
const response = await fetch("https://api.example.com/reviews");
const reviews = await response.json();

// POST: send data
const response = await fetch("https://api.example.com/reviews", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ rating: 5, comment: "Brilliant" }),
});
```

Every API call you see in the Network tab is a `fetch()` (or its older equivalent, `XMLHttpRequest`).

## DevTools: The Four Tabs You Need

Open DevTools: press F12, or Cmd+Option+I on macOS, or right-click any element and select "Inspect".

### Elements Tab

Shows the DOM tree. Click any element to see its CSS. Edit styles in real time. Add or remove classes. Change text. Everything updates live in the browser (not in your files).

Use it to: debug layout issues, test style changes before writing them, and understand how someone else's website is built.

For JobPilot, the Elements tab is essential. Try it now: open a Greenhouse job application page, right-click a form field, and click Inspect. You'll see the HTML structure that JobPilot's content script needs to target. Greenhouse wraps fields in `<div>` elements with predictable class names. Lever uses a completely different layout. Workday buries fields inside nested iframes that you have to expand one by one. Without the Elements tab, there's no way to know what structure the content script will encounter on each site. This is how you'll figure out what to tell AI when you ask it to write the code that reads those forms.

If you're building a web app instead of an extension, you'd use the Elements tab the same way: inspect the page, understand the structure, then describe it to AI.

### Console Tab

JavaScript's terminal. Shows errors in red, warnings in yellow, and your `console.log()` messages. Type JavaScript directly to test things.

```javascript
// Type this in the Console to count all images on a page
document.querySelectorAll("img").length;
```

Use it to: see errors, test JavaScript snippets, and inspect variables.

> **Try It:** Open any webpage. Press F12. Go to the Console tab. Type `document.title` and press Enter. Now type `document.title = "I own this page"` and watch the browser tab change. Everything in the DOM is editable from JavaScript.

### Network Tab

Shows every HTTP request the page makes. Each row shows: the URL, the method (GET/POST), the status code (200/404/500), the size, and the time.

Click any request to see:

- **Headers:** what was sent (method, auth token, content type)
- **Preview/Response:** what came back (the actual data)
- **Timing:** how long each phase took

Use it to: debug failed API calls, see what data your app sends and receives, and identify slow requests.

### Application Tab

Shows browser storage: cookies, localStorage, sessionStorage, IndexedDB. Also shows service workers and cache.

Use it to: inspect what data the browser stores for a site, clear storage when debugging, and understand how authentication tokens are stored (Chapter 19 explains authentication and tokens in detail).

## Storage: Where the Browser Keeps Data

|                | localStorage             | sessionStorage       | Cookies                           |
| -------------- | ------------------------ | -------------------- | --------------------------------- |
| Lifetime       | Until you delete it      | Until the tab closes | Until expiry date                 |
| Size           | About 5 MB               | About 5 MB           | About 4 KB                        |
| Sent to server | No                       | No                   | Automatically, with every request |
| Access from JS | Yes                      | Yes                  | Yes (unless HttpOnly)             |
| Best for       | User preferences, drafts | Temporary form data  | Auth tokens, sessions             |

```javascript
// localStorage: persists across sessions
localStorage.setItem("theme", "dark");
const theme = localStorage.getItem("theme"); // "dark"

// sessionStorage: dies when you close the tab
sessionStorage.setItem("searchQuery", "shoes");

// Cookies: set by the server, sent automatically
// document.cookie = "session=abc123; HttpOnly; Secure";
// (HttpOnly cookies can't be set from JavaScript -- server only)
```

## Common Beginner Mistake

**Ignoring the Network tab.** When something doesn't work, beginners stare at the code. Professionals open the Network tab. If the request returned a 401, authentication failed. If it returned a 500, the server crashed. If there's no request at all, the event listener isn't firing. The Network tab tells you what actually happened, not what you assumed happened.

## Checklist

- [ ] I can open DevTools and navigate to Elements, Console, Network, and Application tabs
- [ ] I understand the DOM as a tree that JavaScript can modify
- [ ] I know what CSS specificity is and why my styles sometimes don't apply
- [ ] I can use the Network tab to debug failed API calls
- [ ] I know the difference between localStorage, sessionStorage, and cookies

## Summary

The browser parses HTML into a DOM tree that JavaScript can manipulate in real time. CSS styles elements through selectors with a specificity hierarchy. JavaScript responds to user events and makes API calls with `fetch()`. DevTools has four essential tabs: Elements (DOM and CSS), Console (errors and testing), Network (HTTP requests and responses), and Application (browser storage). The Network tab is the single most useful debugging tool for web development.

## Challenge

1. Open any website you use regularly. Open DevTools. In the Network tab, find one GET request and one POST request. Note the URL, status code, and response data for each.

2. In the Console tab, type `document.querySelectorAll("a").length` to count the links on the page. Then type `document.querySelectorAll("img").length` to count the images. Try changing the text of the page's main heading using `document.querySelector("h1").textContent = "I changed this"`.
