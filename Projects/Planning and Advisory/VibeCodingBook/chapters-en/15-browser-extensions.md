# Chapter 17 Browser Extensions: Building on Top of Chrome (MV3)

A browser extension is a programme that runs inside the browser. It can read pages, modify their content, add buttons, intercept requests, and interact with browser APIs that normal websites cannot access. This chapter covers the architecture you need to understand to build or maintain one.

## Key Terms

| Term                  | What it means                                                                          |
| --------------------- | -------------------------------------------------------------------------------------- |
| **Manifest V3 (MV3)** | The current Chrome extension architecture (mandatory since 2024)                       |
| **manifest.json**     | The configuration file that defines an extension's permissions, scripts, and structure |
| **Service Worker**    | The extension's background brain: runs when triggered, sleeps between events           |
| **Content Script**    | JavaScript injected into web pages, can read and modify the DOM                        |
| **Popup**             | The small UI that appears when the user clicks the extension icon                      |
| **Message Passing**   | How the extension's different contexts communicate with each other                     |
| **Permissions**       | The capabilities the extension requests from the user                                  |

## The Three Contexts

A browser extension has three isolated environments. They cannot access each other's variables or DOM. Communication happens through message passing.

### 1. Service Worker (background.js)

The central coordinator. It runs in the background, handles events (extension installed, alarm fired, message received), manages state, and makes API calls to external services.

Key facts:

- Lives in its own context (no DOM, no `document`, no `window`)
- Wakes up when triggered by an event, sleeps when idle
- Can access Chrome APIs: storage, alarms, tabs, runtime
- Handles business logic and communication with external servers

```javascript
// background.js (service worker)
chrome.runtime.onMessage.addListener((message, sender, sendResponse) => {
  if (message.type === "ANALYSE_PAGE") {
    // Process the data, call an API, return the result
    analyseContent(message.data).then((result) => {
      sendResponse({ analysis: result });
    });
    return true; // Keep the message channel open for async response
  }
});
```

### 2. Content Scripts (content.js)

Injected into web pages. They can read and modify the page's DOM, observe changes, and extract information. They share the page's DOM but run in an isolated JavaScript environment (they can't access the page's JavaScript variables).

Key facts:

- Can see and modify the page's HTML
- Cannot access the page's JavaScript variables or functions
- Can send messages to the service worker
- Declared in manifest.json or injected programmatically

```javascript
// content.js (injected into web pages)
// Find all job application form fields
const formFields = document.querySelectorAll("input, textarea, select");

// Extract field information
const fields = Array.from(formFields).map((field) => ({
  name: field.name || field.id,
  type: field.type,
  label: document.querySelector(`label[for="${field.id}"]`)?.textContent,
}));

// Send to service worker for processing
chrome.runtime.sendMessage({
  type: "ANALYSE_PAGE",
  data: { fields, url: window.location.href },
});
```

### 3. Popup (popup.html + popup.js)

The user interface. Appears when the user clicks the extension icon. A small HTML page with its own JavaScript. Closes when the user clicks away. Any state not saved to chrome.storage is lost when it closes.

```html
<!-- popup.html -->
<!DOCTYPE html>
<html>
  <head>
    <style>
      body {
        width: 320px;
        padding: 16px;
        font-family: system-ui;
      }
      .status {
        padding: 8px;
        border-radius: 4px;
        margin-bottom: 8px;
      }
      .active {
        background: #e8f5e9;
        color: #2e7d32;
      }
    </style>
  </head>
  <body>
    <h2>JobPilot</h2>
    <div class="status active" id="status">Active on this page</div>
    <button id="fill-btn">Auto-fill Application</button>
    <script src="popup.js"></script>
  </body>
</html>
```

## manifest.json: The Extension's Configuration

Every extension starts with `manifest.json`. It declares the extension's name, version, permissions, and which scripts run where.

```json
{
  "manifest_version": 3,
  "name": "JobPilot",
  "version": "1.0.0",
  "description": "AI-powered job application assistant",

  "permissions": ["activeTab", "storage"],

  "host_permissions": [
    "https://boards.greenhouse.io/*",
    "https://jobs.lever.co/*"
  ],

  "background": {
    "service_worker": "background.js"
  },

  "content_scripts": [
    {
      "matches": ["https://boards.greenhouse.io/*", "https://jobs.lever.co/*"],
      "js": ["content.js"],
      "run_at": "document_idle"
    }
  ],

  "action": {
    "default_popup": "popup.html",
    "default_icon": "icon-48.png"
  },

  "icons": {
    "16": "icon-16.png",
    "48": "icon-48.png",
    "128": "icon-128.png"
  }
}
```

## Message Passing: How the Contexts Talk

The three contexts communicate through Chrome's messaging API. Content script to service worker:

