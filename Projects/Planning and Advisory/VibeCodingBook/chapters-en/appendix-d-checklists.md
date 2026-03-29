# Appendix D: Checklists

Three checklists for three moments. Print them, pin them, use them every time.

## Pre-Merge Checklist

Run through this before merging a branch into main.

- [ ] Code works locally (tested manually)
- [ ] No TypeScript errors (`npm run build` passes)
- [ ] No linting errors (`npm run lint` passes)
- [ ] All existing tests pass (`npm test`)
- [ ] New code has at least basic tests (if it has logic worth testing)
- [ ] No `console.log` debugging statements left in the code
- [ ] No hardcoded secrets or API keys
- [ ] `.env.example` updated if new environment variables were added
- [ ] Commit messages are descriptive (not "fix stuff" or "update")
- [ ] PR description explains what changed and why
- [ ] Risky features are behind a feature flag
- [ ] Dependencies added are justified (checked the package: downloads, maintenance, size)

## Pre-Deploy Checklist

Run through this before deploying to production.

- [ ] All tests pass on the CI/CD pipeline
- [ ] Preview deployment tested (if available)
- [ ] Environment variables set correctly in the production platform
- [ ] Different API keys for production vs development
- [ ] HTTPS confirmed on the production URL
- [ ] Error monitoring configured (Sentry or equivalent)
- [ ] Logging configured (structured logs, appropriate log level)
- [ ] Security headers set (Content-Security-Policy, X-Frame-Options)
- [ ] Database migrations applied (if applicable)
- [ ] Rollback plan ready (feature flag, previous deploy, or git revert)
- [ ] Not deploying on Friday

## Chrome Extension Publish Checklist

Run through this before submitting to the Chrome Web Store.

- [ ] manifest.json has correct version number (bumped from last release)
- [ ] Permissions are minimal (use activeTab instead of \<all_urls\> where possible)
- [ ] No API keys or secrets in any file that ships to the user
- [ ] Extension tested on at least two target sites (for JobPilot: Greenhouse and Lever)
- [ ] Popup works when no tab is active
- [ ] Content script handles pages where expected elements don't exist (graceful failure)
- [ ] Icons provided at 16x16, 48x48, and 128x128
- [ ] Screenshots of the extension in action (at least one showing the popup, one showing the result)
- [ ] Description clearly states what data the extension accesses and why
- [ ] Privacy policy URL set (required if you use remote code, user data, or authentication)
- [ ] Build output tested as an unpacked extension on a clean Chrome profile
- [ ] Zip file created from the dist/ folder (not the source folder)

## Pre-Launch Checklist

Run through this before telling the world about your product.

### Security

- [ ] All API keys are server-side only (no secrets in frontend code)
- [ ] .env is in .gitignore and not in the repository history
- [ ] Authentication works (signup, login, logout, password reset)
- [ ] Authorisation works (users can only access their own data)
- [ ] Input validation on the server for all endpoints
- [ ] SQL queries use parameterised statements (no string concatenation)
- [ ] User-generated content is sanitised before rendering
- [ ] Cookies have HttpOnly, Secure, and SameSite flags
- [ ] CORS configured correctly (not allow all origins)

### Product

- [ ] Core feature works end-to-end (the ONE thing it must do)
- [ ] Tested on Chrome, Firefox, and Safari (or at least Chrome and one other)
- [ ] Tested on mobile (responsive design or mobile view)
- [ ] Error states handled gracefully (not blank screens or raw error messages)
- [ ] Loading states shown during async operations
- [ ] 404 page exists
- [ ] Contact option available (email, form, or support link)

### Monitoring

- [ ] Error monitoring active (Sentry)
- [ ] Uptime monitoring active (UptimeRobot or Better Stack)
- [ ] Health endpoint available (/api/health)
- [ ] Spending limits set on all paid APIs
- [ ] Analytics installed (PostHog, Plausible, or similar)

### Distribution

- [ ] Landing page or product listing is live
- [ ] Clear one-line description of what the product does
- [ ] Screenshots or demo video prepared
- [ ] Launch plan written (where to post, when, to whom)
- [ ] Feedback channel ready (email, form, or community)
