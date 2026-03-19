# Select AI Agent in Oracle AI Database

## Overview

Select AI Agent is Oracle's autonomous agent framework inside Oracle AI Database and Autonomous AI Database. Oracle documents it as combining planning, tool use, reflection, and memory to support multi-turn workflows.

Use this file when you need agent teams, tasks, tools, built-in tools, or `DBMS_CLOUD_AI_AGENT`. If the question is specifically about `select_ai.agent.*` classes in Python, start with `select-ai-python.md` and use this file for the database-side agent model.

---

## Documentation Map

| Topic | Oracle documentation |
|---|---|
| Agent overview and concepts | Oracle Database Select AI User's Guide, **Select AI Agent** |
| Agent architecture and ReAct pattern | Oracle Database Select AI User's Guide, **Select AI Agent** |
| End-to-end SQL and PL/SQL examples | Oracle Database Select AI User's Guide, **Examples of Using Select AI Agent** |
| Package and view support | `DBMS_CLOUD_AI_AGENT` Package, Views, and History Views |
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

Oracle's examples page makes the built-in categories more concrete:

- `NOTIFICATION` includes `EMAIL` and `SLACK`
- `WEBSEARCH` examples use an OpenAI credential path
- `SQL` and `RAG` tools point at Select AI profiles

Treat tool creation as explicit configuration, not as an implicit prompt capability.

---

## Minimal Lifecycle Pattern

Oracle's examples show the core object lifecycle in this order:

1. create an agent with `CREATE_AGENT`
2. create tools with `CREATE_TOOL`
3. create tasks with `CREATE_TASK`
4. assemble them into a team with `CREATE_TEAM`
5. run with `SELECT AI AGENT ...` or `DBMS_CLOUD_AI_AGENT.RUN_TEAM`

Documented examples:

```sql
BEGIN
  DBMS_CLOUD_AI_AGENT.CREATE_AGENT(
    agent_name => 'CustomerAgent',
    attributes =>'{
       "profile_name": "GOOGLE",
       "role": "You are an experienced customer agent who deals with customers return request."
     }'
  );
END;
/

BEGIN
  DBMS_CLOUD_AI_AGENT.CREATE_TASK(
    task_name  => 'FETCH_LOGS_TASK',
    attributes =>'{
       "instruction": "Fetch the log entries from the database based on user request: {query}",
       "tools": ["log_fetcher"],
       "enable_human_tool" : "true"
     }'
  );
END;
/

BEGIN
  DBMS_CLOUD_AI_AGENT.CREATE_TEAM(
    team_name  => 'Ops_Issues_Solution_Team',
    attributes => '{"agents": [{"name":"Data_Engineer","task" : "fetch_logs_task"},
                               {"name":"Ops_Manager","task" : "analyze_log_task"}],
                    "process": "sequential"}');
END;
/
```

Use these examples as the canonical mental model for how the package is intended to be assembled.

---

## How to Use It

Oracle documents two primary entry points:

- the Select AI `agent` action
- `DBMS_CLOUD_AI_AGENT.RUN_TEAM`

Use the `agent` action when you want conversational agent behavior from prompt execution. Use `RUN_TEAM` when you need an explicit package-based invocation path.

Oracle's examples also document a client-surface distinction:

- use `SELECT AI AGENT ...` on SQL command line style surfaces
- use `DBMS_CLOUD_AI_AGENT.RUN_TEAM` in Database Actions or APEX Service

Oracle explicitly notes that Database Actions and APEX Service should pass the team with the `team_name` argument to `RUN_TEAM` instead of using `SET_TEAM`.

---

## History Views and Troubleshooting

Oracle documents both object views and run-history views for Select AI Agent.

Object and attribute views include:

- `USER_AI_AGENTS`
- `USER_AI_AGENT_TOOLS`
- `USER_AI_AGENT_TASKS`
- `USER_AI_AGENT_TEAMS`
- corresponding `*_ATTRIBUTES` views

Run-history views include:

- `USER_AI_AGENT_TEAM_HISTORY`
- `USER_AI_AGENT_TASK_HISTORY`
- `USER_AI_AGENT_TOOL_HISTORY`

Oracle's examples also use `USER_CLOUD_AI_CONVERSATION_PROMPTS` when reviewing the latest team run.

