# 0009 — Failed jobs to the agent

## Status

Open

## Goal

G4 on GitLab ([ADR 0041](../adr/0041-forges-github-and-gitlab.md)). Needs 0007.

## What

A failed job in a GitLab MR's panel has _Send to Agent_, as a failed check on GitHub does: the job and the last 150
lines of its log go to the agent pane's session.

## Notes

- `GET /projects/:id/jobs/:job_id/trace`; strip the ANSI colours and GitLab's section markers
  (`section_start:…`/`section_end:…`) before cutting the last lines.
- `sendCheck`'s prompt (`review.ts`) says "on GitHub": take the forge's noun.
- When done: devlog entry for 0005–0009, UX.md, and tick nothing yet: G2 is GitHub's goal and still open.
