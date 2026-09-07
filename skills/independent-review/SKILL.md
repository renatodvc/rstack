---
name: "independent-review"
description: "Spawn one or more uncontaminated subagents to review completed work, then a separate uncontaminated verifier subagent to validate their findings against the work itself. Use for any produced artifact: code, text, specs, emails, drafts, documents. Called with /independent-review"
---

Review completed work using fresh subagents that have not seen the conversation, then verify their findings with another fresh subagent. Fixing issues is out of scope: this skill ends by presenting a report.

## Contamination rules (apply to every subagent spawned by this skill)

- Spawn with no conversation context: use a standard subagent, which starts fresh. Spawning as a fork (subagent_type: "fork" or context: fork) is forbidden, regardless of session defaults, because forks inherit the full conversation.
- Provide only: the goal of the work, a factual description of what was done, the work itself, and any decisions the user explicitly deferred or exceptions the user explicitly granted.
- Never share your own reasoning, opinions, doubts, or expectations about the work.
- When the work lives in files, pass file paths and let the subagent read them. When it exists only in the conversation (an email draft, a plan, a snippet), pass the verbatim content, never a paraphrase.

## Step 1: Scope

Identify the artifact under review, the original goal and requirements, and any user-deferred decisions or granted exceptions. Decide the number of reviewers based on size and stakes:

- Small or low-stakes work: 1 generalist reviewer.
- Larger or higher-stakes work: 2 or 3 reviewers, each with a distinct lens. Choose lenses that fit the artifact, for example:
  - Correctness: is the content right, sound, and internally consistent?
  - Completeness: does it fulfill every stated requirement and cover the needed cases?
  - Fitness: clarity, usability, tone, and suitability for the intended audience or purpose.

## Step 2: Review

Spawn the reviewers in parallel. Each reviewer prompt must include the contamination-safe context, its lens (if any), the severity rubric below, and the output format. Each finding gets an ID (R1-F1, R1-F2, R2-F1, ...) with: location in the work, description of the issue, severity, and the evidence supporting it.

### Severity rubric

- **Blocking**: Must fix before acceptance. The issue makes the result fundamentally invalid, unsafe, unusable, or violates an explicit requirement.
- **Major**: Should fix. The result has a valid core, but the issue materially affects correctness, completeness, reliability, or fulfillment of an important requirement.
- **Minor**: Worth fixing, but the result remains correct and usable. The issue is localized and does not materially affect the outcome.

Assign severity based on impact, not the size or difficulty of the fix. A reviewer with no issues returns exactly "No findings".

## Step 3: Merge duplicates

Before verification, merge findings that are clearly the same issue at the same location. Keep the reviewers' original wording grouped under one ID, keep the highest severity assigned, and note how many reviewers reported it. Do not rewrite, reinterpret, or merge borderline overlaps; when in doubt, leave findings separate and let the verifier resolve them.

If every reviewer returned "No findings", skip to Step 5.

## Step 4: Verify

Spawn one fresh verifier subagent under the same contamination rules. Give it the merged findings, the contamination-safe context, and the work itself. The verifier examines the work directly and rules on each finding:

- **VALID**: supported by evidence cited from the work itself, not by quoting the reviewer.
- **INVALID**: refuted by evidence from the work, or by reasoning that invalidates the finding.
- **NEED_CONTEXT**: cannot be judged without information the verifier lacks. It must state exactly what is missing.

The verifier may re-classify a finding's severity when its evidence supports a different level, stating the justification. The report shows both the original and the revised severity.

If the verifier discovers a new issue absent from the reviewers' findings, it reports it as **UNVERIFIED**, with severity and evidence.

### NEED_CONTEXT escalation (one round)

Subagents cannot ask the user questions, so the orchestrator handles escalation: ask the user for exactly the missing information (AskUserQuestion when available), then spawn a fresh verifier pass covering only the NEED_CONTEXT findings, with the answers included. Supply only facts, user statements, or pointers into the work; never your own judgment of the finding. Findings still unresolved after this round remain NEED_CONTEXT in the report.

## Step 5: Report

Write the report to a markdown file in the working directory (e.g. `independent-review-<timestamp>.md`) and present a concise summary in the conversation. If there were no findings at all, state that the review passed. The report contains, each section ordered from highest to lowest severity:

1. All validated findings, with the verifier's evidence.
2. All unverified findings (new issues raised by the verifier).
3. All findings that still need context, with what is missing.
4. A summary of invalid findings, one line each with the reason.

Do not fix anything. Present the report and stop; what happens next is the user's decision.
