---
name: git-worktree
description: Rules for working in an isolated git worktree environment
---

# Git Worktree Environment

You are working in an **isolated git worktree**. This means your `/workspace` directory is a separate working tree branched from the main repository. Multiple agents may be working on the same repo simultaneously in different worktrees.

## Critical Rules

1. **Never checkout other branches** — your worktree is locked to your branch (`agent/<task-id>`). Running `git checkout` to another branch will corrupt the worktree setup.

2. **Always commit frequently** — since your worktree is isolated, your changes only exist here until pushed. Commit after every meaningful change.

3. **Push your branch before reporting completion**:
   ```bash
   git add -A
   git commit -m "feat: <description of changes>"
   git push origin agent/<your-folder-name>
   ```

4. **Create a Pull Request** when done:
   ```bash
   # Use the delegate_git_auth MCP tool first for credentials
   gh pr create --title "<task title>" --body "<summary of changes>" --base main
   ```

5. **Pull base branch changes** if you need the latest:
   ```bash
   git fetch origin main
   git merge origin/main
   # Resolve any conflicts
   ```

## What NOT to Do

- ❌ `git checkout main` — will break worktree
- ❌ `git branch -d` on your current branch
- ❌ `git worktree` commands — managed by the orchestrator
- ❌ Force-push to `main` — only push your agent branch

## Workflow

```
1. Understand the task from CLAUDE.md
2. cd /workspace (your worktree root)
3. Make changes, test, verify
4. git add -A && git commit -m "..."
5. git push origin agent/<folder>
6. Create PR: gh pr create --base main
7. Report completion with PR URL
```

## Directory Structure

```
/opt/remote-agent/groups/<your-folder>/
├── CLAUDE.md           ← Task context from Delegate
├── .claude/
│   └── settings.json   ← MCP servers & permissions
├── workspace/          ← YOUR GIT WORKTREE (work here)
│   ├── .git            ← Linked to shared bare clone
│   └── ...             ← Repository files
└── worktree-meta.json  ← Worktree metadata (read-only)
```
