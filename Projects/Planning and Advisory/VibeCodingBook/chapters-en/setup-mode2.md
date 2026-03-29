# Mode 2 Setup: Visual Platforms

In Mode 2, you leave the chat and enter platforms that generate complete applications from prompts. The fundamental difference: instead of receiving code to copy, you get a working app with interface, database, and automatic deployment.

This chapter compares every option, shows how to set up each one, and helps you choose.

## Platform overview

| Platform      | Speciality           | Starting price | Generated stack  |
| ------------- | -------------------- | -------------- | ---------------- |
| **Lovable**   | Full web apps        | Free (limited) | React + Supabase |
| **Bolt.new**  | Quick prototypes     | Free (limited) | React/Next.js    |
| **v0.dev**    | UI components        | Free (limited) | React + Tailwind |
| **Base44**    | Data-driven apps     | Free (limited) | React + backend  |
| **VibeCo.de** | Apps from voice/text | Free (limited) | Varies           |
| **Replit**    | Full browser IDE     | Free (limited) | Any language     |

## Lovable

Lovable (formerly GPT Engineer) is the most complete platform for creating web applications from prompts.

### How to set up

1. Go to [lovable.dev](https://lovable.dev)
2. Create an account (Google or email)
3. On the free plan, you get ~5 projects
4. Each project generates a React app with Supabase as backend

### What Lovable does well

- Generates complete, beautiful interfaces
- Integrates authentication and database automatically
- Lets you iterate with natural language: "Add a user table"
- Automatic deployment with public URL

### Limitations

- Generated code can be hard to maintain outside the platform
- Paid plans needed for serious projects (~$20/mo)
- Less control over architecture

## Bolt.new

Bolt.new (by StackBlitz) is focused on speed. Ideal for prototypes.

### How to set up

1. Go to [bolt.new](https://bolt.new)
2. Login with StackBlitz or Google account
3. Start typing what you want , the app appears in real time

### Differences vs Lovable

- Faster for simple prototypes
- Weaker for complex apps
- Better framework integration (Next.js, Remix)
- Easy code export

## v0.dev

v0 (by Vercel) specialises in generating interface components.

### How to set up

1. Go to [v0.dev](https://v0.dev)
2. Login with Vercel account
3. Describe the component you want: "A sales dashboard with charts"

### When to use v0

- When you need specific UI components, not full apps
- For generating landing pages quickly
- For design inspiration

## Base44

Base44 excels at creating applications with solid database backends.

### How to set up

1. Go to [base44.com](https://base44.com)
2. Create an account
3. Define your data model and the platform generates the app around it

### Strength

- Ideal for CRUD apps (create, read, update, delete data)
- Good for internal tools and dashboards

## VibeCo.de

VibeCo.de lets you create applications from text or voice descriptions.

### How to set up

1. Go to [vibeco.de](https://vibeco.de)
2. Create an account
3. Describe what you want to build , by voice or text

## Replit

Replit is a complete development environment in the browser with integrated AI assistant.

### How to set up

1. Go to [replit.com](https://replit.com)
2. Create an account
3. Replit Agent can generate complete apps from prompts
4. Supports any language and framework

### When to use Replit

- When you need more control than visual platforms give
- When you want to learn to see code while you build
- A good middle ground between Mode 2 and Mode 3

## Comparison: which platform to choose?

| I want...                          | Use      |
| ---------------------------------- | -------- |
| A complete web app quickly         | Lovable  |
| A prototype to test an idea        | Bolt.new |
| Beautiful UI components            | v0.dev   |
| A data-focused/CRUD app            | Base44   |
| Maximum flexibility in the browser | Replit   |

**My recommendation**: start with Lovable for your first app. When you need more control, try Bolt.new or Replit. Use v0 as a complement for specific components.

## Common setup for all

Regardless of which platform you choose:

1. **Create accounts on all of them** , they're free and you can switch
2. **Connect GitHub** , most allow exporting code to GitHub
3. **Set up Supabase** , if your app needs a database, Supabase is the standard
4. **Test deployment** , all offer public URLs, verify it works

## Summary

In Mode 2, your stack is:

- A visual platform (Lovable, Bolt, v0, or Base44)
- A Supabase account for database and authentication
- A browser with DevTools to check errors
- Optionally, a GitHub account to save code

The big difference from Mode 1: here you're not copying and pasting code. The platform generates, hosts, and runs everything. You describe, iterate, and ship.

## Challenge

1. Create a free account on Lovable. Describe a simple app: "A habit tracker where I can add habits, mark them as done each day, and see a weekly streak counter." Watch it build. Deploy the result.

2. Try the same prompt on Bolt. Compare the two results: which looks better? Which has more features? Which was faster? This comparison tells you which platform suits your style.
