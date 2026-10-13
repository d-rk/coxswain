# 0008 — Posting to merge requests

## Status

Open

## Goal

G4 on GitLab ([ADR 0041](../adr/0041-forges-github-and-gitlab.md)). Needs 0007.

## What

On a GitLab MR, everything ADR 0037 lets the user write works: _Submit Review_, _Post to GitLab_ on a comment,
resolve and reopen, edit and delete a posted comment, edit the description, assign yourself, mark ready, merge, and
_Open Merge Request…_ for a pushed branch. Each still waits for the user's click.

## Notes

- **Submit Review:** each comment as a draft note (`…/draft_notes`; `position` for a new thread, `in_reply_to_discussion_id`
  for a reply), then `draft_notes/bulk_publish`, then `…/approve` for _Approve_. Draft notes already there are a review
  in progress: ask before adding to them, as on GitHub. Whether _Request Changes_ exists on 19.4 CE through the API, and
  how, is to be found out; leave it out on GitLab if not.
- **Positions:** a diff note needs `base_sha`, `start_sha`, `head_sha` from the MR's `diff_refs` and `old_path`,
  `new_path` with `new_line` and/or `old_line` (an unchanged line needs both). Lines GitLab won't take go to the
  conversation with the code quoted (ADR 0041 decision 5).
- **Single comments:** `POST …/discussions` (new thread) or `…/discussions/:id/notes` (reply), published at once. GitLab
  doesn't refuse these while draft notes are pending.
- **Edit, delete, resolve:** `PUT`/`DELETE …/notes/:id`; `PUT …/discussions/:id?resolved=`. The `<!-- coxswain -->`
  marker works as on GitHub: GitLab hides HTML comments.
- **Change the MR:** `PUT merge_requests/:iid` (`description`, `assignee_ids`, `draft: false`); merge with
  `PUT …/merge` (`sha` as the expected head, `squash` if the project allows it); create with `POST merge_requests`
  (`draft: true`). The merge method is the project's (`merge_method`): the panel shows it rather than offering a pick.
- **Verify:** on a scratch MR, post a review with a new thread, a reply and an approve; post a single comment; edit,
  delete, resolve; the next read links each to its entry and doesn't duplicate any.
