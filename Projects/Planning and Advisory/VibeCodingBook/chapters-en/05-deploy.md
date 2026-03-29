# Chapter 5 Ship It Live: Deploy Your First Project to the Internet

You have code that works on your computer. Your browser shows a perfect page at `localhost`. Nobody else can see it... yet. This chapter takes that page and puts it on the internet, accessible to anyone with a URL. The word for this is deploy, and in 2026, it takes about sixty seconds.

## Key Terms

| Term            | What it means                                                                |
| --------------- | ---------------------------------------------------------------------------- |
| **Deploy**      | Moving your code to a server that's always on, so anyone can visit your site |
| **Hosting**     | The service that provides the server (Vercel, Netlify, GitHub Pages)         |
| **Static site** | A site made of HTML, CSS, and JS files with no backend server                |
| **Domain**      | The human-readable address (lisboawalks.com)                                 |
| **DNS**         | The system that translates domain names to server IP addresses               |
| **SSL/HTTPS**   | Encryption that secures data between the browser and the server              |
| **Build**       | The process of converting source code into deployable files                  |

## Vercel: Push a Folder, Get a URL

Vercel is the simplest way to deploy a web project. It's free for personal use and handles everything automatically.

1. Create an account at [vercel.com](https://vercel.com) (sign in with GitHub, the platform where developers store code; Chapter 16 teaches Git in full)
2. Connect your GitHub repository (or drag and drop a folder; if you don't have one yet, the drag-and-drop option works fine for now)
3. Click Deploy
4. Get a live URL like `my-project.vercel.app`

Every time you push code to GitHub, Vercel automatically redeploys. (Don't worry about "push" or "branches" yet; Chapter 16 covers Git, the version control system that makes this work.) For now, the drag-and-drop method or Vercel CLI below is all you need.

For the Lisboa Walks page from Chapter 2 (Your First AI Conversation):

```bash
npm install -g vercel    # Install Vercel CLI
cd my-project            # Navigate to your project
vercel                   # Deploy (follow the prompts)
```

Ninety seconds later, you have a URL. Share it. Someone on the other side of the world can see your page.

## Netlify: Similar, With Forms

Netlify does what Vercel does, with a bonus: Netlify Forms. If your HTML has a `<form>` tag with the `netlify` attribute, Netlify captures submissions automatically. No backend needed.

```html
<form name="contact" netlify>
  <input type="text" name="name" placeholder="Your name" required />
  <input type="email" name="email" placeholder="Your email" required />
  <textarea name="message" placeholder="Your message"></textarea>
  <button type="submit">Send</button>
</form>
```

Deploy to Netlify. The form works. Submissions appear in the Netlify dashboard. No server, no database, no code.

## GitHub Pages: For Static Sites

GitHub Pages turns any GitHub repository into a website. Free, no configuration, no external service.

1. Create a repository on GitHub
2. Push your HTML files
3. Go to Settings → Pages
4. Select the branch (usually `main`) and folder (root or `/docs`)
5. Wait thirty seconds. Your site is live at `username.github.io/repository-name`

Best for: portfolios, documentation, simple landing pages. Not for apps that need a backend.

## What Just Happened: DNS Explained in Five Sentences

You typed `my-project.vercel.app` in your browser. The browser asked a DNS server: "What IP address is this?" The DNS server replied: "76.76.21.21". The browser connected to that IP address and asked for the webpage. Vercel's server sent back your HTML, CSS, and JavaScript files.

That's it. DNS is a phonebook. Domain names are names. IP addresses are phone numbers.

> **Try It:** If you have an HTML file from a previous chapter, deploy it now. Go to vercel.com, sign up with GitHub, drag your project folder in. In under two minutes, you'll have a live URL you can share with anyone. Send it to a friend. That feeling of "I made this and it's on the internet" is real.

## Custom Domains

The `.vercel.app` URL works but looks unprofessional. A custom domain (lisboawalks.com) costs about €10 per year.

1. Buy a domain (Namecheap, Cloudflare, Google Domains)
2. In Vercel: Settings → Domains → Add `lisboawalks.com`
3. Vercel gives you DNS records (usually CNAME or A records)
4. In your domain registrar: add those DNS records
5. Wait up to 48 hours (usually 10 minutes)

Vercel automatically provides HTTPS. No configuration needed.

## Not Everything Deploys to a URL

The Lisboa Walks page goes to Vercel. If you're building a web app (a habit tracker, a dashboard, a recipe site), you'd deploy to Vercel or Render too. But JobPilot? It deploys to the Chrome Web Store. Completely different process. You zip the extension files, upload them to a developer dashboard, pay a one-time $5 registration fee, and wait 1 to 3 days for Google's review team to approve it. Updates go through the same review. You can't hotfix a content script the way you'd push a one-line change to Vercel.

You won't publish JobPilot to the Chrome Web Store yet (that's Chapter 24). For now, just know that "deploy" doesn't always mean "push to a server." Sometimes it means "submit to a store and wait." Chapter 17 covers the full extension architecture. Chapter 24 covers the Chrome Web Store listing strategy.

## What Static Sites Can Do

Static sites sound limited. They're not. In 2026, a static site can:

- Run complex JavaScript (animations, forms, interactive elements)
- Call external APIs (weather, maps, AI)
- Use serverless functions (small backend code that runs on demand)
- Integrate with databases (Supabase, Firebase)
- Process payments (Stripe Checkout)
- Send emails (via API)

"Static" means the HTML/CSS/JS files are pre-built. It doesn't mean the site can't do anything dynamic.

## Common Beginner Mistake

**Deploying without checking the console.** Your page looks fine locally. You deploy. On the live URL, the images don't load because you used absolute file paths (`/Users/yourname/Desktop/photo.jpg`) instead of relative paths (`./photo.jpg`). Always open DevTools on the deployed version and check the Console and Network tabs for errors.

## Checklist

- [ ] I can deploy a project to Vercel, Netlify, or GitHub Pages
- [ ] I understand what DNS does (translates names to IP addresses)
- [ ] I can set up a custom domain
- [ ] I check the deployed version in the browser (not just localhost)
- [ ] I know that static sites can still call APIs and use databases

## Summary

Deploying means putting your code on a server anyone can access. Vercel, Netlify, and GitHub Pages each do this for free. Vercel auto-deploys from GitHub with preview URLs per branch. Netlify adds free form handling. GitHub Pages is the simplest option for static content. DNS translates domain names to IP addresses. Static sites can call APIs, use databases, process payments, and send emails.

## Challenge

1. Take the page from Chapter 2 (Your First AI Conversation: Lisboa Walks or wedding RSVP). Deploy it to Vercel. Share the URL with someone. Confirm it works on their device.

2. Deploy the same page to a second platform (Netlify or GitHub Pages). Compare: which was faster? Which has a better dashboard? Which URL format do you prefer?
