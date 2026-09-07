---
name: "fix-findings"
description: "Fix the findings from an independent-review report. Follow-on to the independent-review skill, but never run it automatically after a review: the user reads the report first and decides. Only run when the user explicitly invokes /address-findings or explicitly asks to address the review findings."
disable-model-invocation: true
---

Take an independent-review report, resolve every open decision with the user, then implement the fixes directly. This skill spawns no subagents: the orchestrating agent does the fixing.

## Step 1: Locate the report

Resolve the findings source in this order:

1. A path given as an argument to the invocation.
2. The independent-review report already present in the conversation.
3. The most recent `independent-review-*.md` file in the working directory.

If none is found, or multiple candidates are ambiguous, ask the user which report to use. Read the full report before proceeding.

## Step 2: Build the scope

Severity is the verifier's classification; where the report shows an original and a revised severity, the revised one governs.

- **In scope**: all validated findings of Blocking or Major severity.
- **In scope after confirmation**: UNVERIFIED findings of Blocking or Major severity. Each must be confirmed as real by the user during Step 3 before it may be fixed.
- **Triaged via questions**: NEED_CONTEXT findings. Ask the user for the missing context in Step 3; once supplied, classify them into scope (or out of it) like any other finding.
- **Candidate minors**: validated Minor findings that are unambiguous, small, low-risk, and low-impact. Compile them into a proposed list; do not fix any minor without approval of the list in Step 3.
- **Ignored**: invalid findings, and minors that do not meet the bar above.

## Step 3: Resolve decisions with the user

Before touching anything, ask the user about every point that needs their input: decisions the fixes depend on, disambiguation of requirements, trade-offs between competing fixes, confirmation of UNVERIFIED findings, missing context for NEED_CONTEXT findings, and approval (or trimming) of the proposed minor-findings list.

Use AskUserQuestion when available; otherwise ask in plain conversation. Ask as many questions, across as many rounds, as the findings require. Answers may raise new questions; keep going until nothing in scope depends on an unresolved decision. The user may defer or reject any finding here; record that outcome instead of fixing it.

Do not start implementing until all rounds are complete.

## Step 4: Implement

Apply all fixes as one coherent pass, following the user's answers exactly. Fix only what the findings and answers cover; do not refactor, restyle, or improve surrounding work beyond what a fix requires. If a fix turns out to be impossible or to conflict with an answer, stop fixing that finding, mark it blocked with the reason, and continue with the rest.

## Step 5: Report

Write a report to the working directory (e.g. `address-findings-<timestamp>.md`) and give a concise summary in the conversation. For every finding that entered Step 2, state its outcome:

- **Fixed**: what was changed, and where.
- **Deferred**: the user chose not to fix it, with their stated reason.
- **Rejected**: the user judged it not a real issue.
- **Blocked**: could not be fixed, with the reason.

Close by noting that the user can re-run /independent-review on the fixed work for a fresh uncontaminated pass, since the fixing agent cannot impartially review its own fixes.
