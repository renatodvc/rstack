---
name: ears-spec
description: Interviews the user and then writes a requirements spec in EARS (Easy Approach to Requirements Syntax) format plus a traceable task list for a new software project or feature. Use whenever the user wants a spec, requirements document, PRD, functional or non-functional requirements, acceptance criteria, or "shall" statements, or mentions EARS or wants to pin down what a system must do before building it. Also use when the user types /ears-spec. Do not write the spec on the first turn; the skill gathers context interactively first.
---

# EARS Spec Writer

Turn a conversation into `spec.md` (what the system must do) and `tasks.md` (work packages that close it), both readable by an engineer who was not in the room. Gather first, write second.

## Phase 1: Gather context

A dialogue, not a questionnaire. Each turn: state what changed in a sentence, propose or push back where useful, then ask one question (a second only if tightly coupled). Propose candidate requirements in EARS form early and often, prefixed "Proposed:" until accepted.

Challenge anything that would fail the checks below, plus conflicts between statements, missing failure cases (invalid input, timeouts, dependency down, duplicates, partial failure, abuse), and scope creep.

Cover, in whatever order the conversation allows:
- What exists now and what problem this solves
- Goals and non-goals (always ask for non-goals)
- System name (one name, used everywhere), boundary, actors, external systems
- Behavior across all EARS patterns: always-true, event-driven, state-dependent, unwanted, optional
- Quality attributes, each with a number
- Interfaces: data shapes, types, signatures, contracts
- Constraints: mandated stack, integrations, deadlines
- Unknowns and things deliberately left undecided

Track what is known, assumed, and open. Never silently promote an assumption to a requirement.

Offer to write when the system, actors, boundary, goals, and non-goals are named, every applicable area has a requirement with numbers where they belong, and no open question would change the shape of the spec. If the user says "write it" earlier, comply and record the gaps as assumptions or open questions.

## Phase 2: Write the files

Say "Writing the spec now." and produce both files without narrating. Default location `specs/<feature-name>/`. Never overwrite existing files; suffix `-2` or ask.

### spec.md

1. **Context.** 3 to 5 sentences: what exists now, what problem this solves, hard constraints.
2. **Goals / Non-goals.** Bullets. Non-goals must be non-empty.
3. **Requirements.** One EARS sentence per line, grouped by area. Quality attributes live here too (usually ubiquitous with a number). IDs `REQ-01`, or `REQ-<AREA>-01` once the spec spans several areas or about 20 requirements. Never renumber. Unmarked means user-stated; append `(proposed)` for an accepted Claude proposal or `(assumed, Q-nn)` for an assumption.
4. **Interfaces.** Schemas, types, signatures, message formats, in code blocks. Not prose.
5. **Open questions.** `Q-01`... Each with why it matters and the REQ IDs depending on it. Provisional decisions go here as `Assumed: <X>, confirm with <who>`.

```markdown
- **REQ-AUTH-03** When a user submits valid `Credentials`, the Auth Service shall issue a `SessionToken` that expires 24 hours after issuance.
- **REQ-AUTH-04** If the identity provider does not respond within 3 seconds, then the Auth Service shall return HTTP 504 and record the timeout. (proposed)
```

### tasks.md

Work packages, each independently mergeable and verifiable against the requirements it closes. No implementation steps.

```markdown
## T-01 Issue session tokens
Closes: REQ-AUTH-03, REQ-AUTH-04
Done when: valid credentials return a `SessionToken` with 24h expiry; a 3s+ IdP delay returns 504 and a logged timeout.
Touches: `SessionToken` schema, auth handler
```

`Closes: enabling` is allowed only for work no requirement can name (scaffolding, migrations, CI). Keep such tasks few.

### Checks

Run on your own draft; a failure means edit and re-run.

- One trigger and one outcome per requirement, one test case each. Repeated `shall` is fixed by rewording, not splitting.
- No requirement for an outcome the trigger already implies, or for a side effect the user never asked for.
- No adjective without a number: "fast" becomes "p95 under 200 ms".
- If it can be a schema or a type, it is in Interfaces. EARS sentences name a schema, never describe its shape.
- Unknowns go in Open questions, never guessed. Every `(assumed, Q-nn)` points at a real entry.
- Every REQ is closed by a task; every task closes a REQ or is tagged enabling.
- Non-goals is non-empty.

Present both files with a two-sentence summary, the open questions that most need the user, and any enabling tasks.

## EARS reference

`<precondition> <trigger> the <system name> shall <response>`

| Pattern | Template |
|---|---|
| Ubiquitous | The `<system>` shall `<response>`. |
| Event-driven | When `<trigger>`, the `<system>` shall `<response>`. |
| State-driven | While `<state>`, the `<system>` shall `<response>`. |
| Unwanted behavior | If `<undesired condition>`, then the `<system>` shall `<response>`. |
| Optional feature | Where `<feature is included>`, the `<system>` shall `<response>`. |
| Complex | While `<state>`, when `<trigger>`, the `<system>` shall `<response>`. |

- One trigger, one outcome per requirement, verifiable by a single test case. Write one `shall` and join the facets of that outcome with "and": "shall print the command list and exit with status 0". A repeated `shall` is a style error; fix it by removing the word, not by splitting. Split only when facets have different triggers or targets, or would plausibly be built or verified separately.
- A requirement earns its place only if someone could build it wrong. Do not state what the trigger already rules out (no request is sent when the URL cannot be parsed) or cosmetic side effects nobody asked for.
- Name the system; never "it", "the app", "we". `shall` only; never should, may, will, must, can. No escape clauses (where possible, as needed).
- `when` for a discrete event, `while` for a persisting condition, `if/then` for what should not happen but might.
