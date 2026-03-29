# Chapter 21 Testing & Debugging: Finding Bugs Before Users Do

AI writes code that looks right. It runs. It passes a quick check. Then a user enters a date in an unexpected format, clicks submit twice rapidly, or uses a screen reader, and the app crashes. Testing catches these failures before they reach production. Debugging finds them after they happen.

## Key Terms

| Term                      | What it means                                                             |
| ------------------------- | ------------------------------------------------------------------------- |
| **Unit test**             | Testing one function in isolation                                         |
| **End-to-end (E2E) test** | Simulating a real user flow through the entire application                |
| **Test runner**           | The tool that finds and executes your tests (Jest, Vitest)                |
| **Assertion**             | A statement that something should be true (expect X to equal Y)           |
| **Breakpoint**            | A marker in your code where execution pauses so you can inspect variables |
| **Stack trace**           | The list of function calls that led to an error (read bottom to top)      |
| **Regression**            | A bug introduced by a change that broke something that previously worked  |

## console.log: The Simplest Debugging Tool

Before reaching for anything complex, `console.log` answers most questions:

```javascript
async function submitReview(data) {
  console.log("submitReview called with:", data);

  const response = await fetch("/api/reviews", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(data),
  });

  console.log("Response status:", response.status);

  const result = await response.json();
  console.log("Response body:", result);

  return result;
}
```

Three `console.log` calls and you know: what data was sent, what status came back, and what the response contains. If the data is wrong, the bug is before the function call. If the status is an error, the bug is on the server. If the response is unexpected, the bug is in how you process the response.

Remove `console.log` statements before deploying. Or use a logging library that can be silenced in production.

## Chrome DevTools: Four Debugging Powers

### Network Tab

Your first stop when data doesn't load. Every HTTP request is listed with its URL, method, status code, headers, and response body.

What to look for:

- **Red rows:** Failed requests (4xx or 5xx status codes)
- **0 bytes responses:** The server returned nothing
- **CORS errors:** The browser blocked the request (check Console for details)
- **Timing:** Which requests are slow

Click any request to see the full details. The Preview tab shows the response formatted as JSON. The Headers tab shows what was sent.

### Console Tab

Shows errors in red (with stack traces), warnings in yellow, and your `console.log` output. Type JavaScript directly to test things.

Useful patterns:

```javascript
// Inspect the page structure
document.querySelectorAll("button");

// Check a specific element
document.querySelector("#submit-btn").disabled;

// Test a function
JSON.parse('{"valid": true}'); // works
JSON.parse("invalid json"); // shows the exact error
```

### Elements Tab

Edit CSS and HTML in real time. Useful for:

- Testing style changes before writing them
- Finding why a layout is broken (select an element, check its computed styles)
- Verifying that dynamic content was added to the DOM

### Application Tab

Inspect cookies, localStorage, sessionStorage. Essential for:

- Checking if an authentication token was stored
- Clearing storage during debugging
- Verifying that cache data is correct

## Breakpoints: Pausing Execution

When `console.log` isn't enough, breakpoints let you pause execution and inspect every variable at that moment.

In Chrome DevTools:

1. Open the Sources tab
2. Find your file in the left panel
3. Click the line number where you want to pause
4. Interact with the app to trigger that code
5. Execution pauses. Inspect variables in the Scope panel on the right.
6. Step through code line by line with the buttons at the top

In VS Code:

1. Click the left margin of a line (a red dot appears)
2. Run the debugger (F5 or the debug panel)
3. When the breakpoint hits, inspect variables in the sidebar
4. Step through the code with F10 (step over) or F11 (step into)

> **Try It:** Open any JavaScript file in VS Code. Click the left margin on a line inside a function (a red dot appears). Press F5 to start the debugger. When it pauses, hover over variables to see their values. This is ten times more useful than scattering `console.log` everywhere.

## Unit Tests With Jest or Vitest

A unit test checks one function with specific inputs and expected outputs.

```javascript
// calculateShipping.js
export function calculateShipping(weight, distance, express) {
  const base = 4.99;
  const weightCost = weight * 0.5;
  const distanceCost = distance * 0.1;
  const multiplier = express ? 2 : 1;
  return (base + weightCost + distanceCost) * multiplier;
}

// calculateShipping.test.js
import { calculateShipping } from "./calculateShipping";

test("standard shipping for 2kg, 50km", () => {
  expect(calculateShipping(2, 50, false)).toBe(10.99);
});

test("express shipping doubles the cost", () => {
  const standard = calculateShipping(2, 50, false);
  const express = calculateShipping(2, 50, true);
  expect(express).toBe(standard * 2);
});

test("zero weight and distance returns base rate", () => {
  expect(calculateShipping(0, 0, false)).toBe(4.99);
});

test("negative weight throws an error", () => {
  expect(() => calculateShipping(-1, 50, false)).toThrow();
});
```

