# Chapter 2 Your First AI Conversation That Produces Code

A friend was getting married. She wanted a simple page where guests could RSVP, pick between fish and meat, and leave a message. She asked me on a Tuesday. By Wednesday morning, the page was live, styled to match her wedding colours, with responses saved to a Google Sheet.

The entire thing was built through a conversation with Claude. No framework (a pre-built code structure, explained in Chapter 3), no terminal (the text-only interface for running commands, covered in Chapter 6), no build step. One HTML file with inline CSS and JavaScript, deployed on Vercel (Chapter 5 walks you through this). Total time: forty minutes.

This chapter teaches you how to have that conversation.

## Key Terms

| Term              | What it means                                                               |
| ----------------- | --------------------------------------------------------------------------- |
| **Prompt**        | The text you send to the AI                                                 |
| **Context**       | Background information that helps the AI understand the task                |
| **Constraints**   | Rules that limit what the AI produces (language, style, format)             |
| **System prompt** | The AI's built-in instructions (usually hidden, but you can influence them) |
| **Iteration**     | Going back and forth with the AI to refine the result                       |
| **Hallucination** | When the AI generates something plausible but wrong                         |

## The Four Ingredients of a Good Prompt

Every effective prompt has four parts. Miss one, and the result degrades:

**1. Task.** What do you want? Be specific. "Build me a webpage" is vague. "Build me a single-page RSVP form for a wedding, with fields for name, meal choice (fish/meat/vegetarian), and a free-text message" is concrete.

**2. Context.** What should the AI know? The wedding is on 15 June. The colours are sage green and cream. The couple's names are Ana and Pedro. Responses should be saved somewhere.

**3. Constraints.** What are the rules? Everything in one HTML file. No external dependencies. Mobile-friendly. The form must validate required fields before submission.

**4. Format.** What should the output look like? "Output the complete HTML file, ready to save and open in a browser. Include all CSS and JavaScript inline."

Putting it together:

```
Build me a single-page RSVP form for a wedding.

Context:
- Couple: Ana and Pedro
- Date: 15 June 2026
- Colours: sage green (#9CAF88) and cream (#FFFDD0)
- Fields: guest name, meal choice (fish/meat/vegetarian), message

Constraints:
- One HTML file with inline CSS and JS
- Mobile-friendly (responsive)
- Validate required fields (name, meal choice)
- When submitted, show a confirmation message

Format:
- Output the complete file, ready to save as index.html
```

### Your First JobPilot Prompt

Now let's write the first prompt for the project you're actually building. Same four ingredients, applied to JobPilot:

```
Build me a Chrome extension that auto-fills job application forms.

Context:
- The extension runs on Greenhouse job application pages
  (URLs like boards.greenhouse.io/*)
- It reads form fields (name, email, phone, resume) and fills them
  with data the user saved earlier
- The user stores their info through the extension's popup

Constraints:
- Chrome Manifest V3
- One content script, one background service worker, one popup
- Store user data in chrome.storage.local
- No external dependencies for the MVP

Format:
- Output manifest.json, content.js, background.js, popup.html,
  and popup.js as separate files
```

