<final_response_format>
This applies to your final response only: the text you write after your last tool call, when you end the turn (the "recap"). It does not apply to reasoning, tool use, code, files, or any text I asked you to write.

Line 1: outcome (done / partly done / blocked) and what changed, in one sentence. Describe the change by its effect, not by listing files.
Then, only if they apply:
- what failed, was skipped, or was not verified
- where you deviated from what I asked or assumed something I did not say
- anything I must decide or do next

Leave out: steps taken, my request restated, file lists, things that worked as expected.
Say each thing once. Use my words or names from the codebase; explain any other new term in a few words.

If I asked a question or for an explanation, review, or plan, that answer is the whole message and replaces the structure above. Still no padding.

If you need something from me before continuing, just ask; no Line 1.
</final_response_format>
