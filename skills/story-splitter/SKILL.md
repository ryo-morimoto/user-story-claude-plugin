---
name: story-splitter
description: Use when splitting PRD, requirements, or features into user stories. Triggers on "split stories", "create user stories", "split into stories", "break down into stories".
version: 1.0.0
---

# User Story Splitting Skill

Rules and process for splitting PRD/requirements into appropriately-sized user stories.

## When to Apply

- After PRD creation, during pre-implementation planning
- When splitting large features into smaller stories
- When asked to "split into user stories"

## Splitting Process

### Step 1: Input Verification

Read PRD/requirements and identify:

1. **Feature overview**: What it achieves
2. **Users**: Who the feature is for
3. **Main workflows**: How it will be used
4. **Business rules**: What constraints exist

### Step 2: Apply 9 Patterns (Value Priority Order)

Apply patterns in the following order to split:

#### Pattern 1: Workflow Steps

```
Condition: Multiple steps in a workflow
Method: Split each step into independent stories
Example:
  "Receive products" ->
  - "Can search products by receiving number"
  - "Can select products from search results"
  - "Can confirm receiving of selected products"
```

#### Pattern 2: Operations (CRUD)

```
Condition: Generic verbs like "manage", "operate"
Method: Split into Create/Read/Update/Delete units
Example:
  "Can manage inventory" ->
  - "Can create new inventory"
  - "Can view inventory list"
  - "Can update inventory info"
  - "Can delete inventory"
```

#### Pattern 3: Business Rule Variations

```
Condition: Multiple business rules exist
Method: Split each rule variation into independent stories
Example:
  "Can search with flexible dates" ->
  - "Can search by date range"
  - "Can search by weekends"
  - "Can search by +/- N days"
```

#### Pattern 4: Variations in Data

```
Condition: Multiple types of data handled
Method: Split by data type
Example:
  "Can register product info" ->
  - "Can register text info (name, description)"
  - "Can register images"
  - "Can register pricing info"
```

#### Pattern 5: Data Entry Methods

```
Condition: Multiple input method variations
Method: Split by UI complexity (simple -> rich)
Example:
  "Can enter date" ->
  - "Can enter date via text input"
  - "Can select date via calendar UI"
```

#### Pattern 6: Simple/Complex

```
Condition: Feature has many variations or exceptions
Method: Simple core version first, complex cases later
Example:
  "Can search flights" ->
  - "Can search flights between two locations"
  - "Can specify number of connections"
  - "Can include nearby airports"
```

#### Pattern 7: Defer Performance

```
Condition: Performance requirements exist
Method: Implement functionality first, optimize performance later
Example:
  "Can search quickly" ->
  - "Can search (basic implementation)"
  - "Can display search results within 5 seconds"
```

#### Pattern 8: Major Effort

```
Condition: 80/20 rule applies
Method: Major implementation first, additional support later
Example:
  "Can pay with multiple credit cards" ->
  - "Can pay with one type of credit card"
  - "Can support remaining credit card types"
```

#### Pattern 9: Break Out a Spike (Last Resort)

```
Condition: High technical uncertainty
Method: Extract investigation task
Example:
  "Can integrate with external API" ->
  - "Investigate external API specifications"
  - "Implement external API integration"
```

### Step 3: Quality Check (INVEST Principles)

Verify each story meets the following:

| Principle | Check Item | Action on Violation |
|-----------|------------|---------------------|
| **I**ndependent | Dependencies <= 2 | Reduce dependencies or warn |
| **N**egotiable | Implementation not fixed | Remove technical details |
| **V**aluable | Is a Vertical Slice | **Error**: Must re-split |
| **E**stimable | Can be estimated | Extract unknowns to Spike |
| **S**mall | Files <= 3, AC <= 5 | **Error**: Must re-split |
| **T**estable | Can write Given-When-Then | Clarify AC |

### Step 4: Vertical Slice Validation

