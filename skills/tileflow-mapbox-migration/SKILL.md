---
name: tileflow-mapbox-migration
description: Migrate an existing Mapbox GL JS, MapLibre GL JS, or MapTiler SDK JS web map to Tileflow. Use when the user asks to change the provider, style pipeline, or associated services to Tileflow.
license: Apache-2.0
compatibility: Requires Node.js 22 or newer, a package manager, and network access to tileflow.dev, api.tileflow.dev, and the npm registry.
metadata:
  revision: '2026-11-09.1'
---

# Migrate a GL JS map to Tileflow

1. Trace one existing map from its mount to its cleanup. Identify the actual renderer import, style URL, source and layer IDs, markers, popups, listeners, camera, and service clients. Record credential **names** and which hosts receive them. MapLibre can use MapTiler tiles and a third party for routing. Mapbox documents that its Geocoding API responses may only be used with a Mapbox map. If that screen uses them, migrate geocoding with the basemap or keep the screen on Mapbox. Record the existing mount, dependency manifest, and lockfile before editing. Keep one representative view and interaction for comparison.
2. Read the [agent index](https://tileflow.dev/llms.txt) and follow the migration guide for the source provider. If the index predates that guide, use its direct MapLibre, interaction, and service references. Read the [release manifest](https://tileflow.dev/docs-manifest.json) for capability status, implementation mode, evidence, and exact package pins. Compare with the project's lockfile. Install the selected CLI and packages at their individual exact versions with the project's package manager. Include the manifest's Capture package when capturing. Never install `latest` or a version range. Run `npx tileflow` only after installing the CLI locally. Check that `npm ls @tileflow/core` (or the package manager's equivalent) reports a single version. Resolve duplicates before editing the map.
3. Use the **installed** CLI to run `tileflow language manifest --json`, then `tileflow describe <domain> --json` or `tileflow language schema --json` for the fields you need. Read the installed browser and service exports for runtime calls. If a capability is planned, unavailable, or lacks proof for this release, identify which caller needs the old integration. Keep it working until the user chooses an alternative.
4. Translate the style and mount using the [provider-specific decisions](references/gl-js.md). Put the portable design, camera, and a named standalone scene in `tileflow.config.ts`. Wire the application to the compiled map through its existing build stack. Keep attribution and the source data IDs used by the application. Move Mapbox Geocoding in the same change as the basemap. For Directions, Matrix, and other providers, review the applicable terms and ask the user before retaining their results on the new map. Preserve each adapter's input, result, failure, and attribution handling.
5. From the project root run the local `tileflow validate --json`, `tileflow preview --scene <scene>`, and `tileflow capture <scene> --json`. Inspect the PNG and receipt for the expected area, layers, and failed resources. Check popups, layer selection, and listeners after remount in the real app. For an application capture, run its usual server and pass the named application scene and its loopback origin to Capture.
6. Remove an old import or key reference only after checking all its callers. Report changed files, retained provider dependencies, exact pins, and verified behavior. If a render fails, record the diagnostic and the check it prevented. Keep hosted publication, credential changes, and baseline updates outside a local migration.

## Stop and ask

Stop before changing the map if any of these apply.

- The screen depends on a provider service without an available replacement.
- The release manifest does not list the required capability as published for the app's environment.
- The migration requires a MapLibre major upgrade the user did not request.

If `tileflow validate` or `tileflow capture` fails for a reason outside the migration, stop and record the diagnostic. If you already edited the project, undo only your migration changes, including restoring the previous mount, dependency manifest, and lockfile. Run the app's usual compile check. Report what remains unmigrated and ask how to proceed.
