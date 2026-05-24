# References and Design Choices

This project is not a direct implementation of any single paper or vendor framework. It is a lightweight Codex workflow adapted from public multi-agent and subagent engineering patterns, then simplified for day-to-day human-in-the-loop work.

In short: patterns not adopted here were not ignored by default. Most were intentionally left out because they require heavier framework support, increase coordination cost, reduce user visibility, or are a poor fit for ordinary Codex sessions.

## Sources Reviewed

- Anthropic Engineering: [How we built our multi-agent research system](https://www.anthropic.com/engineering/multi-agent-research-system)
- Anthropic Claude Code Docs: [Create custom subagents](https://code.claude.com/docs/en/sub-agents)
- Anthropic Claude Agent SDK Docs: [Subagents in the SDK](https://code.claude.com/docs/en/agent-sdk/subagents)
- OpenAI Agents SDK Docs: [Agent orchestration](https://openai.github.io/openai-agents-python/multi_agent/)
- OpenAI Agents SDK Docs: [Guardrails](https://openai.github.io/openai-agents-python/guardrails/)
- OpenAI Agents SDK Docs: [Tracing](https://openai.github.io/openai-agents-python/tracing/)
- OpenAI: [A practical guide to building agents](https://cdn.openai.com/business-guides-and-resources/a-practical-guide-to-building-agents.pdf)
- Google Agent Development Kit: [Workflows: multi-agent, multi-node applications](https://adk.dev/workflows/)
- Microsoft AutoGen Docs: [AgentChat](https://microsoft.github.io/autogen/stable/user-guide/agentchat-user-guide/index.html)
- Microsoft AutoGen Docs: [Teams](https://microsoft.github.io/autogen/stable/user-guide/agentchat-user-guide/tutorial/teams.html)
- Microsoft Research / arXiv: [AutoGen: Enabling Next-Gen LLM Applications via Multi-Agent Conversation](https://arxiv.org/abs/2308.08155)

## Adopted Patterns

| Pattern | Why we adopted it | How it appears in this kit |
|---|---|---|
| Orchestrator-worker structure | Anthropic's research architecture and OpenAI's manager-agent pattern both emphasize one coordinator that delegates to specialists and synthesizes results. | The main thread is always `Lead`; subagents only handle bounded intermediate work. |
| Use multi-agent only for parallelizable work | Anthropic notes multi-agent systems shine for breadth-first tasks, but burn more tokens and are weaker when tasks have tight dependencies. | The kit starts with complexity classification and discourages subagents for tiny or tightly coupled tasks. |
| Bounded specialist tasks | OpenAI recommends specialized agents for focused tasks; Claude Code subagents are selected by clear descriptions and task fit. | Every subagent gets a task contract: role, input scope, output, DoD, ownership, and constraints. |
| Context isolation | Claude Code subagents run in separate context and return summaries; this helps preserve main-thread context. | The task card includes "must-know context" so the Lead explicitly passes needed paths, decisions, and constraints. |
| Lead-owned final answer | OpenAI's "agents as tools" pattern is useful when one manager should own the final answer and combine specialist outputs. | Subagents do not directly decide the final conclusion. Lead accepts, rejects, or merges their output. |
| Read-only exploration vs. write ownership | Claude Code's Explore-style agents and tool restrictions show the value of separating search from mutation. | The kit recommends read-only Explorer/Reviewer roles and disjoint write ownership for Workers. |
| Artifact references for large outputs | Anthropic describes persisting subagent outputs outside the coordinator to reduce information loss and context pressure. | The kit asks subagents to return concise summaries plus evidence paths, and only write artifacts when the Lead assigns a path. |
| Traceable dispatch and collection | OpenAI tracing and AutoGen team logs show why multi-agent work needs observability. | Instead of full tracing infrastructure, the kit uses lightweight dispatch summary, collection summary, and unresolved gaps. |
| Guardrails and human intervention | OpenAI's guardrails and practical guide emphasize escalation for risky or high-impact actions. | The kit keeps destructive, irreversible, or high-stakes decisions under the Lead/user rather than a subagent. |
| Reviewer / critic role | AutoGen's team examples often use a critic/reflection pattern. | The recommended minimum setup includes Lead + Explorer + Reviewer before adding more Workers. |

## Considered but Not Adopted by Default

| Pattern | Why not adopted by default |
|---|---|
| Full graph workflow orchestration | Google ADK and AutoGen support graph or team workflows, but this kit is an `AGENTS.md`-level operating rule, not an application framework. Graph orchestration would be too heavy for ordinary Codex sessions. |
| Round-robin multi-agent chat | AutoGen supports group chats, but round-robin discussion can create noise, token cost, and weak accountability. This kit prefers task contracts and Lead synthesis. |
| Specialist handoff that takes over the user-facing conversation | OpenAI handoffs are useful for routing, but this kit keeps one visible Lead to avoid user-facing drift and forgotten constraints. |
| Recursive subagent spawning | Claude Agent SDK states subagents cannot spawn their own subagents. Even where a platform allows nesting, uncontrolled recursion increases coordination risk. |
| Large autonomous fan-out | Anthropic reported early systems spawning too many subagents for simple queries. This kit defaults to small teams and expands only when tracks are independent. |
| Persistent subagent memory | Claude Code supports persistent memory, but it introduces privacy, versioning, and stale-context risk. This kit leaves memory out unless a project deliberately defines stable specialist agents. |
| Automatic model routing | Some platforms support choosing cheaper/faster models per subagent. This is platform-specific and may confuse users; the kit focuses on workflow shape rather than model policy. |
| Always-on background agents | Background execution is useful, but it can reduce user visibility and control. This kit is designed for explicit dispatch and collection inside a session. |
| Full guardrail implementation | Guardrails are important in production systems, but this kit is a Markdown rule pack. It adopts the principle through escalation and "do not change" constraints rather than code-level guardrails. |

## Current Practical Rule

Use subagents when they create real parallel leverage or protect the main context. Do not use them just because the task feels large. The Lead should always be able to answer:

- Why does this subagent exist?
- What exactly will it return?
- What context does it need?
- What is it not allowed to change?
- How will its result be verified and integrated?
