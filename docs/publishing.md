---
layout: page
title: Publishing
---

# Publishing

UIFlow can have public documentation without publishing the package source code.

## Recommended Option: Private Package Repo With Public Pages

Keep the UIFlow repository private and publish only the generated GitHub Pages site.

1. Push this repository to GitHub.
2. Open `Settings > Pages`.
3. Set `Build and deployment` source to `GitHub Actions`.
4. Run the `Deploy documentation` workflow or push to `main`.

The workflow builds only the `docs/` folder and deploys the generated site.

## Alternative: Public Docs-Only Repo

If you prefer a fully public repository, create a separate repo such as:

```text
UIFlow-Docs
```

Copy only these files into it:

```text
docs/
.github/workflows/pages.yml
```

Do not copy:

```text
Runtime/
Editor/
Samples/
package.json
```

This keeps source code and package internals private while still giving users a public documentation website.

## After Publishing

Add the Pages URL to:

- Unity Asset Store description, if allowed.
- Package README.
- Discord `updates` or `support` channel.
- Release announcements.
