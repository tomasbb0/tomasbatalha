# Appendix B: Top 50 Beginner Mistakes

Organised by category. Each mistake includes the chapter where the correct approach is explained.

## Security Mistakes

| #   | Mistake                                 | Consequence                                         | Fix                                                                                     | Ch  |
| --- | --------------------------------------- | --------------------------------------------------- | --------------------------------------------------------------------------------------- | --- |
| 1   | API key in frontend code                | Anyone can use your key, run up bills               | Move to backend, use environment variables (JobPilot had this exact bug)                | 18  |
| 2   | Secrets committed to Git                | Keys exposed in repository history forever          | Add .env to .gitignore before first commit, rotate keys (happened to JobPilot on day 3) | 14  |
| 3   | No server-side validation               | Users bypass frontend checks, send bad data         | Validate on the server, always                                                          | 17  |
| 4   | Auth only in CSS (hiding elements)      | Anyone can unhide with DevTools                     | Backend authorisation on every endpoint                                                 | 17  |
| 5   | Billing logic in the browser            | Users change prices via DevTools                    | Calculate prices server-side                                                            | 7   |
| 6   | SQL string concatenation                | SQL injection: attackers read/delete your database  | Parameterised queries                                                                   | 17  |
| 7   | Rendering user input as HTML            | XSS: malicious scripts run in other users' browsers | Use textContent or a sanitisation library                                               | 17  |
| 8   | Tokens in localStorage                  | XSS can steal auth tokens                           | Use HttpOnly cookies                                                                    | 18  |
| 9   | No HTTPS                                | Data sent in plain text, interceptable              | Use platforms with automatic HTTPS (Vercel, Netlify)                                    | 17  |
| 10  | Firebase/Supabase rules disabled        | Database open to anyone with the project URL        | Enable and configure security rules / RLS                                               | 18  |
| 11  | GET requests for state changes          | CSRF: malicious sites trigger actions on your app   | Use POST/PUT/DELETE for mutations                                                       | 17  |
| 12  | No rate limiting on login               | Brute force attacks on user accounts                | Limit failed attempts, add delays                                                       | 17  |
| 13  | Predictable IDs without ownership check | BOLA: users access other users' data                | Verify ownership on every endpoint                                                      | 17  |

## Code and Architecture Mistakes

| #   | Mistake                                      | Consequence                                                          | Fix                                             | Ch  |
| --- | -------------------------------------------- | -------------------------------------------------------------------- | ----------------------------------------------- | --- |
| 14  | Editing files in dist/ or build/             | Changes overwritten on next build                                    | Edit source files in src/                       | 9   |
| 15  | Editing node_modules/ directly               | Changes lost on next npm install                                     | Fork the package or find an alternative         | 11  |
| 16  | Running npm install in wrong folder          | Dependencies installed in wrong location, project can't find them    | Confirm package.json is in current directory    | 11  |
| 17  | No .gitignore                                | node_modules/ committed (huge repo), .env committed (secrets leaked) | Create .gitignore before first commit           | 14  |
| 18  | Committing directly to main                  | No review, no safety net, hard to revert                             | Use branches and pull requests                  | 14  |
| 19  | Mutating React state directly                | Component doesn't re-render, stale UI                                | Use setter function with new object/array       | 12  |
| 20  | Missing useEffect dependency array           | Infinite re-renders or stale data                                    | Include all dependencies, use [] for mount-only | 12  |
| 21  | Absolute file paths in deployed code         | Images and links break in production                                 | Use relative paths (./photo.jpg)                | 5   |
| 22  | No error handling on fetch()                 | App crashes silently when API fails                                  | Check response.ok, add try/catch                | 13  |
| 23  | Ignoring TypeScript errors                   | Bugs reach production that the compiler caught                       | Fix type errors before deploying                | 12  |
| 24  | Frontend calls database directly without RLS | No access control on data                                            | Add a backend or configure RLS properly         | 7   |
| 25  | Mixing concerns (business logic in UI)       | Untestable, hard to change                                           | Separate logic from presentation                | 7   |