Use these views when you need to:

- confirm what agents, tasks, tools, and teams exist
- audit tool usage and outputs
- inspect the latest run state
- debug prompts, responses, and task-level results

Documented troubleshooting queries include:

```sql
select * from USER_AI_AGENT_TOOL_HISTORY
order by START_DATE desc;

select * from USER_AI_AGENT_TASK_HISTORY
order by START_DATE desc;

select * from USER_AI_AGENT_TEAM_HISTORY
order by START_DATE desc;
```

---

## `WAITING_FOR_HUMAN` and Resuming Runs

Oracle documents `WAITING_FOR_HUMAN` as a task-history state for agent teams that pause pending user input or approval.

When you run a team through `DBMS_CLOUD_AI_AGENT.RUN_TEAM`, Oracle documents resuming the same team by passing the original `conversation_id` in `params`:

```sql
DECLARE
  v_response VARCHAR2(4000);
BEGIN
  v_response := DBMS_CLOUD_AI_AGENT.RUN_TEAM(
    team_name   => '<same initial team name>',
    user_prompt => 'response to request',
    params      => '{"conversation_id": "<same initial conversation_id>"}'
  );
  DBMS_OUTPUT.PUT_LINE(v_response);
END;
```

Oracle documents that, in this state, `user_prompt` acts as the human response that resumes the workflow.

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
- Inspect `USER_AI_AGENT_*` history views when debugging team behavior instead of treating the run as opaque.
- Use `RUN_TEAM` with `conversation_id` for stateless clients that need resume behavior.
- Keep agent privileges aligned with the actual tools and data paths it can invoke.

---

## Common Mistakes

### Mistake 1: Using an Agent When a Single Select AI Action Would Do

If the task is only NL2SQL, explanation, or summarization, the simpler Select AI actions are usually the right entry point.

### Mistake 2: Treating Tools as Purely Prompt-Level Concepts

Oracle documents a concrete package and tool model around teams, tasks, and tools. Configure those objects explicitly.

### Mistake 3: Ignoring Version and Tool Availability Differences

The capability matrix shows that agent support and built-in tools vary by release line and service type.

### Mistake 4: Treating Database Actions and SQL Command Line as the Same Runtime Surface

Oracle documents different invocation patterns for SQL command line versus Database Actions or APEX Service.

---

## Oracle Version Notes (19c vs 26ai)

- **Oracle Database 19c (generic):** Select AI Agent is not part of the generic 19c baseline.
- **Autonomous AI Database 19c / Oracle Database 19.29+:** Oracle documents `DBMS_CLOUD_AI_AGENT` package and views support starting with this line.
- **Oracle AI Database 23.26 / 26ai docs:** Current documentation includes the broadest Select AI Agent surface, including examples, views, history views, and `WAITING_FOR_HUMAN` resume guidance.
- **Capability matrix:** Tool and service availability still varies by release and deployment type, so verify the exact environment before depending on a specific built-in tool.

---

## Sources

- [Oracle Database Select AI User's Guide - Select AI Agent](https://docs.oracle.com/en/database/oracle/oracle-database/26/selai/select-ai-agent1.html)
- [Oracle Database Select AI User's Guide - Select AI Agent Details](https://docs.oracle.com/en/database/oracle/oracle-database/26/selai/select-ai-agent2.html)
- [Oracle Database Select AI User's Guide - Examples of Using Select AI Agent](https://docs.oracle.com/en/database/oracle/oracle-database/26/selai/examples-using-select-ai-agent1.html)
- [Oracle Database Select AI Capability Matrix](https://docs.oracle.com/en/database/oracle/oracle-database/26/saicm/index.html)
- [DBMS_CLOUD_AI_AGENT Views](https://docs.oracle.com/en/database/oracle/oracle-database/26/selai/dbms_cloud_ai_agent-views.html)
- [DBMS_CLOUD_AI_AGENT History Views](https://docs.oracle.com/en-us/iaas/autonomous-database-serverless/doc/dbms-cloud-ai-agent-views-history.html)
- [DBMS_CLOUD_AI_AGENT Package](https://docs.oracle.com/en/cloud/paas/autonomous-database/dedicated/dbmzc/)
