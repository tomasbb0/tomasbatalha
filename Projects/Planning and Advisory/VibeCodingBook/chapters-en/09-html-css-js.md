# Chapter 4 HTML, CSS, JavaScript: The Three Layers

Every website you have ever visited is made of three things: HTML for structure, CSS for appearance, and JavaScript for behaviour. This chapter builds a complete page from scratch so you can see how the three layers work together.

## Key Terms

| Term              | What it means                                                                                  |
| ----------------- | ---------------------------------------------------------------------------------------------- |
| **HTML**          | HyperText Markup Language: defines the structure and content of a page                         |
| **CSS**           | Cascading Style Sheets: controls colours, fonts, spacing, and layout                           |
| **JavaScript**    | The programming language that adds interactivity and behaviour                                 |
| **Tag**           | An HTML instruction wrapped in angle brackets: `<p>`, `<div>`, `<img>`                         |
| **Attribute**     | Extra information on a tag: `<img src="photo.jpg" alt="A photo">`                              |
| **Semantic HTML** | Using tags that describe meaning (`<nav>`, `<article>`, `<footer>`) instead of generic `<div>` |
| **Build step**    | The process that transforms your source code into optimised files for the browser              |

## HTML: Structure

HTML is a document format. Think of it as a Word document's outline. Headings, paragraphs, lists, images, links, tables, forms.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Café Pessoa</title>
    <link rel="stylesheet" href="styles.css" />
  </head>
  <body>
    <header>
      <nav>
        <a href="#menu">Menu</a>
        <a href="#about">About</a>
        <a href="#contact">Contact</a>
      </nav>
      <h1>Café Pessoa</h1>
      <p>Coffee and poetry since 1927.</p>
    </header>

    <main>
      <section id="menu">
        <h2>Our Menu</h2>
        <ul>
          <li>Espresso &mdash; £2.80</li>
          <li>Galão &mdash; £3.20</li>
          <li>Pastel de Nata &mdash; £2.50</li>
        </ul>
      </section>

      <section id="about">
        <h2>About Us</h2>
        <p>
          Located in Chiado, Café Pessoa has been serving the finest Portuguese
          coffee for nearly a century.
        </p>
      </section>

      <section id="contact">
        <h2>Visit Us</h2>
        <address>
          Rua Garrett 120, 1200-205 Lisboa<br />
          Open daily, 8:00 to 22:00
        </address>
      </section>
    </main>

    <footer>
      <p>&copy; 2026 Café Pessoa</p>
    </footer>

    <script src="script.js"></script>
  </body>
</html>
```

Every tag has a purpose. `<header>` for the top section. `<nav>` for navigation links. `<main>` for primary content. `<section>` for thematic groups. `<footer>` for the bottom. This is semantic HTML: tags that describe meaning, not just appearance.

## CSS: Appearance

CSS controls how the HTML looks. Colours, fonts, spacing, layout, animations.

```css
/* Global reset */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: "Georgia", serif;
  line-height: 1.6;
  color: #333;
  background-color: #faf8f5;
}

/* Navigation */
nav {
  display: flex;
  gap: 24px;
  padding: 16px 32px;
  background-color: #2c1810;
}

nav a {
  color: #faf8f5;
  text-decoration: none;
  font-size: 14px;
  text-transform: uppercase;
  letter-spacing: 1px;
}

nav a:hover {
  text-decoration: underline;
}

/* Header */
header {
  text-align: center;
  padding: 80px 32px 48px;
  background-color: #2c1810;
  color: #faf8f5;
}

header h1 {
  font-size: 48px;
  margin-bottom: 8px;
}

/* Sections */
section {
  max-width: 720px;
  margin: 0 auto;
  padding: 48px 32px;
}

section h2 {
  font-size: 28px;
  margin-bottom: 16px;
  border-bottom: 2px solid #2c1810;
  padding-bottom: 8px;
}

/* Menu list */
ul {
  list-style: none;
}

li {
  padding: 12px 0;
  border-bottom: 1px solid #e0dcd8;
  font-size: 18px;
}

/* Footer */
footer {
  text-align: center;
  padding: 32px;
  background-color: #2c1810;
  color: #faf8f5;
  font-size: 14px;
}

/* Responsive */
@media (max-width: 600px) {
  header h1 {
    font-size: 32px;
  }
  section {
    padding: 32px 16px;
  }
  nav {
    justify-content: centre;
  }
}
```

Three files. Same page. HTML defines what is on the page. CSS defines how it looks. Changing one doesn't break the other.

## JavaScript: Behaviour

JavaScript makes the page interactive. It responds to user actions, manipulates the DOM, fetches data, and modifies what the user sees.

```javascript
// Smooth scroll to sections when nav links are clicked
document.querySelectorAll("nav a").forEach((link) => {
  link.addEventListener("click", (event) => {
    event.preventDefault();
    const targetId = link.getAttribute("href").substring(1);
    const target = document.getElementById(targetId);
    target.scrollIntoView({ behavior: "smooth" });
  });
});

