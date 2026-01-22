# Story Splitter Examples

## Basic Example (Auction Receiving)

### Input

```markdown
## Auction Receiving Feature

Manage receiving of products arrived from auctions.

### Receiving Flow
1. Enter receiving number (box number:branch number)
2. Display product list
3. Select products and confirm receiving
```

### Output

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
      # Functional
      - type: "functional"
        given: "Viewing the receiving screen"
        when: "Enter receiving number (e.g., 1:1,2,3) and press search button"
        then: "Matching product list is displayed"
      # UI - error
      - type: "ui"
        given: "Invalid receiving number entered"
        when: "Execute search"
        then: "Error message is displayed below the input field"
      # UI - loading
      - type: "ui"
        given: "Search is executing"
        when: "Waiting for results"
        then: "Loading spinner is displayed in the results area"
      # UI - empty
      - type: "ui"
        given: "No matching products"
        when: "Search completes"
        then: "Empty message is displayed in the results area"
      # UI - keyboard
      - type: "ui"
        given: "Focus is on the search input"
        when: "Press Enter key"
        then: "Search is executed"
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

## Form Example (User Registration)

```yaml
stories:
  - id: "REG-001"
    title: "Can register new user account"
    description: |
      As a visitor, I want to register a new account.
      Because I need to access member features.
    priority: "high"
    acceptance_criteria:
      # Functional
      - type: "functional"
        given: "Viewing registration form"
        when: "Enter valid email and password and submit"
        then: "Account is created and redirected to dashboard"
      # UI - error
      - type: "ui"
        given: "Invalid email format entered"
        when: "Focus leaves the email field"
        then: "Validation error is displayed below the email field"
      # UI - loading
      - type: "ui"
        given: "Form is being submitted"
        when: "Waiting for server response"
        then: "Submit button shows loading state and is disabled"
      # UI - keyboard
      - type: "ui"
        given: "Focus is on password field"
        when: "Press Enter key"
        then: "Form is submitted"
    pattern_used: "workflow"
```

## List Example (Product List)

```yaml
stories:
  - id: "PRD-001"
    title: "Can view product list with pagination"
    description: |
      As a user, I want to view products with pagination.
      Because I need to browse large product catalogs.
    priority: "high"
    acceptance_criteria:
      # Functional
      - type: "functional"
        given: "Viewing product list page"
        when: "Page loads"
        then: "First 20 products are displayed"
      # Functional
      - type: "functional"
        given: "Viewing product list"
        when: "Click next page button"
        then: "Next 20 products are displayed"
      # UI - loading
      - type: "ui"
        given: "Page is loading"
        when: "Fetching products"
        then: "Skeleton placeholders are displayed in the list area"
      # UI - empty
      - type: "ui"
        given: "No products exist"
        when: "Page loads"
        then: "Empty state with 'Add product' action is displayed"
      # UI - keyboard
      - type: "ui"
        given: "Focus is on a product row"
        when: "Press Enter key"
        then: "Product detail is opened"
    pattern_used: "crud"
```
