# Installation Guide

This guide explains how to install zeVillage skills into your AI agent platform.

## Quick Install

### Choose Your Platform

**Cursor:**

```bash
cp -r skills/. ~/.cursor/skills/
```

**GitHub Copilot:**

```bash
cp -r skills/. ~/.github/skills/
```

**OpenCode:**

```bash
cp -r skills/. ~/.config/opencode/skills/
```

**Custom Directory:**

```bash
cp -r skills/. /your/custom/path/
```

---

## Prerequisites

Some skills require Python packages to function. Install only what you need.

### Document Skills

| Skill    | Required Packages                    | Documentation                                                                                                                                                                          |
| -------- | ------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **pdf**  | pypdf, pdfplumber, Pillow, pdf2image | [pypdf](https://pypdf.readthedocs.io/), [pdfplumber](https://github.com/jsvine/pdfplumber), [Pillow](https://pillow.readthedocs.io/), [pdf2image](https://github.com/Belval/pdf2image) |
| **docx** | defusedxml, lxml                     | [defusedxml](https://github.com/tiran/defusedxml), [lxml](https://lxml.de/)                                                                                                            |
| **pptx** | defusedxml, lxml, Pillow             | (see above)                                                                                                                                                                            |
| **xlsx** | openpyxl, defusedxml, lxml           | [openpyxl](https://openpyxl.readthedocs.io/), (see above)                                                                                                                              |

### Media Skills

| Skill                 | Required Packages                                                     | Documentation                                                                                                         |
| --------------------- | --------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| **slack-gif-creator** | pillow>=10.0.0, imageio>=2.31.0, imageio-ffmpeg>=0.4.9, numpy>=1.24.0 | [Pillow](https://pillow.readthedocs.io/), [imageio](https://imageio.readthedocs.io/), [NumPy](https://numpy.org/doc/) |

### Development Skills

| Skill           | Required Packages          | Documentation                                                                                           |
| --------------- | -------------------------- | ------------------------------------------------------------------------------------------------------- |
| **mcp-builder** | litellm>=1.0.0, mcp>=1.1.0 | [LiteLLM](https://docs.litellm.ai/docs/), [MCP SDK](https://modelcontextprotocol.github.io/python-sdk/) |

### Install All Prerequisites

```bash
# Core document dependencies
pip install pypdf pdfplumber Pillow pdf2image defusedxml lxml openpyxl

# Media/GIF dependencies
pip install pillow imageio imageio-ffmpeg numpy

# MCP builder dependencies
pip install litellm mcp
```

> **Note:** On Windows with pdf2image, you also need [Poppler](https://github.com/oschwartz10612/poppler-windows/releases/). Add the `bin/` folder to your system PATH.

---

## Install Specific Skills

### Copy Individual Skills

```bash
# Single skill
cp -r skills/pdf ~/.config/opencode/skills/pdf

# Multiple skills
cp -r skills/pdf skills/xlsx skills/docx ~/.config/opencode/skills/
```

### By Category

**Design & Animation:**

```bash
cp -r skills/threejs skills/gsap-animation skills/animejs skills/matterjs \
      skills/vantajs skills/cobejs-globe skills/animation-systems \
      ~/.config/opencode/skills/
```

**CSS Effects:**

```bash
cp -r skills/css-border-gradient skills/css-alpha-masking skills/progressive-blur \
      ~/.config/opencode/skills/
```

**Design Systems:**

```bash
cp -r skills/tailwind-design-system skills/design-system-patterns \
      skills/responsive-design skills/interaction-design \
      skills/ui-designer skills/ui-ux-pro-max skills/ux-designer \
      ~/.config/opencode/skills/
```

**Page Design:**

```bash
cp -r skills/landing-page-design skills/pricing-page-design skills/features-page \
      ~/.config/opencode/skills/
```

**Marketing:**

```bash
cp -r skills/copywriting skills/marketing-psychology skills/analytics-tracking \
      skills/brand-guidelines \
      ~/.config/opencode/skills/
```

**Development:**

```bash
cp -r skills/api-design skills/code-review skills/git-workflow \
      skills/testing skills/refactoring skills/security-audit \
      skills/project-scaffold skills/devops skills/mcp-builder \
      skills/webapp-testing skills/web-artifacts-builder \
      ~/.config/opencode/skills/
```

**Document Generation:**

```bash
cp -r skills/docx skills/pdf skills/pptx skills/xlsx \
      ~/.config/opencode/skills/
```

---

## Install Agent Profiles

Agent profiles give your AI agent a specific role and workflow.

```bash
# Create agents directory in your platform
mkdir -p ~/.config/opencode/agents

# Copy all agents
cp -r agents/*.md ~/.config/opencode/agents/
```

Or copy individually:

| Agent           | Best For                                    |
| --------------- | ------------------------------------------- |
| orchestrator.md | Meta-agent that routes tasks to specialists |
| coder.md        | Senior Frontend Developer                   |
| backend.md      | Senior Backend Developer                    |
| reviewer.md     | Code Review Specialist                      |
| architect.md    | System Architect                            |
| planner.md      | Project Planner                             |
| devops.md       | DevOps Engineer                             |
| writer.md       | Technical Writer                            |

---

## Manual Installation

If you prefer manual setup:

### 1. Create Skills Directory

```bash
mkdir -p ~/.config/opencode/skills
```

### 2. Copy Skill Folders

Each skill is a self-contained folder with:

- `SKILL.md` - Instructions with YAML frontmatter
- `LICENSE.txt` - License terms
- Optional: `scripts/`, `templates/`, `references/`

```bash
# Example: Manual copy for pdf skill
mkdir -p ~/.config/opencode/skills/pdf
cp skills/pdf/SKILL.md ~/.config/opencode/skills/pdf/
cp skills/pdf/LICENSE.txt ~/.config/opencode/skills/pdf/
cp -r skills/pdf/scripts ~/.config/opencode/skills/pdf/
```

### 3. Verify Installation

Check that skills are loaded:

```bash
ls ~/.config/opencode/skills/
```

---

## Uninstall

Remove the skills directory:

```bash
# Specific platform
rm -rf ~/.config/opencode/skills
rm -rf ~/.cursor/skills
rm -rf ~/.github/skills
```

---

## Troubleshooting

### "Module not found" Errors

Install required packages:

```bash
pip install <package-name>
```

### Permission Denied

Use sudo if needed:

```bash
sudo cp -r skills/. /opt/opencode/skills/
```

### Skills Not Loading

- Verify skill folders are in the correct platform directory
- Check that `SKILL.md` exists in each skill folder
- Restart your AI agent

---

## Project Structure

```
zeVillage/
├── skills/                 # 51 skill directories
│   ├── gsap-animation/
│   │   ├── SKILL.md        # Instructions + YAML frontmatter
│   │   ├── LICENSE.txt     # License
│   │   └── scripts/        # Optional executable scripts
│   ├── threejs/
│   └── ...
├── agents/                 # 8 agent profiles
│   ├── orchestrator.md
│   ├── coder.md
│   └── ...
├── SPEC/                   # Agent Skills specification
├── template/               # Skill template
├── INSTALL.md             # This file
└── README.md              # Project overview
```
