# Chapter 11 Deploy & Monitoring: Your App in the Wild

Chapter 5 (Ship It Live) deployed a static page. This chapter covers production deployment: multiple environments, environment variables, continuous integration, logging, error monitoring, and uptime tracking. The difference between a side project and a product is what happens after you deploy.

## Key Terms

| Term                     | What it means                                                                    |
| ------------------------ | -------------------------------------------------------------------------------- |
| **Environment**          | A distinct instance of your app (development, preview, production)               |
| **Environment variable** | A configuration value set outside your code (API keys, URLs, feature flags)      |
| **CI/CD**                | Continuous Integration / Continuous Deployment: automated testing and deployment |
| **Logging**              | Recording what your app does (requests, errors, events)                          |
| **Observability**        | The ability to understand your app's internal state from its external outputs    |
| **Uptime monitoring**    | A service that checks if your site is accessible and alerts you when it's not    |

## Three Environments

### Development (Your Machine)

Your laptop. Run the app locally. Break things freely. Use test data and development API keys. Nobody sees this except you.

### Preview (Auto-Deployed Branches)

Push a branch to GitHub. Vercel (and most platforms) automatically deploys it to a unique URL like `my-app-feature-xyz.vercel.app`. Test the change before it reaches production. Share the preview URL with collaborators for review.

### Production (The Real Thing)

The `main` branch. Deployed to your public URL. Real users, real data, real consequences. Changes here affect everyone.

Each environment has its own configuration:

```bash
# .env.development
API_URL=http://localhost:3000
SUPABASE_URL=https://dev-project.supabase.co
STRIPE_KEY=sk_test_abc123
LOG_LEVEL=debug

# .env.production
API_URL=https://api.myapp.com
SUPABASE_URL=https://prod-project.supabase.co
STRIPE_KEY=sk_live_xyz789
LOG_LEVEL=error
```

Different database, different API keys, different log verbosity. The code is identical. The configuration changes.

## Deploy Platforms

### Vercel

Best for: Next.js, React, and other frontend frameworks.

- Auto-deploys from GitHub on every push
- Preview deployments for every branch and pull request
- Environment variables in the dashboard (separate for preview and production)
- Edge functions for serverless backend logic
- Automatic HTTPS

### Render

Best for: backend services, full-stack applications, databases.

- Deploys Node.js, Python, Go, Docker containers
- Managed PostgreSQL databases
- Auto-deploy from GitHub
- Environment groups (share variables across services)
- Health checks and auto-restart

### Netlify

Best for: static sites, JAMstack, serverless functions.

- Drag-and-drop deployment
- Netlify Functions (AWS Lambda under the hood)
- Form handling without a backend
- Split testing (A/B test different deploys)

### Chrome Web Store (Browser Extensions)

Best for: Chrome extensions, browser add-ons.

This is where JobPilot ships. The extension itself goes through the Chrome Web Store. That means: zip the build folder, upload it to the Chrome Developer Dashboard, fill out the listing (description, screenshots, permissions justification), and submit for review. Google reviews extensions manually, which can take one to five days.

The backend service that calls OpenAI lives on Render. So JobPilot has a split deployment: the extension goes to the Chrome Web Store, the API goes to Render. Two deploy pipelines, two sets of environment variables, two things that can break independently.

If you're building a web app instead, you won't need this split. Everything goes to one platform (Vercel, Render, or Netlify). But if your project touches a store listing (Chrome extension, mobile app), expect two deploy targets.

## CI/CD: Automated Testing and Deployment

CI/CD automates the process: push code → run tests → deploy if tests pass. GitHub Actions is the most common tool.

```yaml
# .github/workflows/deploy.yml
name: Deploy

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20

      - run: npm ci # Install dependencies
      - run: npm run lint # Check code style
      - run: npm run test # Run unit tests
      - run: npm run build # Verify the build succeeds
```

This runs on every push to `main` and every pull request. If any step fails (a test breaks, the build fails), the deployment stops. You find out before users do.

