# Contribution #1: Android Long Press App Icon New Reminder

**Contribution Number:** 1
**Student:** Minh Pham
**Issue:** [Android long press app icon new reminder](https://github.com/streetwriters/notesnook/issues/9694)
**Status:** Phase I Complete

---

## Why I Chose This Issue

I chose this issue because it is a small but meaningful mobile usability improvement. Notesnook already provides a quick way to create a new note from the Android app icon long-press menu, and this issue asks for a similar shortcut for creating a new reminder. I like that this feature would make a common user workflow faster and more convenient without requiring users to open the full app and navigate through multiple screens first.

This issue also matches my learning goals because it gives me the opportunity to explore how a React Native mobile app integrates with Android-specific platform features. I am interested in learning more about Android launcher shortcuts, deep links, native Android configuration, and how those connect back into the JavaScript/React Native side of the app. Through this issue, I hope to practice navigating a large open-source codebase, tracing an existing feature, and implementing a focused improvement that follows the project’s current patterns.

---

## Understanding the Issue

### Problem Description

Currently, the Notesnook Android app supports creating a new note from the app icon long-press menu, but it does not provide a similar shortcut for creating a new reminder. This means users who frequently use reminders must open the app normally and manually navigate to the reminder creation flow instead of accessing it directly from the launcher shortcut menu.

### Expected Behavior

When a user long-presses the Notesnook app icon on Android, they should see an option to create a new reminder, similar to the existing “New note” shortcut. Tapping this shortcut should open Notesnook and bring the user directly to the new reminder creation flow.

### Current Behavior

The Android app icon long-press menu includes an option for creating a new note, but there is no equivalent option for creating a new reminder.

### Affected Components

The likely affected components are the Android mobile launcher shortcut configuration and the app’s existing deep-link handling for reminders. Based on my initial review, this may involve the Android app manifest or shortcut XML resources, along with the existing React Native flow that handles opening the new reminder screen.


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