**Required check**: All stories must be Vertical Slices.

The following splits are **rejected as errors**:
- "Implement UI" (UI only)
- "Create API" (API only)
- "Design DB schema" (DB only)
- Any split by technical layer

**Correct split**: One feature includes UI + logic + data access

## Output Format

### YAML Format

```yaml
metadata:
  source: "PRD file path or requirements summary"
  created_at: "YYYY-MM-DDTHH:MM:SSZ"
  feature_prefix: "3-letter prefix representing the feature"
  pattern_stats:
    workflow: 0
    crud: 0
    rules: 0
    data: 0
    ui: 0
    simple_complex: 0
    performance: 0
    major_effort: 0
    spike: 0

stories:
  - id: "PREFIX-001"
    title: "As a user, can do X"
    description: |
      As a user, I want to do X.
      Because I need Y.
    priority: "high|medium|low"
    acceptance_criteria:
      - given: "Precondition"
        when: "Action"
        then: "Expected result"
    dependencies:
      blocks: []
      blocked_by: []
      related: []
    estimated_files:
      - "Expected file paths to change"
    pattern_used: "Pattern name applied"

review:
  passed: true|false
  issues: []
  warnings: []
```

### Output Location

`./docs/stories/{feature-name}.stories.yaml`

## Quantitative Criteria

| Metric | Limit | On Exceed |
|--------|-------|-----------|
| Changed files | 3 | Re-split |
| Acceptance criteria | 5 | Re-split |
| Dependent stories | 2 | Warning |

## Usage Example

### Input Example

```markdown
## Auction Receiving Feature

Manage receiving of products arrived from auctions.

### Receiving Flow
1. Enter receiving number (box number:branch number)
2. Display product list
3. Select products and confirm receiving
```

### Output Example

```yaml
metadata:
  source: "Auction Receiving Feature"
  created_at: "2026-01-22T00:00:00Z"
  feature_prefix: "AUC"
  pattern_stats:
    workflow: 3

stories:
  - id: "AUC-001"
    title: "Can search products by receiving number"
    description: |
      As a warehouse staff, I want to search products by receiving number.
      Because I need to identify the products for receiving.
    priority: "high"
    acceptance_criteria:
      - given: "Viewing the receiving screen"
        when: "Enter receiving number (e.g., 1:1,2,3) and press search button"
        then: "Matching product list is displayed"
      - given: "Invalid receiving number entered"
        when: "Execute search"
        then: "Error message is displayed"
    dependencies:
      blocks: ["AUC-002"]
      blocked_by: []
      related: []
    estimated_files:
      - "src/app/(authenticated)/auction/page.tsx"
      - "src/app/(authenticated)/auction/_actions/searchByReceivingNumber.ts"
      - "src/app/(authenticated)/auction/_components/SearchForm.tsx"
    pattern_used: "workflow"

  - id: "AUC-002"
    title: "Can select products from search results"
    # ... continues

review:
  passed: true
  issues: []
  warnings: []
```

## Error Handling

### Vertical Slice Violation

```yaml
review:
  passed: false
  issues:
    - story_id: "AUC-003"
      type: "VERTICAL_SLICE_VIOLATION"
      message: "Splitting by technical layer is not allowed"
      suggestion: "Re-split by feature unit including UI, logic, and data access"
```

### Size Exceeded

```yaml
review:
  passed: false
  issues:
    - story_id: "AUC-002"
      type: "SIZE_EXCEEDED"
      message: "Story is too large"
      metrics:
        estimated_files: 5
        acceptance_criteria: 7
      suggestion: "Consider re-splitting with Workflow pattern"
```

## References

- [The Humanizing Work Guide to Splitting User Stories](https://www.humanizingwork.com/the-humanizing-work-guide-to-splitting-user-stories/)
- [Mountain Goat Software - SPIDR](https://www.mountaingoatsoftware.com/blog/five-simple-but-powerful-ways-to-split-user-stories)
