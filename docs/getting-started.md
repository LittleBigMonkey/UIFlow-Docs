---
layout: page
title: Getting Started
---

# Getting Started

This guide creates a basic graph-driven UI flow using the public UIFlow workflow.

## Install UIFlow

Install UIFlow from the Unity Package Manager source where you received the package.

UIFlow core has no required third-party dependency. When the package is first imported, UIFlow can offer to install LitMotion for transition support. You can skip this and still use navigation.

## Optional LitMotion Dependency

Transitions are compiled only when LitMotion is present. If you want animated `TransitionProfile` assets, install LitMotion from the UIFlow installer prompt or add it manually to `Packages/manifest.json`:

```json
"com.annulusgames.lit-motion": "https://github.com/AnnulusGames/LitMotion.git?path=src/LitMotion/Assets/LitMotion"
```

Reopen the installer from:

```text
Tools > UIFlow > Install Dependencies
```

## Create A Navigation Graph

1. Create a graph asset from `Create > UIFlow > Navigation Graph`.
2. Double-click the graph asset to open the graph editor.
3. Add one `Start` node.
4. Add one `Page` node for each page.
5. Connect `Start` to the first view.
6. Connect page ports to other pages, `GoBack`, or `GoHome` nodes.

Button ports use UIToolkit button names. A button named `next-button` inside a view named `Home` emits:

```csharp
FlowManager.Trigger("Home.next-button", button.userData);
```

## Set Up The Scene

Create one GameObject for the graph runtime:

1. Add `NavigationGraphRuntime`.
2. Assign your `NavigationGraph` asset.

Create one GameObject per view:

1. Add `UIDocument`.
2. Assign the view UXML asset.
3. Add `FlowPage`.

The page id is the assigned UXML asset name. Keep graph `viewId` values aligned with those UXML names.

## Run

Enter Play Mode. `NavigationGraphRuntime` resolves the `Start` node, initializes the first view, listens to `FlowManager.TriggerRaised`, and routes matching triggers through the graph.

## Common First Graph

```text
[Start] -> [Home] --next-button--> [Settings]
                      |
                      +--about-button--> [About]

[Settings] --back-button--> [GoBack]
[About]    --home-button--> [GoHome]
```
