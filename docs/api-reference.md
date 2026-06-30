---
layout: page
title: API Reference
---

# API Reference

This reference covers the public API intended for users of UIFlow. It does not include private implementation details.

## Namespace UIFlow

### FlowManager

Static navigation API.

#### CurrentViewId

```csharp
public static string CurrentViewId { get; }
```

The currently displayed view id, or `null` if no view is active.

#### HistoryChanged

```csharp
public static event Action<int> HistoryChanged;
```

Raised whenever navigation history depth changes. The argument is the new history count.

#### TriggerRaised

```csharp
public static event Action<string, object> TriggerRaised;
```

Raised whenever `Trigger` is called. Arguments are trigger name and optional payload.

#### Initialize

```csharp
public static void Initialize(string homeViewId, object data = null);
```

Sets the home view and shows it as the initial screen. Usually called by `NavigationGraphRuntime`.

#### ShowView

```csharp
public static void ShowView(
    string viewId,
    object data = null,
    TransitionAsset hideOverride = null,
    TransitionAsset showOverride = null,
    ViewTransitionMode modeOverride = ViewTransitionMode.Default,
    bool skipOnBack = false);
```

Navigates forward to a registered view. The current view is pushed onto history. `data` is forwarded to the target view's `IFlowViewHandler.OnViewShow`.

#### GoBack

```csharp
public static void GoBack();
```

Navigates to the previous view in history. Frames marked `skipOnBack` are skipped.

#### GoHome

```csharp
public static void GoHome();
```

Clears history and returns to the home view set by `Initialize`.

#### ShowModal

```csharp
public static void ShowModal(
    string modalId,
    object data = null,
    TransitionAsset show = null);
```

Shows a registered modal overlay on its own layer above the active page. The modal stays visible until `CloseModal` is called. `data` is forwarded to the modal's `IFlowViewHandler.OnViewShow`.

#### CloseModal

```csharp
public static void CloseModal(
    string action = null,
    object resultData = null,
    TransitionAsset hide = null);
```

Hides the active modal. `action` and `resultData` are forwarded as a result callback to code that opened the modal.

#### Trigger

```csharp
public static void Trigger(string triggerName, object data = null);
```

Raises a named trigger. Graph navigation and custom listeners can react through `TriggerRaised`.

#### ShowPersistent

```csharp
public static void ShowPersistent(string id, TransitionAsset show = null);
```

Shows a registered persistent surface on its own band above the active page. It stays visible until `HidePersistent` is called; view and modal navigation never affect it. No-op when already shown. `show` plays as it appears, or shows it instantly when null.

#### HidePersistent

```csharp
public static void HidePersistent(string id, TransitionAsset hide = null);
```

Hides a registered persistent surface. No-op when it is not currently shown. `hide` plays as it disappears, or hides it instantly when null.

#### IsPersistentShown

```csharp
public static bool IsPersistentShown(string id);
```

True while the named persistent surface is shown.

### FlowView

`FlowView` is a `MonoBehaviour` placed on each page GameObject. It requires `UIDocument`.

```csharp
[RequireComponent(typeof(UIDocument))]
public class FlowView : MonoBehaviour
```

#### ViewId

```csharp
public string ViewId { get; }
```

Returns the assigned UXML asset name. Use this value in graph `ViewNode.viewId`.

### IFlowViewHandler

Optional lifecycle interface for scripts attached to a view GameObject.

```csharp
public interface IFlowViewHandler
{
    void OnViewShow(object data) { }
    void OnViewHide() { }
}
```

`OnViewShow` receives the payload passed through `ShowView`, graph triggers, or button `userData`.

### FlowPersistent

`FlowPersistent` is a `MonoBehaviour` for a surface with a manual lifecycle, layered above pages on its own sorting band and never touched by view or modal navigation. It requires `UIDocument`.

```csharp
[RequireComponent(typeof(UIDocument))]
public class FlowPersistent : MonoBehaviour
```

#### PersistentId

```csharp
public string PersistentId { get; }
```

Returns the assigned UXML asset name. Use this value in graph `PersistentNode.viewId` and as the `id` for `FlowManager.ShowPersistent` / `HidePersistent`.

The surface registers itself with `FlowManager` on `Awake` and unregisters on `OnDestroy`. Its named `Button` elements raise `<PersistentId>.<buttonName>` triggers, matching the `FlowView` convention.

