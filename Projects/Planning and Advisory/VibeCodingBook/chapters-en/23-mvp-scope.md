# Chapter 23 MVP & Scope: Building the Right Thing

You have the skills to build. The danger now is building the wrong thing, or building too much of the right thing. This chapter is about scope: deciding what to build, how much of it, and when to stop adding features and start testing with real users.

## Key Terms

| Term            | What it means                                                              |
| --------------- | -------------------------------------------------------------------------- |
| **MVP**         | Minimum Viable Product: the smallest thing that tests your hypothesis      |
| **Scope**       | The boundaries of what a project includes (features, users, platforms)     |
| **Scope creep** | Gradual, unplanned expansion of what the project does                      |
| **ICP**         | Ideal Customer Profile: the specific person your product is for            |
| **Hypothesis**  | The assumption you're testing ("people will pay for X")                    |
| **Prototype**   | A non-functional or partially functional version used for testing interest |
| **Pivot**       | Changing direction based on what you learn from the MVP                    |

## What an MVP Actually Is

An MVP is not a bad version of a good product. It's the smallest thing that answers one question: "Do people want this?"

A food delivery app's MVP is not "Uber Eats but worse". It's: one restaurant, one delivery area, orders via WhatsApp, delivery by the founder on a bicycle. If people order, there's demand. Build the app later.

The three questions that define an MVP:

1. **What's the ONE thing it must do?** Not ten things. One.
2. **Who's the ONE person it's for?** Not "everyone". One specific type of user.
3. **What's the ONE metric that proves it works?** Signups? Purchases? Time spent? Pick one.

## Scope Creep: The Silent Killer

Every project starts with a clear vision. Then:

"We should also add user profiles."
"What if we supported dark mode?"
"Can we integrate with Slack?"
"We need an admin panel."
"Users should be able to export data."

Each addition sounds reasonable. Each takes "just a few days". Multiply by twenty and your two-week project is now a six-month project that never ships.

The cure is a written scope document. Before writing any code:

```
PROJECT: Sleep Diary
MVP SCOPE:
  - User logs sleep time (bedtime, wake time)
  - App calculates total hours
  - User sees a 7-day chart

NOT IN MVP:
  - User accounts (use localStorage for now)
  - Sleep quality scoring
  - Recommendations
  - Apple Watch integration
  - Export to PDF
  - Social sharing
```

The "NOT IN MVP" list is as important as the feature list. It makes the boundaries explicit and prevents "while we're at it" additions.

### JobPilot's MVP Scope (Use This as a Template)

Here's what JobPilot's actual MVP scope looked like. Copy this format for your own project:

```
PROJECT: JobPilot AI
MVP SCOPE:
  - User enters their profile once (name, email, phone, work history)
  - Fills out Greenhouse application forms with one click
  - Calls GPT-4o-mini to match profile fields to form fields

NOT IN MVP:
  - LinkedIn Easy Apply support
  - Lever support
  - Workday support
  - Resume upload / PDF parsing
  - Cover letter generation
  - User accounts / login
  - Usage analytics dashboard
  - Multi-language support
  - Mobile version
```

Every item in the "NOT IN MVP" list was something someone suggested during the first week. Each one was a good idea. Each one would have pushed the launch by at least a week. The MVP had exactly one integration (Greenhouse), one AI feature (field matching), and one action (click to fill). It shipped in under two weeks. Three people used it the first week. That was enough to know the idea worked.

## From Idea to MVP: The Workflow

### Step 1: Define the Hypothesis

Not "I want to build an app". Instead: "Freelancers in Porto waste 3 hours a week tracking invoices. A simple tool that generates invoices from time entries will save them time, and they'll pay €9/month for it."

### Step 2: Define the ICP (Ideal Customer Profile)

Who specifically:

- **Demographics:** Freelance web developers, 25 to 40 years old, based in Portugal
- **Pain:** Manually creating invoices in Word, losing track of billable hours
- **Current solution:** Excel spreadsheets, generic invoice templates
- **Willingness to pay:** Yes, if it saves more than an hour per week

### Step 3: Decide What to Build

Use the "What + Who + Metric" framework:

