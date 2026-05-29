---
layout: page
title: Samples
---

# Samples

Import samples from Unity Package Manager:

1. Select `UIFlow`.
2. Expand `Samples`.
3. Click `Import` next to the sample you want.
4. Open the imported scene and press Play.

## 01 Basic Navigation

Linear three-page navigation driven entirely by a `NavigationGraph`.

Shows:

- Graph as the source of truth.
- Auto-wired UIToolkit buttons.
- `GoBack` and `GoHome` actions.

## 02 Navigation Payload

Catalog-to-detail flow where a clicked item is passed as a typed payload.

Shows:

- `button.userData` payload forwarding.
- `IFlowViewHandler.OnViewShow(object data)`.
- Runtime-built list rows.

## 03 Persistent Data

Multi-step wizard that shares data across pages with a plain static store.

Shows:

- Persistent state outside the navigation stack.
- Form-like page progression.
- Summary page binding.

## 04 Custom Triggers

Programmatic navigation with named triggers.

Shows:

- `FlowManager.Trigger("event:name")`.
- `CustomTriggerNode`.
- Navigation decoupled from the caller.

## 05 Transitions

Reusable transition profiles assigned at graph, view, and edge level.

Requires LitMotion.

Shows:

- Built-in transition library.
- `TransitionProfile` assets.
- Sequential and parallel view transition modes.
- Per-edge transition overrides.

## 06 Persistent NavBar

Shell layout with persistent top or bottom navigation while UIFlow swaps content pages.

Shows:

- Multiple `UIDocument` layers.
- Shared `PanelSettings`.
- UI outside the page navigation lifecycle.

## 07 Modal Dialogs

Confirm, alert, and bottom-sheet overlays layered above a UIFlow page.

Shows:

- Modal layer outside normal page replacement.
- Trigger-based overlay opening.
- Result callbacks from modal controller to base page.

Note: modal dialogs are demonstrated as an overlay pattern. UIFlow page navigation itself replaces the active page rather than stacking modal pages.
