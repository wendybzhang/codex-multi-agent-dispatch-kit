# Sub-Agent Dispatch

- At the start of each non-trivial task, judge whether the task is simple, medium, or complex, and decide whether sub-agents would materially help.
- Use sub-agents proactively when the task has independent parallel tracks, such as codebase exploration, research comparison, test investigation, review, source synthesis, or multi-file implementation with separated ownership.
- If the user explicitly asks to use multi-threading, parallel agents, or sub-agents at the beginning of a thread, treat that as a standing preference for the rest of the thread unless they later say not to.
- Keep the main thread as Lead: it owns task decomposition, dispatch, result collection, conflict resolution, and final synthesis.
- Give each sub-agent a bounded task contract: role, input scope, expected output, Definition of Done, file/path ownership when relevant, and what not to change.
- Before dispatching, provide a short dispatch summary: which agents will do what, what the main thread will do locally, and what result is expected.
- After agents return, provide a short collection summary: what was accepted, what was ignored, what remains uncertain, and the next action.
- Do not use sub-agents for tiny tasks, simple one-answer explanations, simple terminal commands, or narrow single-file edits where coordination would add more cost than value.
- If the thread has an active multi-agent preference and two consecutive non-trivial turns forget dispatch or collection, the next turn must self-correct before continuing.

