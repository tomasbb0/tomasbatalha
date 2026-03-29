# Chapter 27 The Efficient Vibe Coder: Workflows, Tokens & the Art of Spending Less

> "Perfection is achieved, not when there is nothing more to add, but when there is nothing left to take away."
> (Antoine de Saint-Exupéry)

Ada Lovelace wrote her programs on paper. Every instruction had to be precise because there was no undo, no free tier, no retry. Each step cost her time, ink, and mental energy. She could not afford waste. Neither can you.

This chapter is about a principle that separates amateurs from professionals: **the less AI you use (without hurting your outcomes) the better.** Not because AI is bad, but because every AI call has a cost in money, time, and unpredictability. The best vibe coders build workflows where AI does the hard thinking and everything else is mechanical, automated, and free.

## Key Terms

| Term                    | What it means                                                                                               |
| ----------------------- | ----------------------------------------------------------------------------------------------------------- |
| **Token efficiency**    | Getting the same or better results with fewer AI tokens (shorter prompts, cached context, smarter routing)  |
| **Mechanical workflow** | A step in your process that runs without AI: a script, a keyboard shortcut, a template, a VS Code task      |
| **AI-necessary step**   | A step that genuinely requires intelligence: understanding intent, generating novel code, making a decision |
| **Task runner**         | A system that executes predefined commands automatically (VS Code Tasks, npm scripts, Make)                 |
| **Prompt template**     | A reusable prompt structure where you change only the variable parts                                        |
| **Context window**      | The maximum amount of text an AI model can process in one request (affects both cost and quality)           |
| **Cold start cost**     | The tokens wasted re-explaining context to AI because you lost your conversation thread                     |

## The Core Principle: AI vs Mechanical

Every step in your development workflow is either **AI-necessary** or **mechanical**. The goal is to make this distinction ruthlessly clear, and automate every mechanical step so AI never touches it.

| Step                                                  | AI-necessary? | Better approach                                     |
| ----------------------------------------------------- | ------------- | --------------------------------------------------- |
| "Create a new React component called UserCard"        | No            | VS Code snippet or file template                    |
| "Generate a complex algorithm for route optimisation" | Yes           | AI prompt with structured output                    |
| "Run ESLint and fix formatting"                       | No            | VS Code task on save (automatic)                    |
| "Figure out why the API returns 401"                  | Yes           | AI debugging with error context                     |
| "Create the boilerplate for a new API endpoint"       | No            | Code generator script or snippet                    |
| "Design the data model for a new feature"             | Yes           | AI conversation with schema output                  |
| "Commit and push to GitHub"                           | No            | Git alias or VS Code task                           |
| "Write unit tests for pure functions"                 | Maybe         | AI for first draft, then template for similar tests |

**The rule:** if a step can be done with a template, a script, a shortcut, or a task runner, it should never consume AI tokens.

Here's how that split looked when building JobPilot. The mechanical steps: creating new content script files (file template), running the Vite build (VS Code task bound to a shortcut), refreshing the unpacked extension in Chrome (a muscle memory click), and formatting code (ESLint auto-fix on save). The AI-necessary steps: figuring out how Workday structures its form fields (every ATS is different), writing the logic for matching profile data to unknown field names, and debugging why `chrome.storage` returned `undefined` in a content script (permissions issue). The mechanical steps happened hundreds of times. They had to be instant. The AI steps happened maybe 20 times total. They were worth waiting for.

Whatever you're building, the split is the same. Figure out which steps are repetitive (mechanical) and which require thinking (AI). Automate the first. Pay for the second.

## VS Code: The Efficiency Machine

Before you open an AI chat, configure your editor to do everything it can without AI. This saves tokens, saves money, and saves time (AI responses take seconds; keyboard shortcuts take milliseconds).

### Essential Efficiency Extensions

**1. VS Code Tasks (built-in, free)**

Tasks are commands that run with a single shortcut. Instead of typing `npm run dev` in the terminal every time, define a task:

```json
// .vscode/tasks.json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "Dev Server",
      "type": "shell",
      "command": "npm run dev",
      "group": { "kind": "build", "isDefault": true },
      "problemMatcher": ["$tsc"],
      "isBackground": true
    },
    {
      "label": "Lint & Fix",
      "type": "shell",
      "command": "npm run lint -- --fix",
      "group": "test"
    },
    {
      "label": "Deploy Preview",
      "type": "shell",
      "command": "vercel --no-wait",
      "group": "none"
    }
  ]
}
```

