# Recipes

Starting points for common requests. Field names come from the installed CLI and the linked guide,
not from here.

## Store locator, listings, events

- Keep locations in the application's data and render them as marker annotations, using the Markers
  and popups guide. Give each one a stable ID and an accessible label.
- Use a tooltip for a short description and a popup for anything with links or buttons.
- List and map share one selection state. With controlled state, commit each requested update in the
  framework callback, or the popup will not change.
- Add Search for "near me" entry, and read its [temporary-results rule](services.md).
- Sort by straight-line distance in the application. Use a Routing matrix only when the user needs
  travel time.

## The user's own data

- GeoJSON belongs in a runtime source drawn by a custom layer.
- Do not use `vectorTiles` for it: that replaces the map's primary geographic data. A PMTiles dataset
  goes in `sources`, which keeps the primary data, and an overlay draws it.

## Regions colored by a value

- Follow the Boundary areas guide. The polygons join to rows by code, so keep codes as strings to
  preserve leading zeros, and select the class and country before joining.
- After a join, inspect the unmatched, ambiguous, and duplicate rows and tell the user about them.
- All polygons missing: check the map declares boundary areas and a release covers the region. Some
  missing: compare the exact key, since `28079` and `ES:INE:28079` are different.

## Draw a route

- Pass the returned line to the application's GeoJSON layer, and use the optional `route.*` theme
  tokens so it follows light and dark themes.
- Show the returned attribution with the route.

## Dark mode and themes

- Extend an existing theme and override only the tokens that change.
- Declaring `themes` replaces the inherited set, so include every theme to keep, each with the same
  token keys. Use `systemThemes` to follow the browser preference.
- Scenes and builds use a concrete theme name. Add a scene per theme to review each one.
- Reference tokens in module styles. A literal value fails the theme audit unless wrapped in
  `fixed(value, {reason})`.

## Empty or broken map

Run `npx tileflow validate` first, then check in this order: container height, failed network
requests, a camera that covers the data, and the browser worker setup. Do not rewrite the map until
one of those explains the failure. Use the Troubleshooting guide for other symptoms.