Run with `npx jest` or `npx vitest`. Each `test()` block checks one specific behaviour. The tests document what the function does and catch regressions when the code changes.

### Testing the Field Matcher

The core of JobPilot is a function called `matchFieldsToProfile`. It takes a list of form field names and a user profile, then returns a mapping. That's a pure function with clear inputs and outputs. Perfect for unit testing.

Here's what you'd write:

```javascript
import { matchFieldsToProfile } from "./fieldMatcher";

const profile = {
  name: "Sofia Mendes",
  email: "sofia@email.com",
  phone: "+351 912 345 678",
  location: "Lisbon, Portugal",
};

test("maps standard Greenhouse fields", () => {
  const fields = ["first_name", "last_name", "email", "phone"];
  const result = matchFieldsToProfile(fields, profile);
  expect(result.email).toBe("sofia@email.com");
  expect(result.first_name).toBe("Sofia");
});

test("returns empty mapping for unrecognised fields", () => {
  const fields = ["favourite_colour", "shoe_size"];
  const result = matchFieldsToProfile(fields, profile);
  expect(Object.keys(result)).toHaveLength(0);
});

test("handles empty field list without crashing", () => {
  const result = matchFieldsToProfile([], profile);
  expect(result).toEqual({});
});
```

Three tests. Took five minutes to write. They caught a bug where the matcher crashed on an empty field list (Workday sometimes returns a blank form section). Tests don't have to be complicated. They just have to run.

**What to test:**

- Pure functions with clear inputs and outputs
- Business logic (pricing, validation, calculations)
- Edge cases (zero, negative, empty, null)
- Error conditions (what happens when things go wrong)

**What not to test (yet):**

- UI rendering details (button colours, font sizes)
- External services (test your code, mock external APIs)
- Implementation details (test what the function does, not how)

## End-to-End Tests With Playwright

E2E tests simulate a real user: open a browser, go to a page, click buttons, fill forms, verify results.

```javascript
import { test, expect } from "@playwright/test";

test("user can submit a review", async ({ page }) => {
  await page.goto("http://localhost:3000");

  // Click the rating
  await page.click('[data-rating="5"]');

  // Type a comment
  await page.fill("#comment", "Excellent service, highly recommended");

  // Submit
  await page.click("#submit-btn");

  // Verify the success message appears
  await expect(page.locator(".success-message")).toBeVisible();
  await expect(page.locator(".success-message")).toContainText(
    "Review submitted",
  );
});

test("empty review shows validation error", async ({ page }) => {
  await page.goto("http://localhost:3000");

  // Try to submit without rating
  await page.click("#submit-btn");

  // Verify the error message
  await expect(page.locator(".error-message")).toContainText(
    "Please select a rating",
  );
});
```

Playwright runs a real browser. It catches bugs that unit tests miss: the button that's technically correct but unreachable behind an overlay, the form that works in Chrome but breaks in Safari, the race condition when the user clicks submit while data is still loading.

## Common Beginner Mistake

**Testing nothing because "it works on my machine."** It works on your machine, with your data, in your browser, on your network. Production is different: different data, different browsers, different network conditions, different timezones. Automated tests catch the differences before users do. Start with three unit tests and one E2E test. That's enough to catch the worst problems.

## Checklist

- [ ] I use console.log and the Network tab as first-line debugging tools
- [ ] I can read a stack trace (bottom to top: what called what)
- [ ] I can set breakpoints in the browser or VS Code
- [ ] I have at least a few unit tests for my core business logic
- [ ] I have at least one E2E test for my most critical user flow
- [ ] I remove console.log statements before deploying

## Summary

Debugging starts with `console.log` and the Network tab. DevTools has four essential panels: Network (HTTP requests), Console (errors and logs), Elements (DOM and CSS), and Application (storage). Breakpoints pause execution for detailed inspection. Unit tests verify individual functions with specific inputs and outputs. End-to-end tests simulate real user flows in a real browser. Start small: three unit tests and one E2E test for the most critical flow.

## Challenge

1. Take any function from your project. Write three unit tests: one for the normal case, one for an edge case, and one for an error condition. Run them. If any fail, the test just found a bug.

2. Install Playwright (`npm init playwright@latest`). Write one E2E test for your app's most important flow (login, submit a form, view a list). Run it. Watch the browser automate the steps.
