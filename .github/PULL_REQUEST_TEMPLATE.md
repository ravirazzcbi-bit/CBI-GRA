name: Pull Request
description: Submit code changes
title: "[TYPE] Brief description"
labels: []
assignees: []

body:
  - type: markdown
    attributes:
      value: |
        Thank you for submitting a pull request! Please fill out the details below.

  - type: textarea
    id: description
    attributes:
      label: Description
      description: Describe the changes in this PR
      placeholder: "What does this PR do?"
    validations:
      required: true

  - type: dropdown
    id: type
    attributes:
      label: Type of Change
      options:
        - Bug fix
        - New feature
        - Enhancement
        - Documentation
        - Refactoring
        - Other
    validations:
      required: true

  - type: input
    id: related-issues
    attributes:
      label: Related Issues
      description: "Link related issues (e.g., Fixes #123)"
      placeholder: "Fixes #123, Related to #456"

  - type: textarea
    id: testing
    attributes:
      label: Testing Done
      description: How have you tested these changes?
      placeholder: |
        - [ ] Tested on emulator
        - [ ] Tested on physical device
        - [ ] All unit tests pass
        - [ ] Manual testing completed
    validations:
      required: true

  - type: textarea
    id: screenshots
    attributes:
      label: Screenshots (if applicable)
      description: "UI changes? Add before/after screenshots"
      placeholder: "Add images here"

  - type: textarea
    id: database-changes
    attributes:
      label: Database Schema Changes (if applicable)
      description: "Do these changes modify the database schema?"
      placeholder: "Describe schema changes, migrations, or versioning updates"

  - type: textarea
    id: migration-notes
    attributes:
      label: Migration Notes
      description: "Any special steps needed to deploy this change?"
      placeholder: "e.g., device re-provisioning required, data migration, etc."

  - type: textarea
    id: breaking-changes
    attributes:
      label: Breaking Changes
      description: "Does this PR introduce breaking changes?"
      placeholder: "Describe any breaking changes"

  - type: textarea
    id: additional
    attributes:
      label: Additional Context
      description: Any other relevant information
      placeholder: "Additional context..."

  - type: checkboxes
    id: checklist
    attributes:
      label: Checklist
      options:
        - label: Code follows style guidelines
          required: true
        - label: No new warnings introduced
          required: true
        - label: Tests added/updated (if applicable)
          required: false
        - label: Documentation updated (if applicable)
          required: false
        - label: Commits are atomic and well-documented
          required: true
        - label: PR is ready for review
          required: true