For Vercel projects, deployment is often automatic (Vercel watches the repo). The CI/CD pipeline runs tests and the deployment only proceeds if everything passes.

## Logging: What Your App Did

`console.log` is fine for development. In production, you need structured logging.

```javascript
// Development: console.log
console.log("Review submitted:", review.id);

// Production: structured logging
logger.info("review.submitted", {
  reviewId: review.id,
  userId: req.user.id,
  rating: review.rating,
  timestamp: new Date().toISOString(),
});
```

Structured logs include context (who, what, when) and can be searched, filtered, and aggregated. When something goes wrong at 3am, structured logs answer "what happened?" without guessing.

**Log levels:**

- **error:** Something broke. Investigate immediately.
- **warn:** Something unexpected happened. Might become an error later.
- **info:** Normal operations. User signed up, review submitted, payment processed.
- **debug:** Detailed information for development. Too verbose for production.

Set the log level per environment. Production: `info` and above. Development: `debug` and above.

> **Try It:** Open any project with a backend. Replace one `console.log` with a structured log object: `console.log(JSON.stringify({ event: "user.login", userId: 123, timestamp: new Date().toISOString() }))`. Run it. Notice how much easier it is to search and filter compared to `console.log("user logged in")`.

## Error Monitoring With Sentry

Users don't report errors. They leave. Sentry catches errors automatically.

```javascript
import * as Sentry from "@sentry/node";

Sentry.init({
  dsn: process.env.SENTRY_DSN,
  environment: process.env.NODE_ENV,
  release: process.env.GIT_COMMIT_SHA,
});

// Errors are captured automatically
// You can also capture manually:
try {
  await processPayment(order);
} catch (error) {
  Sentry.captureException(error, {
    extra: { orderId: order.id, amount: order.total },
  });
  throw error;
}
```

Sentry provides:

- **Stack traces:** Exactly where the error occurred
- **Breadcrumbs:** What happened before the error (user actions, API calls)
- **Release tracking:** Which deployment introduced the error
- **Alerts:** Email or Slack notification when new errors appear

The free tier covers most small projects. Set it up before launch, not after the first user complaint.

## Uptime Monitoring

Your app might be down right now. Would you know? Uptime monitors check your site every few minutes and alert you when it's unreachable.

Free options: UptimeRobot, Better Stack (formerly Better Uptime), Vercel's built-in monitoring.

Configure:

- Check every 5 minutes
- Alert via email, Slack, or SMS
- Monitor both the frontend URL and the API health endpoint

A simple health endpoint:

```javascript
app.get("/api/health", (req, res) => {
  res.json({ status: "ok", timestamp: new Date().toISOString() });
});
```

If this endpoint stops responding, the monitor alerts you.

## Common Beginner Mistake

**Using the same environment variables for development and production.** Your test Stripe key in production means real charges don't work. Your production database URL in development means you accidentally delete real user data. Keep environments strictly separated. Different .env files. Different values in the deployment platform's dashboard.

## Checklist

- [ ] I have separate development, preview, and production environments
- [ ] Environment variables are different per environment (especially API keys)
- [ ] Tests run automatically on every push (CI/CD)
- [ ] I use structured logging in production (not console.log)
- [ ] Error monitoring is set up (Sentry or equivalent) before launch
- [ ] Uptime monitoring sends alerts when the site goes down

## Summary

Production deployment involves three environments: development (your machine), preview (auto-deployed branches), and production (the real thing). Each environment has its own configuration and API keys. CI/CD automates testing and deployment through GitHub Actions. Structured logging records what the app does with searchable context. Sentry catches errors automatically with stack traces and breadcrumbs. Uptime monitoring alerts you when the site goes down. Set up monitoring before launch, not after the first incident.

## Challenge

1. Set up a GitHub Actions workflow for your project. Define three steps: install dependencies, run lint, and run the build. Push to GitHub and watch the workflow execute.

2. Create a free Sentry account. Add Sentry to your project. Deliberately throw an error in your code. Deploy. Check Sentry's dashboard for the captured error. Examine the stack trace and breadcrumbs.
