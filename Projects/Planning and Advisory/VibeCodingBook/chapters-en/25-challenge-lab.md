# Chapter 25 The Challenge Lab: 15 Hands-On Challenges

This chapter is a workshop. No theory. Fifteen challenges that use everything from the book. Each challenge states its tier level, estimated time, and which chapters it draws from. Work through them in order or pick the ones that match your current level.

## Tier 1 Challenges (Mode 1: Copy, Paste, Ship)

### Challenge 1: Personal Portfolio Page

**Chapters:** 2, 5 | **Time:** 2 to 3 hours | **Level:** Tier 1

Use an AI assistant (ChatGPT, Claude, or Cursor) to build a personal portfolio page. Include: your name, a short bio, three projects (real or fictional), and a contact section. Deploy to Vercel. Share the live URL.

**Success criteria:** Someone can visit the URL and learn about you.

### Challenge 2: Downloads Organiser

**Chapters:** 2, 3 | **Time:** 1 to 2 hours | **Level:** Tier 1

Ask an AI assistant to write a Python script that organises your Downloads folder by file type. PDFs in a `documents/` folder, images in `images/`, videos in `videos/`, everything else in `other/`. Run the script on your actual Downloads folder.

**Success criteria:** Files are sorted. The script is reusable.

### Challenge 3: Read an Extension's Manifest

**Chapters:** 3, 17 | **Time:** 1 hour | **Level:** Tier 1

Go to `chrome://extensions`. Enable Developer Mode. Pick any extension. Open its files. Find `manifest.json`. For every field, write one sentence explaining what it does. Compare with this book's Chapter 17 (Browser Extensions).

**Success criteria:** You can explain every field in the manifest without looking things up.

## Tier 2 Challenges (Mode 2: Build With Platforms)

### Challenge 4: Habit Tracker With Auth

**Chapters:** 7, 9 | **Time:** 3 to 4 hours | **Level:** Tier 2

Use Lovable (or Bolt) to build a habit tracker. Features: user creates habits, logs daily completion, sees a streak counter. Add Firebase or Supabase authentication. Deploy.