## Namespace UIFlow.Graph

### NavigationGraph

Graph asset storing nodes, edges, and default transition settings.

```csharp
[CreateAssetMenu(fileName = "NavigationGraph", menuName = "UIFlow/Navigation Graph")]
public sealed class NavigationGraph : ScriptableObject
```

Important fields:

```csharp
public TransitionAsset defaultShowTransition;
public TransitionAsset defaultHideTransition;
public ViewTransitionMode defaultTransitionMode;
```

### NavigationGraphRuntime

Runtime driver for a `NavigationGraph`.

```csharp
public sealed class NavigationGraphRuntime : MonoBehaviour
{
    public NavigationGraph graph;
    public void GoBack();
    public void GoHome();
}
```

### ViewTransitionMode

```csharp
public enum ViewTransitionMode
{
    Default,
    Sequential,
    Parallel
}
```

Controls sequencing between hide and show transitions.

### NavigationEdge

Serializable edge between two graph nodes.

```csharp
public sealed class NavigationEdge
{
    public string fromNodeGuid;
    public string fromPort;
    public string toNodeGuid;
    public TransitionAsset hideTransitionOverride;
    public TransitionAsset showTransitionOverride;
    public ViewTransitionMode modeOverride;
}
```

### ViewNode

Represents one `FlowView` in the graph.

```csharp
public sealed class ViewNode : NavigationNode
{
    public string viewId;
    public VisualTreeAsset viewUxml;
    public List<string> extraTriggers;
    public TransitionAsset showTransition;
    public TransitionAsset hideTransition;
    public bool skipOnBack;
}
```

### PersistentNode

Shows a persistent surface from the graph. Wired to the `Start` node, it brings the surface up at launch.

```csharp
public sealed class PersistentNode : ViewNodeBase { }
```

`viewId` is the `PersistentId` of the surface to show; `showTransition` plays as it appears.

### HidePersistentNode

Action node that hides a named persistent surface. The target is identified explicitly because persistent surfaces are not stacked.

```csharp
public sealed class HidePersistentNode : NavigationNode
{
    public string targetId;
}
```

### ModalNode

Shows a modal overlay above the current page.

```csharp
public sealed class ModalNode : ViewNodeBase { }
```

`viewId` is the registered modal id; `showTransition` plays as it appears. The modal receives incoming trigger data via `IFlowViewHandler.OnViewShow`.

### CloseModalNode

Action node that closes the active modal.

```csharp
public sealed class CloseModalNode : NavigationNode { }
```

A button click (edge source) becomes the result action, passed back to the code that opened the modal.

### CustomTriggerNode

Entry point for a named trigger.

```csharp
public sealed class CustomTriggerNode : NavigationNode
{
    public string triggerName;
}
```

### StartNode, GoBackNode, GoHomeNode

Graph nodes for initial entry, back navigation, and home navigation.

```csharp
public sealed class StartNode : NavigationNode { }
public sealed class GoBackNode : NavigationNode { }
public sealed class GoHomeNode : NavigationNode { }
```

## Namespace UIFlow.Transitions

### TransitionAsset

Base type for transition assets.

```csharp
public abstract class TransitionAsset : ScriptableObject
{
    public abstract Awaitable PlayAsync(
        VisualElement root,
        TransitionDirection direction = TransitionDirection.Forward);
}
```

### TransitionDirection

```csharp
public enum TransitionDirection
{
    Forward,
    Backward
}
```

### TransitionProfile

LitMotion-backed transition asset. Available only when LitMotion is installed and the transition assembly is compiled.

```csharp
[CreateAssetMenu(fileName = "TransitionProfile", menuName = "UIFlow/Transition Profile")]
public sealed class TransitionProfile : TransitionAsset
{
    public TransitionPlayMode playMode;
    public AnimationStep[] components;
}
```

### TransitionPlayMode

```csharp
public enum TransitionPlayMode
{
    Parallel,
    Sequential
}
```

Controls how animation steps inside one profile are combined.

### AnimationStep

Base type for LitMotion animation components inside a `TransitionProfile`.

```csharp
public abstract class AnimationStep
{
    public bool enabled;
    public float duration;
    public Ease ease;
    public float delay;
    public float TotalDuration { get; }
}
```

Built-in animation steps include opacity, translate X percent, translate Y percent, rotate, and uniform scale.
