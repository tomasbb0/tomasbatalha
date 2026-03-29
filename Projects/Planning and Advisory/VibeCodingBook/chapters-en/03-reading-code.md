# Chapter 3 Reading Code Without Writing It

You'll read ten times more code than you write. The AI generates it. Your job is to understand what it did, spot what's wrong, and explain what needs changing. This chapter teaches you how to read code the way a chef reads a recipe: not to memorise every step, but to understand the structure, the ingredients, and where things might go wrong.

## Key Terms

| Term                | What it means                                                                              |
| ------------------- | ------------------------------------------------------------------------------------------ |
| **Language**        | The grammar rules code follows (JavaScript, Python, HTML, CSS, SQL)                        |
| **Library**         | A collection of pre-written code you install and use (lodash, date-fns)                    |
| **Framework**       | A structure that organises your code and makes decisions for you (React, Next.js, Express) |
| **Runtime**         | The engine that executes the code (Node.js for JavaScript, CPython for Python)             |
| **Package manager** | An app store for code libraries (npm for JavaScript, pip for Python)                       |
| **Function**        | A reusable block: takes input, processes it, returns output                                |
| **Import**          | Bringing code from another file or library into the current file                           |

## Five Languages You'll See

### HTML (Structure)

The skeleton. Defines what's on the page: headings, paragraphs, images, forms, buttons. Not a programming language; it's a markup language.

```html
<h1>Lisboa Walks</h1>
<p>Guided walking tours in Lisbon since 2020.</p>
<button>Book Now</button>
```

Tags open (`<h1>`) and close (`</h1>`). Content goes between them. Tags nest inside each other.

### CSS (Style)

The paint. Defines how things look: colours, sizes, spacing, layout. Also not a programming language.

```css
h1 {
  color: #2d5a27;
  font-size: 2rem;
}
button {
  background: #9caf88;
  border: none;
  padding: 12px 24px;
}
```

Selectors (like `h1`, `button`, `.card`) target elements. Properties (like `color`, `font-size`) set the style.

### JavaScript (Behaviour)

The brain. Makes things happen: respond to clicks, fetch data, change the page, communicate with servers.

```javascript
const button = document.querySelector("button");
button.addEventListener("click", () => {
  alert("Booking confirmed!");
});
```

JavaScript is the programming language of the browser. When you see `function`, `const`, `let`, `if`, `for`, `=>`, you're reading JavaScript.

### TypeScript (JavaScript with Safety Checks)

JavaScript, but with type declarations. Catches errors before the code runs, not after.

```typescript
function calculateTotal(price: number, discount: number): number {
  return price * (1 - discount / 100);
}
```

The `: number` parts are type annotations. They tell the compiler (and the human reading this) what kind of values are expected. Chapter 13 covers TypeScript and React in depth.

### Python (Automation, AI, Data)

Clean, readable syntax. Used for scripts, data processing, AI backends.

```python
import os
files = os.listdir("./downloads")
for f in files:
    print(f)
```

Indentation defines structure (no curly braces). `import` brings in libraries.

### SQL (Databases)

The language for talking to databases. Reads like English.

```sql
SELECT name, email FROM users WHERE city = 'Lisbon' ORDER BY name;
```

`SELECT` = what columns. `FROM` = which table. `WHERE` = filter. `ORDER BY` = sort. Chapter 9 covers data storage in detail.

## Language vs Library vs Framework vs Runtime

This distinction trips up every beginner. Here's the map:

| Concept             | Analogy                | Example                                             |
| ------------------- | ---------------------- | --------------------------------------------------- |
| **Language**        | Grammar                | JavaScript, Python                                  |
| **Library**         | A specific tool        | lodash (utilities), date-fns (dates)                |
| **Framework**       | A workshop with rules  | React (UI), Express (servers), Next.js (full stack) |
| **Runtime**         | The engine             | Node.js (JS outside browser), CPython (Python)      |
| **Package manager** | An app store for tools | npm (JS), pip (Python)                              |

React is not a language. It's a framework written in JavaScript (Chapter 13 teaches React). Node.js is not a language. It's a runtime that lets JavaScript run outside the browser. npm is not a language. It's the package manager that installs libraries and frameworks (Chapter 15 explains how dependencies work).

