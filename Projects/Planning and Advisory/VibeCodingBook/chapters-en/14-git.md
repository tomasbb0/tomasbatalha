# Chapter 16 Git: Your Time Machine

You've been writing code, and everything works. You make one change. Everything breaks. You try to undo it. You can't remember what you changed. Your only option is starting over. Git prevents this entirely. It tracks every change to every file and lets you travel back in time to any previous version.

## Key Terms

| Term                  | What it means                                                                       |
| --------------------- | ----------------------------------------------------------------------------------- |
| **Git**               | A version control system that tracks changes to files                               |
| **Repository (repo)** | A project folder tracked by Git                                                     |
| **Commit**            | A snapshot of your project at a specific moment                                     |
| **Branch**            | A parallel version of your code (for experiments without breaking the main project) |
| **Merge**             | Combining changes from one branch into another                                      |
| **Remote**            | A copy of your repository on a server (GitHub, GitLab)                              |
| **Pull Request (PR)** | A proposal to merge your branch into the main branch, with review                   |

## The Core Workflow

Git's fundamental loop has five commands.

### git init

Creates a new Git repository in your project folder:

```bash
cd my-project
git init
```

This creates a hidden `.git/` folder. From now on, Git watches every file in this directory.

### git add

Tells Git which changes to include in the next snapshot:

```bash
git add index.html          # Add one file
git add src/                # Add an entire folder
git add .                   # Add everything that changed
```

### git commit

Takes a snapshot of the added changes:

```bash
git commit -m "Add review submission form"
```

The message after `-m` describes what changed. Good messages are specific: "Add review submission form" not "Update stuff". Each commit is a point you can return to.

### git push

Sends your commits to a remote server (GitHub):

```bash
git push origin main
```

Now your code is backed up online and accessible from any computer.

> **Try It:** Create a folder, run `git init`, create a file, `git add .`, `git commit -m "first commit"`. Then run `git log --oneline`. You just created your first checkpoint. Everything from here is just more checkpoints.

### git pull

Downloads changes from the remote server to your machine:

```bash
git pull origin main
```

If someone else pushed changes (or you pushed from another computer), this brings your local copy up to date.

## Branches: Parallel Universes

The `main` branch is your production code. It should always work. When you want to add a feature or fix a bug, create a branch:

```bash
git checkout -b feature/dark-mode     # Create and switch to a new branch
```

Now you're on `feature/dark-mode`. Every commit you make lives on this branch. `main` is untouched. If your experiment breaks everything, `main` is still safe.

```bash
# Work on the feature
git add .
git commit -m "Add dark mode toggle"
git commit -m "Apply dark theme to all components"

# When done, merge back into main
git checkout main                      # Switch to main
git merge feature/dark-mode            # Bring in the changes
```

If the merge succeeds, `main` now has dark mode. If there are conflicts (two branches changed the same line), Git asks you to resolve them manually.

## Handling Merge Conflicts

Conflicts happen when two branches modify the same line. Git marks the conflict in the file:

```
<<<<<<< HEAD
  <h1>Welcome to the Shop</h1>
=======
  <h1>Welcome to Our Store</h1>
>>>>>>> feature/rebrand
```

The text between `<<<<<<< HEAD` and `=======` is what `main` has. The text between `=======` and `>>>>>>>` is what your branch has. You choose which to keep (or combine both), delete the markers, and commit:

```bash
git add index.html
git commit -m "Resolve heading conflict: use 'Our Store'"
```

## GitHub: More Than Backup

GitHub stores your repository online and adds collaboration features:

1. **Backup.** Your code exists in at least two places: your computer and GitHub.
2. **Deployment trigger.** Vercel, Netlify, and others watch your GitHub repo and redeploy when you push.
3. **Collaboration.** Multiple people can work on the same project with branches and pull requests.
4. **Issue tracking.** Bug reports and feature requests, linked to code changes.

## Pull Requests: Proposing Changes With Review

