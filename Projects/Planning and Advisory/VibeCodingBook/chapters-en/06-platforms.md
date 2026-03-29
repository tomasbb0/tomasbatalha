# Chapter 7 Platform Building: Lovable, Bolt, v0 & Friends

Your vibe coding journey started with prompting an AI assistant and reading its output. This chapter introduces something different: platforms that generate entire applications from a description. You write a paragraph, and the platform builds a working app with a user interface, a database (Chapter 9 explains data storage), and authentication (Chapter 19 covers how login systems work). This is Mode 2.

## Key Terms

| Term                    | What it means                                                                   |
| ----------------------- | ------------------------------------------------------------------------------- |
| **Mode 2**              | Using generative platforms that build full apps from prompts                    |
| **Generative platform** | A service that creates working applications from natural language descriptions  |
| **Auth**                | Authentication and authorisation: knowing who the user is and what they can do  |
| **CRUD**                | Create, Read, Update, Delete: the four basic operations on any data             |
| **Component library**   | Pre-built interface pieces (buttons, forms, modals) you can use directly        |
| **Eject**               | Export the source code from a platform to continue development in a code editor |

## How Generative Platforms Work

Traditional development: you write code → the computer runs it. Generative platforms: you describe what you want → the platform writes the code → validates it → deploys it. You never touch a code file unless you choose to.

These platforms combine:

1. An AI model that generates code from descriptions
2. A preview environment that renders the result in real time
3. A built-in database and authentication system
4. One-click deployment

The result: a working application in minutes, not weeks.

## Lovable: Build a Full App From Prompts

Lovable is the platform that most closely resembles having a junior developer on call. You describe what you want. It builds. You give feedback. It iterates.

**Walkthrough: Building a Review Collection App**

This is how ReviewOrb was built. A business collects customer reviews through a kiosk tablet.

Prompt sequence:

1. "Build a review collection app. Customers tap a rating (1 to 5 stars), write an optional comment, and submit. The business owner sees a dashboard with all reviews, average rating, and a chart over time."

2. Lovable generates: a React frontend, a Supabase database, two pages (submit and dashboard), and basic styling.

3. "Change the submit screen to use a glowing orb that changes colour based on the rating. Green for 5, orange for 3, red for 1."

4. Lovable updates the UI component. The orb animates between colours.

5. "Add Google OAuth login for the dashboard. Only authenticated users should see the reviews."

6. Lovable adds Supabase Auth with Google provider, route protection, and a login page.

Six prompts. Working app. Deploy to a Lovable subdomain. Done.

## v0 by Vercel: Generate UI Components

v0 operates differently. Rather than building entire apps, it generates individual UI components. Describe a pricing table. Get a React component with Tailwind CSS styling. Copy it into your project.

Best for:

- Landing page sections (hero, pricing, testimonials, FAQ)
- Dashboard layouts
- Form designs
- Navigation bars and footers

v0 generates code you can paste into any React project. It doesn't manage databases or authentication. Think of it as a component catalogue powered by your descriptions.

## Bolt: Full-Stack Generation

Bolt generates full-stack applications in the browser: frontend, backend, database schema. The differentiator is its integration with StackBlitz, a browser-based development environment. You describe an app, Bolt generates it, and you can edit the code directly in the browser without installing anything.

Best for:

- Prototyping ideas quickly
- Learning by seeing generated code and modifying it
- Building apps when you don't want to set up a local environment

## When Platforms Hit a Wall

Every platform has limits. Understanding those limits matters more than understanding their features.

**Customisation ceiling.** The platform generates code in its patterns. When you need something it doesn't support (a specific animation, an unusual data structure, a third-party API it doesn't know), you're stuck describing what you want and hoping it understands.

**Complex logic.** Business rules with multiple conditions, edge cases, and exceptions are hard to express in natural language. "If the user is on a paid plan AND their subscription is active AND they haven't exceeded their monthly limit AND the feature flag is enabled, show the premium dashboard" becomes a frustrating conversation.

**Security audit.** Generated code may have vulnerabilities you can't inspect easily. The platform handles authentication (Chapter 19), but are the database rules correct? Are API endpoints properly protected (Chapter 18 covers security)? Without reading the code, you trust the platform blindly.

**Performance.** Generated apps use generic patterns. When performance matters (large datasets, real-time features, complex computations), platform code may be slower than hand-optimised solutions.

### Why JobPilot Can't Be Built on a Platform

JobPilot can't be built on any generative platform. Chrome extensions need a `manifest.json`, content scripts that inject into other websites, a service worker for background logic, and Chrome APIs like `chrome.storage` and `chrome.identity`. Lovable, Bolt, and v0 don't know what any of that means. No generative platform supports Manifest V3 architecture.

This is why the rest of the book uses Mode 3. If your project needs direct access to browser APIs, device hardware, or an unusual runtime environment, you're in Mode 3 from day one. If you're building something simpler (a habit tracker, a landing page, a dashboard), Mode 2 might be all you need. Use this chapter's tools and skip ahead to the chapters on deployment and distribution.

## Mode 2 to Mode 3: The Transition

Most platforms offer an "eject" button. This exports the generated source code as files you can open in VS Code or Cursor. Once ejected:

- You own the code
- You can modify anything
- The platform no longer manages it
- You need to host and deploy it yourself

This transition matters because it means Mode 2 projects don't have a dead end. Build fast on the platform, eject when you need control, continue in Mode 3.

Signs you're ready to eject:

1. You keep fighting the platform's limitations
2. You understand the generated code well enough to modify it (thanks to Chapter 3, Reading Code)
3. You need integrations the platform doesn't support
4. You want custom deployment or hosting arrangements

> **Try It:** Go to lovable.dev (free account). Type: "Build a simple bookmark manager. Users can add a URL and a title, see a list of bookmarks, and delete them." Watch it generate the app. Click around. Now ask yourself: if you wanted to add browser extension integration, could the platform do it? That's the wall.

## Common Beginner Mistake

**Staying on the platform too long.** Platforms get you to 80% fast. The last 20% takes longer on a platform than it would in a code editor. When you find yourself writing longer and longer prompts, trying to explain increasingly specific behaviour, it's time to eject and work with the code directly.

## Checklist

- [ ] I understand the difference between Mode 1 (copy-paste from AI), Mode 2 (platforms), and Mode 3 (IDE)
- [ ] I can build a simple app on Lovable or Bolt from prompts
- [ ] I know what "eject" means and when to do it
- [ ] I recognise the limits of platform building (customisation, security, performance)
- [ ] I can describe an app in prompts that get progressively more specific

## Summary

Generative platforms build working applications from natural language descriptions. Lovable generates full apps with database and auth. v0 generates individual UI components. Bolt provides full-stack generation with in-browser editing. All platforms have limits: customisation ceilings, difficulties with complex logic, security opacity, and performance trade-offs. The "eject" feature lets you export code and continue in a code editor when the platform's constraints outweigh its convenience.

## Challenge

1. Go to Lovable (or Bolt). Describe a simple app: a habit tracker where users log daily habits, see a streak counter, and view a weekly summary chart. Build it entirely through prompts. Deploy it.

2. Now describe the same app to v0. Compare what you get: a full app vs a set of components. Write down three differences.
