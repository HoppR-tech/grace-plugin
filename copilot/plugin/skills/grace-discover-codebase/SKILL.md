---
name: grace-discover-codebase
description: "Grace project onboarding: use this as soon as `grace_prepare_task` returns no active talent. Do not write code. Read the project, ask the developer to confirm your architecture hypotheses, then propose a list of talents and install them after explicit approval."
---

# Project discovery — procedure

The old onboarding asked five blind questions (project type, language, architecture, testing
approach and domain), concatenated the answers and matched that string against talent keywords.
No model read the project, and a talent whose triggers did not match the developer's words was
never found.

This procedure replaces that approach. **You** read the project and derive the answers to each
axis from files that prove them. The developer confirms or corrects them in a question call, and
**you** infer the talents. Nothing is activated without an explicit “yes”.

## Output discipline — absolute

This procedure has exactly four possible visible outputs throughout its execution, and nothing
else:

1. the question call(s) in phase 3 — one or more if the drift inventory does not fit in a
   single call, but nothing between calls;
2. the list of talents in phase 6;
3. the installation request in phase 7, immediately following the list;
4. the single phase-6 line when no talent survives phase 4 — it replaces outputs 2 and 3,
   and the procedure stops.

Output 3 requests permission before a write. It is not a closing comment; the prohibition
below does not apply to it. Omitting it would leave discovery without effect.

Everything else — reconnaissance, hypotheses, evidence, contradictions, near misses and
verdicts — is internal reasoning. Do not print or summarize it, or introduce or follow any of
the outputs with a comment, progress note, count or suggestion. No “I analyzed…”, no
“here is…”. If you want to say something outside those four outputs, say nothing.

## Phase 1 — Reconnaissance

Target: the path supplied by the user, otherwise the current working directory.

Read in this order, and stop as soon as your hypotheses are supported:

1. Manifests and configuration: `package.json`, `tsconfig.json`, `go.mod`,
   `pyproject.toml`, `pom.xml`, `Cargo.toml`, lockfiles, `docker-compose*.yml`,
   CI workflows.
2. Shape: the directory tree two or three levels deep, excluding `node_modules`/`dist`/
   `.git`. Note the **actual vocabulary** (`domain/`, `services/`, `models/`,
   `handlers/`, `usecases/`…); you will use it for phase-4 queries.
3. Entry points: the manifest's `main`/`scripts` targets and the first files in the
   largest source directory.
4. Tests: their locations, runner, unit/integration division and naming.
5. Three to five representative source files in different directories. **Read them
   completely.** This is where the real signal is.

Read only: write nothing and run nothing.

## Phase 2 — Hypotheses

Produce three to six hypotheses. Each has:

- **claim** — one sentence answering an onboarding axis (project type, language/stack,
  architecture, testing approach, domain);
- **evidence** — two or three `path/file.ts:42` references and what they show. A
  hypothesis without a file is a guess: discard it or label it `low`;
- **confidence** — `high` | `medium` | `low`;
- **question** — the single question that would decide it, framed so that “no” is useful.

State each hypothesis in the **project's vocabulary**, never naming a talent. Mapping to
talents belongs to phase 4 and nowhere earlier.

### The trap to avoid

Do not infer architecture from the very surface the audit will evaluate. “There is a
`domain/` directory, therefore the architecture is hexagonal” proves nothing: it would
grade the project against a convention it might not actually follow.

The informative signals are where **the code contradicts itself**:

- a `domain/` directory importing Express, Prisma, `fs` or anything performing I/O;
- a one-implementation port duplicated by a path that bypasses it;
- a layer imported by everything that, in turn, imports everything;
- tests booting the entire app to test a pure function;
- competing words for the same concept in different directories;
- a configuration declaring something the project has neither installed nor connected.

Always include at least one hypothesis of this kind if you find one, phrased neutrally
(“`user/domain/account.ts:12` imports the Prisma client — should the domain stay
dependency-free here?”). Such hypotheses are worth confirming and will also be the first
drifts reported by the audit.

### Drift inventory

The other hypotheses answer an axis and stop at three to six. Drifts are different: they
form a **list**, which must be **exhaustive** — each drift the developer does not see becomes
a violation you decided for them.

Do not stop at one. Scan the project for every place where code contradicts itself, and
record each as a separate item:

- **what** — one sentence naming the symptom in project vocabulary;
- **where** — exact `path/file.ts:42` lines. A drift without a line reference does not
  count: discard it;
- **why it is a drift** — the invariant or boundary it violates.

Do not group or judge items at this stage. Do not drop an “obviously intentional” drift:
that decision belongs to the developer in phase 3, and collecting that decision is the
point of the phase. Ten drifts is normal in a real repository. If you found only one,
you have not scanned thoroughly enough.

## Phase 3 — Reverse prompting

Keep the hypotheses to yourself. The first thing the developer sees throughout the
procedure is the question call: no table, preamble or dump of evidence.

Ask questions in batches (no more than four per call), most discriminating first. A
certain hypothesis that distinguishes nothing is not worth a question. Options must be
concrete and mutually exclusive, and one must be your most likely correction, rather
than just “yes”/“no”.