```javascript
// content.js → background.js
chrome.runtime.sendMessage(
  { type: "FILL_FORM", data: { fields: extractedFields } },
  (response) => {
    if (response.success) {
      applyAutoFill(response.values);
    }
  },
);
```

Service worker to content script (on a specific tab):

```javascript
// background.js → content.js on a specific tab
chrome.tabs.sendMessage(tabId, {
  type: "HIGHLIGHT_FIELDS",
  data: { fieldsToHighlight: ["name", "email", "phone"] },
});
```

Popup to service worker:

```javascript
// popup.js → background.js
chrome.runtime.sendMessage({ type: "GET_STATUS" }, (response) => {
  document.getElementById("status").textContent = response.status;
});
```

Every message has a type (a string that identifies the action) and optionally data. The receiver listens with `chrome.runtime.onMessage.addListener` and routes based on the type.

### How a Single Auto-fill Actually Flows

Here's the full message journey when a user clicks "Auto-fill" on a Greenhouse job application. This is the core of what you're building:

1. **Content script** scans the page, extracts every form field name, and sends `{ type: "FILL_FORM", fields: ["first_name", "email", "phone", ...] }` to the service worker.
2. **Service worker** receives the message, loads the user's profile from `chrome.storage.local`, and calls the OpenAI API to match profile data to form fields.
3. **OpenAI** returns a JSON mapping: `{ "first_name": "Sofia", "email": "sofia@email.com" }`.
4. **Service worker** sends the mapping back to the content script as the message response.
5. **Content script** loops through the mapping, finds each field on the page by name, and fills in the value.

Five steps. Three contexts. Two message hops. One API call. That's the entire auto-fill feature. Every other piece of JobPilot (the popup for managing profiles, the options page for settings, the badge icon showing status) follows the same message pattern.

If you're building a different kind of extension (a price tracker, a reading tool, a social media helper), the message flow is identical. Only the message types and data change.

## Permissions: The Contract With Users

Permissions determine what your extension can do. Requesting fewer permissions builds trust. Requesting too many triggers review scrutiny and user suspicion.

**activeTab:** Temporary access to the current tab, only when the user clicks your extension icon. Preferred over blanket permissions.

**storage:** Access to chrome.storage for saving extension data. Synchronises across the user's devices.

**host_permissions:** Access to specific domains. `"https://boards.greenhouse.io/*"` means your extension can run on Greenhouse pages. Reviewers check these carefully.

**\<all_urls\>:** Access to every website. Legitimate for some extensions (ad blockers, screen readers) but triggers extra scrutiny. Avoid unless necessary.

MV3 rule: no remote code. You cannot load and execute JavaScript from an external URL. Everything must be bundled in the extension package. This is a security measure.

## Chrome Storage API

Extensions use `chrome.storage` instead of localStorage. It works across all three contexts and can sync across the user's devices.

```javascript
// Save data
await chrome.storage.local.set({
  userProfile: { name: "Sofia", preferences: { autoFill: true } },
});

// Load data
const result = await chrome.storage.local.get("userProfile");
const profile = result.userProfile;

// Sync across devices (limited to 100 KB)
await chrome.storage.sync.set({ theme: "dark" });
```

## Common Beginner Mistake

**Requesting permissions you don't need.** Each permission you request appears on the installation prompt. "This extension can read and change all your data on all websites" makes users click away. Request only what the extension actually uses. Use `activeTab` instead of broad host permissions when possible. Chrome Web Store reviewers reject extensions with unjustified permissions.

> **Try It:** Go to `chrome://extensions` in your browser. Enable Developer Mode (toggle in the top right). Pick any extension you have installed and click "Details". Read the permissions list. Now imagine you're a user seeing those permissions for the first time. Would you install it?

## Checklist

- [ ] I understand the three extension contexts (service worker, content scripts, popup)
- [ ] I can read a manifest.json and identify permissions, content scripts, and the service worker
- [ ] I know that message passing connects the three contexts
- [ ] I request minimal permissions (activeTab over \<all_urls\>)
- [ ] I use chrome.storage instead of localStorage for extension data
- [ ] I understand the MV3 rule: no remote code execution

## Summary

Browser extensions have three isolated contexts: the service worker (background logic), content scripts (injected into web pages), and the popup (user interface). They communicate through Chrome's message passing API. manifest.json declares the extension's structure, permissions, and scripts. Permissions control what the extension can access: prefer activeTab over broad host permissions. Chrome storage provides persistent, synced data storage. MV3 requires all code to be bundled locally, with no remote code execution.

## Challenge

1. Install a Chrome extension you use daily. Go to `chrome://extensions`, enable Developer Mode, and click "Details". Read its permissions. Open its files (click the "service worker" link). Identify the manifest.json, background script, and content scripts.

2. Create a minimal extension: a manifest.json with a popup that displays the current page's title. The popup sends a message to a content script that reads `document.title` and sends it back. Load it as an unpacked extension and test it.