**Success criteria:** Two different users can log in and see their own data (not each other's).

### Challenge 5: Landing Page With Waitlist

**Chapters:** 7, 9, 12 | **Time:** 2 to 3 hours | **Level:** Tier 2

Build a landing page for a product idea. Include: a headline, a description, three benefits, and a waitlist form. The form saves submissions to a database (Supabase or Firebase). Deploy. Share with five people.

**Success criteria:** You can see the email addresses of people who signed up.

### Challenge 6: Google Sheets Email Add-on

**Chapters:** 2, 14 | **Time:** 4 to 5 hours | **Level:** Tier 2

Create a Google Sheets Add-on that reads a column of email addresses and a column of names, then sends personalised emails using Gmail's API. Each email should include the person's name.

**Success criteria:** Ten personalised emails sent from a spreadsheet with one click.

### Challenge 7: Static Site With Serverless Form

**Chapters:** 12, 15 | **Time:** 2 to 3 hours | **Level:** Tier 2

Build a static website with a contact form. Deploy to Netlify. Use Netlify Functions (serverless) to process the form submission and send a notification email. No backend server needed.

**Success criteria:** Form submissions arrive in your email inbox.

## Tier 3 Challenges (Mode 3: Full Control)

### Challenge 8: Price Comparison Extension

**Chapters:** 10, 14, 17 | **Time:** 6 to 8 hours | **Level:** Tier 3

Build a Chrome extension that detects product prices on Amazon. For each product, call a price comparison API to find cheaper alternatives. Display the results in a popup or overlay on the page.

**Success criteria:** Browse Amazon, see the extension highlight products with cheaper alternatives elsewhere.

### Challenge 9: PDF Question Answering Bot

**Chapters:** 10, 14 | **Time:** 5 to 7 hours | **Level:** Tier 3

Build a web app where users upload a PDF. Extract the text. Use an AI model with structured outputs to answer questions about the document. Every answer must include the source passage from the PDF.

**Success criteria:** Upload a PDF, ask a question, receive an answer with a citation from the document.

### Challenge 10: Fork and Extend an AI Agent

**Chapters:** 10, 16 | **Time:** 4 to 6 hours | **Level:** Tier 3

Fork an open-source browser agent repository. Change the AI model from one provider to another (e.g. GPT to Claude). Add a new tool: "take screenshot" that captures the current browser state as an image.

**Success criteria:** The agent uses your new tool when instructed to take a screenshot.

### Challenge 11: Rate Limiting and Cost Tracking

**Chapters:** 20 | **Time:** 3 to 4 hours | **Level:** Tier 3

Add rate limiting and cost tracking to any AI feature in an existing project. Track: tokens used per request, cost per request, total cost per day. Set a daily spending limit. When the limit is reached, return a fallback response.

**Success criteria:** A dashboard (even a console log) showing token usage, costs, and a limit that actually stops API calls.

### Challenge 12: Testing Suite

**Chapters:** 21 | **Time:** 3 to 4 hours | **Level:** Tier 3

Write three unit tests and one end-to-end test for any project. The unit tests should cover: a normal case, an edge case, and an error case. The E2E test should simulate the most critical user flow.

**Success criteria:** All four tests pass. Run them with a single command.

### Challenge 13: Multi-Environment Deployment

**Chapters:** 11 | **Time:** 3 to 4 hours | **Level:** Tier 3

Set up a preview and production deployment with different environment variables. Create a GitHub Actions workflow that runs tests on every push. Merge to main and watch the production deployment happen automatically.

**Success criteria:** Push to a branch, get a preview URL. Merge to main, see the production site update. Different API keys in each environment.

### Challenge 14: Feature Flag Workflow

**Chapters:** 22 | **Time:** 2 to 3 hours | **Level:** Tier 3

Add a feature flag to any project. Deploy the new feature behind the flag (disabled). Enable it for yourself. Verify the feature appears. Then enable it for 50% of users using PostHog or a simple random check.

**Success criteria:** The feature appears for some users and not others, controllable without redeploying.

### Challenge 15: The Final Boss

**All chapters** | **Time:** 10 to 15 hours | **Level:** Tier 3+

Build a Chrome extension that uses AI to summarise any webpage. Requirements:

1. **Extension architecture** (Ch 17): Service worker, content script, popup
2. **AI integration** (Ch 10): Structured outputs for the summary (title, key points, sentiment)
3. **API design** (Ch 14): Backend endpoint that receives page content and returns the AI summary
4. **Authentication** (Ch 19): Users log in to access the summarisation feature
5. **Cost management** (Ch 20): Track tokens per request, implement a daily limit per user
6. **Testing** (Ch 21): At least two unit tests and one E2E test
7. **Deployment** (Ch 11): Deploy the backend to Render, the extension to the Chrome Web Store
8. **Error monitoring** (Ch 11): Add Sentry to the backend
9. **Feature flag** (Ch 22): Put the AI summarisation behind a flag

**Success criteria:** A working Chrome extension, published on the Chrome Web Store, with a monitored backend, proper authentication, cost controls, tests, and a feature flag.

This challenge is essentially "build a mini JobPilot." The same architecture you've been building throughout this book (content script + service worker + popup + backend API + OpenAI), the same deployment split (Chrome Web Store + Render), and the same concerns (cost control, security, authentication). If you complete this, you can build any browser-based AI product.

If you want to build something other than a Chrome extension, adapt the challenge: replace the extension layer with a web frontend, replace chrome.storage with a database, and keep everything else the same.

This challenge combines nearly every concept in the book. Take a week. Work through it methodically. When you complete it, you've built a production-grade product.

## How to Use This Lab

Pick challenges that match your current level. Each challenge is self-contained. If you get stuck, reread the referenced chapters. If you're still stuck, ask an AI assistant for help (that's the entire point of vibe coding).

Track your progress:

- [ ] Challenge 1
- [ ] Challenge 2
- [ ] Challenge 3
- [ ] Challenge 4
- [ ] Challenge 5
- [ ] Challenge 6
- [ ] Challenge 7
- [ ] Challenge 8
- [ ] Challenge 9
- [ ] Challenge 10
- [ ] Challenge 11
- [ ] Challenge 12
- [ ] Challenge 13
- [ ] Challenge 14
- [ ] Challenge 15 (Final Boss)
