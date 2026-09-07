---
name: "explain"
description: "Explain the assistant's previous message, or specific parts of it passed as arguments separated by |. Use when the user invokes /explain or explicitly asks for an explanation of the previous reply."
---

Explain your own previous message: the last assistant message before this invocation.

Arguments: $ARGUMENTS

- If no arguments were given, explain the whole previous message.
- If arguments were given, they are one or more excerpts separated by `|`. Explain each one, in the order given, under a short quote of the excerpt so it is clear which part is being addressed.

Excerpts may be partial or loosely paraphrased; match each to the closest passage in the previous message. If an excerpt matches nothing in that message, say so and ask what was meant instead of guessing.

An explanation should make the original clearer, not restate it. Depending on what the passage needs: unpack the reasoning behind it, define jargon or ambiguous terms, state assumptions that were left implicit, or give a concrete example.

Clearer doesn't mean more verbose, keep it proportional; a sentence may need only a sentence to explain.

Be honest about your previous message. If, on re-reading, part of it was wrong, unsupported, or poorly phrased, say so plainly and correct it rather than constructing a justification for it.
