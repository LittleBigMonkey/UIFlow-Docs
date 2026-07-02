---
layout: page
title: Core Concepts
---

# Core Concepts

## NavigationGraph

`NavigationGraph` is a `ScriptableObject` asset that stores nodes, edges, and default transition settings. It is the source of truth for graph-driven navigation.

Create one from:

```text
Create > UIFlow > Navigation Graph
```

## NavigationGraphRuntime

`NavigationGraphRuntime` is a `MonoBehaviour` that runs a `NavigationGraph` in Play Mode.

It:

- Applies graph default transitions to `FlowManager`.
- Finds the page connected to the `Start` node.
- Calls `FlowManager.Initialize`.
- Subscribes to `FlowManager.TriggerRaised`.
- Executes matching graph edges.

## FlowPage, FlowModal, And FlowPersistent

`FlowView` is the shared base class for UIFlow-managed surfaces. Add one of its concrete components to a `UIDocument` GameObject:

- `FlowPage` for normal page navigation.
- `FlowModal` for overlay dialogs.
- `FlowPersistent` for manually shown surfaces such as navigation bars.

Each surface id is based on the assigned UXML asset name:

```csharp
public string Id => UIDocument && UIDocument.visualTreeAsset
    ? UIDocument.visualTreeAsset.name
    : "None";
```

When a view is shown, UIFlow:

- Calls attached `IFlowViewHandler.OnViewShow`.
- Wires named `Button` elements to `FlowManager.Trigger`.
- Makes the root visual element visible.
- Plays the optional show transition.

When hidden, it unwires buttons, calls `OnViewHide`, plays the optional hide transition, and sets display to `None`.

## Auto-Wired Button Triggers

Every named UIToolkit `Button` inside a visible `FlowPage`, `FlowModal`, or `FlowPersistent` is automatically wired.

The trigger format is:

```text
<Id>.<buttonName>
```

Example:

```text
Catalog.item-selected
```

The button's `userData` is forwarded as the trigger payload. This is useful for generated lists and item detail pages.

## IFlowViewHandler

Add `IFlowViewHandler` to a view script when you need lifecycle hooks.

```csharp
using UIFlow;
using UnityEngine;

public sealed class DetailView : MonoBehaviour, IFlowViewHandler
{
    public void OnViewShow(object data)
    {
        var item = data as CatalogItem;
        // Bind labels, buttons, and state here.
    }

    public void OnViewHide()
    {
        // Stop timers, unsubscribe, or clear temporary state here.
    }
}
```

## Custom Triggers

Any code can raise a trigger:

```csharp
FlowManager.Trigger("event:success");
FlowManager.Trigger("event:detail", payload);
```

Use a `CustomTriggerNode` with the same trigger name when the graph should navigate from that event.

You can also subscribe to all triggers:

```csharp
FlowManager.TriggerRaised += (name, data) =>
{
    // Analytics, overlays, debugging, or non-navigation reactions.
};
```

## Navigation History

`FlowManager.ShowPage` pushes the current page onto history. `GoBack` returns to the previous page. `GoHome` clears history and returns to the initialized home page.

Use `skipOnBack` for temporary pages that should not become a back destination. Use `reverseOnBack` to control whether Back replays that page's entrance transition in reverse.

## Persistent Surfaces

A persistent surface is UI with a manual lifecycle that stays outside normal navigation. It is layered on its own sorting band above pages (and below modals), and is never touched by view or modal navigation. Use it for shells such as a navigation bar, a heads-up display, or a toast layer.

`FlowPersistent` is a `MonoBehaviour` for each persistent surface. Like other UIFlow surfaces it requires a `UIDocument`, and its `Id` is the assigned UXML asset name. Each surface registers itself with `FlowManager` on `Awake` and unregisters on `OnDestroy`.

Unlike views, persistent surfaces are not stacked and have no history. They are shown and hidden explicitly:

```csharp
FlowManager.ShowPersistent("NavBar");
FlowManager.HidePersistent("NavBar");
bool visible = FlowManager.IsPersistentShown("NavBar");
```

In the graph, a `PersistentNode` shows a surface and a `HidePersistentNode` hides one. Wiring a `PersistentNode` to the `Start` node brings the surface up at launch; the surface is shown separately and is never treated as the initial view.

A persistent surface auto-wires its named `Button` elements exactly like a `FlowPage`, raising `<Id>.<buttonName>` triggers with the button's `userData` payload. This lets a navigation bar drive page navigation entirely through the graph, with no navigation code in the surface script.

## Modals

A modal is a temporary overlay displayed above a page or persistent surface. Unlike pages, modals do not replace the active page - they layer above it. Modals stack above each other and can report a result when closed.

Use `FlowManager.OpenModal(modalId, data)` to open a modal, or `await FlowManager.OpenModalAsync(modalId, data)` when you need a result inline. When done, `FlowManager.CloseModal(action, resultData)` closes the top modal and reports a result. Pass `modalId` by name when you need to close a specific modal in the stack.

In the graph, a `ModalNode` shows a modal and a `CloseModalNode` closes it. The edge source (button that fired it) becomes the result action, automatically forwarded as the first argument to `CloseModal`.

A modal uses the same `FlowView` and `IFlowViewHandler` lifecycle as a page - buttons are auto-wired the same way. `GoBack` closes the top modal first, and page navigation closes all open modals.

## Transitions

Transitions are optional. Core navigation references the abstract `TransitionAsset` type. The LitMotion-backed implementation is `TransitionProfile`.

Transition resolution for graph navigation is:

1. Edge override.
2. View transition field.
3. Graph default transition.

`ViewTransitionMode` controls whether the leaving hide and entering show run sequentially or in parallel.
