# Select AI Agent in Oracle AI Database

## Overview

Select AI Agent is Oracle's autonomous agent framework inside Oracle AI Database and Autonomous AI Database. Oracle documents it as combining planning, tool use, reflection, and memory to support multi-turn workflows.

Use this file when you need agent teams, tasks, tools, built-in tools, or `DBMS_CLOUD_AI_AGENT`.

---

## Documentation Map

| Topic | Oracle documentation |
|---|---|
| Agent overview and concepts | Oracle Database Select AI User's Guide, **Select AI Agent** |
| Agent architecture and ReAct pattern | Oracle Database Select AI User's Guide, **Select AI Agent** |
| Capability and tool support by release | Oracle Database Select AI Capability Matrix |
| Package object support | Oracle Database Select AI Capability Matrix |

---

## Agent Architecture

Oracle documents four architectural layers for Select AI Agent:

- Planning
- Tool Use
- Reflection
- Memory Management

Oracle also documents the ReAct pattern as the reasoning-and-acting pattern used by the framework.

Use this model when deciding whether a workflow really needs agent orchestration instead of a simpler Select AI prompt action.

---

## Core Objects

Oracle documents the `DBMS_CLOUD_AI_AGENT` package for managing:

- teams
- agents
- tasks
- tools

The capability matrix lists core procedures and functions including:

- `CREATE_TEAM`
- `CREATE_AGENT`
- `CREATE_TASK`
- `CREATE_TOOL`
- `ENABLE_*` and `DISABLE_*`
- `DROP_*`
- `RUN_TEAM`
- `RUN_TOOL`
- `GET_TEAM`
- `SET_TEAM`
- `SET_ATTRIBUTE`
- `SET_ATTRIBUTES`

## Built-In Tools

The capability matrix documents these built-in tool categories:

- `SQL`
- `RAG`
- `WEBSEARCH`
- `NOTIFICATION`
- custom PL/SQL

Oracle documents agent workflows as being able to combine built-in tools, custom PL/SQL procedures, and external REST services.

---

## How to Use It

Oracle documents two primary entry points:

- the Select AI `agent` action
- `DBMS_CLOUD_AI_AGENT.RUN_TEAM`

Use the `agent` action when you want conversational agent behavior from prompt execution. Use `RUN_TEAM` when you need an explicit package-based invocation path.

---

## Prerequisites

Oracle documents that Select AI Agent requires:

- privileges for `DBMS_CLOUD_AI_AGENT`
- the underlying privileges required for Select AI
- configuration of any tools the agent will use

Do not treat agent setup as isolated from profile, credentials, or tool governance.

---

## Best Practices

- Start with a narrow team and a small tool set before expanding to complex workflows.
- Use built-in tools when the workflow already matches Oracle's documented SQL or RAG paths.
- Add custom PL/SQL tools only when the workflow needs domain-specific operations that Select AI does not already provide.
- Keep agent privileges aligned with the actual tools and data paths it can invoke.

---

## Common Mistakes

### Mistake 1: Using an Agent When a Single Select AI Action Would Do

If the task is only NL2SQL, explanation, or summarization, the simpler Select AI actions are usually the right entry point.

### Mistake 2: Treating Tools as Purely Prompt-Level Concepts

Oracle documents a concrete package and tool model around teams, tasks, and tools. Configure those objects explicitly.

### Mistake 3: Ignoring Version and Tool Availability Differences

The capability matrix shows that agent support and built-in tools vary by release line and service type.

---

## Oracle Version Notes (19c vs 26ai)

- **Autonomous AI Database 19c:** The capability matrix marks AI Agent as supported.
- **Oracle AI Database 19.30:** The capability matrix marks AI Agent as supported.
- **Oracle AI Database 23.7+:** The capability matrix marks AI Agent as unavailable.
- **Oracle AI Database 23.26.1 / 26ai docs:** Current documentation includes Select AI Agent concepts, use cases, examples, and package surfaces.

---

## Sources

- [Oracle Database Select AI User's Guide - Select AI Agent](https://docs.oracle.com/en/database/oracle/oracle-database/26/selai/select-ai-agent1.html)
- [Oracle Database Select AI User's Guide - Select AI Agent Details](https://docs.oracle.com/en/database/oracle/oracle-database/26/selai/select-ai-agent2.html)
- [Oracle Database Select AI Capability Matrix](https://docs.oracle.com/en/database/oracle/oracle-database/26/saicm/index.html)