Instead of merging directly, you push your branch to GitHub and open a Pull Request:

1. Push your branch: `git push origin feature/dark-mode`
2. On GitHub: click "Compare & Pull Request"
3. Write a description of what changed and why
4. Someone (or you, for personal projects) reviews the changes
5. If approved, merge the PR

Pull Requests create a record of every change: who made it, why, and when. Even on solo projects, they're useful for keeping a clean history.

## .gitignore: Files That Should Never Be Committed

Some files should never go into Git:

```
# .gitignore

# Environment secrets
.env
.env.local

# Dependencies (anyone can reinstall these)
node_modules/

# Build output (generated, not source)
dist/
build/

# OS files
.DS_Store
Thumbs.db

# IDE settings
.vscode/
.idea/
```

Create `.gitignore` before your first commit. If you accidentally commit a secret (like an API key), it stays in the history even if you delete the file. Rotate the key and add the file to `.gitignore`.

### What Happened With JobPilot's API Key

During early development, JobPilot had its OpenAI API key stored directly in a JavaScript file that got committed to Git. The key wasn't in `.env`. It was right there in the source code: `const OPENAI_KEY = "sk-..."`. One push to a public GitHub repo and within four hours, someone used the key to run their own requests. The bill was small (about $12), but the lesson was permanent.

The fix took three steps: revoke the leaked key in the OpenAI dashboard, generate a new one, and move it into a `.env` file that was listed in `.gitignore`. The old key is still visible in the Git history if you know where to look. That's why revoking the key matters more than deleting the file.

Do this now, before your first commit: add `.env` to your `.gitignore`. Not after. Before. This applies whether you're building JobPilot or any other project.

## The Recovery Commands

Things go wrong. Git has tools for each situation:

**git stash:** Temporarily shelve changes you're not ready to commit.

```bash
git stash              # Hide current changes
git stash pop          # Bring them back
```

**git revert:** Create a new commit that undoes a previous commit. Safe for shared branches because it doesn't rewrite history.

```bash
git revert abc123      # Undo commit abc123 with a new commit
```

**git reset:** Move back to a previous commit. More aggressive. Use with caution on shared branches.

```bash
git reset --soft HEAD~1    # Undo last commit, keep changes staged
git reset --hard HEAD~1    # Undo last commit, discard changes entirely
```

**git log:** See the history of commits.

```bash
git log --oneline          # Compact view: one line per commit
```

## Common Beginner Mistake

**Committing secrets to the repository.** An `.env` file with your Stripe secret key, pushed to a public repository, will be found. Bots scan GitHub continuously for exposed keys. The fix: add `.env` to `.gitignore` before committing, use environment variables in deployment, and rotate any key that was ever committed.

## Checklist

- [ ] I can initialise a repo, add files, commit changes, and push to GitHub
- [ ] I use branches for new features (never commit directly to main for anything significant)
- [ ] I can resolve a merge conflict
- [ ] My `.gitignore` excludes `.env`, `node_modules/`, and `dist/`
- [ ] I write descriptive commit messages
- [ ] I know how to use git stash, git revert, and git reset for recovery

## Summary

Git tracks every change to every file in your project. The core workflow is: init, add, commit, push, pull. Branches let you work on features without risking the main codebase. Merge conflicts happen when two branches change the same line. GitHub provides backup, deployment triggers, and collaboration through Pull Requests. `.gitignore` prevents secrets and generated files from being committed. Recovery tools include stash (temporary shelving), revert (undo via new commit), and reset (move back to a previous state).

## Challenge

1. Create a new project folder. Initialise Git. Create an `index.html` file. Add it. Commit with the message "Initial HTML structure". Make a change. Commit again. Run `git log --oneline` to see both commits.

2. Create a branch called `feature/about-page`. Add an `about.html` file. Commit on the branch. Switch back to `main`. Confirm that `about.html` doesn't exist on `main`. Merge the branch. Confirm the file now exists.
