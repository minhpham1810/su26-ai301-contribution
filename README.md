# Contribution #1: Graph Editor: Group Nodes to Create Node Graph

**Contribution Number:** 1 <br>
**Student:** Minh Pham <br>
**Issue:** [Graph Editor: Group nodes to create node graph](https://github.com/AcademySoftwareFoundation/MaterialX/issues/2018) <br>
**Status:** Phase 4 Complete

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

**Status:** Phase II Complete

## Reproduction Process

### Environment Setup

I built the MaterialX Graph Editor locally from my fork. The local CMake build is configured with `MATERIALX_BUILD_GRAPH_EDITOR=ON`, and the Graph Editor executable was generated at `build/bin/MaterialXGraphEditor`.

I verified the executable runs by calling:

`./build/bin/MaterialXGraphEditor --help`

This printed `MaterialXGraphEditor version 1.39.6` and the available command-line options.

### Steps to Reproduce

1. Launch the Graph Editor with an example material:

   `./build/bin/MaterialXGraphEditor --material resources/Materials/Examples/StandardSurface/standard_surface_marble_solid.mtlx --path .`

2. In the editor window, select multiple existing nodes in the same graph.
3. Open the editor help / keyboard shortcuts and confirm there is no shortcut for grouping selected nodes into a node graph.
4. Press `Tab`, choose `Node Graph`, and create a new node graph node.
5. Double-click into the new node graph.

**Expected:** There should be a command or hotkey that creates a node graph from the currently selected nodes and moves those selected nodes into the new node graph, ideally preserving connections.

**Actual:** The Graph Editor can create an empty node graph, but it does not group the selected nodes into that node graph. The selected nodes remain in the original graph, and the user must manually copy/cut/paste nodes into the node graph.

### Reproduction Evidence

- **Working branch:** https://github.com/minhpham1810/MaterialX/tree/issue-2018-group-nodes-nodegraph
- **Issue reproduced locally:** The Graph Editor supports creating empty node graphs, selecting nodes, and copy/cut/paste, but there is no command that combines these behaviors into “group selected nodes into a node graph.”
- **My findings:** The relevant code is in `source/MaterialXGraphEditor/Graph.cpp`. Selected nodes are already gathered in the main graph loop, node graph creation already exists, and copy/cut/paste behavior already moves/copies selected nodes. The missing part is a dedicated command that creates a node graph from the current selection.

## Solution Approach

### Analysis

This is not a rendering or MaterialX document parsing bug. It is a missing editor workflow. The Graph Editor already has most of the pieces separately:

- `Graph::addNode` can create a new node graph when the category is `"nodegraph"`.
- The main editor loop gathers selected nodes with `ed::GetSelectedNodes`.
- Existing shortcut handling supports copy, cut, paste, delete, frame selection, and navigating up from a subgraph.
- The help menu documents shortcuts for editing, but there is no shortcut for grouping selected nodes into a node graph.

The issue discussion also notes that nested node graphs are currently unsupported, so the implementation should reject or ignore selections that already contain a node graph.

### Proposed Solution

Add a new Graph Editor command/hotkey that takes the currently selected nodes, validates the selection, creates a new node graph, moves or copies the selected nodes into that graph, and updates the UI state so the user can continue editing without manually rebuilding the graph.

For the first version, I will focus on the core workflow: create the node graph from selected non-nodegraph nodes and move the selected nodes into it. Preserving external connections may be handled carefully as part of this work, or scoped as a follow-up if it becomes too large.

### Implementation Plan

**Understand:** The Graph Editor currently requires users to manually create a node graph and move nodes into it. The requested behavior is a direct grouping workflow for selected nodes.

**Match:** Similar logic already exists in the editor’s selection and clipboard paths. The code around selected-node gathering, `ed::AcceptCopy`, `ed::AcceptCut`, `ed::AcceptPaste`, `copyUiNode`, `copyInputs`, and `deleteNode` shows how the editor maps selected UI nodes back to MaterialX elements and updates the graph state.

**Plan:**
1. Add a new helper method in `Graph` for grouping selected nodes into a node graph.
2. Reuse the existing selected-node collection from the main editor loop.
3. Validate the selected nodes before grouping:
   - require at least one selected node,
   - reject read-only documents,
   - reject selections that include an existing node graph to avoid unsupported nesting.
4. Create the new MaterialX node graph and corresponding `UiNode`.
5. Move or copy the selected nodes into the new node graph using the existing copy/cut/paste patterns where possible.
6. Rebuild or relink the graph state after the operation using existing graph update helpers.
7. Add or update help text so the new hotkey is visible to users.
8. Test manually in the Graph Editor with simple selected nodes and with an invalid selection that includes a node graph.

**Implement:** Branch: https://github.com/minhpham1810/MaterialX/tree/issue-2018-group-nodes-nodegraph

**Review:** I will follow MaterialX’s contribution guidelines: keep the change focused, follow the existing C++ style, use camelCase naming, avoid unrelated refactors, and make sure the branch is pushed to my fork before opening a PR.

**Evaluate:** I will verify the fix by rebuilding `MaterialXGraphEditor`, repeating the reproduction steps, and confirming that selected nodes can be grouped into a new node graph. I will also run the available MaterialX tests or at least the relevant build target to check for regressions.
---

## Phase III: Build

### Implementation Notes

This phase implemented the Graph Editor workflow for grouping selected nodes into a new node graph using the `Shift+C` shortcut.

**What I built:**
- Added a command that groups selected Graph Editor nodes into a new compound `NodeGraph`.
- Preserved internal links between selected nodes.
- Preserved external upstream and downstream connections by creating nodegraph interface inputs and outputs.
- Supported grouping at the current graph level, including inside existing nodegraphs.
- Updated nested compound nodegraph connection resolution in MaterialX core.
- Fixed the `Shift+C` hotkey so it is detected directly by the Graph Editor instead of relying on the node editor’s built-in shortcut handler.
- Added regression coverage for nested nodegraph connections and the grouping shortcut predicate.

### Code Changes

**Working branch:**  
https://github.com/minhpham1810/MaterialX/tree/issue-2018-group-nodes-nodegraph

**Key commits:**
- `02dc5d47` - `Support grouping selected nodes into nested nodegraphs`
- `f9f1acd2` - `Fix graph editor Shift+C node grouping shortcut`

**Main files modified:**
- `source/MaterialXGraphEditor/Graph.cpp`
- `source/MaterialXGraphEditor/Graph.h`
- `source/MaterialXGraphEditor/GraphShortcuts.h`
- `source/MaterialXCore/Interface.cpp`
- `source/MaterialXTest/CMakeLists.txt`
- `source/MaterialXTest/MaterialXCore/Node.cpp`
- `source/MaterialXTest/MaterialXGraphEditor/CMakeLists.txt`
- `source/MaterialXTest/MaterialXGraphEditor/GraphShortcuts.cpp`

### Challenges Faced

The first challenge was preserving graph structure. Simply moving selected nodes into a nodegraph was not enough because links crossing the selected-node boundary would be lost. I solved this by converting outside-to-inside links into nodegraph interface inputs and inside-to-outside links into nodegraph outputs.

The second challenge was nested nodegraphs. Grouping inside an existing nodegraph exposed a MaterialX core issue where nested compound nodegraph ports did not resolve correctly against their parent graph scope. I updated the connection resolver so nested boundary connections can resolve properly.

The final challenge was the `Shift+C` shortcut itself. The original implementation checked the shortcut inside the node editor’s built-in shortcut handler, but that handler only accepts certain built-in shortcuts such as copy, cut, paste, duplicate, and space. I fixed this by moving the grouping shortcut detection outside that path and adding a small testable shortcut predicate.

## Testing Strategy

### Unit Tests

- Added `Nested compound nodegraph connections` coverage in `MaterialXTest` to verify nested nodegraph boundary connections resolve correctly.
- Added `Graph editor grouping shortcut` coverage to verify `Shift+C` activates only when the right modifier/key state is present.
- Verified the shortcut is disabled when conflicting UI states are active, such as graph editor popups or file dialogs.

### Build and Regression Testing

Validation completed:

```bash
git diff --check
/private/tmp/materialx-hotkey-build/bin/MaterialXTest "Graph editor grouping shortcut"
cmake --build build --target MaterialXGraphEditor
```
### Manual Testing

I tested the Graph Editor workflow by selecting nodes in a graph and using Shift+C to group them into a new nodegraph. The grouped nodes moved into the new nodegraph, internal connections were preserved, and external links were represented through nodegraph interface inputs and outputs.

---

## Pull Request

**PR Link:** https://github.com/AcademySoftwareFoundation/MaterialX/pull/3002

**PR Description:** 

Fixes the MaterialX Graph Editor `Shift+C` node-grouping shortcut so it actually triggers when graph nodes are selected.

The shortcut had been checked inside the node editor’s built-in shortcut handler, which only activates for its own copy/cut/paste-style shortcuts. This moves the grouping shortcut detection outside that gate while keeping it disabled when graph editor popups or file dialogs are active.

**Maintainer Feedback:**
- [Date]: [Summary of feedback received]
- [Date]: [How you addressed it]

**Status:**  Awaiting Review

---

## Learnings & Reflections

### Technical Skills Gained

C++, debugging, testing

### Challenges Overcome

The hard part for me was the C++ language itself, since I was not very familiar with it. As I progress in implementing this feature, it took a lot of time for me to understand and write in C++, but eventually I could afford to make myself a little more comfortable with it.

### What I'd Do Differently Next Time

I would write the shortcut predicate test earlier, before wiring the command into the editor loop. That would have made the shortcut limitation easier to isolate. I would also investigate framework shortcut behavior earlier instead of assuming all key combinations could be handled through the same built-in shortcut path.

---

## Resources Used

- [Issue discussion](https://github.com/AcademySoftwareFoundation/MaterialX/issues/2018)
- [learncpp website](https://www.learncpp.com/)

# Contribution 2: Graph Editor: Add UI support for enumerated values

**Contribution Number:** 2 
**Student:** Minh Pham  
**Issue:** [GitHub issue link](https://github.com/AcademySoftwareFoundation/MaterialX/issues/2366#event-27859335602)  
**Status:** Phase I

---

## Why I Chose This Issue

This issue comes from the same project where I made my first open-source contribution. After working with the codebase and successfully completing that contribution, I became much more confident in my ability to understand the project, navigate its development workflow, and contribute effectively. I believe this issue would be a strong next step for me because it would allow me to continue improving my C++ skills while building on the familiarity I have already gained with the project. More importantly, it gives me the opportunity to make another meaningful contribution to influential software that is actively used and maintained by a broader community.

---

## Understanding the Issue

### Problem Description

`GraphEditor` currently displays inputs with enumerated values as free-form text fields. This requires users to know or manually look up the exact supported values in the MaterialX specification or node definitions. It also allows invalid values to be entered and may trigger unnecessary shader rebuilds each time the text changes.

My plan is to add enum-specific handling in `Graph::showPropertyEditorValue()` that renders an ImGui combo box instead of a text field. The combo box will use the enum labels and values provided by the active NodeDef input, match the current value to the appropriate selection, and write the corresponding value back when the user chooses another option.

Rather than adding enum parsing only within `GraphEditor`, I plan to update the shared enum metadata path in `mx::getUIProperties()` so enum information is also available for non-uniform inputs. The current implementation only extracts this metadata when `input->getIsUniform()` is true, even though several useful standard-library enum inputs are not marked as uniform. Relaxing or removing that restriction should avoid duplicated parsing logic and allow the improvement to benefit both GraphEditor and `MaterialXView`.

The implementation will follow the existing enum-to-combo behavior in `MaterialXView/Editor.cpp` while extending support beyond integer enums to include relevant types such as strings and colors.

### Expected Behavior

When a property has enum and enumvalues metadata, the property editor should display a combo box containing the available enum labels instead of a free-form text field.

The current value should be matched to the correct item in the combo box. When the user selects another item, the corresponding enum value should be written back to the input. Users should only be able to select valid predefined values, and changing the selection should trigger a shader rebuild only when the value actually changes.

This behavior should work for both uniform and non-uniform enum inputs and should remain consistent between `GraphEditor` and `MaterialXView`.

### Current Behavior

Enumerated properties are currently rendered as regular text-entry fields in `GraphEditor`. Users must manually enter the exact enum value, which means they may need to consult the MaterialX specification or node definition to determine which values are valid.

Because the field accepts arbitrary text, users can enter unsupported values. In addition, editing the text can cause repeated shader rebuilds as the value changes, even before the user has finished entering the intended value.

The shared `mx::getUIProperties()` utility currently exposes enum metadata only when an input is marked as uniform. As a result, enum information is unavailable for several non-uniform standard-library inputs that would still benefit from a dropdown interface.

### Affected Components

- `Graph::showPropertyEditorValue()` in `GraphEditor`, where property editor widgets are selected and rendered.
- `mx::getUIProperties()` in `MaterialXRender/Util.cpp`, where enum labels and values are extracted from input metadata.
- `MaterialXView/Editor.cpp`, which contains the existing integer enum combo-box implementation that can be used as a reference.
- Standard-library NodeDef inputs containing enum and enumvalues attributes, including inputs that are not marked with `uniform="true"`.
- Shader rebuild behavior triggered by property-value changes.

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
