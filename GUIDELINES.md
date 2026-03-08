# Project Guidelines for AI Agents

> **Purpose:** Read this file whenever creating, editing, or reviewing any skill, script, template, or asset in this repository. These rules ensure consistency across the entire project.

---

## 1. Ownership & Copyright

- **Copyright holder:** Ziad Elnagar
- **Year:** Use the current year (not hardcoded — check the date before writing)
- Every skill folder **must** include a `LICENSE.txt` with this exact content (update the year):

```
© <YEAR> Ziad Elnagar. All rights reserved.

LICENSE: Use of these materials (including all code, prompts, assets, files,
and other components of this Skill) is subject to the following terms.

RESTRICTIONS: Users may not:

- Extract these materials or retain copies outside their intended use
- Reproduce or copy these materials, except for temporary copies created
  automatically during authorized use
- Create derivative works based on these materials
- Distribute, sublicense, or transfer these materials to any third party
- Make, offer to sell, sell, or import any inventions embodied in these
  materials
- Reverse engineer, decompile, or disassemble these materials

The receipt, viewing, or possession of these materials does not convey or
imply any license or right beyond those expressly granted above.

Ziad Elnagar retains all right, title, and interest in these materials,
including all copyrights, patents, and other intellectual property rights.
```

---

## 2. Vendor Neutrality

This project is **vendor-agnostic**. All content must work with any AI agent, not just one specific provider.

### Naming & Language

| Do NOT write                                     | Write instead                                                |
| ------------------------------------------------ | ------------------------------------------------------------ |
| Any vendor or product name (Claude, Codex, etc.) | "the AI agent", "the agent"                                  |
| Any vendor company name                          | (omit or use "the provider")                                 |
| "[Product] will..."                              | "The AI agent will..."                                       |
| Vendor-specific model IDs                        | Generic examples (e.g., `gpt-4o`, `gemini/gemini-2.0-flash`) |

### Rules

- **SKILL.md files:** Never reference a specific AI vendor. Use "the AI agent" or "the agent."
- **Comments & docstrings:** Same rule — no vendor names.
- **Default author strings:** Use `"AI Agent"` (not a vendor name) for metadata like document author fields.
- **CSS variables / UI text:** Use generic prefixes like `--ui-*`, not vendor-branded ones.
- **Model examples in docs:** Use provider-neutral examples first (e.g., `gpt-4o`). If listing multiple, avoid singling out any one vendor.

### Allowed exceptions

- **Configurable defaults** in code constants are acceptable if the value is overridable via environment variable or CLI flag. Example:
  ```python
  AGENT_CLI = os.environ.get("AGENT_CLI", "opencode")  # OK — configurable
  ```

---

## 3. LLM SDK: Use litellm

When scripts need to call an LLM:

- **Use `litellm`** (`litellm>=1.0.0`) — never import a vendor-specific SDK (no `anthropic`, no `openai` directly).
- Default model: `gpt-4o` (neutral default).
- Use `litellm.completion()` / `litellm.acompletion()` with OpenAI-compatible tool_calls format.
- **Extended thinking / vendor-specific features:** Do NOT detect by model name. Instead, use an explicit CLI flag like `--extended-thinking` and let the user opt in.

---

## 4. CLI Tool References

When scripts invoke an agent CLI binary:

- Define configurable constants at the top of the file:
  ```python
  AGENT_CLI = os.environ.get("AGENT_CLI", "opencode")
  AGENT_CONFIG_DIR = os.environ.get("AGENT_CONFIG_DIR", ".config/opencode")
  AGENT_ENV_GUARD = os.environ.get("AGENT_ENV_GUARD", "AGENT_CODE")
  ```
- Never hardcode the CLI name, config directory, or env guard variable in the function body — use the constants.
- Docstrings should reference "the agent CLI" not a specific tool name.

---

## 5. Skill Structure

Every skill folder must contain at minimum:

```
skill-name/
├── SKILL.md          # Required — frontmatter + instructions
└── LICENSE.txt       # Required — use template from Section 1
```

Optional subdirectories:

```
skill-name/
├── SKILL.md
├── LICENSE.txt
├── scripts/          # Python/shell helper scripts
│   └── requirements.txt
├── reference/        # Documentation for the skill
├── templates/        # HTML/JS/CSS templates
└── eval/             # Evaluation sets and results
```

### SKILL.md frontmatter

```yaml
---
name: my-skill-name # lowercase, hyphens
description: >
  Use this skill when...      # Imperative voice, 100-200 words max.
                              # Focus on user intent, not implementation.
---
```

