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

#### Trigger

```csharp
public static void Trigger(string triggerName, object data = null);
```

Raises a named trigger. Graph navigation and custom listeners can react through `TriggerRaised`.

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
