# Ideation — Capture Ideas

## When to use
Developer has ideas to capture — one or many. Raw thoughts, feature requests, bugs, improvements.

## Checklist

### Capture each idea

- [ ] For each idea from developer:
  ```
  brain.create_memory({
    title: "<concise idea title>",
    kind: "ideas",
    tags: ["<area/x>", "<persona/y>"]
  })
  ```
  → Brain response gives file path and format guide
  → Write 5W content to the file:
    - **Why:** what problem does this solve
    - **What:** the idea in concrete terms
    - **Who:** who benefits
    - **When:** urgency or timeline
    - **How:** approach or constraints (if known)
  → 3-6 checkboxes for concrete deliverables (don't over-plan)

### Attach reference files (if any)

- [ ] For each reference file (sketches, samples, screenshots, Figma exports, CSVs):
  ```
  brain.attach_to_memory({
    file_path: "<path to file>",
    memory_file: "<idea memory_file>"
  })
  ```

### Present summary to developer

- [ ] List all captured ideas with titles and tags
- [ ] Ask: "Ready to plan any of these? Which ones?"
- [ ] If developer picks ideas to plan → hand off to planning skill
