# Skills

A skill is a reusable set of instructions, scripts, and context that enables an agent to perform a **specific** task. That is to say, skills provide **procedural knowledge**

Each skill lives ina `SKILL.md` file that contains a frontmatter and a body.

The agent skills open standard supports several fields in the SKILL.md frontmatter. Two are required, and the rest are optional:

- `name` (required) — Identifies your skill. Use lowercase letters, numbers, and hyphens only. Maximum 64 characters. Should match your directory name.
- `description` (required) — Tells Claude when to use the skill. Maximum 1,024 characters. This is the most important field because Claude uses it for matching.
- `allowed-tools` (optional) — Restricts which tools Claude can use when the skill is active.
- `model` (optional) — Specifies which Claude model to use for the skill.

You can see [here](https://code.claude.com/docs/en/skills#frontmatter-reference) the complete list of fields that can be added in the frontmatter

The **body** is the actual set of instructions the agent needs to know to perform the task.

The rule of thumb is simple: if you find yourself explaining the same thing to an agent repeatedly, that's a skill waiting to be written.

## How are skills triggered

They are conditionally triggered. By default, at the start of a session the **name** and **description** of every available skill are loaded into context.

The **description** is how the agent decides whether to use the skill.
Once the agent decides it needs to use a specific task, it loads then the **body** of the skill and any other script or tool used by it. This is called _progressive disclosure_ and helps to not clutter the context window unneccessarily.

**Note:** Scripts execute without loading their contents into context — only the output consumes tokens, keeping context efficient

## Where do skills live?

Skills live in one of four locations, each with different scope and priority. In case of conflict, Claude Code resolves them in a strict order:

1. **Enterprise**: Managed settings, highest priority
2. **Personal**: Your home directory (~/.claude/skills)
3. **Project**: The .claude/skills directory inside a repository
4. **Plugins**: Installed plugins, lowest priority

This lets organizations enforce standards through enterprise skills while still allowing individual customization

## Custom Slash Commands are now Skills

- Custom commands have been merged into skills. A file at `.claude/commands/deploy.md` and a skill at `.claude/skills/deploy/SKILL.md` both create `/deploy` and work the same way. Your existing `.claude/commands/` files keep working.
- Skills add optional features: a directory for supporting files, frontmatter to control whether you or Claude invokes them, and the ability for Claude to load them automatically when relevant.

## Open standard

Skills are an open standard, which means that they are described, stored, and shared using a universal public language. This ensures interoperability.
