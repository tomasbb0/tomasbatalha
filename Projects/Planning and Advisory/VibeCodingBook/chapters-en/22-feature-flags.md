# Chapter 22 Feature Flags & Rollbacks: Shipping Without Fear

You merged a feature. You deployed. Something is wrong. Users are seeing errors. Traditional response: revert the code, redeploy, wait 5 minutes. Feature flag response: toggle the flag off. The feature disappears in seconds. No redeployment needed.

## Key Terms

| Term               | What it means                                                        |
| ------------------ | -------------------------------------------------------------------- |
| **Feature flag**   | A conditional that enables or disables a feature without redeploying |
| **Rollout**        | Gradually enabling a feature for increasing percentages of users     |
| **Rollback**       | Reverting to a previous working state                                |
| **Canary release** | Releasing to a small group first, monitoring, then expanding         |
| **Kill switch**    | A feature flag specifically designed for emergency shutoff           |
| **Stale flag**     | A feature flag that's been at 100% for so long it should be removed  |

## What Feature Flags Are

A feature flag wraps a feature in a conditional. The feature exists in the code but only activates when the flag is on.

```javascript
// Without feature flags: the feature is always active
function Dashboard() {
  return (
    <div>
      <ReviewList />
      <AIInsightsPanel /> {/* New, untested in production */}
    </div>
  );
}

// With feature flags: the feature is controllable
function Dashboard() {
  const showAIInsights = useFeatureFlag("ai-insights-panel");

  return (
    <div>
      <ReviewList />
      {showAIInsights && <AIInsightsPanel />}
    </div>
  );
}
```

The code is deployed. The feature is hidden. You turn it on from a dashboard (not from the codebase). If it breaks, you turn it off. No commit, no build, no deploy.

> **Try It:** Add `const SHOW_BETA = false;` to any JavaScript file. Wrap a UI element in an `if (SHOW_BETA)` check. Change it to `true`. Refresh. That's a feature flag in its simplest form. Everything else in this chapter is just better ways to manage that boolean.

## PostHog: Feature Flags and Analytics

PostHog is an open-source tool that combines feature flags, analytics, and session recording. For feature flags:

```javascript
import posthog from "posthog-js";

posthog.init("phc_your_project_key", {
  api_host: "https://eu.posthog.com",
});

// Check a feature flag
if (posthog.isFeatureEnabled("ai-insights-panel")) {
  showAIInsightsPanel();
}

// With percentage rollout:
// In PostHog dashboard: ai-insights-panel → 10% of users
// 10% see the feature, 90% don't
```

PostHog integrates analytics with flags. You can see: of the users who saw the feature, how did their behaviour differ from those who didn't? This is A/B testing built into your feature rollout.

## Gradual Rollout

The safest way to ship a new feature:

1. **0%**: Feature deployed behind a flag, nobody sees it
2. **Internal team**: Enable for your own accounts, test in production
3. **5%**: Enable for a small percentage of real users
4. **Monitor**: Watch error rates, performance, user feedback for 24 hours
5. **25%**: If metrics are healthy, expand
6. **50%**: Continue monitoring
7. **100%**: Full rollout
8. **Clean up**: Remove the flag from code (it's now a permanent feature)

At any step, if something goes wrong, set the flag back to 0%. Instant recovery.

### Rolling Out a New ATS: LinkedIn Support

JobPilot started with Greenhouse only. When LinkedIn Easy Apply support was ready, it went behind a feature flag called `linkedin-autofill`. Here's how the rollout went:

1. **0%**: LinkedIn code deployed, disabled for everyone.
2. **Internal**: Enabled for two test accounts. Discovered that LinkedIn's form structure changed depending on whether the job was posted by a recruiter or a company page. Fixed.
3. **5%**: Enabled for a small group. Found that some users had LinkedIn open in multiple tabs, causing duplicate fills. Added a tab-locking mechanism.
4. **25%**: Expanded. Monitored error rates for 48 hours. Clean.
5. **100%**: Full rollout.

Without the flag, step 2 and step 3 bugs would have hit every user on the first day. With the flag, they hit five people, got fixed, and nobody else noticed.

## Three Types of Rollback

### 1. Feature Flag Off (Fastest)

Toggle the flag in the dashboard. Takes effect in seconds. The code stays deployed. The feature disappears. Use this when you have a flag in place.

### 2. Deploy Rollback (Minutes)

Redeploy the previous version. On Vercel: go to Deployments, find the last good one, click "Promote to Production". On Render: similar. This replaces the current code with the previous version.

Use this when there's no feature flag, or when the bug is in code that isn't behind a flag.

### 3. Git Revert (Permanent Fix)

Create a new commit that undoes the problematic changes:

```bash
git revert abc1234          # Reverses the specified commit
git push origin main        # Triggers a new deployment
```

This is the proper fix. It creates a traceable record (the revert commit explains what was undone and why) and the automated pipeline deploys the corrected version.

## The Complete Workflow

A feature from code to production:

1. **Code on a branch:** `git checkout -b feature/ai-insights`
2. **Wrap in a feature flag:** The new component only renders when the flag is on
3. **Pull Request:** Push the branch, open a PR, review the changes
4. **Merge to main:** Auto-deploy to production. Feature is deployed but hidden (flag off).
5. **Enable for internal users:** Test in production with real data
6. **Gradual rollout:** 5% → 25% → 50% → 100%, monitoring at each step
7. **If broken at any step:** Flag off. Investigate. Fix. Restart rollout.
8. **Clean up:** Once at 100% and stable for two weeks, remove the flag from code

## Cleaning Up Stale Flags

Feature flags are powerful but create technical debt if left indefinitely. A codebase with 50 active feature flags has 50 conditionals that make the code harder to read and test.

Rules for cleanup:

- If a flag has been at 100% for more than two weeks and no issues were reported, remove it
- If a flag was at 0% for more than a month (abandoned experiment), remove it
- Schedule a monthly "flag cleanup" session
- Track stale flags in PostHog or your project management tool

## Common Beginner Mistake

**Never cleaning up feature flags.** Six months later, nobody remembers what `experiment-v2-final-FINAL` does. The code has nested conditions that nobody dares to touch. Feature flags are temporary by design. They're a deployment safety mechanism, not a permanent architecture pattern. Budget time for removal.

## Checklist

- [ ] New risky features are deployed behind feature flags
- [ ] I can toggle a flag off in seconds if something breaks
- [ ] I know three rollback strategies: flag off, deploy rollback, git revert
- [ ] I use gradual rollouts for user-facing features
- [ ] I clean up stale flags regularly (every two weeks or monthly)
- [ ] I monitor error rates after every rollout increase

## Summary

Feature flags let you deploy code with features hidden behind toggles. Enable gradually (5%, 25%, 50%, 100%) while monitoring. Three rollback strategies exist: flag off (seconds), deploy rollback (minutes), and git revert (proper fix). PostHog combines feature flags with analytics. The complete workflow: code on a branch, wrap in a flag, merge, test internally, roll out gradually, and clean up once stable. Stale flags create technical debt and must be removed regularly.

## Challenge

1. Add a feature flag to any project. Use a simple boolean in an environment variable (`ENABLE_NEW_FEATURE=true`). Toggle it. Confirm the feature appears and disappears without redeploying code.

2. Create a free PostHog account. Add the JavaScript SDK to your project. Create a feature flag called "experimental-ui". Set it to 50% rollout. Reload your app several times. Confirm that the feature appears roughly half the time.
