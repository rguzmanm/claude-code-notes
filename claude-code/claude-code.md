# Claude Code

It is an agentic coding tool that understands codebases, edits files, run commands, and integrates with development environments.

## Tools

LLMs by themselves can only process text and return text. They can't actually read files or run command. To overcome this limitation, coding assistants, like Claude Code, use a system called "tool use".

```
LLM generates structured text describing actions
↓
External runtime executes actions
↓
Results are returned as text
↓
LLM reasons over results
↓
Loop repeats
```

Tools are capabilities that allow Claude Code to interact with your codebase and development environment. They allow the AI to read files, run commands, and more.

## The Context Window

It is the working memory of the AI model. It determines how much of your conversation, file contents command outputs, and more it can store and reference. If you reach that limit, it will automatically remove or summarize the information to bring the context window down to a manageable size.

### Context Management

- Use `/compact` to run compactation manually. Compaction summarizes important details and removes unnecessary tool call results to free up space. Note that this process can potentially lose details.
- Use `/clear` to clear the context windows and start fresh.
- Use `/context` to check the state of your context. You'll get a high-level overview of your context size, categories using more space and a graphic showing the breakdown

### Tips For Saving Context Space

- **Be specific**: Without clear instructions, Claude Code is forced to explore your codebase more, which can take up more context space than a detailed prompt would.
- **Manage you MCP servers**: MCP servers load all of their tools into context, so disabling unnecessary servers can save space. You can also use Skills, which work similarly to MCP servers but don't load everything into context upfront.
- **Use subagents**: Subagents have their own context window and only return a summary of their work to the main agent, which helps keep your main context window smaller.

## The "Explore --> Plan --> Code --> Commit" workflow

This workflow is the recommended way to use Claude Code.

- **Explore** gives Claude the relevant context it needs for your project.
- **Plan** creates a plan of action that Claude uses to measure success.
- **Code** is the back and forth between you and Claude before settling on the final outcome.
- **Commit** helps you review and push your code so you can start on your next feature

## The CLAUDE.md file

The CLAUDE.md file is a markdown file you add to the root of the project that Claude Code reads automatically **at the start of every session** to understand the project's context, goals, and conventions. It is like an onboarding script for your codebase

### How does it affect the context window?

Even though the file is read only at the start of every session, it's contents are converted into **tokens** and injected into the **Context Window**.
Therefore, only include things that apply broadly. For domain knowledge or workflows that are only relevant sometimes, use **skills** instead (Claude loads them on demand without bloating every conversation.)
If the file is too large, it can cause the following problems:

- Every prompt in the conversation comes with a "token tax" - the file contents are included in every message.
- The "Lost in the Middle" effect - Claude may prioritize the firsts or most recent instructions over the file contents.
- Due to the high token usage, latency can increase.

### Project vs User level file

- **Project-level** CLAUDE.md lives in the root directory of your project. Shared with the team.
- **User-level** CLAUDE.md lives in your configuration folder. This one is just for you and applies across all your projects. Put your personal preferences here.

## Useful commands

- `/memory`: Opens the memory file (`CLAUDE.md`) to edit and save persistent memories.
- `/effort`: Allows you to control how deeply the AI reasons before responding.
- `/rewind`: Jump back to an earlier point in the conversation. It also works by hitting Escape twice

When in plan mode, you can use `Ctrl+G` to open the planning text in the editor

## CLAUDE.md vs Skills vs Subagents vs Hooks vs MCP Servers

|                            | CLAUDE.md                                                           | Skills                                                                     | Subagents                                                                                                | Hooks                                                                      | MCP Servers                                                                 |
| -------------------------- | ------------------------------------------------------------------- | -------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| **Definition**             | Markdown file that provides project context, goals, and conventions | Reusable instructions that provide procedural knowledge for specific tasks | Specialized assistants that run in their own isolated context window                                     | Shell commands or HTTP endpoints that execute at specific lifecycle points | External servers that provide tools and integrations                        |
| **Trigger Mechanism**      | Automatic — loaded at the start of every session                    | Conditional — loaded on demand when description matches the request        | Automatic delegation when description matches, or explicit @mention                                      | Deterministic — always fires at configured lifecycle events                | Always available — tools loaded into context                                |
| **Context Window Impact**  | High — tokens injected into every conversation                      | Low — only name/description loaded initially; body loaded on activation    | Isolated — only name/description loaded initially; work stays in separate context; only summary returned | Minimal — runs as subprocess/HTTP call outside the context                 | High — all server tools, resources, and prompts loaded into context upfront |
| **Execution Context**      | Main context window                                                 | Main context window                                                        | Isolated context window                                                                                  | Subprocess or external HTTP endpoint                                       | External server process                                                     |
| **Configuration Format**   | Markdown file                                                       | Markdown + YAML frontmatter (SKILL.md)                                     | Markdown + YAML frontmatter                                                                              | JSON in `.claude/settings.json`                                            | Server configuration in settings                                            |
| **Where It Lives / Scope** | Project root (shared) or user config folder (personal)              | Enterprise → Personal → Project → Plugin                                   | Session → Project (.claude/agents/) → User (~/.claude/agents/) → Plugins                                 | `.claude/settings.json`                                                    | External — configured in settings                                           |
| **Tool Access**            | N/A — provides instructions only                                    | Can restrict tools via `allowed-tools` and specify model                   | Explicit `tools` list; should be limited to what's needed                                                | N/A — intercepts tool calls but doesn't use tools itself                   | Provides tools to Claude Code                                               |
| **Best For**               | Always-on project standards and conventions                         | Task-specific expertise that doesn't need to be in every conversation      | Delegated work where the result matters more than the process                                            | Compliance, guardrails, automation, notifications                          | External integrations and specialized capabilities                          |