- `description` must be in **imperative voice**: "Use this skill when..." not "This skill does..."
- Keep under 200 words — it competes with other skills for the agent's attention.

---

## 6. Python Scripts

- **Syntax:** All `.py` files must pass `ast.parse()` with zero errors.
- **Imports:** Use `litellm` for LLM calls. Use standard library otherwise. List dependencies in `scripts/requirements.txt`.
- **Default strings:** Author fields → `"AI Agent"`. Never use a vendor name.
- **Type hints:** Use modern Python syntax (`str | None`, not `Optional[str]`).
- **No duplicate parameters** in function signatures.
- **Indentation:** 4 spaces, never tabs. Watch for formatter-induced indentation bugs in `if/else` blocks.

---

## 7. Agents

Agents are role-based system prompts that give an AI agent a specific persona, workflow, and set of priorities. They live in `.github/agents/`.

### Agent File Structure

```
agents/
├── README.md       # Overview and usage instructions
├── coder.md        # Senior Frontend Developer
├── reviewer.md     # Code Review Specialist
├── architect.md    # System Architect
├── devops.md       # DevOps Engineer
└── writer.md       # Technical Writer
```

### Using Agents

Pass an agent file as the system prompt to any CLI-based AI agent:

```bash
opencode --system-prompt .github/agents/coder.md
```

### Agent Markdown Template

Every agent file should include these sections:

| Section                 | Purpose                                          |
| ----------------------- | ------------------------------------------------ |
| **Identity**            | Who the agent is, experience level, perspective  |
| **Priorities**          | Numbered list of what matters most (1 = highest) |
| **Communication Style** | How the agent speaks and formats output          |
| **Workflow**            | Step-by-step process the agent follows           |
| **Rules**               | Hard constraints the agent must never violate    |
| **Skill Affinity**      | Which skills to prefer when available            |

### Rules for Agents

- All vendor-neutrality rules (Section 2) apply to agent files
- Agent files are Markdown — no YAML frontmatter needed
- Each agent should reference specific skills it works well with
- Keep agent files under 150 lines — concise is better
- Agent names are lowercase, no spaces: `coder.md`, `devops.md`

---

## 8. Checklist for Adding a New Agent

- [ ] Created `agents/<name>.md` with all required sections (Identity, Priorities, Communication Style, Workflow, Rules, Skill Affinity)
- [ ] No vendor names — uses "the AI agent" or "the agent"
- [ ] Skill Affinity references only skills that exist in the skills folder
- [ ] File is under 150 lines
- [ ] Added agent to the table in `agents/README.md`

---

## 9. Checklist for Adding a New Skill

Use this checklist every time:

- [ ] Created `skill-name/SKILL.md` with proper frontmatter (name + description)
- [ ] Description uses imperative voice, is under 200 words, and mentions no vendor
- [ ] Created `skill-name/LICENSE.txt` using the template from Section 1 with the current year
- [ ] All `.py` scripts use `litellm` (not vendor SDKs), pass `ast.parse()`
- [ ] No hardcoded vendor names anywhere (grep for `claude`, `codex`, `anthropic`, `openai` in the skill folder)
- [ ] Default author/creator strings are `"AI Agent"`
- [ ] CLI tool references use configurable constants (Section 4)
- [ ] CSS vars use `--ui-*` prefix, not vendor-branded names
- [ ] `scripts/requirements.txt` exists if the skill has Python dependencies

---

## 10. Checklist for Editing an Existing Skill

- [ ] Read the current file contents before editing (don't assume — formatters may have changed things)
- [ ] After editing, verify with `ast.parse()` for any modified `.py` files
- [ ] Grep the skill folder for vendor names to confirm none slipped in
- [ ] Confirm `LICENSE.txt` still has the correct year and copyright holder

---

## 11. Quick Validation Commands

```powershell
# Check all Python files parse correctly
python -c "
import ast, pathlib
for f in pathlib.Path('skills').rglob('*.py'):
    try: ast.parse(f.read_text(encoding='utf-8'))
    except SyntaxError as e: print(f'FAIL: {f} - {e}')
"

# Find any remaining vendor references
Select-String -Path (Get-ChildItem -Path "skills" -Recurse -Include *.py,*.md,*.txt,*.html,*.js,*.sh) -Pattern "(?i)(anthropic|claude|codex)"

# Verify all LICENSE files have the correct copyright holder
Select-String -Path (Get-ChildItem -Path "skills" -Recurse -Filter "LICENSE.txt") -Pattern "Ziad Elnagar"
```
