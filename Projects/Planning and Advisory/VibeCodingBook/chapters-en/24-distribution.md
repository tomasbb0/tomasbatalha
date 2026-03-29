# Chapter 24 Distribution: Products Don't Sell Themselves

You built a product. It works. It's deployed. Nobody knows it exists. This is the default state of every side project, startup, and indie product. Distribution is the work of getting your product in front of the people who need it. A worse product with better distribution beats a better product with no distribution, every time.

## Key Terms

| Term                 | What it means                                                      |
| -------------------- | ------------------------------------------------------------------ |
| **Distribution**     | How your product reaches users (channels, platforms, strategies)   |
| **Growth engine**    | A repeatable, scalable mechanism that brings new users             |
| **SEO**              | Search Engine Optimisation: ranking in Google search results       |
| **Product Hunt**     | A platform where makers launch products to an interested audience  |
| **Chrome Web Store** | Where users discover and install Chrome extensions                 |
| **Niche**            | A small, specific market segment where competition is lower        |
| **Cold outreach**    | Contacting potential users directly (email, LinkedIn, communities) |

## Distribution Channels by Product Type

### Chrome Extensions → Chrome Web Store

The Chrome Web Store is the primary discovery channel for extensions. Users search by keyword, browse categories, and install directly.

Optimise your listing:

- Clear title with relevant keywords ("AI Job Application Assistant", not "JobPilot v2")
- Screenshots showing the extension in action (not just the logo)
- Description that explains the problem, the solution, and how it works in three sentences
- Category: choose the most specific one that fits

Beyond the store:

- Write a blog post explaining the problem your extension solves
- Post in relevant communities (Reddit, forums specific to your extension's domain)
- Create a landing page with a "Install from Chrome Web Store" button

JobPilot's discovery was almost entirely Reddit. A single post in r/jobsearch explaining that auto-filling Greenhouse forms by hand was tedious, with an honest description of what the extension does (and doesn't do), generated 200 installs in three days. The Chrome Web Store listing itself brought fewer than 10 installs per week from search. The lesson: the store is where people install, but the community is where they discover. Don't wait for the store algorithm. Go where your users already complain about the problem you're solving.

If you're building a web app, the same principle applies. SEO is slow. Communities are fast. Find where your target users gather and show up with something useful.

### Web Apps → SEO, Product Hunt, Communities

For web applications, distribution happens through multiple channels:

**SEO:** Create content around the problems your app solves. If you built an invoice generator for freelancers, write articles like "How to create professional invoices as a freelancer in Portugal". Organic search traffic compounds over time.

**Product Hunt:** A single launch day can generate hundreds or thousands of visitors. Prepare: a clear one-line description, a short demo video, and a link to the live product. Launch on a Tuesday or Wednesday for maximum visibility.

