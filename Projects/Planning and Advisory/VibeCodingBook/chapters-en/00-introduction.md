# Introduction

> _"I have absolutely no idea about space exploration. I'm a software guy. But because I'm a non-expert, I've been able to bring the software concept of modularity into the space sector, which was never done before."_
>
> (Naveen Jain, co-founder of Moon Express)

Naveen Jain had no aerospace degree. He's also not someone you'd want to hold up as a role model for business ethics (look up InfoSpace if you're curious). But the quote stuck with me because the guy walked into an industry that had nothing to do with him, brought an idea from a completely different field, and it worked.

That pattern shows up everywhere. Shunryu Suzuki called it beginner's mind: the idea that approaching a field with no assumptions lets you see what the experts miss. The Wright brothers ran a bicycle shop and invented powered flight. In 1969, Margaret Hamilton, a mathematician with no computer science degree, wrote the navigation software that landed Apollo 11 on the Moon. And in 2025, people with zero programming experience started building real software with AI, because the tools finally got good enough that what you know about a problem matters more than whether you can write a for loop.

If you picked up this book, you probably feel some version of what Jain described. You don't know how any of this works. You've never opened a terminal. You think "deploy" is a military word. Good. This book is for the Margaret Hamiltons of the AI era: people who understand their field deeply and just need the vocabulary to build the solution themselves. The feeling is familiar to me because I had it too, about six months ago.

---

In January 2026, I had a master's in strategic management, experience at a couple of big tech companies doing business stuff, and precisely zero programming skills. If you'd handed me a code file and asked me to explain a single line, I would've stared at it the way you stare at ancient Egyptian hieroglyphs.

Two months later, I had 21 products in production.

Real products, with real users, running on real servers. A Chrome extension with thousands of lines of code that auto-fills job applications across Workday, Greenhouse, and Lever. An AI agent that navigates the browser in real time and clicks buttons on its own. A CV editing platform with AI chat and PDF export. A sales toolkit that processes 1,500 leads per day for exactly zero euros.

I built all of it with AI. And this book exists because, while I was building, there was no book that explained how.

## Why a Book (and Why This Misconception Will Cost You)

Most people think vibe coding is just "using ChatGPT." Open a chat, describe what you want, copy the code, done. If that were true, you wouldn't need a book. A blog post would do.

Here's what that belief gets wrong:

**ChatGPT doesn't know your project.** It generates code in a vacuum. It doesn't know your folder structure, your database schema, your authentication flow, or the 47 other files that need to work together. A chat produces fragments. A book teaches you how to assemble those fragments into something that survives contact with real users.

**Speed without direction is just motion.** You can generate 500 lines of code in five minutes. If those 500 lines are architecturally wrong (secrets in the frontend, business logic in the browser, no error handling, no tests), you haven't saved time. You've created a debt that compounds with every feature you stack on top.

**AI is a power tool.** A circular saw cuts faster than a handsaw. But if you don't know where to cut, speed makes the disaster worse.

The history of technology is full of powerful machines waiting for the right operator. The Analytical Engine could execute any instruction but couldn't decide what to compute. A SpaceX rocket can reach orbit, but someone has to define the trajectory. Today's AI will generate any code you describe. The value is in knowing what to describe, and why, and in what sequence.

This book teaches principles that compound forever, alongside tools that work today. When the tools change (and they will), the principles carry you forward. When the principles feel abstract, the tools ground you.

## The Numbers That Matter

According to GitHub's Octoverse 2024 report, 73% of open source developers already use AI tools for coding or documentation. Python overtook JavaScript as the most used language on GitHub for the first time in ten years, driven by the explosion of generative AI projects (98% year-over-year growth). In 2024, more than 70,000 new public generative AI projects were created on the platform. The total stands at 150,000.

At Y Combinator, the most influential accelerator in the world, the Winter 2025 batch had the highest-ever percentage of startups whose code was written primarily with AI assistance. The originator of the "vibe coding" concept, Andrej Karpathy (former head of AI at Tesla, OpenAI co-founder), coined the term in February 2025 to describe exactly this: building real software by describing what you want in natural language.

This is not a passing trend. The percentage of code written without AI assistance inside the largest technology companies is shrinking every month.

## Why This Book, Why Me

Before any of this, I worked in tech without being technical. Dashboards, market analysis, client presentations, business development. I sat next to engineers every day. I understood almost nothing of what they did. The barrier between "having the idea" and "building the thing" felt permanent.

My workflow was Excel, email, and brute force. Every time I thought "this could be automated," the answer was "you need an engineer for that."

In 2025... I stopped needing one.

After co-founding Pairwire (an AI due-diligence platform), I realised the tools had changed irreversibly. I sat down with Cursor, Claude, and ChatGPT, and in two months I built 21 complete applications:

Chrome extensions, web platforms, desktop apps, sales tools, Google Sheets add-ons. 21 products in total, all running live on the internet for real users.

Every single one was built with AI, and there's a difference between "with AI" and "by AI" that this entire book exists to teach you.

## What We're Building: JobPilot AI

This book has one main project. You'll build it piece by piece, chapter by chapter, like following a LEGO instruction manual.

**JobPilot AI** is a Chrome extension that fills out job applications for you.

Here's what happens when you use it:

1. You go to a job application page (Greenhouse, Lever, or Workday — the websites companies use to collect applications)
2. You click the JobPilot icon in your browser
3. A small window pops up showing your saved profile (name, email, phone, work history, skills)
4. You click "Auto-fill"
5. Every field on the application form fills itself in, automatically, in about two seconds
6. You review, tweak anything that looks off, and submit

That's it. One click. Form done. No more typing your address into 30 different websites.

Under the hood, it works like this:

- A **content script** (a piece of code that runs on the job application page) reads the form and collects the field names
- A **service worker** (a background process) takes those field names and your saved profile, and sends them to OpenAI
- OpenAI figures out which profile data goes in which field and sends back a clean mapping
- The content script fills every field on the page

The extension stores your profile locally in Chrome (so it syncs across your devices), authenticates with Google (so only you can access your data), and costs about $0.0002 per fill (you'll calculate this yourself in Chapter 20).

Every chapter in this book teaches you how to build one piece of this. By the end, you'll have built the whole thing. And along the way, if you want to apply the same ideas to a different project (a habit tracker, a recipe app, a tool for your specific job), the book shows you how to adapt.

Now let's go.

## Bring Your Own Expertise

The most important thing you bring to vibe coding isn't a project idea. It's what you already know about a problem.

Maybe you've spent five years in logistics and you know exactly why dispatchers hate their scheduling tool. Maybe you run a small bakery and you know which part of the ordering process wastes the most time. Maybe you're a recruiter who's filled out the same form on 30 different platforms and thought "someone should automate this" every single time.

That knowledge is the hard part. Understanding a problem deeply, knowing what users actually need, having opinions about what's broken. You can't prompt your way into that. AI can write code. It can't work your job for five years and develop the intuition you have.

This book teaches the rest: how to turn that knowledge into something that runs on a screen. You bring the expertise. AI brings the syntax. Throughout the chapters, you'll find moments to apply what you're learning to your own domain. If you don't have a specific idea yet, the book's examples carry you. But if you do, the book becomes a personalised manual.

## Who Should Read This

This book is for founders, product managers, designers, marketing professionals, and anyone who wants to build software with AI but never had technical training. If you have an idea and the discipline to execute it, this book teaches the rest.

If you have a computer science degree, the book may bore you. If you have been programming for years, you already know most of what is here. This is for everyone else.

## Tools vs Principles

The tools in this book have a half-life. The specific platforms, models, and APIs will change. Some monthly. What compounds forever are the principles: how web apps work, how to structure a project, how to keep data safe, how to ship a product. This book teaches both, and tells you which is which.

**Tools** (change): ChatGPT, Claude, Lovable, Cursor, Vercel. The book explains how to use them in 2026. In two years, some will have been replaced.

**Principles** (compound): how to read code, how to organise an application, how to keep secrets out of the codebase, how to test, how to ship. These principles hold regardless of the tool.

## The Three Tiers

This book organises vibe coding into three tiers:

**Tier 1: Chat-to-Code.** You describe what you want to ChatGPT, Claude, or Gemini. It generates code. You copy, paste, run. Good for scripts, automations, and learning. The ceiling is low (around 500 lines before things get messy), but the speed is extraordinary.

**Tier 2: Prompt-to-App.** You describe what you want to a platform like Lovable, Bolt, or v0. It generates a full application: UI, database, authentication. Good for MVPs, dashboards, and landing pages. The ceiling is medium, and when you hit it, you need to understand enough to escape.

**Tier 3: AI-Supercharged IDE.** You work in a proper code editor (Cursor, VS Code with Copilot) and the AI assists every step. You write prompts, the AI writes code, you review, iterate, and ship. The ceiling is unlimited, because you're in full control.

Most vibe coders start at Tier 1, get excited, hit a wall, and either quit or jump to Tier 2. This book teaches all three, and tells you when each one is the right choice.

## How to Read This Book

The book has five sections. Each one builds on the previous, and each one builds a new piece of JobPilot.

**Tier 1: Apprentice (Chapters 1 to 5).** You learn how to talk to AI, read the code it writes, set up your workspace, and put something live on the internet. By the end of this tier, you'll have JobPilot's first prompt written, its basic popup working, and the concept of deploying to the Chrome Web Store clear in your head.

**Tier 2: Builder (Chapters 6 to 11).** You learn how the browser works, how data is stored, and how web apps are structured. By the end of this tier, you'll understand why JobPilot needs a service worker, where the user's profile lives, and how the popup talks to the background code.

**Tier 3: Master (Chapters 12 to 17).** Full control. TypeScript, React, APIs, Git, browser extensions, and AI integration. By the end of this tier, JobPilot's architecture is complete: content scripts reading forms, a service worker calling OpenAI, structured outputs mapping fields, and message passing connecting everything.

**The Survival Kit (Chapters 18 to 23).** Security, authentication, costs, testing, deployment, and feature flags. This is where JobPilot goes from "works on my machine" to "works for real users without leaking API keys or costing a fortune."

**Launch (Chapters 24 to 27).** Distribution, MVP scope, challenges, and hardware. You publish JobPilot to the Chrome Web Store and learn how to get people to actually install it.

If you want to build something other than a Chrome extension (a web app, a mobile tool, a dashboard), every chapter shows how the same concept applies to your project too. JobPilot is the main path. Your own idea is the parallel path.

You can stop after any tier and still have gained real, usable skills. Tier 1 alone lets you build and deploy simple projects. Tiers 1 and 2 together let you build real apps. All five sections give you professional-level vibe coding capability.

## Conventions

Throughout the book:

- **Bold terms** in the text are defined in the Key Terms table at the start of each chapter
- **Code blocks** contain real, runnable code (not pseudocode)
- **JobPilot steps** show what you're building in that chapter and how it fits the bigger picture
- **"If you're building something else" boxes** show how the same concept applies to web apps, dashboards, or whatever your idea is
- **Checklists** at the end of each chapter are pre-deploy verification items
- **Challenges** are practical tasks tied to the chapter's content

## A Note on Honesty

AI is an extraordinary tool. It's also a tool that makes you confident about things you should question. This book doesn't pretend that vibe coding is equivalent to software engineering. It isn't. What it teaches is how to build real, working, deployable software with AI assistance, while understanding enough to know when things go wrong and how to fix them.

The two fatal mistakes of vibe coding are blindness (not understanding what the AI wrote) and overconfidence (trusting the AI with things it's bad at). Every chapter in this book fights both.
