# Chapter 15 Dependencies, Packages & the Build Step

You installed Node.js in Chapter 6 (Your Workshop). Now it's time to understand what happens when you type `npm install`. A "hello world" project has 200 MB in its node_modules folder. This chapter explains why, and why it matters.

## Key Terms

| Term              | What it means                                                              |
| ----------------- | -------------------------------------------------------------------------- |
| **Dependency**    | Someone else's code that your project uses                                 |
| **Package**       | A published, reusable piece of code (a library, a framework, a tool)       |
| **npm**           | Node Package Manager: installs, manages, and publishes JavaScript packages |
| **package.json**  | The file that lists your project's dependencies and configuration          |
| **node_modules/** | The folder where npm puts all downloaded packages                          |
| **SemVer**        | Semantic Versioning: major.minor.patch (e.g. 2.1.4)                        |
| **Build step**    | The transformation from source code to deployable files                    |

## What a Dependency Is

A dependency is someone else's code that you install and use as if you wrote it. Instead of writing a date formatting function from scratch, you install a package that already does it:

```bash
npm install date-fns
```

Now your code can use it:

```javascript
import { format } from "date-fns";
format(new Date(), "dd/MM/yyyy"); // "15/01/2026"
```

One line of code. Behind it: 40,000 lines someone else wrote, tested, and maintained. That's a dependency.

## package.json: Your Project's Ingredient List

Every Node.js project has a `package.json` file. It describes the project and lists its dependencies.

```json
{
  "name": "cafe-pessoa",
  "version": "1.0.0",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "react": "^18.3.0",
    "react-dom": "^18.3.0",
    "date-fns": "^3.6.0",
    "@supabase/supabase-js": "^2.43.0"
  },
  "devDependencies": {
    "vite": "^5.4.0",
    "typescript": "^5.5.0",
    "@types/react": "^18.3.0"
  }
}
```

Three key sections:

**dependencies:** What your app needs to run. React for the interface. date-fns for date formatting. Supabase for the database. These get shipped to production.

**devDependencies:** What developers need to build the project. Vite for the build step. TypeScript for type checking. These stay on your machine and never reach users.

**scripts:** Shortcuts for common commands. `npm run dev` starts the development server. `npm run build` produces production files. You define these yourself.

## What npm install Actually Does

When you run `npm install`:

1. npm reads `package.json`
2. For each dependency, npm downloads the package from the npm registry (a massive online catalogue)
3. Each package has its own dependencies, which npm also downloads
4. Everything goes into the `node_modules/` folder
5. npm creates or updates `package-lock.json` (the exact versions of everything installed)

Your project depends on 5 packages. Those 5 depend on 40 others. Those 40 depend on 200 more. The result: a `node_modules/` folder with hundreds of packages and millions of lines of code.

This is normal. This is how modern software works.

## SemVer: What the Numbers Mean

Version numbers follow Semantic Versioning: `major.minor.patch`.

- **Patch** (2.1.3 → 2.1.4): Bug fix. Nothing breaks.
- **Minor** (2.1.4 → 2.2.0): New feature. Existing code still works.
- **Major** (2.2.0 → 3.0.0): Breaking change. Your code might need updating.

In `package.json`, the `^` symbol matters:

- `"react": "^18.3.0"` means "install 18.3.0 or any newer version that doesn't change the major number". So 18.4.0, 18.5.2 are fine. 19.0.0 is not.
- `"react": "~18.3.0"` means "only patch updates". 18.3.1 is fine. 18.4.0 is not.
- `"react": "18.3.0"` means "exactly this version". Nothing else.

The `^` default is convenient but risky. A minor update can introduce bugs. The `package-lock.json` file protects you: it records the exact version that was installed, so everyone on the team gets identical dependencies.

> **Try It:** Run `npm init -y` in an empty folder. Then `npm install react`. Open `node_modules/` and count the folders inside (hint: `ls node_modules | wc -l`). Now open `package-lock.json` and scroll. That's the dependency tree. Every folder in `node_modules/` is someone else's code that your project now trusts.

## pip and requirements.txt: The Python Equivalent

Python uses the same concept with different tools:

```bash
pip install requests flask
pip freeze > requirements.txt
```

`requirements.txt` is Python's `package.json`:

```
requests==2.31.0
flask==3.0.3
```

To install everything: `pip install -r requirements.txt`.

The principle is identical: list your dependencies. Anyone can reproduce your environment.

## The Build Step: Why Source Code ≠ Shipping Code

Modern web projects have a build step. The code you write is not the code that reaches users. Between your source files and the deployed output, several transformations happen:

### Transpilation (TypeScript → JavaScript)

You write TypeScript for safety and clarity. Browsers only understand JavaScript. The build step converts one to the other.

```typescript
// You write (TypeScript)
function greet(name: string): string {
  return `Hello, ${name}`;
}

// Browser receives (JavaScript)
function greet(name) {
  return `Hello, ${name}`;
}
```

### Bundling (Many Files → Few Files)

Your project has 200 files imported into each other. The build tool traces the imports and combines them into one or two files. Fewer files means fewer HTTP requests means faster page loads.

### Minification (Readable → Small)

```javascript
// Before (readable, 847 bytes)
function calculateShippingCost(weight, distance, express) {
  const baseRate = 4.99;
  const weightFactor = weight * 0.5;
  const distanceFactor = distance * 0.1;
  const expressFactor = express ? 2 : 1;
  return (baseRate + weightFactor + distanceFactor) * expressFactor;
}

// After (minified, 127 bytes)
function c(w, d, e) {
  return (4.99 + w * 0.5 + d * 0.1) * (e ? 2 : 1);
}
```

Same logic. 85% less data. Multiply this across thousands of functions.

### Tree Shaking (Remove Unused Code)

If you import one function from a library of 500 functions, the build tool includes only the one you use. The other 499 don't make it into the bundle.

**Vite** handles all four transformations. Run `npm run build` and it produces a `dist/` folder with optimised, production-ready files.

### JobPilot's Build: Vite + CRXJS

JobPilot uses Vite with a plugin called CRXJS. Standard Vite produces a single-page web app. Chrome extensions need something different: a `manifest.json`, separate entry points for the popup, the service worker, and each content script. CRXJS reads your manifest and tells Vite how to bundle each piece separately.

The build step still does all four things (transpile, bundle, minify, tree-shake), but the output is a folder with multiple files instead of one big bundle. That folder is what you'll zip and upload to the Chrome Web Store in Chapter 24. Without CRXJS, you'd have to write a custom Vite config for each file, which is exactly the sort of annoying plumbing that plugins exist to handle.

If you're building a web app instead, standard Vite is all you need. `npm run build` and you're done.

## Supply Chain Risk

Your project depends on hundreds of packages you've never read. Each package depends on more packages. This chain of trust is your supply chain.

In 2024, a developer intentionally corrupted two popular npm packages (`colors` and `faker`), breaking thousands of projects. In 2023, malicious packages on npm stole environment variables from developers who installed them.

Mitigations:

- Lock versions with `package-lock.json`
- Run `npm audit` to check for known vulnerabilities
- Be cautious with packages that have few downloads or unknown authors
- Use `npx` for one-off tools instead of installing globally
- Review what you install (at least read the README and check the GitHub repository)

## Common Beginner Mistake

**Running npm install in the wrong folder.** If you run `npm install` in your home directory instead of your project folder, npm creates a `node_modules/` and `package.json` in the wrong place. Your project won't find the packages. Always confirm you're in the project root (`ls` should show `package.json`) before installing.

## Checklist

- [ ] I understand what dependencies are (other people's code my project uses)
- [ ] I can read a `package.json` and distinguish dependencies from devDependencies
- [ ] I know what SemVer means (major.minor.patch) and what `^` does
- [ ] I understand the build step: transpilation, bundling, minification, tree shaking
- [ ] I never edit files in `dist/` or `node_modules/`
- [ ] I run `npm install` in the correct folder (where `package.json` is)

## Summary

Dependencies are reusable packages installed with npm (JavaScript) or pip (Python). `package.json` lists your dependencies and scripts. `node_modules/` holds all downloaded packages. SemVer (major.minor.patch) communicates the risk level of updates. Modern projects use a build step that transpiles TypeScript to JavaScript, bundles many files into few, minifies code for size, and removes unused code via tree shaking. Vite is the standard build tool. Supply chain risk is real: lock versions, audit regularly, and be deliberate about what you install.

## Challenge

1. Create a new project: `npm init -y`. Install `date-fns`. Open `node_modules/` and count how many folders are inside. Install `react` and `react-dom`. Count again. Notice the exponential growth.

2. Check `package-lock.json`. Find the exact version of `date-fns` installed. Change the version in `package.json` to an older one (e.g. `"date-fns": "2.0.0"`). Run `npm install`. Check what changed in the lock file.
