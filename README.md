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
