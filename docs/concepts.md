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
- Finds the view connected to the `Start` node.
- Calls `FlowManager.Initialize`.
- Subscribes to `FlowManager.TriggerRaised`.
- Executes matching graph edges.

## FlowView

`FlowView` is a `MonoBehaviour` for each UIToolkit page. It requires a `UIDocument`.

Its `ViewId` is based on the assigned UXML asset name:

```csharp
public string ViewId => UIDocument && UIDocument.visualTreeAsset
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

Every named UIToolkit `Button` inside a visible `FlowView` is automatically wired.

The trigger format is:

```text
<ViewId>.<buttonName>
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

`FlowManager.ShowView` pushes the current view onto history. `GoBack` returns to the previous view. `GoHome` clears history and returns to the initialized home view.

Use `skipOnBack` for temporary views that should not become a back destination.

## Persistent Surfaces

A persistent surface is UI with a manual lifecycle that stays outside normal navigation. It is layered on its own sorting band above pages (and below modals), and is never touched by view or modal navigation. Use it for shells such as a navigation bar, a heads-up display, or a toast layer.

`FlowPersistent` is a `MonoBehaviour` for each persistent surface. Like `FlowView` it requires a `UIDocument`, and its `PersistentId` is the assigned UXML asset name. Each surface registers itself with `FlowManager` on `Awake` and unregisters on `OnDestroy`.

Unlike views, persistent surfaces are not stacked and have no history. They are shown and hidden explicitly:

```csharp
FlowManager.ShowPersistent("NavBar");
FlowManager.HidePersistent("NavBar");
bool visible = FlowManager.IsPersistentShown("NavBar");
```

In the graph, a `PersistentNode` shows a surface and a `HidePersistentNode` hides one. Wiring a `PersistentNode` to the `Start` node brings the surface up at launch; the surface is shown separately and is never treated as the initial view.

A persistent surface auto-wires its named `Button` elements exactly like a `FlowView`, raising `<PersistentId>.<buttonName>` triggers with the button's `userData` payload. This lets a navigation bar drive page navigation entirely through the graph, with no navigation code in the surface script.

## Modals

A modal is a temporary overlay displayed above a view or persistent surface. Unlike views, modals do not replace the active page — they layer above it. Unlike persistent surfaces, only one modal can be shown at a time and they have a result callback.

Use `FlowManager.ShowModal(modalId, data)` to open a modal. When done, `FlowManager.CloseModal(action, resultData)` closes it and returns a result. The `action` string can carry information about which button was clicked (typically the button name or a custom action value).

In the graph, a `ModalNode` shows a modal and a `CloseModalNode` closes it. The edge source (button that fired it) becomes the result action, automatically forwarded as the first argument to `CloseModal`.

A modal uses the same `FlowView` and `IFlowViewHandler` lifecycle as a page — buttons are auto-wired the same way. View and persistent-surface navigation never affect an open modal; you must explicitly close it before navigating.

## Transitions

Transitions are optional. Core navigation references the abstract `TransitionAsset` type. The LitMotion-backed implementation is `TransitionProfile`.

Transition resolution for graph navigation is:

1. Edge override.
2. View transition field.
3. Graph default transition.

`ViewTransitionMode` controls whether the leaving hide and entering show run sequentially or in parallel.
