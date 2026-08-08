name: Bug Report
description: Report a bug or issue
title: "[BUG] "
labels: ["bug", "needs-triage"]
assignees: []

body:
  - type: markdown
    attributes:
      value: |
        Thank you for reporting a bug! Please fill out the details below to help us fix it quickly.

  - type: textarea
    id: description
    attributes:
      label: Description
      description: Clear and concise description of the bug
      placeholder: "What is the issue?"
    validations:
      required: true

  - type: textarea
    id: reproduction
    attributes:
      label: Steps to Reproduce
      description: |
        How can we reproduce this bug? List the exact steps.
      placeholder: |
        1. Open the app
        2. Navigate to [screen]
        3. Click [button]
        4. Observe [behavior]
    validations:
      required: true

  - type: textarea
    id: expected
    attributes:
      label: Expected Behavior
      description: What should happen?
      placeholder: "The app should..."
    validations:
      required: true

  - type: textarea
    id: actual
    attributes:
      label: Actual Behavior
      description: What actually happens?
      placeholder: "The app actually..."
    validations:
      required: true

  - type: dropdown
    id: device
    attributes:
      label: Device Type
      options:
        - Emulator
        - Physical Device
        - Unknown
    validations:
      required: true

  - type: input
    id: android-version
    attributes:
      label: Android Version
      description: "e.g., Android 11, Android 12"
      placeholder: "Android X.X"
    validations:
      required: true

  - type: input
    id: device-model
    attributes:
      label: Device Model
      description: "e.g., Samsung A51, Pixel 4"
      placeholder: "Device model"

  - type: input
    id: app-version
    attributes:
      label: App Version
      description: "e.g., 1.0.0"
      placeholder: "1.0.0"
    validations:
      required: true

  - type: textarea
    id: logs
    attributes:
      label: Logs or Error Messages
      description: Any relevant logs, error messages, or stack traces
      placeholder: "Paste logs here (use code block with ```)"
      render: shell

  - type: textarea
    id: screenshots
    attributes:
      label: Screenshots
      description: "If applicable, add screenshots showing the bug"
      placeholder: "You can paste images directly or drag and drop"

  - type: textarea
    id: additional
    attributes:
      label: Additional Context
      description: Any other context that might help
      placeholder: "Additional information..."

  - type: checkboxes
    id: checklist
    attributes:
      label: Checklist
      options:
        - label: I have searched existing issues
          required: true
        - label: I am using the latest version
          required: false
        - label: This issue is reproducible
          required: true
