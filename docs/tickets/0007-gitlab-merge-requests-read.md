# 0007 — Reading merge requests

## Status

Open

## Goal

G2, G3 on GitLab ([ADR 0041](../adr/0041-forges-github-and-gitlab.md)). Needs 0006.

## What

On a GitLab project: the open MRs to start a workspace from, a branch workspace finding its MR, the MR's chip and
panel (state, description, author, assignee, reviewer, approvals, labels, conversation, pipeline), and its discussions
on the lines as threads, following and going outdated like GitHub's. The UI says MR and "on GitLab". Nothing is
written to GitLab yet.

## Notes

- **Endpoints:** `merge_requests?state=opened` (list; `source_branch=` to find a branch's), `merge_requests/:iid`
  (`diff_refs.base_sha` is the merge base, `sha` the head, `draft`, `detailed_merge_status`), `…/discussions` (paged),
  `…/approvals`, `…/pipelines` or the MR's `head_pipeline` and its `jobs`. The project id in a path is the
  URL-encoded repo path.
- **Threads:** a discussion is a review thread when its first note is a `DiffNote`: `position.new_line` (new side) or
  `old_line` (old side), `line_range` for several lines, at `position.head_sha`; `resolved` per discussion. Other
  discussions are the conversation; system notes are left out. A note's id is a number, stored as text.
- **Titles in the rail:** `listPullRequestTitles` is one GraphQL request on GitHub; on GitLab,
  `merge_requests?iids[]=…`.
- **Checks:** a job's state from its `status` (`success`, `failed`, `running`, `pending`, `canceled`, `skipped`,
  `manual`, …), matched exhaustively with a fallback (ADR 0035). A commit's dot is its latest pipeline's
  (`repository/commits/:sha` `last_pipeline`, or one `pipelines?sha=` per commit; pick the cheaper).
- **Skills:** the code-review skill's `gh pr view` (`skills.ts:33`) becomes the forge's: `glab mr view` on GitLab.
- **Verify:** parser tests on recorded discussions (single line, range, old side, file-level, system notes); a real MR on
  gitlab.device-insight.com reads in the running app, its threads on the right lines.
