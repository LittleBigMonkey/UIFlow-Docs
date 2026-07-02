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
- The `Start` node connects to a `PageNode`.
- The target `PageNode.viewId` matches a `FlowPage.Id`.
- The page GameObject has both `UIDocument` and `FlowPage`.

## A Button Does Not Navigate

Check:

- The button has a non-empty `name` in UXML.
- The view is active when clicked.
- The graph edge starts from the matching view port.
- The trigger name follows `<Id>.<buttonName>`.

Example:

```text
Catalog.item-selected
```

## A Payload Is Null

Check:

- For buttons, set `button.userData` before the click.
- For manual navigation, pass data to `FlowManager.ShowPage` or `FlowManager.Trigger`.
- In `OnViewShow`, cast to the expected type carefully.

## Back Navigation Skips A View

The page may have `skipOnBack` enabled on its `PageNode`, or navigation may have passed `skipOnBack: true` to `FlowManager.ShowPage`.

## Transitions Are Missing

Transitions require LitMotion. Without LitMotion, the core package still works and views show or hide instantly.

Check:

- LitMotion is installed in `Packages/manifest.json`.
- Unity has resolved packages after the install.
- The transition assembly is compiling.

## Modal Pages Replace The Current Page

That is expected for normal UIFlow page navigation. For overlays, add a `FlowModal` to a separate `UIDocument`, open it with a `ModalNode` or `FlowManager.OpenModal`, and close it with a `CloseModalNode` or `FlowManager.CloseModal`.

## Duplicate View Id Warning

Each `FlowPage.Id`, `FlowModal.Id`, or `FlowPersistent.Id` must be unique within its surface type. Because `Id` comes from the assigned UXML asset name, duplicate UXML names can create duplicate ids.

## Support Request Checklist

When asking for help, include:

- Unity version.
- UIFlow version.
- Whether LitMotion is installed.
- The view id and button name involved.
- Console error text.
- A screenshot of the graph, if the issue is navigation-related.
