<!-- grace:begin -->
At the first exchange of a new conversation (thread), before handling the request, follow `grace-update` to check the local kit: fetch the manifest with `grace_check_installation({ harness, locale })`, validate it and read its current procedure. If MCP is unavailable, use the public fallback described by the skill. An explicit update request authorizes application after backup; otherwise, offer **Update / Later**.

## Grace Talents (MCP)

The architecture and coding conventions come from the **Grace and Custom Talents** served by the `grace` MCP server. They take precedence over conventions inferred from the repository and your habits. Do not guess them.

Briefly announce the talents received and the planned checks. Explain decisions influenced by their rules. If blocked, state the consequence and the next action. Distinguish received instructions from checks actually performed; keep the summary for the end.
After harness selection, before every review or retry, announce the requested level, exact model, native effort and scope; in Auto, add the chosen level and reason. Group identical configurations. Unknown value: “unavailable” and limitation, without inferring effort from level. Distinguish planned selection, observed configuration and confirmed execution; correct any returned mismatch.

1. **Before writing any code** — After checking the kit, call `grace_prepare_task({ talentIds })` before even planning the implementation.
   - This covers any creation or modification of code, including tests, configuration-as-code and migrations. Reading or explaining alone is excluded.
   - Designate every relevant talent from the directory described by the tool; designate Grace and Custom talents the same way. Do not describe the task or leave the list empty. An undesignated talent will neither be served nor reviewed.
   - Keep the `resolutionId`: it seals the review scope.

2. **If no talent is active** — Do not write code. Call `grace_discover_codebase` immediately and load its matching skill if available. Follow the entire procedure: analyze the repository, confirm hypotheses with the user, obtain explicit approval of the talent list, install the talents, call `grace_doctor`, then install the prerequisites. Do not suggest bypassing this gate; a small task or a silent user is no exception. The sole exception is if the user spontaneously asked earlier to work without talents.

3. **As needed** — Call `grace_deepen({ uri })` on the supplied `grace://…` addresses. Add the preparation's `resolutionId` when available to associate the reading with the resolution. For multiple addresses, use `grace_deepen({ uris: [{ uri }, …] })` in sequential batches of at most 20.

4. **Before finishing** — Load `grace-review`, then call `grace_review({ resolutionId, format: "inline-v1" })` and, when the response announces several parts, each following `part` with its `layoutDigest`: the dossier contains every check, Grace rule and Custom instruction. Do not call it per talent or per reviewer; give each reviewer its exact criteria. These tools supply instructions, not a verdict: verify the entire change and use `grace_record_validation` to record each check's result, explanation and supporting evidence, for every verifiable Grace rule and every Custom Talent in scope. Fix blocking violations, rerun the affected checks and record the complete updated report with a new `submissionId`.

Load skills by name from the locations provided by the harness. Let the harness load them automatically if supported. An available skill takes precedence over this summary, never over rules served by the tools; otherwise the MCP server instructions are authoritative.
<!-- grace:end -->