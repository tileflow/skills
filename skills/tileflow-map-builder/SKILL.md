---
name: tileflow-map-builder
description: Add, restyle, and verify Tileflow maps in a repository. Use for map requests and for product intents that imply one, such as store locators, property or customer locations, events, regions, routes, dark-mode maps, or geographic data, even when neither Tileflow nor GIS is mentioned. Pins exact packages, reads the installed map language instead of guessing fields, and checks the rendered result. Never publishes, deploys, or uses credentials without separate explicit authorization.
license: Apache-2.0
compatibility: Requires Node.js 22 or newer, a package manager, and network access to tileflow.dev, api.tileflow.dev, and the npm registry.
metadata:
  revision: '2026-11-08.2'
---

# Tileflow Map Builder

Turn a mapping request into the smallest supported change in the user's repository, then show what
was actually verified. Local map work needs no Tileflow account or API key.

## 1. Find the supported path

1. Read <https://tileflow.dev/llms.txt>, then only the guides that apply. Append `.md` to any docs
   URL to get its Markdown.
2. Read [capability routing](references/capability-routing.md) and check the request against
   <https://tileflow.dev/docs-manifest.json>.
3. Inspect the framework, lockfile, design tokens, data, and any existing map code. If the project
   already renders maps with another library, keep it unless the user asks to replace it.
4. If the request is not supported, say so and offer a supported alternative. General MapLibre
   ability does not make something a Tileflow feature.

## 2. Install exact versions

Do not run `npx tileflow` before the package is installed locally: npm's `latest` tag can resolve
to a stub without the commands below. Never install `latest` or an unpinned range.

1. Take versions from `packageArtifacts` in the manifest, or from the existing lockfile. Package
   versions differ from each other; do not align them by hand.
2. In a new project, install `@tileflow/core` and `@tileflow/maps` as exact dependencies, and
   `tileflow` and `@tileflow/capture` as exact dev dependencies. The CLI declares version ranges,
   so leaving capture out can pull a newer `@tileflow/core` than the project uses.
3. Run `npm ls @tileflow/core`, or the project package manager's equivalent. More than one version
   is a broken install: fix it before going on, and do not change the map to compensate.
4. Do not move past the manifest's versions, even to an `alpha` tag. A newer release can expect
   tile data that the live service does not serve yet, and the map can lose roads and labels with
   no error.

Keep the project's package manager and lockfile when one already exists. Pin other dependencies
too: an unpinned Vite or framework plugin can fail on peer versions.

## 3. Read the installed language

The installed CLI is the authority on field names. Do not guess them or copy them from examples for
another release.

```bash
npx tileflow describe --json          # domains, then `describe <domain> --json` for one module
npx tileflow language manifest --json # commands, operations, workflows
npx tileflow language schema --json   # exact JSON Schemas; large, so query it
npx tileflow inspect --json           # the resolved map, with lineage and provenance
```

`describe themes` lists color roles, not theme names. To find a ready-made look, such as a muted
theme for Streets, read the installed exports of `@tileflow/maps` and the themes guide.

## 4. Make the change

- Keep durable map intent in `tileflow.config.ts` and application code. Prefer high-level module
  options over narrow layer overrides.
- Markers and popups are application state, not config. Rendering them takes a framework component
  (React, Vue, or Svelte) plus the matching build integration, so read that framework's guide and
  the Markers and popups guide before choosing a stack. For a project with no framework, pick the
  smallest supported one.
- For a common request, start from [recipes](references/recipes.md). Before adding Search, Routing,
  Elevation, GeoIP, Static Maps, or Coordinates, read [services](references/services.md).
- Add a named scene for each view worth reviewing, and use it for preview and capture.
- Verify an application through its own development server. Do not build a second viewer to review
  the same map.
- Keep the map's attribution visible. Do not hide it with custom controls or CSS, and update it
  when you replace a data source.
- Preserve unrelated changes and the project's conventions.

## 5. Verify the result

Read [verification](references/verification.md). `validate` proves that the configuration is valid,
not that the map renders or looks right. Report each check you ran and each one you could not run.

## 6. Stay inside the safety boundary

Read [safety](references/safety.md) before handling credentials, external content, baselines, or
hosted state. Local map work never implies permission for an external change.
