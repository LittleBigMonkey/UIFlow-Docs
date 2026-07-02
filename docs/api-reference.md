---
layout: page
title: API Reference
---

# API Reference

This reference covers the public API intended for users of UIFlow. It does not include private implementation details.

## Namespace UIFlow

### FlowManager

Static navigation API.

#### CurrentPageId

```csharp
public static string CurrentPageId { get; }
```

The currently displayed page id, or `null` if no page is active.

#### ModalDepth

```csharp
public static int ModalDepth { get; }
```

Number of modals currently stacked above the active page.

#### IsModalOpen

```csharp
public static bool IsModalOpen { get; }
```

True while at least one modal overlay is open.

#### TopModalId

```csharp
public static string TopModalId { get; }
```

Id of the top-most open modal, or `null` when none is open.

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

#### ModalStackChanged

```csharp
public static event Action<int> ModalStackChanged;
```

Raised whenever modal stack depth changes. The argument is the new modal count.

#### ModalClosed

```csharp
public static event Action<ModalResult> ModalClosed;
```

Raised when a modal closes.

#### Initialize

```csharp
public static void Initialize(string homePageId, object data = null);
```

Sets the home page and shows it as the initial screen. Usually called by `NavigationGraphRuntime`.

#### ShowPage

```csharp
public static void ShowPage(
    string pageId,
    object data = null,
    TransitionAsset hideOverride = null,
    TransitionAsset showOverride = null,
    ViewTransitionMode modeOverride = ViewTransitionMode.Default,
    bool skipOnBack = false,
    bool reverseOnBack = true);
```

Navigates forward to a registered page. The current page is pushed onto history. `data` is forwarded to the target page's `IFlowViewHandler.OnViewShow`.

#### GoBack

```csharp
public static void GoBack();
```

Navigates to the previous page in history. Frames marked `skipOnBack` are skipped. If a modal is open, Back closes the top modal before touching navigation history.

#### GoHome

```csharp
public static void GoHome();
```

Clears history and returns to the home page set by `Initialize`.

#### OpenModal

```csharp
public static void OpenModal(
    string modalId,
    object data = null,
    TransitionAsset showOverride = null,
    TransitionAsset hideOverride = null);
```

Opens a registered modal overlay above the active page. Modals stack above each other until closed. `data` is forwarded to the modal's `IFlowViewHandler.OnViewShow`.

#### OpenModalAsync

```csharp
public static Awaitable<ModalResult> OpenModalAsync(
    string modalId,
    object data = null,
    TransitionAsset showOverride = null,
    TransitionAsset hideOverride = null);
```

Opens a modal and completes with a `ModalResult` when that modal closes.

#### CloseModal

```csharp
public static void CloseModal(
    string action = null,
    object resultData = null,
    string modalId = null);
```

Closes the top modal by default. When `modalId` is supplied, closes the top-most matching modal. `action` and `resultData` are reported through `ModalClosed` and any awaiting `OpenModalAsync` call.

#### CloseAllModals

```csharp
public static void CloseAllModals();
```

Closes every open modal, top-down, each as a plain dismissal.

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

### FlowPage

`FlowPage` is a `MonoBehaviour` placed on each page GameObject. It requires `UIDocument`.

```csharp
[RequireComponent(typeof(UIDocument))]
public class FlowPage : FlowView
```

#### Id

```csharp
public string Id { get; }
```

Returns the assigned UXML asset name. Use this value in graph `PageNode.viewId`.

### FlowModal

`FlowModal` is a `MonoBehaviour` for overlay dialogs. It requires `UIDocument`.

```csharp
[RequireComponent(typeof(UIDocument))]
public class FlowModal : FlowView
```

Important fields:

```csharp
public const string BackdropElementName = "backdrop";
public bool dismissOnBackdropClick;
```

Its `Id` is the assigned UXML asset name. Use this value in graph `ModalNode.viewId` and as the `modalId` for `FlowManager.OpenModal`.

### IFlowViewHandler

Optional lifecycle interface for scripts attached to a view GameObject.

```csharp
public interface IFlowViewHandler
{
    void OnViewShow(object data) { }
    void OnViewHide() { }
}
```

`OnViewShow` receives the payload passed through `ShowPage`, `OpenModal`, graph triggers, or button `userData`.

### FlowPersistent

`FlowPersistent` is a `MonoBehaviour` for a surface with a manual lifecycle, layered above pages on its own sorting band and never touched by view or modal navigation. It requires `UIDocument`.

```csharp
[RequireComponent(typeof(UIDocument))]
public class FlowPersistent : FlowView
```

#### Id

```csharp
public string Id { get; }
```

Returns the assigned UXML asset name. Use this value in graph `PersistentNode.viewId` and as the `id` for `FlowManager.ShowPersistent` / `HidePersistent`.

The surface registers itself with `FlowManager` on `Awake` and unregisters on `OnDestroy`. Its named `Button` elements raise `<Id>.<buttonName>` triggers, matching the `FlowPage` convention.

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

### PageNode

Represents one `FlowPage` in the graph.

```csharp
public sealed class PageNode : ViewNodeBase
{
    public string viewId;
    public VisualTreeAsset viewUxml;
    public List<string> extraTriggers;
    public TransitionAsset showTransition;
    public TransitionAsset hideTransition;
    public bool skipOnBack;
    public bool reverseOnBack;
}
```

### PersistentNode

Shows a persistent surface from the graph. Wired to the `Start` node, it brings the surface up at launch.

```csharp
public sealed class PersistentNode : ViewNodeBase { }
```

`viewId` is the `Id` of the surface to show; `showTransition` plays as it appears.

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
