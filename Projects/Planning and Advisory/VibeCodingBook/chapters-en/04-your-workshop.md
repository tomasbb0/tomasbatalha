# Chapter 6 Your Workshop: Tools That Don't Require a CS Degree

Every trade has a workshop. Carpenters have workbenches, saws, and clamps. Vibe coders have an editor, a terminal, and extensions. This chapter sets up your tools, explains what each does, and gets you ready to build.

## Key Terms

| Term          | What it means                                                                |
| ------------- | ---------------------------------------------------------------------------- |
| **VS Code**   | The most popular code editor (free, made by Microsoft)                       |
| **Cursor**    | VS Code fork with AI deeply integrated into every feature                    |
| **Terminal**  | A text interface for running commands (inside the editor or standalone)      |
| **Extension** | A plugin that adds features to the editor                                    |
| **CLI**       | Command-Line Interface: interacting with the computer through typed commands |
| **npm**       | Node Package Manager: installs JavaScript libraries and tools                |
| **git**       | Version control system that tracks changes to files                          |

## The Editor: VS Code vs Cursor

**VS Code** is the standard. Free, open-source, used by most professional developers. It has an enormous extension marketplace, excellent terminal integration, and works on macOS, Windows, and Linux.

**Cursor** is VS Code with AI built into the core. It uses the same interface, the same extensions, the same settings, but adds AI features everywhere: inline code generation, chat with your codebase, multi-file editing from a single prompt.

Which to use: **Cursor for vibe coding** (the AI integration is beyond anything you can achieve with extensions alone). **VS Code when you need stability** or when a project has specific tooling that Cursor doesn't yet support well.

You can use both. They share the same settings and extensions; switching takes seconds.

## The Terminal: 10 Commands That Cover 90%

The terminal looks intimidating. It's a text box where you type commands instead of clicking. But for vibe coding, you need about ten commands:

| Command | What it does                    | Example                            |
| ------- | ------------------------------- | ---------------------------------- |
| `cd`    | Change directory                | `cd my-project`                    |
| `ls`    | List files in current directory | `ls`                               |
| `mkdir` | Create a folder                 | `mkdir new-project`                |
| `touch` | Create an empty file            | `touch index.html`                 |
| `cat`   | Show file contents              | `cat package.json`                 |
| `rm`    | Delete a file                   | `rm old-file.js`                   |
| `cp`    | Copy a file                     | `cp template.html page.html`       |
| `mv`    | Move or rename a file           | `mv old.js new.js`                 |
| `npm`   | Install packages, run scripts   | `npm install`, `npm run dev`       |
| `git`   | Version control commands        | `git add .`, `git commit -m "msg"` |

These cover file management, package installation, and version control. You'll use `cd`, `npm`, and `git` daily. Don't worry about mastering npm or git yet: Chapter 15 covers dependencies in depth, and Chapter 16 teaches Git from scratch.

## Essential Extensions

Install these in VS Code or Cursor:

**ESLint**: catches JavaScript errors and style issues as you type. Red underlines mean something's wrong.

**Prettier**: auto-formats your code on save. Consistent indentation, consistent spacing, no arguments about style.

**Live Server**: right-click an HTML file and open it in a browser. Changes update automatically.

**GitLens**: shows who changed what line of code, and when. Useful for understanding a project's history.

## AI Tools Comparison

| Tool               | Type             | Best for                                                                                                                  | Cost                  |
| ------------------ | ---------------- | ------------------------------------------------------------------------------------------------------------------------- | --------------------- |
| **Cursor**         | Editor + AI      | Full vibe coding workflow (Mode 3). Composer generates multi-file changes; Agent mode runs terminal commands autonomously | Free tier + $20/month |
| **Windsurf**       | Editor + AI      | Cursor alternative with generous free tier                                                                                | Free tier + $15/month |
| **GitHub Copilot** | Editor extension | Line-by-line code completion, Copilot Chat for Q&A                                                                        | $10/month             |
| **Claude Code**    | Terminal agent   | Anthropic's CLI coding agent. Reads your codebase, makes edits, runs tests                                                | Pro plan ($20/month)  |
| **ChatGPT**        | Chat             | Mode 1 conversations, explaining code                                                                                     | Free tier + $20/month |
| **Claude**         | Chat             | Long code generation, following precise instructions                                                                      | Free tier + $20/month |
| **DeepSeek**       | Chat             | Free, open-source, strong at code                                                                                         | Free                  |
| **Gemini**         | Chat             | Quick questions, Google ecosystem                                                                                         | Free tier             |

For Mode 1 (chat-to-code), ChatGPT, Claude, or DeepSeek. For Mode 2 (prompt-to-app), Lovable or Bolt (Chapter 7, Platform Building). For Mode 3 (IDE), Cursor or Windsurf. Claude Code sits between Mode 1 and Mode 3: it works in your terminal, reads your project files, and makes edits directly.

JobPilot is built in Cursor. Here's the setup you'll use for the rest of the book: ESLint catching errors in content scripts (Chrome extensions have strict rules about what code can run where), Prettier keeping 15+ files formatted consistently, and a second monitor (or a split screen) showing chrome://extensions with the unpacked extension loaded. Every time you save a file, you hit the refresh button on the extension card and test on a live Greenhouse page. The integrated terminal runs the Vite build (more on that in Chapter 15). No switching between apps. Everything in one window.

## Setting Up Your First Project

Open your terminal (inside VS Code/Cursor: `Ctrl+` `on Windows,`Cmd+` ` on macOS):

```bash
mkdir my-first-project
cd my-first-project
touch index.html style.css script.js
```

You now have a project folder with three files. Open the folder in VS Code/Cursor (File → Open Folder).

Install Live Server extension, right-click `index.html`, select "Open with Live Server". A browser window opens. Every time you save a file, the page updates automatically.

This is your workshop. Editor on the left, browser on the right, terminal at the bottom.

> **Try It:** Open VS Code or Cursor. Press Ctrl+` (or Cmd+` on macOS) to open the integrated terminal. Type `pwd` to see where you are. Type `mkdir test-project && cd test-project && touch index.html`. You just created a project folder and a file without leaving the editor. This is faster than using Finder or File Explorer. Get used to it.

## Common Beginner Mistake

**Not using the integrated terminal.** Beginners open a separate terminal app, go to the project folder manually, then switch between windows. VS Code and Cursor have terminals built in, already set to the project directory. Use them.

## Checklist

- [ ] I have VS Code or Cursor installed
- [ ] I can open the integrated terminal
- [ ] I know the 10 essential commands (cd, ls, mkdir, touch, cat, rm, cp, mv, npm, git)
- [ ] I've installed ESLint, Prettier, Live Server, and GitLens
- [ ] I've created a project folder with HTML, CSS, and JS files

## Summary

VS Code is the standard editor; Cursor adds deep AI integration; Windsurf is a strong free alternative. The terminal needs ten commands for daily work. Four extensions (ESLint, Prettier, Live Server, GitLens) cover most needs. For vibe coding, Cursor or Windsurf handles Mode 3, Claude Code works from the terminal, ChatGPT/Claude/DeepSeek handle Mode 1, and Lovable/Bolt handle Mode 2. The integrated terminal is your command centre.

## Challenge

1. Create a project folder. Inside it, create index.html, style.css, and script.js. Open in VS Code or Cursor with Live Server. Write "Hello World" in the HTML and confirm it appears in the browser.

2. In the terminal, practise all ten commands: create a folder, navigate into it, create a file, list the contents, read the file, copy it, rename it, delete it.
