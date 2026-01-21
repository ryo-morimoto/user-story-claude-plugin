---
name: verify-stories
description: Verify user stories with human review. Checks each story for understanding alignment.
arguments:
  - name: file_path
    description: Path to the stories YAML file
    required: true
---

# Story Verification Command

Verify each user story with the human reviewer to ensure mutual understanding.

## Process

### Step 1: Load Stories File

Read the YAML file from `$ARGUMENTS.file_path` and parse all stories.

### Step 2: For Each Story, Verify Understanding

For each story in `stories[]`, use `AskUserQuestion` to confirm:

1. **Title & Description Check**
   - Display the story ID, title, and description
   - Ask if the understanding is correct

2. **Acceptance Criteria Check**
   - Display all acceptance criteria (given/when/then)
   - Ask if these criteria capture the requirements correctly

3. **Dependencies Check**
   - Show blocks, blocked_by, and related stories
   - Ask if the dependency relationships are accurate

4. **Estimated Files Check**
   - List the estimated files to be changed
   - Ask if these file estimates are reasonable

### Step 3: Track Issues

If the human identifies a misunderstanding:
- Record the story ID and the issue
- Ask for the correction needed
- Suggest updating the stories file

### Step 4: Summary Report

After reviewing all stories, provide:
- Total stories reviewed
- Stories with issues identified
- Recommended corrections

## Verification Flow Per Story

```
For story in stories:
  1. Show story summary (id, title, description)
  2. AskUserQuestion: "Is this story's purpose correctly understood?"
     Options: "Yes, correct" / "Needs clarification" / "Wrong understanding"

  3. If not "Yes, correct":
     - AskUserQuestion: "What needs to be corrected?"
     - Record the feedback

  4. Show acceptance criteria
  5. AskUserQuestion: "Are these acceptance criteria complete and accurate?"
     Options: "Yes" / "Missing criteria" / "Incorrect criteria"

  6. If issues:
     - AskUserQuestion: "What should be added/changed?"
     - Record the feedback

  7. Show dependencies and estimated files
  8. AskUserQuestion: "Are dependencies and file estimates accurate?"
     Options: "Yes" / "Dependencies wrong" / "Files wrong" / "Both wrong"

  9. Record all feedback for this story
```

## Output

After all stories are verified:
- List all stories that passed verification
- List all stories needing correction with details
- Provide actionable next steps

## Example Usage

```
/verify-stories ./docs/stories/auction-receiving.stories.yaml
```

## Question Templates

### Understanding Check
```
Story: {id}
Title: {title}
Description:
{description}

Is this story's purpose and scope correctly understood?
```

### Acceptance Criteria Check
```
Story: {id} - Acceptance Criteria:

{for each ac}
- Given: {given}
  When: {when}
  Then: {then}
{end for}

Are these acceptance criteria complete and accurate?
```

### Dependencies & Files Check
```
Story: {id}

Dependencies:
- Blocks: {blocks}
- Blocked by: {blocked_by}
- Related: {related}

Estimated Files:
{for each file}
- {file}
{end for}

Are the dependencies and file estimates accurate?
```