**Communities:** Find where your target users already gather. Reddit subreddits, Discord servers, LinkedIn groups, Slack communities. Participate genuinely (don't spam). Share your product when it's relevant to the conversation.

### Desktop Apps → Direct Download, Package Managers

Desktop applications distribute through:

- Direct download from your website
- Package managers: `brew cask` on macOS, Chocolatey or winget on Windows
- Mac App Store (optional, adds discoverability but takes a 30% cut)

### Google Workspace Add-ons → Google Marketplace

If your product extends Google Sheets, Docs, or Gmail, the Google Workspace Marketplace is the distribution channel. Users discover add-ons through the built-in marketplace within Google apps.

### Mobile → PWA or App Stores

Progressive Web Apps (PWAs) install from the browser. No app store needed. Add a manifest and a service worker, and users can add your web app to their home screen.

For the App Store and Google Play: expect a review process, a developer fee (Apple: £79/year, Google: one-time £20), and compliance with platform guidelines.

## Four Growth Engines

### 1. Organic Search (SEO)

Write content that answers questions your target users ask. Tools: Google Search Console (free), a blog on your site, and patience. SEO takes months to build but compounds: an article that ranks keeps bringing visitors for years.

### 2. Word of Mouth

Build something people want to tell others about. This isn't a strategy you execute; it's a result of building something genuinely useful. Support it: make sharing easy (a "Share" button, referral links), ask satisfied users for testimonials, and respond to every piece of feedback.

### 3. Community Presence

Show up where your users are. Contribute knowledge, not marketing. If you built a sleep tracking app, write about sleep research. Answer questions about sleep quality. When someone asks for a recommendation, your expertise and your product are already established.

### 4. Cold Outreach

For B2B products or niche tools, direct contact works. Identify 50 potential users. Write a personal message (not a template) explaining how your product solves their specific problem. Expect a 5% to 15% response rate.

## The Niche Advantage

A product for "everyone" competes with everything. A product for "freelance translators in Portugal who need to generate invoices with Portuguese tax numbers" competes with almost nothing. Niche products:

- Are easier to describe ("you know exactly what this does")
- Are easier to find users for ("I know where translators gather online")
- Have less competition ("nobody built this specific thing")
- Convert better ("this solves my exact problem")

Start niche. Expand later. Amazon started with books. Facebook started at Harvard.

> **Try It:** Search for your product idea (or a similar one) on Product Hunt, Reddit, and Google. Count the competing products on the first page. Now narrow the niche: add a geography, an industry, or a specific user type. Search again. The competition shrinks dramatically. That's your entry point.

## Common Beginner Mistake

**Building a second feature instead of distributing the first.** After launch, the temptation is to keep adding features. "If only we had dark mode / export / mobile app, users would come." Users won't come because of features. They'll come because they found you. Spend time on distribution, not the roadmap, until you have consistent traffic.

## The Science Behind It

**Diffusion of Innovations (Everett Rogers, 1962).** New products don't spread evenly. There's a sequence: innovators (2.5%), early adopters (13.5%), early majority (34%), late majority (34%), laggards (16%). Your MVP is not for the majority. It's for early adopters: people who tolerate imperfections because the problem you solve is acute enough. If you try to please everyone from day one, you please no one.

**The Four Growth Engines (adapted from Andrew Chen, The Cold Start Problem, 2021).** All sustainable growth comes from one of four engines: viral (users bring users), paid (you spend money to acquire), content (you attract via search and articles), and sales (you contact clients directly). Running all four at once dilutes everything. Pick one, master it, then layer others on top.

**Product Market Fit (Marc Andreessen, 2007).** Product market fit is the moment you stop needing to convince people to use your product. They find it, use it, and return on their own. Before that moment, all distribution is effort. After that moment, distribution is amplification. Most vibe coding projects never reach this point because they never ship.

## Checklist

- [ ] I know the primary distribution channel for my product type
- [ ] I have a launch plan (Product Hunt, communities, or direct outreach)
- [ ] My product listing / landing page clearly explains the problem and solution
- [ ] I've identified where my target users gather online
- [ ] I have at least one repeatable growth engine (SEO, community, or outreach)
- [ ] I spend as much time on distribution as I do on building

## Summary

Distribution is how products reach users. Each product type has a primary channel: Chrome Web Store for extensions, SEO and Product Hunt for web apps, package managers for desktop apps, Google Marketplace for Workspace add-ons. Four growth engines drive discovery: organic search, word of mouth, community presence, and cold outreach. Niche products have structural advantages: less competition, easier discovery, and higher conversion. The most common mistake after launch is continuing to build features instead of investing time in distribution.

## Challenge

1. Choose a product (yours or a hypothetical one). Write a distribution plan: primary channel, secondary channel, three communities where target users gather, and one piece of content you could create to attract organic search traffic.

2. Find a product on Product Hunt that's similar to something you might build. Analyse its launch: how was the description written? How many upvotes did it get? Read the comments. What questions did people ask? Use these insights for your own launch plan.
