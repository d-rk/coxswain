# 0006 — GitLab hosts and projects

## Status

Open

## Goal

G2 on GitLab ([ADR 0041](../adr/0041-forges-github-and-gitlab.md)). Needs 0005.

## What

Settings lists each GitLab host `glab` is signed in to, with the user signed in there. The repository list shows a
host's projects next to GitHub's, searchable, and picking one clones it. _Add Local Repository…_ on a repository whose
`origin` is on such a host makes a GitLab project.

## Notes

- **Hosts:** the keys of `hosts:` in `glab`'s `config.yml` (`$GLAB_CONFIG_DIR`, else `~/.config/glab-cli/`), less
  those without a token. `glab auth status` is too slow to ask: it calls every host, and an unreachable one times out.
- **Token:** `glab config get token --host <host>`, asked each time and never stored. Empty means signed out, or a
  token in the system keyring, which `glab config get` can't read; find out which on a keyring setup before settling
  it (`glab auth status --hostname <host> --show-token` is the fallback).
- **Client:** `fetch` against `https://<host>/api/v4`, `PRIVATE-TOKEN` header; pages by `x-next-page`. TLS to
  gitlab.device-insight.com works with Node's own CAs (checked 2026-10-13).
- **Repository list:** `GET /projects?membership=true&order_by=last_activity_at&search=…&simple=true`.
  `path_with_namespace` is the repo, any depth. `Projects.tsx`'s `RepoRow` shows the full path; `format.ts`'s
  `projectLabel` must not split it into owner and name.
- **Clone:** `glab config get git_protocol --host <host>`; HTTPS clones use `glab`'s credential helper if the user set it
  up, else theirs (ADR 0006 decision 4).
- **Verify:** parser tests on recorded API JSON; add a project from gitlab.device-insight.com both ways (clone and local
  repository) in the running app.
