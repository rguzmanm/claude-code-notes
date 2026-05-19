# Subagents

Subagents are specialized assistants that Claude Code can delegate tasks to. They are focused helpers: each one runs in **its own conversation context window**, does its work, and returns a summary to the main thread. The intermediate steps -- all the file reads, searches, and tool calls -- stay isolated and never clutter the main conversation.

The tradeoff is that you lose visibility into how the subagent reached its conclusions. Therefore, use subagents only when what matters is the result and not a step-by-step explanation of how it was found.

They are defined as Markdown files with YAML frontmatter that dictates their behavior.

- `name`: A unique identifier for the subagent. This is how you reference it, either by asking Claude directly or by typing `@agent code-quality-reviewer` in your message.
- `description`: Controls when Claude decides to use the subagent. This must be a single line (use escaped newline characters \n if you need breaks). You can include example conversations here to help Claude understand when delegation is appropriate.
- `tools`: Lists which tools the subagent can access. This matches whatever you selected during generation, but you can edit the list here at any time.
- `model`: Specifies which Claude model to use: sonnet, opus, haiku, or inherit.
- `color`: The UI color for identifying the subagent.

You can see [here](https://code.claude.com/docs/en/sub-agents#supported-frontmatter-fields) the complete list of fields that can be added in the frontmatter

The body of the markdown file (everything below the YAML frontmatter) is the system prompt. This is where you give the subagent its instructions: what it should focus on, how it should analyze things, and how it should report findings back to the main agent.

## How is a subagent triggered?

Every subagent has a `description` field in its configuration. Claude treats this description as a trigger phrase or "routing hint." When your request matches the scope of a subagent’s description, the main agent automatically delegates the work to that subagent.
You can also make a direct mention of the subagent by name in your prompt to invoke it explicitly.

When a message is sent to the main context window agent, the name and description of every available subagent are included in the system prompt. This is how the main agent decides which subagent to launch and when. For better control over when a subagent gets triggered automatically, the name and description are what should be tweaked.

When the main agent launches a subagent it writes an input prompt to kick off the task. The `description` is used as guidance for writing that prompt. This means that the description controls **when** a subagent runs and **what** it is told to do.

**Note**: Subagents **cannot** trigger other subagents (no nesting). This prevents infinite loops and uncontrolled token usage.

## Where do subagents live?

Subagent files live in one of four locations, each with different scope and priority. When names collide, Claude Code resolves them in a strict order:

- Session-defined agents
- Project agents (.claude/agents/)
- User agents (~/.claude/agents/)
- Plugin agents

The practical rule:

- Project-level agents should define the team's shared specialists — the reviewer, the test runner, the documentation writer. These get committed to the repo so every engineer works with the same agent definitions.
- User-level agents (~/.claude/agents/) are personal tools you bring to every project — a coding style enforcer tuned to your preferences, or a research agent that accumulates knowledge about topics you care about over time.

## Techniques to write effective subagents

A subagent that's poorly configured will wander, run too long, or produce output the main agent can't use. Do the following to write effective subagents:

1. **Specific descriptions**: The description controls when the subagent is launched and what instructions it receives. Write it to steer both.
2. **Structured output**: Define an output format in the system prompt so the subagent knows when it's done and returns information the main thread can use.
3. **Obstacle reporting**: Include a section in the output format for workarounds, quirks, and problems so the main thread doesn't have to rediscover them.
4. **Limited tool access**: Only give a subagent the tools it actually needs. Read-only for research, bash for reviewers, edit/write only for agents that should change code.

## Skill access

- Built-in agents (like Explorer, Plan, and Verify) can't access skills at all
- Custom subagents you define can use skills, but only when you explicitly list them
- Skills are loaded when the subagent starts, not on demand like in the main conversation