Press `Cmd+Shift+B` → your dev server starts. No AI tokens spent. No typing. No thinking.

**2. Task Sync / Project Manager**

When you switch between projects, you lose context. Task Sync extensions remember your workspace state: which terminals are open, which tasks are running, which files are active. This means you spend zero tokens re-explaining your project setup to AI.

Recommended extensions:

- **Project Manager** (`alefragnani.project-manager`): switch between projects instantly
- **Task Explorer** (`spmeesseman.vscode-taskexplorer`): visual task management
- **Auto Run Command** (`gabrielgriesser.autoruncommand`): run tasks automatically when opening a workspace

**3. Snippets (built-in, free)**

User snippets are templates that expand with a tab key. Define them once, use them forever:

```json
// .vscode/snippets/react.code-snippets
{
  "React Functional Component": {
    "prefix": "rfc",
    "body": [
      "interface ${1:Component}Props {",
      "  $2",
      "}",
      "",
      "export function ${1:Component}({ $3 }: ${1:Component}Props) {",
      "  return (",
      "    <div>",
      "      $0",
      "    </div>",
      "  );",
      "}"
    ],
    "description": "React Functional Component with TypeScript"
  }
}
```

Type `rfc`, press Tab, type the component name. A complete TypeScript React component appears. Zero AI tokens. Half a second.

**4. Multi-Cursor Editing**

Select a variable name → `Cmd+D` to select the next occurrence → repeat → type the new name. All instances update simultaneously. AI would charge you tokens to do a find-and-replace that VS Code does for free.

**5. Emmet (built-in for HTML/CSS)**

Type `div.container>ul>li*5>a.link` → press Tab → a complete HTML structure with 5 list items appears. Faster than describing it to AI, and cheaper by infinity percent.

### The Efficiency Stack

Set up these VS Code settings once and every project benefits:

```json
// .vscode/settings.json
{
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit",
    "source.organizeImports": "explicit"
  },
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "files.autoSave": "onFocusChange",
  "terminal.integrated.defaultProfile.osx": "zsh"
}
```

Every time you save a file: auto-format, auto-fix lint errors, auto-organise imports. No AI needed. No tokens spent. No manual work.

## Token Efficiency: The Math That Saves You Money

Chapter 20 (AI Costs & Rate Limits) covered API pricing. This section covers how to minimise tokens in your development workflow: the tokens you spend while building, not the tokens your app spends in production.

### 1. Stop Re-Explaining Context

The biggest token waste in vibe coding is re-explaining your project to the AI every time you start a new conversation.

**Bad workflow (expensive):**

```
New chat → "I'm building a React app with TypeScript, using Supabase
for the database, Tailwind for styling, deployed on Vercel. The app is
a review aggregation platform. I have a component called ReviewCard
that displays..." (500 tokens of context before you even ask your question)
```

**Good workflow (cheap):**

Create a project context file that you reference:

```markdown
<!-- .cursor/rules or project-context.md -->

# Project: ReviewOrb

- Stack: React + TypeScript + Supabase + Tailwind
- Deploy: Vercel
- Structure: /src/components, /src/lib, /src/pages
- Database: Supabase with tables: reviews, stores, stages
- Auth: Supabase Auth with Google OAuth
- Key patterns: server components, Tanstack Query for fetching
```

In Cursor, this file is automatically included as context. In ChatGPT/Claude, paste it once at the start of a session. Either way, you define it once instead of retyping it every conversation.

### 2. Prompt Templates Save Tokens

Instead of crafting a new prompt every time, use templates with variables:

**Bug fix template (save it in a file):**

```
## Bug Report
**What I expected:** [expected]
**What happened:** [actual]
**Error message:** [paste error]
**File:** [filename]
**Relevant code:** [paste 10-20 lines around the error]

Fix the bug. Show only the changed lines.
```

**Feature template:**

```
## New Feature
**What it does:** [description]
**Where it goes:** [file path]
**Inputs:** [what data it receives]
**Output:** [what it returns or renders]
**Constraints:** [must work with existing X, don't change Y]

Generate the implementation. TypeScript. Follow existing patterns in the codebase.
```

Each template eliminates 100-200 tokens of ad hoc explanation. Over 50 prompts a day, that is 5,000-10,000 tokens saved, roughly €0.50/day with GPT-4o, or €15/month.

### 3. Ask for Diffs, Not Full Files

