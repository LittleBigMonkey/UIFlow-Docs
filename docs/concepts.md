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

## Transitions

Transitions are optional. Core navigation references the abstract `TransitionAsset` type. The LitMotion-backed implementation is `TransitionProfile`.

Transition resolution for graph navigation is:

1. Edge override.
2. View transition field.
3. Graph default transition.

`ViewTransitionMode` controls whether the leaving hide and entering show run sequentially or in parallel.
