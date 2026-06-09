# Contribution #1: Graph Editor: Group Nodes to Create Node Graph

**Contribution Number:** 1 <br>
**Student:** Minh Pham <br>
**Issue:** [Graph Editor: Group nodes to create node graph](https://github.com/AcademySoftwareFoundation/MaterialX/issues/2018) <br>
**Status:** Phase 1 Complete

---

## Why I Chose This Issue

I chose this issue because it focuses on improving the user experience of a graph-based editor. The current workflow requires users to manually create a node graph node, enter it, and then copy or paste existing nodes into it. This issue proposes a smoother workflow where selected nodes can be grouped into a new node graph directly, which feels like a practical feature that would make graph editing faster and more intuitive.

This issue also matches my learning goals because it gives me a chance to work with a larger open-source codebase that is connected to graphics, materials, and node-based editing. I am interested in learning how visual editor actions are connected to underlying data structures, especially how selected nodes, node graph creation, and node connections are represented in code. Through this contribution, I hope to strengthen my skills in reading unfamiliar C++/graphics-oriented code, understanding editor command patterns, and contributing to a real production tool used by the digital content creation community.

---

## Understanding the Issue

### Problem Description

In the MaterialX Graph Editor, users can currently create node graph nodes, but there is no direct command or hotkey to group already-selected nodes into a new node graph. If a user has already built part of a graph and later decides that those nodes should live inside their own node graph, they must manually create the node graph and move the nodes through copy, cut, and paste. This adds extra steps and can interrupt the editing workflow.

### Expected Behavior

The expected behavior is that the Graph Editor should provide a command or hotkey that takes the currently selected nodes, creates a new node graph node, and places the selected nodes inside that new node graph. Ideally, this should preserve the relevant connections so users do not have to rebuild their graph structure manually.

### Current Behavior

Currently, users must create a node graph node first, double-click into it, and then add nodes inside it. Existing nodes cannot be grouped into a node graph directly; they have to be moved manually using copy, cut, or paste. The issue discussion also notes that nested node graphs are currently unsupported, so selected nodes should be checked to make sure they do not already include a node graph.

### Affected Components

The likely affected components are the MaterialX Graph Editor’s node selection logic, node graph creation workflow, copy/cut/paste or move-node behavior, and any command or hotkey system used by the editor. The solution will likely involve understanding how the editor represents selected nodes, how it creates node graph nodes, and how it updates connections when nodes are moved into a new graph.

---

## Reproduction Process

### Environment Setup

[Notes on setting up your local development environment - challenges you faced, how you solved them]

### Steps to Reproduce

1. [Step 1]
2. [Step 2]
3. [Observed result]

### Reproduction Evidence

- **Commit showing reproduction:** [Link to commit in your fork]
- **Screenshots/logs:** [If applicable]
- **My findings:** [What you discovered during reproduction]

---

## Solution Approach

### Analysis

[Your analysis of the root cause - what's causing the issue?]

### Proposed Solution

[High-level description of your fix approach]

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** [Restate the problem]

**Match:** [What similar patterns/solutions exist in the codebase?]

**Plan:** [Step-by-step implementation plan]
1. [Modify file X to do Y]
2. [Add function Z]
3. [Update tests]

**Implement:** [Link to your branch/commits as you work]

**Review:** [Self-review checklist - does it follow the project's contribution guidelines?]

**Evaluate:** [How will you verify it works?]

---

## Testing Strategy

### Unit Tests

- [ ] Test case 1: [Description]
- [ ] Test case 2: [Description]
- [ ] Test case 3: [Description]

### Integration Tests

- [ ] Integration scenario 1
- [ ] Integration scenario 2

### Manual Testing

[What you tested manually and results]

---

## Implementation Notes

### Week [X] Progress

[What you built this week, challenges faced, decisions made]

### Week [Y] Progress

[Continue documenting as you work]

### Code Changes

- **Files modified:** [List]
- **Key commits:** [Links to important commits]
- **Approach decisions:** [Why you chose certain approaches]

---

## Pull Request

**PR Link:** [GitHub PR URL when submitted]

**PR Description:** [Draft or final PR description - much of the content above can be adapted]

**Maintainer Feedback:**
- [Date]: [Summary of feedback received]
- [Date]: [How you addressed it]

**Status:** [Awaiting review / Iterating / Approved / Merged]

---

## Learnings & Reflections

### Technical Skills Gained

[What you learned technically]

### Challenges Overcome

[What was hard and how you solved it]

### What I'd Do Differently Next Time

[Reflection on your process]

---

## Resources Used

- [Link to helpful documentation]
- [Tutorial or Stack Overflow post that helped]
- [GitHub issues or discussions that helped]