When you ask AI to fix a bug in a 200-line file, it often regenerates the entire file: 200 lines of output tokens you already had. Instead:

**Expensive:**

```
"Fix the bug in this file: [paste 200 lines]"
→ AI returns 200 lines (you pay for 200 lines of output)
```

**Cheap:**

```
"Fix the bug in this file. Show ONLY the changed lines with 3 lines
of context above and below. Do not regenerate the entire file."
→ AI returns 10-15 lines (you pay for 15 lines of output)
```

Output tokens cost 4-6x more than input tokens on most models. Reducing output is the most impactful efficiency move.

### 4. Use the Right Model for the Right Task

| Task                                                       | Best model                   | Why                               |
| ---------------------------------------------------------- | ---------------------------- | --------------------------------- |
| "Explain what this error means"                            | GPT-4o-mini / Claude Haiku 4 | Simple comprehension, cheap       |
| "Generate a complex React component with state management" | GPT-4o / Claude Sonnet 4     | Needs reasoning ability           |
| "Rename this variable across the file"                     | No AI needed                 | Use VS Code Rename Symbol (F2)    |
| "Write a regex for email validation"                       | GPT-4o-mini                  | Pattern matching, well-known task |
| "Design the database schema for a marketplace"             | GPT-4o / Claude Sonnet 4     | Needs architectural reasoning     |
| "Format this JSON"                                         | No AI needed                 | Prettier does it on save          |

### 5. The Conversation Checkpoint

Long AI conversations accumulate context. After 20 messages, the AI processes thousands of tokens of history with every new message. The fix:

1. After reaching a milestone, ask the AI: _"Summarise what we've built and decided so far in a compact bullet list."_
2. Start a new conversation, paste only the summary.
3. Continue from there.

This "conversation checkpoint" cuts context by 80-90% while preserving all decisions.

## The Efficient Workflow: A Complete System

Here is a complete development workflow that minimises AI usage while maximising output:

### Morning Setup (0 AI tokens)

1. `Cmd+Shift+P` → "Project Manager: Open" → select project
2. `Cmd+Shift+B` → dev server starts (VS Code task)
3. Git pull (terminal alias: `gpl`)

### Feature Development (targeted AI only)

1. **Plan the feature** (AI-necessary): one prompt with the feature template. Get the design back.
2. **Create boilerplate** (mechanical): VS Code snippet → `rfc` for component, file template for API route.
3. **Implement logic** (AI-necessary): prompt the AI with the specific logic needed. Ask for diffs only.
4. **Style the component** (mechanical): Tailwind classes. If complex, AI for initial layout only.
5. **Test** (mixed): AI generates the first test, then copy-paste-modify for similar tests.
6. **Lint and format** (mechanical): happens automatically on save.
7. **Commit** (mechanical): `Cmd+Shift+G` → stage → commit message → push.

### Debugging (targeted AI only)

1. **Read the error** (mechanical): check the terminal, check DevTools.
2. **If obvious** (mechanical): fix it yourself. Typo, missing import, wrong prop name.
3. **If not obvious** (AI-necessary): use the bug fix template. Paste error + 20 lines of context. Ask for diff.

### End of Day (0 AI tokens)

1. `git stash` or `git commit -m "WIP: [feature]"`
2. Close workspace (Task Sync remembers state)

**Result:** AI touches maybe 30% of your workflow. The other 70% runs on muscle memory, shortcuts, templates, and automation. Your AI bill drops. Your speed increases. Your output is more predictable.

## Saving Money on the Apps You Ship

Token efficiency is not just about your development process. The apps you ship to users consume tokens too. Chapter 20 (AI Costs & Rate Limits) covered model routing and caching. Here are the workflow-level strategies:

### 1. Pre-compute what you can

If your app summarises articles, and the same article gets summarised 50 times by different users, that is 50x the cost. Pre-compute the summary once, store it in the database, serve it forever.

```javascript
async function getArticleSummary(articleId) {
  // Check database first
  const cached = await db.summaries.findOne({ articleId });
  if (cached) return cached.summary; // Free

  // Only call AI for new articles
  const article = await db.articles.findOne({ articleId });
  const summary = await callAI("gpt-4o-mini", `Summarise: ${article.text}`);

  // Store for future requests
  await db.summaries.insert({ articleId, summary, createdAt: new Date() });
  return summary; // First call costs money, all future calls are free
}
```

### 2. Degrade gracefully with local logic