Five files came back. Save them in a folder. Load the extension in Chrome (chrome://extensions → Developer Mode → Load Unpacked → select your folder), open a Greenhouse job page, and the fields fill themselves. Forty minutes from prompt to working prototype. The prompt-run-fix cycle below is exactly how you'll refine it from here.

## The Prompt-Run-Fix Cycle

This is the core workflow of Mode 1:

1. **Prompt**: describe what you want
2. **Run**: copy the code, save it, open in browser
3. **Fix**: describe what's wrong, the AI corrects it

The fix step is where most beginners struggle. The AI generates something, it doesn't quite work, and the beginner stares at the screen not knowing what to say... Here's the secret: you don't need to diagnose the problem technically. You need to describe what you see.

"The form submits but nothing happens" is useful. "The background colour doesn't match what I asked for" is useful. "The layout breaks on mobile, the text overflows the card" is useful.

What doesn't work: "It's broken." The AI needs specifics. What did you expect? What happened instead?

## Describing Errors to AI

When code breaks, you'll see error messages. These are your most valuable debugging tool. Copy the entire error and paste it to the AI:

```
I saved the HTML file and opened it in Chrome. When I click the submit
button, nothing happens. The console shows this error:

Uncaught TypeError: Cannot read properties of null (reading 'value')
    at HTMLFormElement.<anonymous> (index.html:47)

Here's my code: [paste the code]
```

This gives the AI everything it needs: what you did, what happened, the exact error, and the code. The fix will come in seconds.

> **Try It:** Open ChatGPT or Claude right now. Paste: "Build me an HTML page with a button that counts how many times it's been clicked. Show the count on the page. Output everything in a single HTML file." Save the response as `counter.html`. Open it in Chrome. Click the button. If it works, you just completed your first prompt-run cycle. If it doesn't, describe what you see back to the AI. That's the fix step.

## Your First Project: Lisboa Walks

Let's build something real. A page for a walking tour company in Lisbon.

Open ChatGPT, Claude, or Gemini. Paste this:

```
Build me a landing page for a walking tour company called "Lisboa Walks".

Context:
- Small company offering guided walking tours in Lisbon
- Three tours: Alfama & Castelo (2h, €20), Belém & Discoveries (3h, €25),
  Bairro Alto Night Walk (1.5h, €15)
- Contact: lisboawalks@email.com

Requirements:
- Single HTML file with inline CSS and JS
- Hero section with company name and tagline
- Three tour cards with name, duration, price, and a "Book Now" button
- Contact section with email
- Responsive (works on mobile)
- Clean, modern design with a warm colour palette

Format:
- Output the complete HTML file, ready to save and open in a browser
```

Save the result as `index.html`. Open in your browser. You have a landing page.

Now iterate. Pick one thing that isn't right and describe it:

"The tour cards are stacked vertically even on desktop. Can you make them appear side by side in a row on screens wider than 768px?"

"The Book Now buttons don't do anything. When clicked, show an alert saying 'Booking for [tour name]. We will email you a confirmation at [email address].'"

"Add a smooth scroll effect when clicking the navigation links."

Each iteration improves the page. Three or four rounds of this, and you have something you could genuinely publish.

## Choosing a Model

Different AI models have different strengths for code:

| Model                 | Best for                                    | Notes                                              |
| --------------------- | ------------------------------------------- | -------------------------------------------------- |
| **Claude (Sonnet 4)** | Long code, full files, careful instructions | Follows constraints well, explains clearly         |
| **ChatGPT (GPT-4o)**  | General coding, broad knowledge             | Good all-rounder, sometimes verbose                |
| **DeepSeek (V3/R1)**  | Free, open-source alternative               | Went viral Jan 2025, strong at code, free API tier |
| **Gemini**            | Quick answers, integration with Google      | Free tier is generous                              |
| **Cursor / Copilot**  | Working inside an editor (Mode 3)           | Not for chat-style Mode 1 use                      |

For Mode 1 (chat-to-code), any of the first three works. Try all three with the same prompt. You'll develop a preference.

## Common Beginner Mistake

**Accepting the first result without reading.** The AI generates 150 lines. It looks like it works. You deploy it. A week later, someone fills in the form and their data goes nowhere because the submission handler was a placeholder.

Before declaring anything "done", open the code and scan for: TODO comments, placeholder URLs (`https://example.com`), mock data that should be real, and console.log statements that should be removed.

## Checklist

- [ ] I can write a prompt with the four ingredients (task, context, constraints, format)
- [ ] I know the prompt-run-fix cycle
- [ ] I can describe errors to the AI (what I expected, what happened, the error message)
- [ ] I've built and run my first project from an AI conversation
- [ ] I've iterated at least twice to improve the result

## Summary

Good prompts have four parts: task, context, constraints, and format. The core workflow is prompt, run, fix. Describing errors precisely ("the button does nothing, here's the console error") gets better fixes than vague complaints. Different models have different strengths. Always scan the generated code for TODOs, placeholders, and mock data before shipping.

## Challenge

1. Build the Lisboa Walks page from the prompt above. Deploy it to Vercel (Chapter 5 covers this). Share the URL.

2. Open a second AI (if you used Claude, try ChatGPT). Give the exact same prompt. Compare the two results: which is better designed? Which has cleaner code? Which followed the constraints more closely?
