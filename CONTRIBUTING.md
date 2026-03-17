## Contributing to `skills`

This repository is a personal catalog of skills and guidelines for AI agents. Even if you are the only contributor, keeping a clear process makes it easier to evolve and reuse these skills across tools and projects.

### Adding a new skill

1. **Create a folder**  
   - Place skills under a logical area, for example:
     - `frontend/` (or directly `a11y/` as now),
     - `backend/`,
     - `infra/`,
     - `process/`.
   - Inside that folder, create a `SKILL.md`.

2. **Use the template**  
   - Start from `SKILL_TEMPLATE.md`.
   - Fill in:
     - `name`,
     - `description`,
     - `tags`,
     - `scope`,
     - `version`,
     - `status` (`draft` / `stable` / `deprecated`).

3. **Structure the content**
   - Keep the following sections:
     - `## Goal`
     - `## When to use`
     - `## Rules / guidelines`
     - `## Checklist`
     - `## Examples` (optional)
   - Write rules as short, imperative bullets that an AI agent can follow directly.

4. **Update the index**
   - Add the new skill to `SKILLS_INDEX.md` with:
     - `name`,
     - `description`,
     - `path`,
     - `tags`,
     - `status`.

### Naming and tags

- **Folder names**: use short, descriptive, kebab-case names (e.g. `frontend-a11y`, `react-patterns`, `backend-observability`).
- **Tags**: pick a small set from:
  - `frontend`, `backend`, `fullstack`,
  - `react`, `node`, `infra`,
  - `a11y`, `performance`, `security`,
  - `process`, `review`, `testing`.

### Versioning and stability

- Use semantic-ish versions for bigger changes, e.g. `0.1.0`, `0.2.0`, `1.0.0`.
- Adjust `status` as the skill matures:
  - `draft`: experimental, may change frequently.
  - `stable`: safe to use by default in agents.
  - `deprecated`: kept for history; prefer a newer skill.

### Style guidelines

- Write in clear, concise English.
- Prefer concrete rules over vague advice.
- Assume the reader is an AI agent orchestrator:
  - Avoid tool-specific instructions unless the skill is meant for one tool.
  - Focus on what to enforce, not how the tool wires it in.

