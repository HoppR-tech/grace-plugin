---
name: grace
description: Recognize the Grace project of the current Git repository before writing, changing or reviewing code, then follow the Grace server instructions (prepare the task, apply the conventions, review). Use it at the start of every coding task.
---

# Grace

Grace serves your team's architecture and code conventions for the project you work on. This plugin
has no local detector: you declare the repository yourself, from read-only Git commands.

## 1. Declare the working copy

Run these read-only commands in the working directory. They never contact the remote.

```sh
git rev-parse --is-inside-work-tree
git rev-parse --show-prefix
git config --get remote.origin.url | sed -E 's|^([A-Za-z][A-Za-z0-9+.-]*://)[^/]*@|\1|; s|[?#].*$||'
```

On Windows PowerShell, read the origin with:

```powershell
(git config --get remote.origin.url) -replace '^([A-Za-z][A-Za-z0-9+.-]*://)[^/]*@','$1' -replace '[?#].*$',''
```

Always read the origin through this filter, never with a bare `git remote -v` or `git config`: it removes
the user, password or token of any `scheme://` origin, and any query or fragment, before you see it.

Then call the Grace tool `grace_resolve_context` with:

| Situation | Arguments |
| --- | --- |
| Not inside a Git work tree | `status: "outside_repository"`, `repository: null`, `subpath: null` |
| Inside, with an origin | `status: "repository"`, `repository: null`, `originUrl: <filtered origin>`, `subpath: <prefix without trailing slash, "" at the root>` |
| Inside, without an origin | `status: "unrecognized_repository"`, `repository: null`, `subpath: <prefix without trailing slash>` |

`sessionId` is an identifier you choose once for this conversation (letters, digits, `_` or `-`)
and reuse for every call. To work on another folder of the same repository, add `targetSubpath`
relative to the repository root.

These values are declarations: they grant no access. Grace decides what you may use.

If Grace refuses `originUrl` as invalid (for example an `http://` or local origin), call
`grace_resolve_context` again with `status: "unrecognized_repository"`, `repository: null`, the same
`subpath` and no `originUrl`.

## 2. Follow Grace

- One project returned: use it. Several: ask the user which one. None: offer to pick an accessible
  project with `grace_list_projects` or to associate the repository; never guess one.
- Then follow the Grace server instructions: prepare the task with `grace_prepare_task` before
  writing code, apply the conventions it returns, and review the change with `grace_review` before
  you finish.
- This plugin already bundles the Grace kit: the `grace-review`, `grace-discover-codebase` and
  `grace-effort` skills. Use them; do not install or update those skills or the Grace instructions
  block in the project, the plugin update replaces them. Local `grace-review-*` agent profiles stay
  managed by the local kit. Without the bundled review client, `grace-review` follows its manual
  procedure.
- If a Grace tool is unavailable or refuses access, say so; do not invent projects, talents or a
  successful review.
