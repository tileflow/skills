---
name: tileflow-google-maps-migration
description: Migrate an existing Google Maps JavaScript API web app to Tileflow. Use for replacing its map, markers, info windows, camera, geocoding, routes, matrices, or static images with released Tileflow capabilities.
license: Apache-2.0
compatibility: Requires Node.js 22 or newer, a package manager, and network access to tileflow.dev, api.tileflow.dev, and the npm registry.
metadata:
  revision: '2026-11-09.1'
---

# Migrate Google Maps JavaScript to Tileflow

1. Follow the app's map route. Identify the loader, imported Google libraries, map IDs, overlays, events, service clients, and where their results are stored. Record credential names and call sites without reading or printing key values. Choose a known location and a user action such as opening a store's info window. Record the existing mount, dependency manifest, and lockfile before editing.
2. Read the [agent index](https://tileflow.dev/llms.txt), the Google Maps migration guide it lists, and the [release manifest](https://tileflow.dev/docs-manifest.json). If the index predates that guide, follow its framework, interaction, and service references. Match each requested behavior to its status, environment, evidence, and implementation mode. Review the terms that apply to the user's agreement before changing the screen. [Google Maps Platform Terms, section 3.2.3(e)](https://cloud.google.com/maps-platform/terms)
3. Compare the released package pins with the app's lockfile. Install the map packages, local CLI, capture tool, and adapters at their individual exact versions. Never install `latest` or a version range. Run `npx tileflow` only after installing the CLI locally. Check that `npm ls @tileflow/core` (or the package manager's equivalent) reports a single version. Resolve duplicates before editing the map. Use the installed `tileflow language manifest --json`, `tileflow describe <domain> --json`, and `tileflow language schema --json` to discover the actual fields. Read the installed framework and service exports before wiring calls.
4. Convert Google objects and service responses at the app boundary using the [translation notes](references/google-maps.md). Add a named map and standalone scene to `tileflow.config.ts`. Connect its compiled assets to the existing app's map route. Keep application markers, selection, and popup rendering in the application. Migrate the screen only when every Google service it uses has a released, available Tileflow replacement. Otherwise leave the whole screen on its Google map, explain which dependency blocks it, and ask how to proceed.
5. Run the installed `tileflow validate --json`, `tileflow preview --scene <scene>`, and `tileflow capture <scene> --json`. Inspect both the PNG and receipt. In the real app, find the known location, open the marker by keyboard, close the popup, and revisit the route. Check service response mapping with the app's fixtures. For an application capture use its normal server and the named application scene's loopback origin.
6. Remove the Google loader or dependency only when its last caller has migrated. Report files, versions, visual evidence, behaviors still supplied by Google, and any check that could not run. Local migration does not authorize changing Cloud credentials, deploying, or approving a new baseline.

## Stop and ask

Stop before changing the map if any of these apply.

- The screen depends on a Google service without an available replacement.
- The release manifest does not list the required capability as published for the app's environment.
- The migration requires a MapLibre major upgrade the user did not request.

If `tileflow validate` or `tileflow capture` fails for a reason outside the migration, stop and record the diagnostic. If you already edited the project, undo only your migration changes, including restoring the previous mount, dependency manifest, and lockfile. Run the app's usual compile check. Report what remains unmigrated and ask how to proceed.
