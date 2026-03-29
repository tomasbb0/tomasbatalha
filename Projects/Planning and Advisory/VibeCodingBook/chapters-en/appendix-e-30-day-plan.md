# Appendix E: The 30-Day Study Plan

A structured plan to work through this book and build your first real project. Each day takes 1 to 2 hours. By day 30, you'll have a deployed product.

## Week 1: Foundations (Mode 1)

| Day   | Activity                                                                                                                                          | Chapters |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| **1** | Read Chapter 1 (What Is Vibe Coding). Identify your mode (1, 2, or 3). Set up your learning environment.                                          | 1        |
| **2** | Read Chapter 2 (First AI Conversation). Build the tutorial page using an AI assistant.                                                            | 2        |
| **3** | Read Chapter 3 (Reading Code). Open three projects on GitHub. Read one file from each. Identify the language, the main function, and one pattern. | 3        |
| **4** | Read Chapter 4 (HTML, CSS, JavaScript). Build the Café Pessoa page from the chapter. Modify the colours and add a menu item.                      | 4        |
| **5** | Read Chapter 5 (Deploy). Take the page from Day 2 and deploy it to Vercel. Share the URL with a friend.                                           | 5        |
| **6** | Challenge 1 from Chapter 25: Build a personal portfolio page and deploy it.                                                                       | 25       |
| **7** | Review week 1. Write down three things you found confusing. Search for answers or ask an AI assistant.                                            | All      |

## Week 2: Building With Platforms (Mode 2)

| Day    | Activity                                                                                                                                                                                       | Chapters |
| ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| **8**  | Read Chapter 6 (Your Workshop). Install VS Code or Cursor. Learn the 10 terminal commands. Install recommended extensions.                                                                     | 6        |
| **9**  | Build a simple app on Lovable: a review collector or habit tracker. Deploy it.                                                                                                                 | 7        |
| **10** | Read Chapter 7 (Platform Building). Create accounts on Lovable and v0. Draw the four-box diagram for the app you built yesterday. Identify: what runs in the browser, what runs on the server. | 7        |
| **11** | Read Chapter 8 (Browser). Open DevTools on three websites. Find: an element in the DOM, a network request, a cookie, and a console error (if any).                                             | 8        |
| **12** | Read Chapter 9 (Data & State). Add localStorage to your Café Pessoa page (save the user's preferred language or theme). Then create a Supabase project and move data to the database.          | 9        |
| **13** | Read Chapter 10 (AI Integration). Call an AI API with structured outputs. Get a JSON response and parse it in your code.                                                                       | 10       |
| **14** | Read Chapter 11 (Deploy & Monitoring). Set up CI/CD with GitHub Actions. Add Sentry. Explore node_modules. Run `npm run build` and look at the dist folder.                                    | 11       |

## Week 3: Going Pro (Mode 3)

| Day    | Activity                                                                                                                                                                                                                                                                                       | Chapters |
| ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| **15** | Read Chapter 12 (Architecture). Draw the architecture diagram for your project. Identify frontend, backend, database, and API boundaries.                                                                                                                                                      | 12       |
| **16** | Read Chapter 13 (TypeScript & React). Set up a React + TypeScript project. Build a component with useState.                                                                                                                                                                                    | 13       |
| **17** | Read Chapter 14 (APIs & HTTP). Open the Network tab and monitor requests on a site you use. Build a minimal Express.js API with one GET and one POST endpoint.                                                                                                                                 | 14       |
| **18** | Read Chapter 15 (Dependencies). Create a Vite project. Install two packages. Read the manifest.json. Build a minimal "Hello World" extension.                                                                                                                                                  | 15       |
| **19** | Read Chapter 16 (Git). Initialise a repo for your project. Make three commits. Create a branch. Merge it. Push to GitHub.                                                                                                                                                                      | 16       |
| **20** | Read Chapter 17 (Browser Extensions). Open an installed extension's source files. Read the manifest.json. By now you've been building JobPilot piece by piece. This is where you wire up the full extension: a popup that reads from chrome.storage and a content script that modifies a page. | 17       |
| **21** | Read Chapter 18 (Security). Check your project for the OWASP Top 10. Test: submit `<script>alert(1)</script>` in a form. Verify it's sanitised.                                                                                                                                                | 18       |

## Week 4: Ship It

| Day    | Activity                                                                                                                                                                                                                                                          | Chapters |
| ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| **22** | Read Chapter 19 (Secrets & Auth). Set up .env. Move all secrets out of code. Verify .env is in .gitignore. Add authentication (Supabase Auth or Clerk).                                                                                                           | 19       |
| **23** | Read Chapter 20 (AI Costs). Calculate the cost of your AI feature across three models. Set spending limits.                                                                                                                                                       | 20       |
| **24** | Read Chapter 21 (Testing & Debugging). Write three unit tests for your most important function. Write one E2E test with Playwright.                                                                                                                               | 21       |
| **25** | Read Chapter 22 (Feature Flags). Add one feature flag to your project. Deploy with the feature hidden. Enable it.                                                                                                                                                 | 22       |
| **26** | Read Chapter 23 (MVP & Scope). Write a one-page scope document for a new idea. Define hypothesis, ICP, one feature, "NOT IN MVP" list.                                                                                                                            | 23       |
| **27** | Read Chapter 24 (Distribution). Write a distribution plan for your product. Identify three communities. Draft one piece of content.                                                                                                                               | 24       |
| **28** | Build day. Spend the full session building or polishing your project. Fix bugs. Improve the UI. If you're building a Chrome extension, this is the day to get it published on the Chrome Web Store (follow the Chrome Extension Publish Checklist in Appendix D). | All      |
| **29** | Launch day. Deploy the final version. Share it: post in a community, tell five people, submit to Product Hunt (if ready).                                                                                                                                         | 24       |
| **30** | Retrospective. Write down: what you built, what you learned, what you'd do differently. Pick your next project from Chapter 25's Challenge Lab.                                                                                                                   | 25       |

## Tips for the 30 Days

- **Don't skip days.** Consistency beats intensity. One hour daily is better than five hours once a week.
- **Build something real.** The plan references tutorial exercises, but swap them for your own project whenever possible.
- **Ask AI for help.** That's the point. When you're stuck, describe the problem to your AI assistant.
- **Push to GitHub daily.** Even if it's unfinished. This builds the Git habit and creates a backup.
- **Share progress.** Tell someone about what you built each week. Accountability accelerates learning.
