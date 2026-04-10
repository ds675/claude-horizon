# Git Rules

## Core rule

**Never stage, commit, or push unless the user explicitly asks.**

Completing a task does not imply permission to commit. Always wait for a direct instruction such as "commit", "commit and push", or "push".

## Staging

Do not run `git add` unless the user has asked to commit.

## Commit messages

- Use present-tense imperative: "Add section" not "Added section"
- First line: short summary under 72 characters
- Body: bullet list of what changed and why, grouped by file/area
- Always include the co-author trailer:

  ```
  Co-Authored-By: Claude
  ```

## Push

Do not push unless the user explicitly says to push. "Commit" alone does not mean push.

## Branch

Always check the current branch before committing. Never force-push. Never commit directly to `main` without confirming with the user if the change is large or risky.

## What triggers a commit request

These phrases mean the user wants a commit (and push only if "push" is included):

- "commit the changes"
- "commit and push"
- "save this" / "save the changes"
- "push it"

Everything else — finishing a task, completing a section, making edits — does **not** trigger a commit.