## How Code Is Organised

### Functions = Recipes

A function takes inputs, does something, and returns an output. Like a recipe: ingredients in, dish out.

```javascript
function formatPrice(amount, currency) {
  return `${amount.toFixed(2)} ${currency}`;
}
formatPrice(19.5, "€"); // "19.50 €"
```

### Files = Recipe Cards

Each file contains related functions and data. `auth.js` handles authentication. `api.js` handles server communication. `utils.js` has helper functions.

### Folders = Recipe Books

Folders group related files. `src/components/` for UI pieces. `src/pages/` for page-level code. `src/utils/` for shared helpers.

### Imports = "Bring Me That Card"

When one file needs something from another, it imports it.

```javascript
import { formatPrice } from "./utils.js";
```

This brings the `formatPrice` function from `utils.js` into the current file.

## How to Skim a Codebase

When you open a project, don't read every line. Instead:

1. **Start with `package.json`** (or `requirements.txt` for Python). This tells you what dependencies the project uses.
2. **Look at the folder structure.** `src/`, `pages/`, `components/`, `api/` tell you how the code is organised.
3. **Read the entry point.** Usually `index.js`, `main.js`, `app.js`, or `pages/index.tsx`.
4. **Follow the exports.** Functions that are exported are the public API; they're what other files use.
5. **Read the function names.** Good code has descriptive names: `fetchRestaurants`, `validateEmail`, `handleSubmit`.

> **Try It:** Go to github.com/vercel/next.js (or any popular open-source repo). Don't read the code. Just look at the folder structure and `package.json`. Can you guess what the project does from the folder names and dependency list alone? You'll be surprised how much structure reveals.

## Reading JobPilot's First Code

Remember the five files that came back from your prompt in Chapter 2? Let's look at 12 lines from the content script. This is the code that runs on a Greenhouse job application page. You didn't write it. AI did. Let's read it together.

```javascript
const formFields = document.querySelectorAll("input, textarea, select");

const fields = Array.from(formFields).map((field) => ({
  name: field.name || field.id,
  type: field.type,
  label: document.querySelector(`label[for="${field.id}"]`)?.textContent,
}));

chrome.runtime.sendMessage({
  type: "ANALYSE_PAGE",
  data: { fields, url: window.location.href },
});
```

Line by line, in plain English:

1. **Line 1:** Find every input box, text area, and dropdown on the page. Store them in `formFields`.
2. **Lines 3 to 6:** For each field, extract three things: its name, its type (text, email, file upload), and the label next to it (like "First Name" or "Email"). Put it all in a tidy list called `fields`.
3. **Lines 8 to 11:** Send that list to the extension's background service worker, along with the page URL. The message type `'ANALYSE_PAGE'` tells the service worker what to do with it.

You didn't write this. AI did. But now you can read it. That's the skill this chapter is about. Every chapter from here on, you'll read a bit more of JobPilot's code and understand a bit more of how it works.

## Common Beginner Mistake

**Trying to understand every line before moving forward.** You don't need to understand how a sorting algorithm works to know that `array.sort()` sorts an array. You don't need to understand React's reconciliation engine to know that `useState` stores a value that updates the UI when changed.

Read for structure and intent first. The details come with practice.

## Checklist

- [ ] I can identify the five languages (HTML, CSS, JavaScript, TypeScript, Python, SQL)
- [ ] I understand the difference between language, library, framework, and runtime
- [ ] I can read a function and explain what it does in plain English
- [ ] I know what `import` and `export` mean
- [ ] I can skim a project folder and understand its structure

## Summary

You'll read far more code than you write. The five languages you'll encounter most are HTML (structure), CSS (style), JavaScript (behaviour), TypeScript (JavaScript with types), and SQL (databases). The confusion between language, library, framework, and runtime catches every beginner: learn the distinction once and it saves you months of confusion. Skim codebases by starting with package.json, the folder structure, and function names.

## Challenge

1. Open the code for the Lisboa Walks page from Chapter 2 (Your First AI Conversation). Without AI help, identify: every HTML tag, every CSS property, and every JavaScript function. Write a one-sentence explanation for each.

2. Ask the AI to generate a manifest.json for a Chrome extension (any extension). Read each field and explain what it does. Then check your explanations against the AI's answers.
