# Epilogue: The Theory of Three Modes

This book was built around a simple idea: there are three ways to build software with AI, each with its own level of control, complexity, and potential. I called them modes, not because they're mandatory stages, but because they represent different ways of working.

## The Three Modes

### Mode 1: Chat & Copy-Paste

You open ChatGPT or Claude, describe what you want, receive code, copy it to a file, and open it in your browser. It's the entry point. You don't need to install anything, you don't need to know what a terminal is, and in 30 minutes you have something functional.

The ceiling is low , it works for static pages, simple scripts, small browser extensions. But the floor is too: anyone can start today.

### Mode 2: Visual Platforms

When copy-paste is no longer enough, you move to platforms like Lovable, Bolt, or v0. Here you describe the app and the platform generates, hosts, and runs everything. You get database, authentication, and automatic deployment without touching an IDE.

The ceiling is medium , complete MVPs, landing pages with backend, internal tools. Most of the ideas you have probably fit here.

### Mode 3: IDE + AI Assistants

When you need full control , custom architecture, complex integrations, optimised performance , you enter VS Code or Cursor with Copilot or Claude as your co-pilot. It's slower, requires more knowledge, but has no ceiling.

## The Progression Is Not Linear

Most people assume they should start at Mode 1, move to 2, and eventually reach 3. It doesn't work that way. A project can start at Mode 2 (quick prototype), evolve to Mode 3 (when it needs advanced features), and have components that go back to Mode 1 (a quick script to automate a task).

The modes coexist. Even within the same project.

## My Story

I'm 26 years old. My career spans Google, Amazon, EY, and the co-founding of Pairwire, an AI due-diligence platform.

I hold an MSc in Management from Rotterdam School of Management and a BSc from Nova SBE. Zero programming training.

In a two-month period, I built 21 applications, all in production, all functioning, all used by real people. Chrome extensions, web platforms, desktop apps, sales tools, Google Sheets add-ons.

I don't say this to brag. I say it because it's proof that the three modes work. If I could do it, so can you. And now you have an entire book showing you how.

JobPilot was one of those 21. It started as a Mode 1 experiment ("can AI write a Chrome extension for me?"), grew into a Mode 3 project with a service worker, content scripts, OAuth, OpenAI integration, and a Chrome Web Store listing. It hit every chapter in this book: architecture, APIs, security, testing, deployment, distribution, cost management.

If you followed the book from start to finish, you built it too. From a single prompt in Chapter 2 to a published product in Chapter 24. That's the arc. That's what vibe coding makes possible.

## What I Hope You Take From This Book

Three things:

1. **Knowing when to use each mode.** There's no "best" mode, there's the right mode for each situation
2. **Knowing how to read code without writing it.** This is the difference between someone who builds with AI and someone who gets stuck when something breaks
3. **Knowing how to ship.** An app that only exists on your computer doesn't count

The rest is practice. And practice starts now.

## Ada's Unfinished Machine

This book began with a quote from Ada Lovelace, and it ends with her story.

Ada wrote the world's first computer programs for a machine that was never built. She saw the future clearly. A general-purpose computer that could manipulate not just numbers but symbols, music, anything expressible in logic. She was right about everything. And she never saw any of it run.

You have something Ada did not: the machine exists. It is on your desk, in your pocket, in the cloud. The tools are free or cheap. The AI writes code on command. The deployment takes minutes.

Ada had the vision but not the machine. You have the machine. This book gave you the vision. The only thing left... is to build.
