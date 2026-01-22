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

### Step 2: Sort Stories by Priority

Sort stories by priority before review:

1. **high** - Review first (blocking stories, workflow entry points)
2. **medium** - Review second (standard stories)
3. **low** - Review last (optimizations, improvements)

### Step 3: For Each Story, Verify with Dynamic Questions

For each story in priority-sorted order, use `AskUserQuestion` with dynamically generated questions.

#### Question Batching Rules

- **First call**: Purpose (1 question) + Acceptance criteria 1-3 (up to 3 questions) = max 4 questions
- **Subsequent calls**: Remaining criteria, 3 questions at a time

#### Dynamic Question Generation Guidelines

When generating questions and choices, dig deep to the **Why level**:

**Shallow questions (avoid)**
- "Can search customers by partial name match" (feature description)
- "Display 10 results per page" (How)

**Deep questions (aim for)**
- "High visit frequency means no time for customer selection, so need to identify target customer with minimal input" (Why)
- "Limit to viewable count per screen so selection completes without scrolling" (Why)

Perspectives for question generation:
- **Who** is in what situation
- **What problem** are they facing
- **What outcome** do they want to achieve

#### Choice Generation

Dynamically generate choices by combining two types:

1. **Interpretation patterns**: Present concrete interpretations of the story's intent
2. **Evaluation**: "This purpose/criterion is sufficiently clear", etc.

**Note**: Do not include negative choices ("has issues", "incorrect", etc.). If there are problems, the user selects "Other" and provides free-form input.

### Step 4: Track Issues

If the user selects "Other" and provides feedback:
- Record the story ID and the issue
- Record the correction needed

### Step 5: Summary Report

After reviewing all stories, provide:
- Total stories reviewed
- Stories with issues identified (Other selected)
- Recommended corrections

## Verification Flow Per Story

```
# Sort: high -> medium -> low
sorted_stories = sort(stories, by: priority, order: [high, medium, low])

For story in sorted_stories:
  criteria_count = len(story.acceptance_criteria)

  # First call: Purpose + criteria 1-3
  batch1_criteria = min(3, criteria_count)

  AskUserQuestion with up to 4 questions:
    Q1: Purpose verification (dynamically generated)
        - Display: story.id, story.title, story.description
        - Generate: Why-level interpretation choices

    Q2-Q4: Criteria 1-3 verification (dynamically generated)
        - Display: given/when/then for each criterion
        - Generate: Why-level interpretation choices

  # Subsequent calls: remaining criteria, 3 at a time
  remaining_criteria = story.acceptance_criteria[3:]
  for batch in chunks(remaining_criteria, 3):
    AskUserQuestion with up to 3 questions:
      Each: Criterion verification (dynamically generated)
        - Display: given/when/then
        - Generate: Why-level interpretation choices

  # Record any "Other" responses as issues
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

## Example Question (Purpose)

```
CUS-001: Search and select customer information

[Purpose]
Enable appropriate customer service by referencing past purchase history when a customer visits.

Which interpretation of this purpose is correct?
```

**Dynamically generated choice examples**:
- "High visit frequency means no time for customer selection, need to identify with minimal input"
- "For special treatment of repeat customers, need instant history lookup upon visit"
- "This purpose is sufficiently clear, no additional interpretation needed"

## Example Question (Acceptance Criteria)

```
CUS-001 Criterion 1:
Given: Viewing the customer search screen
When: Enter partial customer name and execute search
Then: List of matching customers is displayed

What is the intent of this criterion?
```

**Dynamically generated choice examples**:
- "Limit to viewable count per screen so selection completes without scrolling"
- "Prioritize speed over precision with partial match, narrow down with 2-3 characters"
- "This criterion has sufficient information for implementation"
