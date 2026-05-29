---
layout: page
title: Troubleshooting
---

# Troubleshooting

## The First View Does Not Show

Check:

- A `NavigationGraphRuntime` exists in the scene.
- The `graph` field is assigned.
- The graph has a `Start` node.
- The `Start` node connects to a `ViewNode`.
- The target `ViewNode.viewId` matches a `FlowView.ViewId`.
- The view GameObject has both `UIDocument` and `FlowView`.

## A Button Does Not Navigate

Check:

- The button has a non-empty `name` in UXML.
- The view is active when clicked.
- The graph edge starts from the matching view port.
- The trigger name follows `<ViewId>.<buttonName>`.

Example:

```text
Catalog.item-selected
```

## A Payload Is Null

Check:

- For buttons, set `button.userData` before the click.
- For manual navigation, pass data to `FlowManager.ShowView` or `FlowManager.Trigger`.
- In `OnViewShow`, cast to the expected type carefully.

## Back Navigation Skips A View

The view may have `skipOnBack` enabled on its `ViewNode`, or navigation may have passed `skipOnBack: true` to `FlowManager.ShowView`.

## Transitions Are Missing

Transitions require LitMotion. Without LitMotion, the core package still works and views show or hide instantly.

Check:

- LitMotion is installed in `Packages/manifest.json`.
- Unity has resolved packages after the install.
- The transition assembly is compiling.

## Modal Pages Replace The Current Page

That is expected for normal UIFlow navigation. For modals, use a separate overlay `UIDocument` layered above the page and communicate with `FlowManager.TriggerRaised`, as shown in the modal dialogs sample.

## Duplicate View Id Warning

Each `FlowView.ViewId` must be unique. Because `ViewId` comes from the assigned UXML asset name, duplicate UXML names can create duplicate ids.

## Support Request Checklist

When asking for help, include:

- Unity version.
- UIFlow version.
- Whether LitMotion is installed.
- The view id and button name involved.
- Console error text.
- A screenshot of the graph, if the issue is navigation-related.
