## skills

This project is primarily a **personal skill set for AI agents** – a place to keep all the skills, workflows, and knowledge I want to share with AI agents and reuse across projects.

Everything in this repository is intended to be **open source and free to use**, unless explicitly stated otherwise. See the license section below.

### Why this repo exists

- **Single source of truth** for how I want AI agents to behave across projects.
- **Reusable guidance** that can be plugged into different tools (e.g. Cursor, other orchestrators) without rewriting the same rules.
- **Documented standards** for future me (and potential collaborators), so decisions are explicit instead of only in my head.

### What you will find here

- **Skills for AI agents**: individual folders (for example `a11y/`) contain specific skills / guidelines that can be attached to agents.
- **Standardized format**: every skill should have its own `SKILL.md` that describes how to use it, its context, and any relevant best practices.

### How to use it

- **As a capability catalog**: you can load these skills into AI agents as needed (for example for accessibility, a specific tech stack, code style, etc.).
- **As inspiration / documentation**: even without agents, you can treat these skills as an overview of your own standards and workflows.

### Example usage with agents (conceptual)

How you wire these skills into agents depends on the specific tool, but the idea is:

- Choose one or more skills relevant to the current task (e.g. `frontend-a11y` when editing frontend pages).
- Load the contents of the corresponding `SKILL.md` into the agent as part of its system / tool-specific configuration.
- Let the agent use the rules and checklist from the skill to guide its changes and reviews.

### Skill index

For a structured overview of available skills, see `SKILLS_INDEX.md`.

### License

Unless a specific file or folder states otherwise, this repository is covered by the license in `LICENSE.txt`.
