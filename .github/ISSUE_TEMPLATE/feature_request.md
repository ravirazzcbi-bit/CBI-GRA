name: Feature Request
description: Suggest a new feature or enhancement
title: "[FEATURE] "
labels: ["enhancement", "needs-triage"]
assignees: []

body:
  - type: markdown
    attributes:
      value: |
        Thank you for the feature suggestion! Please describe what you'd like to see.

  - type: textarea
    id: description
    attributes:
      label: Description
      description: Clear description of the requested feature
      placeholder: "What feature would you like?"
    validations:
      required: true

  - type: textarea
    id: use-case
    attributes:
      label: Use Case
      description: Why is this feature needed? How would it be used?
      placeholder: "This feature is needed because..."
    validations:
      required: true

  - type: textarea
    id: solution
    attributes:
      label: Proposed Solution
      description: How should this feature work?
      placeholder: "The feature could work by..."

  - type: textarea
    id: alternatives
    attributes:
      label: Alternatives Considered
      description: What other approaches have you considered?
      placeholder: "Another approach could be..."

  - type: textarea
    id: additional
    attributes:
      label: Additional Context
      description: Any mockups, research, or other context
      placeholder: "Additional information..."

  - type: dropdown
    id: priority
    attributes:
      label: Priority
      options:
        - Low
        - Medium
        - High
        - Critical

  - type: checkboxes
    id: checklist
    attributes:
      label: Checklist
      options:
        - label: This feature doesn't already exist
          required: true
        - label: This is not a duplicate request
          required: true