- **What:** A page where freelancers enter hours and generate a PDF invoice
- **Who:** Freelancers who track time in spreadsheets
- **Metric:** Number of invoices generated in the first week

### Step 4: Build It

With the skills from this book, a simple invoice generator takes a weekend:

- Mode 2 (Lovable/Bolt): describe the app, generate it, deploy
- Mode 3 (VS Code + AI): code it with React, use a PDF library, deploy to Vercel

### Step 5: Measure

Share with 10 freelancers in your network. Track: did they use it? Did they come back? Would they pay for it?

If nobody uses it, the hypothesis was wrong. That's fine. You spent a weekend, not six months. Pivot or move on.

## Real Project Examples

**mindmatch:** A 13-screen interactive prototype built to test whether people wanted AI-powered team compatibility analysis. No backend, no database, no real AI. Just clickable screens. The prototype alone generated enough interest to validate the concept before writing a single line of backend code.

**SleepLog:** Started as a form with three fields (bedtime, wake time, notes) storing data in Firebase. No accounts, no charts, no recommendations. Users logged sleep for a week. The data showed patterns. Then charts were added. Then accounts. Each feature was added because real usage demanded it, not because it seemed like a good idea.

**DadHelp:** Still a README. The idea exists. The specification is written. The code doesn't exist yet. And that's a legitimate stage. Not every idea deserves code on day one.

> **Try It:** Take any app idea (yours or a practice one). Write three sentences: what's the one thing it does, who's the one person it's for, and what's the one metric that proves it works. If you can't answer all three in one sentence each, the scope isn't tight enough yet.

## The "Ship, Then Learn" Principle

Traditional development: plan → build → launch. By the time you launch, your assumptions are six months old.

MVP development: plan (one page) → build (one weekend) → launch → learn → iterate.

The first version will be embarrassing. That's correct. If you're not slightly embarrassed by your first release, you waited too long to ship.

## Common Beginner Mistake

**Building for six months without showing anyone.** You imagine a complete product, build feature after feature, and launch to silence. Nobody wanted what you built, and you could have discovered that in week one with a landing page and a waitlist form. Ship the smallest thing. Get feedback. Adjust. Repeat.

## The Science Behind It

Three frameworks explain why the MVP approach works:

**Build-Measure-Learn (Eric Ries, The Lean Startup, 2011).** Innovation doesn't start with the solution. It starts with a hypothesis. You build the minimum necessary to test it. You measure real outcomes. You learn, then decide: pivot (change approach) or persevere (keep going). The classic error is skipping measurement and iterating on gut feeling.

**Jobs To Be Done (Clayton Christensen, Harvard Business School).** People don't buy products. They hire solutions for specific problems in their lives. Your user doesn't want "a sleep app." They want to understand why they wake up tired. When defining your ICP, the question isn't "who wants my product?" but "who has a problem this solves better than the current alternative?"

**Pareto's Principle applied to product (80/20).** 80% of a product's value comes from 20% of its features. If you identify which 20%, your MVP writes itself. Everything else goes on the "Later" list.

## Checklist

- [ ] I can state my hypothesis in one sentence
- [ ] I have an ICP (not "everyone")
- [ ] My MVP has ONE core feature and a clear "NOT IN MVP" list
- [ ] I can build and deploy the MVP in one to two weekends
- [ ] I have a metric that tells me whether the MVP works
- [ ] I show the MVP to real users within the first week

## Summary

An MVP is the smallest thing that tests whether people want what you're building. Three questions define it: what's the one thing it does, who's the one person it's for, and what's the one metric that proves it works. Scope creep kills projects through incremental additions that each seem reasonable. A written scope document with an explicit "NOT IN MVP" list prevents this. Build in a weekend, ship, measure, and iterate. If nobody uses the MVP, you saved months of wasted effort.

## Challenge

1. Pick an idea (yours or a practice one). Write a one-page scope document: the hypothesis, the ICP, the one feature, and the "NOT IN MVP" list. Set a time limit: this project must be deployable in one weekend.

2. Build the MVP. Deploy it. Share it with five people who match your ICP. Track one metric for one week. At the end of the week, decide: continue, pivot, or abandon.
