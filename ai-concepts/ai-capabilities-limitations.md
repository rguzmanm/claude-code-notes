# AI Capabilities & Limitations

AI isn't uniformly capable or uniformly unreliable. It is at the same time strong and weak across four different axes.

## Next Token Prediction

AI is closer to a sophisticated autocomplete than a search engine. It predicts the next token based on patterns it has learned from training data.
That single property produces both fluency and hallucination.

Capability zone: tasks that resemble patterns the model has seen many times (summarizing, reformatting, explaining common concepts).
Limitation zone: novel or sparse territory, and anywhere the task requires distinguishing "true" from "sounds true."

## Knowledge

What AI knows comes entirely from training data and is frozen at the knowledge cutoff. Wihtout tools, it has no access to any information after the date.

Capability zone: topics that appeared frequently, recently (within training), and consistently in training data.
Limitation zone: rare, post-cutoff, niche, local, or contested topics.

Web search, retrieval (RAG, MCPs), and tool use exist specifically to patch these gaps.

## Working Memory

It is the fact that the AI model has a fixed context window that it can attend to.

This property has a cliff rather than a gradient. Silent truncation is the failure mode, and you won't always be warned.

The model doesn't learn from your corrections. It only responds to what's currently in context.
Memory features, compaction, projects, larger windows, and multi-agent workflows all exist to push this cliff further out.

More context ≠ better results. Instructions at the start and end of a context window get followed. The middle gets buried. This is why more context ≠ better results — and why "Lost in the Middle" is a real problem.

## Steerability

It is the ability to control, guide, and direct and AI's behavior, tone, style, and output format via Next Token Prediction.

Instructions are honored via pattern-matching, not understanding. When you give an instruction, the AI isn't contemplating the "why" behind your request. Instead, it looks at the prompt as a sequence of tokens and predicts which subsequent tokens are most likely to follow based on its training data.

Capability zone: short, concrete, verifiable instructions. Format specs, length limits, explicit roles.
Limitation zone: long chains of reasoning, abstract or ambiguous instructions, anything requiring native numerical or logical precision

![alt text](<assets/AI Capabilities and Limitations.png>)
