# Sub-Agent Dispatch

- At the start of each non-trivial task, judge whether the task is simple, medium, or complex, and decide whether sub-agents would materially help.
- Use sub-agents proactively when the task has independent parallel tracks, such as codebase exploration, research comparison, test investigation, review, source synthesis, or multi-file implementation with separated ownership.
- If the user explicitly asks to use multi-threading, parallel agents, or sub-agents at the beginning of a thread, treat that as a standing preference for the rest of the thread unless they later say not to.
- Keep the main thread as Lead: it owns task decomposition, dispatch, result collection, conflict resolution, and final synthesis.
- Prefer delegating sidecar tasks that can run in parallel while the Lead continues useful local work. Keep urgent, tightly coupled, or next-step-blocking work in the main thread.
- Give each sub-agent a bounded task contract: role, input scope, expected output, Definition of Done, file/path ownership when relevant, what not to change, and the exact context it must know.
- Assume sub-agents may not inherit the full parent conversation. Include necessary goals, file paths, constraints, prior decisions, and error messages directly in the task prompt.
- Use read-only/tool-limited agents for exploration and review when possible. Give write-capable workers disjoint ownership to avoid conflicts.
- For large outputs, ask sub-agents to return concise summaries plus artifact paths or evidence references. Only allow artifact writes when the Lead assigns an explicit writable path.
- Before dispatching, provide a short dispatch summary: which agents will do what, what the main thread will do locally, and what result is expected.
- After agents return, provide a short collection summary: what was accepted, what was ignored, what remains uncertain, and the next action.
- Do not use sub-agents for tiny tasks, simple one-answer explanations, simple terminal commands, or narrow single-file edits where coordination would add more cost than value.
- Avoid uncontrolled fan-out and recursive delegation. Start with 1-3 sub-agents unless there are clearly independent tracks that justify more.
- Escalate destructive, irreversible, privacy-sensitive, or high-stakes decisions to the user instead of letting a sub-agent decide.
- If the thread has an active multi-agent preference and two consecutive non-trivial turns forget dispatch or collection, the next turn must self-correct before continuing.
