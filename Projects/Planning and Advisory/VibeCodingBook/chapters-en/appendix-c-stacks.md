# Appendix C: Best Stack by Product Type (2026)

These are opinionated recommendations based on what works best for vibe coding in 2026. Each stack is optimised for speed of development, AI assistance quality, and ease of deployment.

## Landing Page / Marketing Site

| Layer      | Tool                       | Why                                     |
| ---------- | -------------------------- | --------------------------------------- |
| Framework  | Next.js or Astro           | Fast, SEO-friendly, deploy to Vercel    |
| Styling    | Tailwind CSS               | Utility classes, no CSS files to manage |
| Components | shadcn/ui                  | Copy-paste, customisable, accessible    |
| Forms      | Netlify Forms or Formspree | No backend needed                       |
| Analytics  | PostHog or Plausible       | Privacy-friendly, free tiers            |
| Deploy     | Vercel or Netlify          | Auto-deploy from GitHub                 |

**Time to ship:** 1 day. AI generates the page structure. Tailwind styles it. Deploy to Vercel.

## SaaS Web Application

| Layer             | Tool                                      | Why                                     |
| ----------------- | ----------------------------------------- | --------------------------------------- |
| Frontend          | React + TypeScript + Vite                 | Industry standard, excellent AI support |
| Styling           | Tailwind CSS + shadcn/ui                  | Fast development, consistent design     |
| Backend           | Node.js + Express (or Next.js API routes) | JavaScript everywhere, simple           |
| Database          | Supabase (PostgreSQL)                     | Free tier, auth built-in, real-time     |
| Auth              | Supabase Auth or Clerk                    | OAuth, email/password, magic links      |
| Payments          | Stripe                                    | Standard for online payments            |
| Deploy (frontend) | Vercel                                    | Auto-deploy, preview URLs               |
| Deploy (backend)  | Render or Railway                         | Managed Node.js hosting                 |
| Monitoring        | Sentry + UptimeRobot                      | Error tracking + uptime alerts          |

**Time to MVP:** 1 to 2 weekends. Supabase handles auth and database. Stripe handles payments. Focus on your unique feature.

## Chrome Extension

| Layer               | Tool                | Why                           |
| ------------------- | ------------------- | ----------------------------- |
| Architecture        | Manifest V3         | Required since 2024           |
| UI (popup)          | React + TypeScript  | Component-based, typesafe     |
| Styling             | Tailwind CSS        | Works in extension context    |
| Storage             | chrome.storage API  | Syncs across devices          |
| Build               | Vite + CRXJS plugin | Hot reload during development |
| Backend (if needed) | Render or Railway   | For AI calls or databases     |
| Distribution        | Chrome Web Store    | Primary discovery channel     |

**Time to MVP:** 1 weekend for a simple extension. 2 to 3 weekends for one with AI integration and a backend.

This is the exact stack you used throughout this book to build JobPilot. The popup is React + TypeScript. The content scripts are plain TypeScript (no framework needed for DOM manipulation on other people's pages). The service worker handles OpenAI calls. CRXJS gives hot reload during development, which saves enormous amounts of time when you're testing on live job application pages.

If you're building a web app instead, swap this stack for the SaaS Starter stack above. The principles from every chapter still apply.

## Desktop Application

| Layer         | Tool                                | Why                                  |
| ------------- | ----------------------------------- | ------------------------------------ |
| Framework     | Electron or Tauri                   | Web technologies for desktop         |
| Frontend      | React + TypeScript                  | Reuse web skills                     |
| System access | Node.js (Electron) or Rust (Tauri)  | File system, processes, OS APIs      |
| Auto-update   | electron-updater or Tauri updater   | Users get new versions automatically |
| Distribution  | Direct download + brew cask (macOS) | No app store approval needed         |

Tauri produces much smaller binaries (5 MB vs 150 MB) but requires some Rust knowledge. Electron is heavier but entirely JavaScript.

**Time to MVP:** 1 to 2 weekends. If you know React, Electron is the fastest path.

## Mobile Application (Cross-Platform)

| Layer                 | Tool                                           | Why                                            |
| --------------------- | ---------------------------------------------- | ---------------------------------------------- |
| Option A (web first)  | PWA (Progressive Web App)                      | No app store, instant updates, web skills      |
| Option B (native)     | React Native + Expo                            | Native feel, one codebase, web skills transfer |
| Backend               | Supabase or Firebase                           | Real-time, auth, free tiers                    |
| Push notifications    | Expo Notifications or Firebase Cloud Messaging | Cross-platform                                 |
| Distribution (PWA)    | Your domain                                    | No approval process                            |
| Distribution (native) | App Store + Google Play                        | Requires developer accounts                    |

PWAs are underrated: they install from the browser, work offline, and send push notifications. For most vibe coding projects, a PWA is faster to build and easier to distribute than a native app.

**Time to MVP:** PWA: 1 weekend. React Native: 2 to 3 weekends.

## AI-Powered Tool

| Layer              | Tool                                 | Why                                  |
| ------------------ | ------------------------------------ | ------------------------------------ |
| Frontend           | React + TypeScript + Vite            | Standard, AI understands it well     |
| Backend            | Node.js + Express                    | Handles AI API calls server-side     |
| AI (primary)       | GPT-4o-mini or Gemini Flash          | Cost-effective for most tasks        |
| AI (complex)       | GPT-4o or Claude Sonnet              | For reasoning-heavy tasks            |
| Structured outputs | OpenAI JSON mode or Instructor       | Predictable response format          |
| Cost management    | Model routing + spending limits      | Stay within budget                   |
| Database           | Supabase                             | Store prompts, responses, usage logs |
| Deploy             | Vercel (frontend) + Render (backend) | Separation of concerns               |

**Time to MVP:** 1 to 2 weekends. The AI provider handles the hard part. Your job is the interface, validation, and cost control.

## Google Sheets Add-on

| Layer         | Tool                            | Why                                   |
| ------------- | ------------------------------- | ------------------------------------- |
| Runtime       | Google Apps Script (JavaScript) | Runs inside Google Sheets             |
| UI            | HTML Service (sidebar/dialog)   | Built-in UI framework                 |
| External APIs | UrlFetchApp                     | Make HTTP requests from Apps Script   |
| Distribution  | Google Workspace Marketplace    | Users install from within Google apps |
| Auth          | Built-in (Google account)       | Users are already authenticated       |

**Time to MVP:** 1 day. Google Apps Script is constrained but fast for simple automation.

## Quick Decision Matrix

| If you're building...      | Start with...                                             |
| -------------------------- | --------------------------------------------------------- |
| A page to validate an idea | Landing page stack → deploy in hours                      |
| A tool with user accounts  | SaaS stack → Supabase handles auth and data               |
| A browser enhancement      | Chrome Extension stack → CRXJS for fast development       |
| A desktop tool             | Electron if you know JS, Tauri if you want small binaries |
| A mobile app               | PWA first, React Native if you need native features       |
| An AI feature              | AI tool stack → cost management from day one              |
| A spreadsheet automation   | Google Sheets Add-on → fastest path to users              |
