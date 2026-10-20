---
layout: page
title: Graph View Controls
---

These controls apply to the Navigation Graph editor. Click the graph canvas before using keyboard shortcuts, and leave text fields first.

## Navigate And Select

| Control | Action |
| --- | --- |
| Mouse wheel | Zoom in or out around the pointer. |
| Middle mouse drag, or Alt + left mouse drag | Pan the canvas. |
| Left-click a node or connection | Select it and show its properties in the graph inspector. |
| Ctrl + click (Windows) / Cmd + click (macOS) | Toggle an element in the selection. |
| Left mouse drag on empty canvas | Select elements with a selection rectangle. |
| Left-click empty canvas | Clear the selection and show Graph Defaults. |
| Drag a node header | Move the node, or move the selected nodes together. |

## Keyboard Shortcuts

| Shortcut | Action |
| --- | --- |
| A | Frame the whole graph. |
| F | Frame the current selection using Unity's Frame Selected command. |
| O | Return the view to the graph origin. |
| [ / ] | Select and frame the previous / next node. |
| Delete (Windows) / Cmd + Backspace (macOS) | Delete selected nodes and connections. |

The navigation shortcuts follow Unity's built-in [GraphView controls](https://github.com/Unity-Technologies/UnityCsReference/blob/6000.0/Modules/GraphViewEditor/Views/GraphView.cs). The Start node cannot be deleted. UIFlow does not implement graph copy/paste, duplication, or undo/redo for graph edits. Use the right-click Create menu to add nodes.

## Create And Connect Nodes

- Right-click empty canvas and choose **Create** to add a **Page**, **Modal**, **Persistent**, **Custom Trigger**, or an **Action** node: **Go Back**, **Go Home**, **Close Modal**, or **Hide Persistent**.
- Drag from an output port to an input port on another node to create a connection. Compatible ports must have opposite directions and belong to different nodes.
- Select a connection and delete it to remove the route.
- Start can connect to the initial page and to persistent surfaces shown at launch. Other output ports support one connection each.

## Node Controls

| Control | Action |
| --- | --- |
| UXML | Assign the view asset. Its name becomes the view identifier, and named buttons become output ports. |
| Show / Hide | Assign the view's transition assets. These are also editable in the graph inspector. |
| + add trigger | Add a preset trigger (`next`, `previous`, `up`, `down`, `left`, `right`), or enter a custom name and click **+**. |
| × beside a custom trigger | Remove the trigger port and its connections. |
| Skip On Back (⇤) | Toggle whether Back skips this page in navigation history. |
| Reverse On Back (⮌) | Toggle whether leaving this page via Back reverses its entrance transition. |
| Header foldout arrow | Expand or collapse the node's additional fields. |
| Trigger Name | Set the event name on a Custom Trigger node. |
| Target Id | Set the persistent surface to hide on a Hide Persistent action. |

The two Back options apply to Page nodes and can also be changed in the graph inspector.

## Inspector, Saving, And Theme

- **Graph Defaults**: with nothing selected, edit **Default Show**, **Default Hide**, and **Transition Mode**. Selecting Start also shows these settings.
- **Transition Overrides**: select a connection to edit **Show Override**, **Hide Override**, and **Transition Mode** for that route.
- **Save Graph**: explicitly save the graph asset. Graph edits are also saved automatically, and the editor saves again when it closes.
- **Theme selector**: switch between **Default** and **UIFlow**. The editor remembers your choice.

