---
layout: page
title: Transitions
---

# Transitions

Transitions are optional. Core navigation works without LitMotion. When LitMotion is installed, the `UIFlow.Transitions` assembly provides `TransitionProfile` assets.

## Enable Transitions

Use the first-run installer prompt or reopen it from:

```text
Tools > UIFlow > Install Dependencies
```

Manual dependency entry:

```json
"com.annulusgames.lit-motion": "https://github.com/AnnulusGames/LitMotion.git?path=src/LitMotion/Assets/LitMotion"
```

## Create A Transition Profile

Create a profile from:

```text
Create > UIFlow > Transition Profile
```

Add animation steps in the inspector. Built-in steps include:

- Opacity.
- Translate percent X.
- Translate percent Y.
- Rotate.
- Scale uniform.

## Profile Play Mode

`TransitionPlayMode` controls how steps inside one profile run:

- `Parallel`: steps play together.
- `Sequential`: steps play one after another.

## View Transition Mode

`ViewTransitionMode` controls how the outgoing view hide and incoming view show are sequenced:

- `Sequential`: hide completes before show starts.
- `Parallel`: hide and show run together.
- `Default`: use the graph or manager default.

## Resolution Order

When graph navigation targets a view, UIFlow resolves transitions in this order:

1. Edge override.
2. View node transition.
3. Graph default transition.

## Built-In Library

UIFlow includes transition profile assets under:

```text
Runtime/Transitions/Library/
```

Library categories include:

- Fades.
- Slides.
- Scales.
- Rotations.
- Composite.
- Creative.