Priority: architecture, then drift inventory, testing approach, domain, project type.
The language and stack can be read from the manifest; never ask for them.

### Drift questions are a checklist, not a choice

Axis questions are single-choice: the developer selects one answer. Drifts are the
opposite. Each item from the phase-2 inventory must reach the developer as **its own
checkbox** with a fixed meaning:

> **checked = tolerated.** A checked item is deliberate and must **never** be reported
> as a violation. An unchecked item is a violation to fix.

State this rule in the text of the question itself, each time, in these terms.
It is the only framing the developer cannot infer from the options.

Construction rules:

- always **multi-select**. Never collapse drifts into one “how should we handle all of this”
  question or ask “is this a drift?” separately for every item;
- **one option for every drift.** An inventory is not a selection. Eleven drifts found
  means eleven options presented;
- question calls allow at most four options per question and four questions per call:
  a complete inventory may span multiple calls. Group by broken boundary (application
  layer, domain encapsulation, vocabulary and boundaries, typing and tests) and use the
  group title. Beyond sixteen items, make another call instead of truncating;
- the option title briefly names the symptom. Its description carries the evidence:
  `file.ts:42` lines and what they contain. The developer decides on evidence; a
  description without a line reference is a broken option;
- never preselect, rank or edit a comment. “Obviously intentional” is not yours to decide.

If the host has no question-call tool, do not improvise and do not abandon the inventory
or its semantics. Print the numbered drifts as text, one per line, each with line evidence;
state the rule in the same terms (**cited = tolerated**, everything else is a violation
to fix), and ask the developer to cite the numbers they tolerate. This list then stands
in for the question call in the output discipline: the format changes, not the question.

Record the checked set as tolerated. It does not select a talent by itself, but shows
which confirmed drifts remain; any unchecked drift is live evidence for the talent
covering it in phase 4.

If the developer corrects you, do not argue or acknowledge it in prose. Silently record
the correction, abandon what it invalidates and proceed directly to phase 4.

## Phase 4 — Map hypotheses to talents

The entire catalog is appended to this procedure: an ID and summary per talent.
There is nothing else to fetch. You translate between project vocabulary and catalog
vocabulary: a hypothesis phrased in the project's words (`handlers`, `usecases`)
does not automatically match a talent saying `adapters`.

The appendix contains only IDs and summaries. A non-active talent's manifest remains
closed: no `languages`, `status`, `agents` or dependency graph. Choose based on the
summary; phase 5 handles everything related to the graph.

Work by hypothesis, never by catalog: answer “what covers this confirmed fact?”, not
“which of these talents could be useful?”. Make one pass per confirmed hypothesis, plus
one per non-tolerated drift. For each candidate, keep its ID, the hypothesis it serves,
and the line of evidence behind that hypothesis. Exclude:

- a talent whose summary targets a stack the project does not use;
- a talent unsupported by any confirmed hypothesis. If you cannot connect it to one,
  it does not belong in the selection, however sensible it sounds;
- a duplicate: when two talents cover the same hypothesis, keep the one whose summary
  uses the project's own words.

Keep near misses in your reasoning; do not print them.

## Phase 5 — Close the graph

The catalog is a graph, not a list: hard prerequisites (`requires`), incompatibilities
(`conflicts`) and complements (`recommends`). These edges are **not** visible here,
because inactive talents' manifests are closed.

Do not guess or invent IDs to fill gaps: close the graph after application, with
`grace_doctor`, which reports missing prerequisites and conflicts against the actually
active selection. Phase 6 therefore outputs the **direct** selection justified by
each confirmed hypothesis and nothing more.

## Phase 6 — Output

The phase-4 selection, as a flat list, and nothing else:

```
- <talentId>
- <talentId>
```

One ID per line. No heading, table, evidence column, count, rejected IDs, verdict or
sentence before the list. Copy IDs exactly as the appendix spells them. Go straight
to phase 7 with nothing between the two.

If none survives phase 4, print one line saying no talent matches and stop — there
is nothing to install.

## Phase 7 — Install

The list does nothing until it is applied, and you are the one to apply it. Do not
leave it to the developer to ask: immediately after the list, ask whether they want
to install these talents. One question, two outcomes, no vague third option.

If “yes”, call `grace_install_talents({ talents: ["<id>", …] })` — a list of bare IDs,
one per chosen talent, no objects or flags — in a single atomic call/revision.
Then call `grace_doctor`, which reports missing prerequisites and conflicts from
the actually active selection, unavailable to you in phase 5. If it reports any,
present them the same way and apply the additional selection in a second
`grace_install_talents` call after approval.

On “no” or silence, write nothing and do not insist. Discovery ends here.

## Appendix — The catalog

One talent per line: its ID and summary. This is the only catalog view during
onboarding and it is enough for phase 4. Never print it: the output discipline
applies to it too.

_(The catalog index is not in this file: it depends on the project and changes as talents are published. Call `grace_discover_codebase` to get it — the response contains this same procedure, including the index.)_


