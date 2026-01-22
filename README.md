# User Story Claude Plugin

A Claude Code plugin that splits PRDs/requirements into user stories using Humanizing Work's 9 patterns.

## Installation

```bash
git clone https://github.com/ryo-morimoto/user-story-claude-plugin ~/.claude/plugins/story-splitter
```

## Components

### Skill: story-splitter

Triggered by natural language:

- "split stories"
- "create user stories from PRD"
- "split into user stories"

### Agent: story-splitter-agent

For autonomous story splitting:

```
Task: story-splitter-agent
Prompt: "Split docs/prd/feature.md into user stories"
```

### Command: /verify-stories

Review generated stories with human verification:

```
/verify-stories ./docs/stories/feature.stories.yaml
```

## Splitting Patterns

1. Workflow Steps
2. Operations (CRUD)
3. Business Rule Variations
4. Variations in Data
5. Data Entry Methods
6. Simple/Complex
7. Defer Performance
8. Major Effort
9. Break Out a Spike

## Output

Stories are generated at `./docs/stories/{feature-name}.stories.yaml` with INVEST validation.

## License

MIT