Not everything needs AI. A "smart" search that calls GPT for every query is expensive. A search that uses database full-text search for simple queries and only escalates to AI for complex natural language queries is 90% cheaper:

```javascript
async function search(query) {
  // Simple keyword search: use database (free)
  if (query.split(" ").length <= 3 && !query.includes("?")) {
    return await db.items.search(query);
  }

  // Complex natural language query: use AI (costs money)
  const structured = await callAI(
    "gpt-4o-mini",
    `Convert this search query to database filters: "${query}"`,
  );
  return await db.items.find(JSON.parse(structured));
}
```

### 3. Batch user requests

If your app processes uploaded documents, process them in batches during off-peak hours using the Batch API (50% cheaper on OpenAI). The user uploads at 2pm, the processing runs at 3am, results are ready by morning.

### 4. Set spending alerts from Day 1

```
OpenAI: Settings → Billing → Set hard limit at €50/month
Anthropic: Settings → Billing → Set monthly limit
Gemini: Google Cloud → Billing → Set budget alert
```

A runaway loop that calls the API 10,000 times in a minute will cost you €500 with GPT-4o. A €50 hard limit means the API stops responding before your wallet empties. Set this before your first deployment. Not after.

## The Philosophy: Less AI, Better Outcomes

This chapter's central argument is counterintuitive in a book about AI-assisted development: **use less AI.**

Not because AI is bad. But because:

1. **AI is non-deterministic.** The same prompt can produce different outputs. Mechanical steps always produce the same result.
2. **AI is slow.** A VS Code snippet expands in 50 milliseconds. An AI response takes 2-5 seconds.
3. **AI costs money.** Every token has a price. Keyboard shortcuts are free forever.
4. **AI introduces variability.** When AI touches a step, the outcome becomes less predictable. When a template handles it, the outcome is identical every time.

The best vibe coders are not the ones who use the most AI. They are the ones who use AI only where it makes an irreplaceable difference (creative decisions, complex reasoning, novel problem-solving) and handle everything else with fast, free, deterministic tools.

Ada Lovelace understood this instinctively. The Analytical Engine was powerful but expensive to operate. Every instruction had to earn its place. She would not waste a punch card on a task that could be done by hand. The same principle applies to every token you send to an AI model in 2026.

## Common Beginner Mistake

**Using AI for everything.** New vibe coders are so excited about AI that they use it to rename variables, format code, and create folder structures (tasks that VS Code handles instantly and for free). This wastes money, wastes time, and builds a fragile dependency on AI for tasks that should be muscle memory. Learn your editor. Then use AI for what your editor cannot do.

## Checklist

- [ ] I have VS Code Tasks configured for build, dev server, lint, and deploy
- [ ] I have user snippets for my most common code patterns (components, API routes, tests)
- [ ] I have format-on-save and auto-fix-on-save enabled
- [ ] I have a `.cursor/rules` or project context file for AI conversations
- [ ] I use prompt templates for bug fixes, feature requests, and code reviews
- [ ] I ask for diffs instead of full file regeneration
- [ ] I use the cheapest model appropriate for each task
- [ ] I use conversation checkpoints for long AI sessions
- [ ] I have spending limits set on all AI providers
- [ ] I pre-compute and cache AI responses in production apps

## Summary

The best vibe coder is not the one who uses the most AI. It is the one who uses AI only where it is irreplaceable. Every development step is either AI-necessary (requires intelligence) or mechanical (can be automated). Mechanical steps should be handled by VS Code tasks, snippets, keyboard shortcuts, and scripts, not AI tokens. Token efficiency compounds: prompt templates, context files, diff-only responses, conversation checkpoints, and model routing all reduce costs. In production apps, pre-computing responses, degrading gracefully to non-AI logic, batching requests, and setting spending limits keep costs manageable. The philosophy is simple: the less AI touches your workflow without hurting outcomes, the faster, cheaper, and more predictable you become.

## Challenge

1. **Audit your workflow.** List every step you take when building a feature (from opening the editor to committing code). Mark each step as "AI-necessary" or "mechanical." For every mechanical step, find or create a VS Code shortcut, snippet, task, or script that handles it without AI.

2. **Set up the efficiency stack.** Configure your VS Code with: format-on-save, lint-on-save, at least 3 user snippets, at least 3 VS Code tasks, and a `.cursor/rules` context file. Measure your token usage before and after for one week.

3. **Calculate your savings.** Track your AI API spending for one week. Implement prompt templates and diff-only responses. Track spending for the following week. Calculate the percentage reduction.
