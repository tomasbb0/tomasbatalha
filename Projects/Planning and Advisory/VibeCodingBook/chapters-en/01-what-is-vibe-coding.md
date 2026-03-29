# Chapter 1 What Vibe Coding Actually Is (and the Two Fatal Mistakes)

Six months ago, a friend asked me to build her a small app. She's a therapist. She wanted a page where clients could book sessions, pick a time slot, and pay. Nothing complex. I opened Claude, described what she needed in three paragraphs, and twenty minutes later had a working prototype with a calendar, a payment form, and a confirmation email.

She looked at the screen and asked: "You made this? Right now?"

That reaction is vibe coding. The speed is real. The excitement is real. The danger, though... comes later.

## Key Terms

| Term               | What it means                                                                              |
| ------------------ | ------------------------------------------------------------------------------------------ |
| **Vibe coding**    | Building software by describing what you want to AI, rather than writing code from scratch |
| **Mode 1**         | Chat-to-Code: you describe, AI generates, you copy and run                                 |
| **Mode 2**         | Prompt-to-App: a platform builds the full app from your description                        |
| **Mode 3**         | AI-Supercharged IDE: you work in an editor with AI assisting every step                    |
| **Blindness**      | Fatal mistake #1: not understanding what the AI wrote                                      |
| **Overconfidence** | Fatal mistake #2: trusting the AI with things it's bad at                                  |

## Three Ways to Build

Think of a woodworking workshop. Three people enter. Each uses a different approach.

The first picks up a hammer. Describes what they want to a colleague, who cuts the pieces and hands them over. The first person places the pieces and checks the result. Quick, limited, dependent on the colleague's interpretation. **That's Mode 1.** ChatGPT, Claude, Gemini: you describe, they generate, you assemble.

The second sits at a CNC machine. Types measurements into a control panel. The machine cuts, drills, and shapes automatically. Impressive output, but when the machine makes an unexpected cut, the operator can't intervene manually. **That's Mode 2.** Lovable, Bolt, v0: you describe a complete app, the platform builds it.

The third uses a power lathe. They hold the piece, the machine does the heavy rotation, but the craftsperson guides every cut. Full control, full speed, full responsibility. **That's Mode 3.** Cursor, VS Code with Copilot: AI assists, you decide.

|                          | Mode 1               | Mode 2                    | Mode 3          |
| ------------------------ | -------------------- | ------------------------- | --------------- |
| **Speed**                | Minutes              | Hours                     | Days            |
| **Ceiling**              | ~500 lines           | MVPs                      | Unlimited       |
| **Understanding needed** | None                 | Low                       | Medium          |
| **When it breaks**       | You're stuck         | Platform support          | You can debug   |
| **Best for**             | Scripts, automations | Landing pages, dashboards | Production apps |

JobPilot started as a Mode 1 experiment. I described a Chrome extension to Claude in a chat, copied the code, and got a basic popup working in an hour. Two weeks later, the project had outgrown Mode 1 entirely. Content scripts, a service worker, message passing between browser contexts, OAuth2 authentication, and OpenAI API calls. That's Mode 3 territory.

You'll follow the same path in this book. The early chapters use Mode 1 to get JobPilot's first pieces working fast. By Part III, you'll be in Mode 3, wiring up the real architecture. The progression isn't always clean, and it doesn't have to be.

## Bring Your Own Expertise

The AI writes the code. You bring the thing it can't have: domain expertise. Five years in healthcare, ten years in hospitality, a lifetime of knowing why a particular process is broken. That's the competitive advantage. Vibe coding turns industry knowledge into working software.

Every chapter has examples and challenges you can apply directly to your own domain. If you don't have a specific idea yet, Chapter 2 (Your First AI Conversation) gets you started with something concrete, and Chapter 23 (MVP & Scope) teaches you how to decide what's worth building.

## The Two Fatal Mistakes

### Mistake #1: Blindness

The AI generates 200 lines of JavaScript. It works. You ship it. Two weeks later, a user reports that their data is visible to other users. You open the code and realise you can't read it. You don't know where the authentication is (the part that checks who's logged in, covered in Chapter 19), where the database query runs (Chapter 9 explains data storage), or why it's exposing data. You're blind.

Blindness doesn't mean you need to write code from scratch. It means you need to read it. Chapter 3 (Reading Code Without Writing It) teaches you exactly that.

### Mistake #2: Overconfidence

The AI is extraordinarily good at generating code that looks right. It compiles. It runs. The interface is polished. But inside, it might have security holes, performance problems, or logic errors that only surface under real usage.

Think of hiring a builder for your house. The builder works fast, produces results that look impressive, and charges less than anyone else. But the builder occasionally skips structural calculations. If you can't read a blueprint, you won't catch the missing beam until the ceiling cracks.

The AI is that builder. Brilliant, fast, cheap, and occasionally structurally unsound. Your job is to read the blueprint.

## What I Built (and What Broke)

In one year, I built over a dozen projects. Every single one had moments where the AI generated something broken:

- JobPilot's first version stored API keys in the content script (don't worry about what these terms mean yet; Chapter 14 covers APIs and Chapter 17 covers browser extensions)
- SleepLog's authentication worked in Chrome but failed silently in Safari (Chapter 19 explains authentication properly)
- ReviewOrb's first Supabase setup had no Row Level Security (any user could read all data; you'll learn about database security in Chapter 18)
- BrowserPilot's first agent loop had no iteration limit (it made 200 requests to an AI service in 3 minutes, costing real money; Chapter 20 covers AI costs)

None of these were caught by the AI. All of them were caught by understanding enough to review what the AI produced. That's what this book teaches.

## What This Book Will Teach You

Three things, in order:

**1. Read.** Understand what the AI generates. Not write, not from scratch, but read: what does this code do, where does data go, what could break. (Parts I and II)

**2. Guide.** Give the AI better instructions. Structure projects, choose the right tools, break problems into pieces the AI can handle. (Parts II and III)

**3. Survive.** Keep your app running in the real world. Security, testing, deployment, monitoring, costs. (Parts IV and V)

## The Three Tiers

The book is structured in three progressive tiers that match the three modes:

**Tier 1 (Part I): Quick Wins.** After completing this, you can chat with AI, generate working code, and deploy simple projects. You operate at Mode 1.

**Tier 2 (Parts I + II): Build Real Apps.** After completing this, you can build and ship multi-feature apps using platforms and basic coding. You operate at Mode 2.

**Tier 3 (Parts I + II + III): Go Pro.** After completing this, you can vibe code at a professional level. You operate at Mode 3, with full IDE control.

Parts IV and V add the survival and shipping skills needed for production.

You can stop at any tier and have gained real, usable skills. The book is designed to be useful at every level, not just at the end.

## Challenge

Open ChatGPT, Claude, or Gemini. Describe a simple webpage: "Build me a personal portfolio page with my name, a photo placeholder, three project cards, and a contact form. Use clean, modern CSS. Output everything in a single HTML file."

Copy the entire response. Save it as `index.html`. Open it in your browser.

You just built something. Now ask yourself: if the contact form stopped working tomorrow, would you know where to look?

That question is the reason this book exists.