// Highlight the current section as the user scrolls
const sections = document.querySelectorAll("section");
const navLinks = document.querySelectorAll("nav a");

window.addEventListener("scroll", () => {
  let current = "";
  sections.forEach((section) => {
    const sectionTop = section.offsetTop;
    if (window.scrollY >= sectionTop - 100) {
      current = section.getAttribute("id");
    }
  });
  navLinks.forEach((link) => {
    link.style.fontWeight = "normal";
    if (link.getAttribute("href") === `#${current}`) {
      link.style.fontWeight = "bold";
    }
  });
});

// Simple form validation (if you add a contact form later)
function validateEmail(email) {
  return email.includes("@") && email.includes(".");
}
```

Three files, one page. Café Pessoa now has smooth scrolling navigation and a visual indicator of which section the reader is viewing.

> **Your Turn:** Create three files: `index.html`, `style.css`, `script.js`. In the HTML, link the CSS and JS files. Write a heading and a paragraph. In the CSS, change the background colour. In the JS, add `console.log("It works")`. Open `index.html` in Chrome and check the Console. You just built the three-layer stack.

## Why They're Separate

Keeping HTML, CSS, and JavaScript in separate files (or at least separate concerns) has practical benefits:

1. **Change the design without touching the content.** Swap the CSS file and the same HTML looks completely different. This is how themes work.
2. **Reuse styles across pages.** One stylesheet for an entire website. Change the heading font once, it updates everywhere.
3. **Different people, different files.** A designer works on CSS. A developer works on JavaScript. Nobody blocks anyone.
4. **Caching.** If only the JavaScript changes, the browser reuses the cached HTML and CSS. Faster loads.

## Three Layers in the Wild: JobPilot's Popup

Let's look at a real piece of what you're building. JobPilot's popup (the small window that opens when you click the extension icon) is a miniature example of all three layers working together. About 30 lines of HTML, 20 lines of CSS, and 10 lines of JavaScript.

The HTML defines a status indicator, a profile summary, and an "Auto-fill" button. The CSS makes it look like a real app, not a default browser form (320px wide, clean fonts, a green "Active" badge). The JavaScript listens for the button click and sends a message to the service worker that kicks off the entire auto-fill process.

Three files, one tiny UI. Every concept from this chapter (tags, selectors, event listeners) appears in those 60 lines. If you want to see the actual code, Chapter 17 (Browser Extensions) walks through it.

## The Build Step

Modern web projects don't ship raw HTML, CSS, and JavaScript. They ship processed versions:

**TypeScript → JavaScript** (transpilation). TypeScript adds types and safety features (Chapter 13 covers TypeScript in depth). Browsers don't understand TypeScript, so it gets converted to plain JavaScript before deployment.

**Many files → one bundle** (bundling). A project might have 200 JavaScript files. The build tool combines them into one or two files for faster loading.

**Readable code → small code** (minification). Variable names get shortened (`handleFormSubmission` becomes `a`). Whitespace gets removed. The file shrinks by 50% or more.

**Modern CSS → compatible CSS** (autoprefixing). Features that work in Chrome but not Safari get vendor prefixes added automatically.

**Vite** is the most popular build tool in 2026. It handles all four transformations. When you run `npm run build` (Chapter 15 explains npm and dependencies), Vite processes your source code and produces optimised files in a `dist/` folder. That folder is what gets deployed.

## Common Beginner Mistake

**Editing the built files instead of the source files.** If your project has a `dist/` or `build/` folder, those files are generated. Editing them is a waste: the next build overwrites your changes. Always edit the source files (usually in `src/`) and let the build tool generate the output.

## Checklist

- [ ] I can write basic HTML with semantic tags (header, nav, main, section, footer)
- [ ] I can style elements with CSS (selectors, properties, media queries)
- [ ] I can add interactivity with JavaScript (event listeners, DOM manipulation)
- [ ] I understand why HTML, CSS, and JS are kept separate
- [ ] I know that modern projects use a build step (TypeScript → JS, bundling, minification)
- [ ] I never edit files in the `dist/` or `build/` folder

## Summary

Every website is HTML (structure), CSS (appearance), and JavaScript (behaviour). Separating these three concerns lets you change the design without touching the content, reuse styles across pages, and enable caching. Modern projects add a build step: TypeScript transpilation, bundling, minification, and CSS autoprefixing. The build tool (typically Vite) produces optimised files in a `dist/` folder. Edit source files, not built files.

## Challenge

1. Create a Café Pessoa page using the HTML, CSS, and JavaScript from this chapter. Open it in your browser. Modify the colours in CSS. Add a new menu item in HTML. Add a click event in JavaScript that shows a message when the user clicks a menu item.

2. If you have Node.js installed, initialise a Vite project: `npm create vite@latest my-cafe`. Move your HTML, CSS, and JS into the Vite structure. Run `npm run build` and compare the source files with the output in `dist/`.