## Deployment and Operations Mistakes

| #   | Mistake                              | Consequence                                                        | Fix                                            | Ch  |
| --- | ------------------------------------ | ------------------------------------------------------------------ | ---------------------------------------------- | --- |
| 26  | Same env vars for dev and production | Test data in production, or production data deleted in development | Separate .env files per environment            | 21  |
| 27  | No CI/CD pipeline                    | Broken code deployed accidentally                                  | GitHub Actions: test on every push             | 21  |
| 28  | No error monitoring                  | Users find bugs before you do                                      | Set up Sentry before launch                    | 21  |
| 29  | No uptime monitoring                 | Site goes down, nobody notices                                     | UptimeRobot or Better Stack (free tiers)       | 21  |
| 30  | No logging in production             | Cannot diagnose issues after the fact                              | Structured logging with log levels             | 21  |
| 31  | Deploying on Friday                  | Bugs discovered over the weekend when nobody's watching            | Deploy Monday to Thursday                      | 21  |
| 32  | No rollback plan                     | Broken deploy stays broken while you debug                         | Feature flags, deploy rollback, git revert     | 22  |
| 33  | New features without feature flags   | No way to disable a broken feature without redeploying             | Wrap risky features in flags                   | 22  |
| 34  | Never cleaning up feature flags      | Codebase full of dead conditionals                                 | Monthly cleanup of stale flags                 | 22  |
| 35  | No preview environment               | Testing in production                                              | Use Vercel preview deploys or separate staging | 21  |

## AI Integration Mistakes

| #   | Mistake                                 | Consequence                                         | Fix                                                | Ch  |
| --- | --------------------------------------- | --------------------------------------------------- | -------------------------------------------------- | --- |
| 36  | No spending limit on AI APIs            | Runaway costs from bugs or loops                    | Set hard limits on every provider                  | 19  |
| 37  | Using expensive models for simple tasks | 17x higher costs than necessary                     | Model routing: cheap for easy, expensive for hard  | 19  |
| 38  | No response caching                     | Paying for identical requests repeatedly            | Cache common responses                             | 19  |
| 39  | Trusting AI output without validation   | Wrong data, format errors, security vulnerabilities | Validate schema and content before acting          | 16  |
| 40  | Free-text AI responses in production    | Parser crashes when format changes                  | Use structured outputs (JSON schema)               | 16  |
| 41  | No fallback when AI is down             | Feature (or entire app) stops working               | Graceful degradation with cached/default responses | 19  |
| 42  | System prompt in frontend code          | Users read and manipulate your AI instructions      | Keep system prompts server-side                    | 16  |
| 43  | No rate limiting on AI endpoints        | Users drain your API budget                         | Per-user rate limits on endpoints that call AI     | 19  |

## Product and Scope Mistakes

| #   | Mistake                                       | Consequence                                      | Fix                                                 | Ch  |
| --- | --------------------------------------------- | ------------------------------------------------ | --------------------------------------------------- | --- |
| 44  | Building for six months without user feedback | Nobody wants what you built                      | Ship an MVP in one to two weekends                  | 23  |
| 45  | No "NOT IN MVP" list                          | Scope creep: every idea gets added               | Written scope document with explicit exclusions     | 23  |
| 46  | Building for "everyone"                       | Competes with everything, appeals to nobody      | Define a specific ICP                               | 23  |
| 47  | Adding features instead of distributing       | Product exists, nobody finds it                  | Spend equal time on distribution and building       | 24  |
| 48  | No launch plan                                | Deploy and hope                                  | Plan: communities, Product Hunt, direct outreach    | 24  |
| 49  | Optimising too early                          | Spending days on performance before having users | Get users first, optimise when data shows a problem | 23  |
| 50  | Giving up after first failure                 | One rejected MVP = "I can't build products"      | Failure is data. Adjust hypothesis and try again.   | 23  |
