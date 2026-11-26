---
name: grace-effort
description: "Show or change my Grace review level: auto, low, medium or high. The level guides the harness’s choice of model and reasoning."
argument-hint: "[auto|low|medium|high]"
---

Use only an invocation method offered by the client: a command such as /grace-effort, a reference such as $grace-effort, or the skill picker. Available methods depend on the client; never invent a command it does not offer. This skill is distinct from the existing grace-effort MCP prompt: keep that prompt.

1. Read the invocation argument. Without an argument, call grace_get_effort({}), display the personal preference, its revision and returned minimums, then stop without writing. Display returned values; never infer Auto from a missing response.
2. With exactly auto, low, medium or high, first call grace_get_effort({}). Keep preference.revision, then call grace_set_effort({ effort: "<argument>", revision: <read preference.revision>, semantics: "review_level_v2" }). Never use a project/organization revision or a stale revision from context. If reading fails or supplies no revision, do not write.
3. Any other argument is invalid: show the four accepted values without changing the preference. In Plan or read-only mode, display the setting and requested change without calling grace_set_effort.
4. On a revision conflict, reread grace_get_effort({}), explain the newly observed setting and unapplied request, then stop. Never retry automatically or overwrite the concurrent change. An authorization or isolation denial stays a denial; do not switch organizations or identities to bypass it.
5. After success, display the returned preference and new revision. Auto is always adaptive: the coordinator selects model and reasoning while honoring applicable review minimums, then passes them when launching each reviewer. This requested setting does not confirm a reviewer’s actual configuration or execution. Do not change project/organization minimums or the client’s native settings.

