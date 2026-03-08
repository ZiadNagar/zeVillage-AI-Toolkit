<h1 align="center">zeVillage</h1>
<p align="center"><strong>A modular AI agent toolkit — 52 skills, 8 agent profiles.</strong></p>
<p align="center">
  Give any AI coding agent deep expertise in frontend, animation, design systems, marketing, DevOps, document generation, and more. Platform-agnostic. Vendor-neutral. Drop in and go.
</p>

<p align="center">
  <a href="#quick-start">Quick Start</a> &bull;
  <a href="#skills-52">Skills</a> &bull;
  <a href="#agents-8">Agents</a> &bull;
  <a href="#installation">Installation</a> &bull;
  <a href="#creating-your-own">Create Your Own</a> &bull;
  <a href="#contributing">Contributing</a>
</p>

---

## Why zeVillage?

AI agents are only as good as the instructions they receive. Most start from zero on every task — no memory of best practices, no awareness of your design system, no knowledge of which testing patterns actually work.

zeVillage fixes that. It's a library of **52 composable skills** and **8 agent profiles** that plug into any AI coding tool. Each skill is a self-contained folder of expert-level instructions that the agent loads on demand. Each agent profile is a system prompt that gives the agent a specific role and set of priorities.

**What makes it different:**

- **Vendor-neutral** — works with Cursor, VS Code, OpenCode, or any agent that supports the [Agent Skills](https://agentskills.io) standard
- **Composable** — install all 52 skills or pick only what you need
- **Production-quality** — every skill is linted, formatted, and validated
- **Simple setup** — copy skills folder to your platform

## Quick Start

1. Clone the repo: `git clone https://github.com/yourusername/zeVillage.git`
2. Copy skills to your platform: `cp -r skills/. ~/.config/opencode/skills/`
3. See [INSTALL.md](./INSTALL.md) for detailed instructions, prerequisites, and platform options.

That's it. Your AI agent now has access to every installed skill.

---

## Skills (52)

### Creative & Design (8)

| Skill                                             | What it teaches the agent                                      |
| ------------------------------------------------- | -------------------------------------------------------------- |
| [algorithmic-art](./skills/algorithmic-art)       | Generative art using p5.js                                     |
| [canvas-design](./skills/canvas-design)           | Visual art in PNG/PDF                                          |
| [data-visualization](./skills/data-visualization) | Charts, dashboards, and interactive data displays              |
| [frontend-design](./skills/frontend-design)       | Production-grade frontend interfaces                           |
| [slack-gif-creator](./skills/slack-gif-creator)   | Animated GIFs for Slack                                        |
| [theme-factory](./skills/theme-factory)           | Theming toolkit for artifacts                                  |
| [image-assets](./skills/image-assets)             | Curated image discovery, optimization, and responsive delivery |
| [unsplash-images](./skills/unsplash-images)       | Unsplash image selection and integration                       |

### Animation & 3D (7)

| Skill                                           | What it teaches the agent                                      |
| ----------------------------------------------- | -------------------------------------------------------------- |
| [gsap-animation](./skills/gsap-animation)       | GSAP tweens, timelines, ScrollTrigger, and easing              |
| [animation-systems](./skills/animation-systems) | Motion design systems with duration scales and tokens          |
| [animejs](./skills/animejs)                     | Anime.js v4 tweens, timelines, stagger, and SVG drawing        |
| [threejs](./skills/threejs)                     | Three.js 3D scenes, materials, lighting, and React Three Fiber |
| [matterjs](./skills/matterjs)                   | Matter.js 2D physics simulations                               |
| [vantajs](./skills/vantajs)                     | Vanta.js animated 3D backgrounds                               |
| [cobejs-globe](./skills/cobejs-globe)           | cobe.js interactive WebGL globe visualizations                 |

### CSS Effects (3)

| Skill                                               | What it teaches the agent         |
| --------------------------------------------------- | --------------------------------- |
| [progressive-blur](./skills/progressive-blur)       | CSS progressive blur effects      |
| [css-border-gradient](./skills/css-border-gradient) | CSS gradient borders and outlines |
| [css-alpha-masking](./skills/css-alpha-masking)     | CSS alpha masking and compositing |

### Design Systems & UI (8)

| Skill                                                         | What it teaches the agent                              |
| ------------------------------------------------------------- | ------------------------------------------------------ |
| [design-system-patterns](./skills/design-system-patterns)     | Component architecture, tokens, and documentation      |
| [tailwind-design-system](./skills/tailwind-design-system)     | Tailwind CSS design system with custom themes          |
| [responsive-design](./skills/responsive-design)               | Responsive layouts, breakpoints, and fluid typography  |
| [interaction-design](./skills/interaction-design)             | Micro-interactions, state transitions, and feedback    |
| [web-interface-guidelines](./skills/web-interface-guidelines) | Comprehensive web interface design principles          |
| [brand-guidelines](./skills/brand-guidelines)                 | Apply brand colors and typography                      |
| [ui-designer](./skills/ui-designer)                           | Visual design systems, spacing, typography, and polish |
| [ui-ux-pro-max](./skills/ui-ux-pro-max)                       | UI/UX design intelligence with searchable database    |
| [ux-designer](./skills/ux-designer)                           | UX strategy, flows, IA, usability, and audits          |

### Page Design (3)

| Skill                                               | What it teaches the agent                           |
| --------------------------------------------------- | --------------------------------------------------- |
| [landing-page-design](./skills/landing-page-design) | High-conversion landing page structure and copy     |
| [pricing-page-design](./skills/pricing-page-design) | Pricing tier layouts and comparison tables          |
| [features-page](./skills/features-page)             | Feature showcase pages with bento grids and reveals |

### Marketing & Content (3)

| Skill                                                 | What it teaches the agent                                   |
| ----------------------------------------------------- | ----------------------------------------------------------- |
| [copywriting](./skills/copywriting)                   | Conversion copywriting, headline formulas, and CTA patterns |
| [marketing-psychology](./skills/marketing-psychology) | 50+ mental models for persuasion and pricing                |
| [analytics-tracking](./skills/analytics-tracking)     | GA4, GTM, event tracking, and UTM strategy                  |

### Development & Code Quality (11)

| Skill                                                   | What it teaches the agent                           |
| ------------------------------------------------------- | --------------------------------------------------- |
| [api-design](./skills/api-design)                       | REST API conventions, OpenAPI specs, error handling |
| [code-review](./skills/code-review)                     | Structured multi-pass code reviews                  |
| [git-workflow](./skills/git-workflow)                   | Commits, branches, PRs, changelogs                  |
| [testing](./skills/testing)                             | Unit, component, E2E, visual, a11y testing          |
| [refactoring](./skills/refactoring)                     | Code smell detection and refactoring patterns       |
| [security-audit](./skills/security-audit)               | Vulnerability scanning, OWASP Top 10, auth review   |
| [project-scaffold](./skills/project-scaffold)           | Bootstrap projects with best practices              |
| [devops](./skills/devops)                               | Docker, CI/CD, deployment, performance budgets      |
| [mcp-builder](./skills/mcp-builder)                     | MCP server creation                                 |
| [webapp-testing](./skills/webapp-testing)               | Browser testing with Playwright                     |
| [web-artifacts-builder](./skills/web-artifacts-builder) | Multi-component HTML artifacts                      |

### Planning & Workflow (1)

| Skill                                               | What it teaches the agent                                       |
| --------------------------------------------------- | --------------------------------------------------------------- |
| [persistent-planning](./skills/persistent-planning) | Filesystem-based planning and progress tracking across sessions |

### Enterprise & Communication (3)

| Skill                                       | What it teaches the agent            |
| ------------------------------------------- | ------------------------------------ |
| [doc-coauthoring](./skills/doc-coauthoring) | Collaborative documentation workflow |
| [internal-comms](./skills/internal-comms)   | Internal communications templates    |
| [skill-creator](./skills/skill-creator)     | Create and optimize skills           |

### Document Generation (4)

| Skill                 | What it teaches the agent          |
| --------------------- | ---------------------------------- |
| [docx](./skills/docx) | Word document creation and editing |
| [pdf](./skills/pdf)   | PDF reading, merging, creation     |
| [pptx](./skills/pptx) | PowerPoint presentations           |
| [xlsx](./skills/xlsx) | Spreadsheet operations             |

---

## Agents (8)

Agent profiles give an AI agent a **role, personality, and workflow**. They're system prompts that transform a generic agent into a specialist.

```bash
# Load a specific agent profile
opencode --system-prompt agents/coder.md
```

| Agent                                    | Role                      | Best For                                                            |
| ---------------------------------------- | ------------------------- | ------------------------------------------------------------------- |
| [orchestrator](./agents/orchestrator.md) | Meta-Agent / Tech Lead    | Analyzing requests and routing to the right skills and agents       |
| [coder](./agents/coder.md)               | Senior Frontend Developer | Writing production-ready, accessible, performant code               |
| [backend](./agents/backend.md)           | Senior Backend Developer  | APIs, databases, server-side logic, distributed systems             |
| [reviewer](./agents/reviewer.md)         | Code Review Specialist    | Reviewing code for bugs, security, accessibility issues             |
| [architect](./agents/architect.md)       | System Architect          | Planning system design, choosing tech stacks, structuring codebases |
| [planner](./agents/planner.md)           | Project Planner           | Breaking down complex tasks, tracking progress, managing scope      |
| [devops](./agents/devops.md)             | DevOps Engineer           | CI/CD pipelines, Docker, deployment, infrastructure                 |
| [writer](./agents/writer.md)             | Technical Writer          | Documentation, READMEs, API references, blog posts                  |

The **orchestrator** is the meta-agent — it understands every skill and agent in the toolkit and can dynamically route sub-tasks to the right specialist. Use it when you want the agent to figure out the best approach on its own.

See [agents/README.md](./agents/README.md) for the full template and guide to creating custom agents.

---

For detailed installation instructions, see [INSTALL.md](./INSTALL.md).

## Project Structure

```
zeVillage/
├── skills/                 # 52 skill directories
│   ├── gsap-animation/
│   │   ├── SKILL.md        # Instructions + YAML frontmatter
│   │   └── LICENSE.txt     # License
│   ├── threejs/
│   ├── landing-page-design/
│   └── ...
├── agents/                 # 8 agent profiles
│   ├── orchestrator.md
│   ├── coder.md
│   └── ...
├── spec/                   # Agent Skills specification
├── template/               # Skill template for creating new skills
├── INSTALL.md              # Installation guide
├── GUIDELINES.md           # Project rules (vendor neutrality, structure, conventions)
└── README.md
```

Each skill is a self-contained folder with a `SKILL.md` (YAML frontmatter + instructions) and a `LICENSE.txt`. Some skills also include `scripts/`, `templates/`, or `reference/` subdirectories with supporting code.

---

## Creating Your Own

### New Skill

Create a folder under `skills/` with a `SKILL.md` and `LICENSE.txt`:

```markdown
---
name: my-skill-name
description: "Use this skill when building X — covers Y, Z, and W."
license: Complete terms in LICENSE.txt
---

# My Skill Name

## When to Use

[Describe the scenarios where this skill applies]

## Implementation

[Step-by-step instructions the agent will follow]

## Code Patterns

[Include code examples the agent can reference]

## Best Practices

- [Guideline 1]
- [Guideline 2]
```

### New Agent

Create a Markdown file in `agents/` following the structure in [agents/README.md](./agents/README.md).

---

## Project Principles

- **Vendor-neutral** — no references to specific AI vendors. Always "the AI agent."
- **Composable** — every skill is independent. Install one or install all.
- **Quality-first** — all Python linted with ruff, all YAML frontmatter validated, all content reviewed
- **Open standard** — follows the [Agent Skills specification](https://agentskills.io)

Read [GUIDELINES.md](./GUIDELINES.md) before creating or editing any skill or agent.

---

## Agent Skills Standard

zeVillage implements the [Agent Skills](https://agentskills.io) open standard:

- [Overview](https://agentskills.io/home.md) — what Agent Skills are and how they work
- [Integration Guide](https://agentskills.io/integrate-skills.md) — how to add support to your agent
- [Using Scripts](https://agentskills.io/skill-creation/using-scripts.md) — bundling executable scripts in skills
- [Specification](https://agentskills.io/specification.md) — the complete format spec

---

## Contributing

1. Read [GUIDELINES.md](./GUIDELINES.md) — it covers vendor neutrality, file structure, copyright, and coding conventions
2. Use the [template](./template/SKILL.md) as a starting point
3. Keep descriptions in imperative voice: "Use this skill when..."
4. Run `ruff check .` and `ruff format .` before submitting
5. Validate YAML frontmatter — `description` must be a quoted string, not a folded scalar

---

## License

© 2026 Ziad Elnagar. See individual skill directories for specific license terms.
