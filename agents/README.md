# Agents

Agents are role-based system profiles that define **how the AI behaves** — its persona, priorities, communication style, and which skills to favor. While skills teach the agent _how to do specific tasks_, agents define _who the agent is_ for a session.

## How to Use

Pass an agent profile to your CLI tool to adopt that role for the session. For example:

```bash
# With opencode or similar CLI agents
opencode --system-prompt .github/agents/coder.md

# Or load it as a system instruction in your agent config
```

Each agent file is a standalone system prompt. The agent will:

1. Adopt the specified role and priorities
2. Automatically favor relevant skills when available
3. Follow the communication style defined in the profile

## Available Agents

| Agent                             | Role                      | Best For                                                |
| --------------------------------- | ------------------------- | ------------------------------------------------------- |
| [orchestrator](./orchestrator.md) | Meta-Agent / Tech Lead    | Routing tasks to the right skills and agents            |
| [coder](./coder.md)               | Senior Frontend Developer | Writing production-ready code with tests                |
| [backend](./backend.md)           | Senior Backend Developer  | APIs, databases, server-side logic, distributed systems |
| [reviewer](./reviewer.md)         | Code Review Specialist    | Reviewing PRs, finding bugs, security audit             |
| [architect](./architect.md)       | System Architect          | Planning projects, choosing tech, structuring codebases |
| [planner](./planner.md)           | Project Planner           | Breaking down complex tasks, tracking progress          |
| [devops](./devops.md)             | DevOps Engineer           | CI/CD, Docker, deployment, infrastructure               |
| [writer](./writer.md)             | Technical Writer          | Documentation, READMEs, API references, blog posts      |

## Creating Custom Agents

Use the template structure:

```markdown
# Role Title

[One-line role summary]

## Identity

[Who you are, your experience level, your specialty]

## Priorities

[Ordered list of what matters most in this role]

## Communication Style

[How to communicate — tone, format, verbosity]

## Workflow

[Step-by-step process for handling requests]

## Rules

[Hard constraints and non-negotiables]

## Skill Affinity

[Which skills to prefer when available]
```

## Guidelines

All agent profiles must follow the project [GUIDELINES.md](../GUIDELINES.md):

- No vendor-specific references (use "the AI agent" not a specific product name)
- Author/creator defaults: "AI Agent"
- Copyright: Ziad Elnagar
