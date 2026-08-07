---
layout: home
title: UIFlow
---

<section id="hero">
  <figure class="media-frame cover">
    <img src="{{ '/assets/media/cover.png' | relative_url }}" alt="UIFlow, graph navigation for Unity UI Toolkit" fetchpriority="high">
  </figure>
</section>

UIFlow is a UIToolkit-based page management and graph-driven navigation package for Unity 6. It lets you describe UI navigation in a `NavigationGraph` asset, connect UXML button names to graph edges, and keep page logic in small view scripts.

<section id="promo-video">
  <h2>See UIFlow In Action</h2>
  <video class="promo" controls playsinline preload="metadata" poster="{{ '/assets/media/cover.png' | relative_url }}">
    <source src="{{ '/assets/media/uiflow-promo.mp4' | relative_url }}" type="video/mp4">
    Your browser does not support embedded videos. <a href="{{ '/assets/media/uiflow-promo.mp4' | relative_url }}">Download the UIFlow presentation video</a>.
  </video>
</section>

## What UIFlow Provides

- Visual navigation graph assets for page flow.
- Runtime page switching through `FlowManager`.
- Automatic UIToolkit button triggers based on button names.
- Optional view lifecycle hooks through `IFlowViewHandler`.
- Navigation history with back and home actions.
- Optional LitMotion-powered transitions through `TransitionProfile` assets.
- Importable samples for common UI patterns.

<section id="feature-showcase">
  <h2>Explore UIFlow</h2>
  <div class="gallery">
    <a class="media-frame" href="{{ '/concepts/' | relative_url }}">
      <img src="{{ '/assets/media/screen-1.png' | relative_url }}" alt="Design UI navigation visually with the UIFlow graph" loading="lazy">
    </a>
    <a class="media-frame" href="{{ '/getting-started/' | relative_url }}">
      <img src="{{ '/assets/media/screen-2.png' | relative_url }}" alt="Build interfaces with Unity UI Builder and connect them through UIFlow" loading="lazy">
    </a>
    <a class="media-frame" href="{{ '/transitions/' | relative_url }}">
      <img src="{{ '/assets/media/screen-3.png' | relative_url }}" alt="Create reusable transition profiles for UIFlow pages and modals" loading="lazy">
    </a>
    <a class="media-frame" href="{{ '/concepts/' | relative_url }}">
      <img src="{{ '/assets/media/screen-4.png' | relative_url }}" alt="Combine pages, modals, and persistent UI in complete flows" loading="lazy">
    </a>
    <a class="media-frame" href="{{ '/transitions/' | relative_url }}">
      <img src="{{ '/assets/media/screen-5.png' | relative_url }}" alt="Run a responsive interface powered by a UIFlow graph" loading="lazy">
    </a>
    <a class="media-frame" href="{{ '/samples/' | relative_url }}">
      <img src="{{ '/assets/media/screen-6.png' | relative_url }}" alt="Learn UIFlow through practical Unity UI Toolkit samples" loading="lazy">
    </a>
    <a class="media-frame" href="{{ '/api-reference/' | relative_url }}">
      <img src="{{ '/assets/media/screen-7.png' | relative_url }}" alt="Control pages, modals, triggers, and data with the UIFlow API" loading="lazy">
    </a>
  </div>
</section>

## Requirements

- Unity `6000.0` or newer.
- UI Toolkit.
- LitMotion is optional and only required for transition profiles.

## Start Here

- [Getting Started](getting-started.md)
- [Core Concepts](concepts.md)
- [API Reference](api-reference.md)
- [Samples](samples.md)
- [Troubleshooting](troubleshooting.md)
- [Support And Community On Discord](https://discord.gg/dGN9EutdUm)

## Source Code Policy

This site documents the public API and package workflows. It intentionally does not publish UIFlow source code or private implementation details.
