# story-splitter

A Claude Code plugin that splits PRDs/requirements into user stories.

## Features

- Apply Humanizing Work's 9 patterns for story splitting
- Quality validation using INVEST principles
- Output stories in YAML format

## Installation

```bash
# Clone to plugins directory
git clone https://github.com/ryo-morimoto/user-story-claude-plugin ~/.claude/plugins/story-splitter
```

## Usage

### As a Skill

```
/story-splitter
```

Or use natural language:

- "split stories"
- "create user stories"
- "split into user stories"

### As an Agent

```
Task: story-splitter-agent
Prompt: "Split docs/prd/feature.md into user stories"
```

## Output

Generates story files at `./docs/stories/{feature-name}.stories.yaml`

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

## License

MIT
