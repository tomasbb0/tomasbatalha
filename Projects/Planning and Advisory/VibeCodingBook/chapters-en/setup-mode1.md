# Mode 1 Setup: Chat & Copy-Paste

Before you write a single line of code, you need the right tools. In Mode 1, your only tool is an AI chat. No IDEs, no terminals, no installations. Just you, a browser, and a smart conversation.

This chapter walks through every option, how to set them up, and which to choose.

## ChatGPT (OpenAI)

ChatGPT was the first mainstream LLM and remains the most popular for code generation.

### Available plans

| Plan     | Price   | Model             | Limit              |
| -------- | ------- | ----------------- | ------------------ |
| **Free** | $0      | GPT-4o mini       | ~15 msgs/3h        |
| **Plus** | $20/mo  | GPT-4o, o3        | ~80 msgs/3h        |
| **Pro**  | $200/mo | o1 pro, unlimited | No practical limit |

### How to set up

1. Go to [chat.openai.com](https://chat.openai.com)
2. Create an account with email or Google
3. On the free plan, you already have GPT-4o mini , enough to start
4. For serious projects, Plus is worth it for GPT-4o and o3 access

### Tips for code generation

- Use **Custom Instructions** to set your context: "I'm a beginner, explain everything, give me complete working code"
- Create a **Custom GPT** for your project with fixed instructions
- Use **Canvas** mode to edit code directly in the interface

## Claude (Anthropic)

Claude is ChatGPT's strongest competitor for programming. Many developers prefer it for the quality of its explanations and generated code.

### Available plans

| Plan     | Price  | Model                 | Limit         |
| -------- | ------ | --------------------- | ------------- |
| **Free** | $0     | Claude Sonnet 4       | ~20 msgs/day  |
| **Pro**  | $20/mo | Claude 4 Opus, Sonnet | ~100 msgs/day |

### How to set up

1. Go to [claude.ai](https://claude.ai)
2. Create an account
3. Claude's **Projects** let you attach files and give permanent context , use them
4. Claude has a huge context window (200K tokens), ideal for large projects

### Why consider Claude

- Clearer, more pedagogical explanations
- Better at following complex instructions
- **Artifacts** mode lets you see rendered code in real time

## Gemini (Google)

Gemini is integrated into Google's suite of tools. If you use Google Workspace, it's the most convenient option.

### How to access

1. Go to [gemini.google.com](https://gemini.google.com)
2. Gemini Advanced ($21.99/mo) gives access to Gemini Ultra with 1M token context
3. Integrates with Google Docs, Sheets, and Gmail

### When to use Gemini

- If your project involves Google Sheets add-ons or Google Apps Script
- If you want to generate code directly inside Google Docs
- For tasks that need integrated web search

## Other models

### Perplexity

Combines AI with real-time web search. Useful when you need code that uses specific APIs or recent libraries.

### Grok (xAI)

Available on X (Twitter). Less tested for code but functional for simple tasks.

### Open-source models (Llama, Mistral)

You can run models locally via Ollama, but that enters Mode 3 territory. For Mode 1, stick to online chats.

## Which one to choose?

| Situation                    | Recommendation                         |
| ---------------------------- | -------------------------------------- |
| First time using AI for code | ChatGPT Free                           |
| Want the best code quality   | Claude Pro                             |
| Heavy Google ecosystem user  | Gemini Advanced                        |
| Need web search + code       | Perplexity                             |
| Zero budget                  | ChatGPT Free + Claude Free (alternate) |

**My recommendation**: start with free ChatGPT. When you hit the limits (and you will), try Claude. Most people end up using both in parallel.

## How to structure your prompts

In Mode 1, your code quality depends 100% on your prompt quality. Here are the principles:

### 1. Be specific about what you want

**Bad**: "Make me a website"

**Good**: "Create an HTML page with a contact form that has fields for name, email and message. When the user submits, show a success message. Use modern CSS with dark colours."

### 2. Ask for complete code

Always say: "Give me the complete file, ready to copy and use." You don't want fragments , you want something that works when you paste it into a file.

### 3. Iterate instead of starting over

If the code doesn't do exactly what you want, don't start a new chat. Say: "Great, now add X" or "Change the button colour to blue and increase the title size."

### 4. Ask for explanations

"Explain what each part of this code does, as if I were 15 years old." This is an investment , the more you understand, the better your next prompts.

## Browser setup

For Mode 1, your browser is your IDE. Set it up:

1. **Always open DevTools** (Cmd+Option+I on Mac, F12 on Windows) , you'll need them to see errors
2. **Create a folder on your desktop** called "projects" , this is where you'll save the files the chat gives you
3. **Use Chrome** , it has the best developer tools

## Summary

In Mode 1, your stack is:

- A browser (Chrome recommended)
- An AI chat (ChatGPT or Claude)
- A text editor (even TextEdit/Notepad works)
- A folder to save files

That's it. No installations, no complex configurations, no developer accounts. Open the chat and start building.

## Challenge

1. Open ChatGPT or Claude. Ask it to generate a complete HTML page for a personal portfolio (your name, a short bio, three fictional projects). Save the response as `index.html` on your desktop. Open it in Chrome. You just built something.

2. Now break it on purpose. Delete a closing `</div>` tag. Open the file again. Press Cmd+Option+I to open DevTools. Look at the Console tab for errors. See the difference between a working page and a broken one.
