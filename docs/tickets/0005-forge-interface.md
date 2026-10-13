# 0005 — GitHub behind a forge interface

## Status

Open

## Goal

G2, G4: makes room for GitLab ([ADR 0041](../adr/0041-forges-github-and-gitlab.md)) without changing what a user
sees.

## What

Everything works as before on GitHub projects and local repositories. Underneath, nothing outside
`core/forges/github.ts` knows it's GitHub.

## Notes

- **Glossary first:** Repository, Pull request, Comment, Entry, Comment thread, Post, Resolved, Review comment,
  Conversation, PR panel, Check, Branch workspace, Local change, Scope, Clone, User and the relation diagram say
  "GitHub" where they mean the project's forge. Then edit ADRs 0006, 0037 and 0040 to match 0041, and accept it.
- **`core/forge.ts`:** shaped by what's called today: `getPullRequest`, `findPullRequest`, `getPullRequestHead`,
  `createPullRequest`, `listPullRequests`, `listPullRequestTitles`, `listRepos`, `getCurrentUser`, `readJobLog`
  (`github.ts`); the mutations in `pull-requests.ts` (`postReview`, `postComment`, `resolveOnGitHub`,
  `changePullRequest`, `permalinkOf`) and `review.ts` (`mutations()`: edit and delete a comment). Raw
  `githubGraphql`/`githubRequest` don't leave the GitHub forge. GitHub's node ids, `databaseId` lookups and
  `PullRequestRef` stay inside it; the interface passes opaque string ids.
- **Capabilities:** merge methods, verdicts (`APPROVE`, `REQUEST_CHANGES`, `COMMENT`), whether a single comment is
  refused while a review is pending, the forge's nouns (PR/MR, "on GitHub").
- **Problems:** `GitHubProblem` becomes `ForgeProblem` carrying the forge and host, so `ProblemMessage`
  (`components/text.tsx`) can say `gh auth login` or `glab auth login --hostname …`.
- **Migration** (check on a copy of the real database first: `COXSWAIN_USER_DATA=/tmp/cx pnpm dev`):
  - `projects.github` → `forge`, `host`, `repo`; the check and the `projects_clones` unique index follow.
  - `entries.github_id`, `github_thread_id`, `github_url`, `github_resolved` → `forge_*`, plus a stored
    in-conversation flag replacing `github_url like '%#issuecomment-%'` (`pull-requests.ts:219`, `inConversation`,
    `review.ts`).
- **Hard-coded github.com:** `git.ts` clone URLs and `gitHubOf()` (becomes "which forge is this remote on"),
  `ghProtocol()`, `permalinkOf`.
- **Names:** IPC channels `github:*` → `forge:*`, the core event `"github"` → `"forge"` (`events.ts`, `queries.ts`),
  `watch.ts`'s `checkGitHub` → `checkForge`. The UI's texts stay "GitHub" until 0007, now coming from the forge's
  nouns.
- **Verify:** the existing tests pass unchanged except for renames; a GitHub PR's read, post, resolve and merge work in
  the running app.
