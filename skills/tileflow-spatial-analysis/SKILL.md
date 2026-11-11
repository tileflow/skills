---
name: tileflow-spatial-analysis
description: Answer spatial questions about the user's own data and show the result on a Tileflow map. Use for requests such as customers within a distance of a store, which district each order falls in, totals or counts per area, the nearest location, what is reachable in a given time, or density on a regular grid, even when the user does not say "spatial" or "GIS". Runs the analysis in the user's code with Turf or DuckDB, then draws it as a runtime layer or a tileset. Never stores Search results or calls paid services without the user's credential.
license: Apache-2.0
compatibility: Requires Node.js 22 or newer and a package manager. DuckDB recipes need the DuckDB spatial extension, which it downloads on first use. Routing recipes need a Tileflow credential with routing access.
metadata:
  revision: '2026-11-11.1'
---

# Tileflow Spatial Analysis

Compute an answer from the user's geographic data in their own code, check it, and show it on a
map. Tileflow has no hosted analysis service: the analysis runs where the application already runs.

## 1. Name the question before choosing a tool

| The user asks                                 | Operation                         | Needs a service |
| --------------------------------------------- | --------------------------------- | --------------- |
| Within X meters or kilometers of a place      | Buffer, or distance and filter    | No              |
| Within X minutes by car, bike, or foot        | Isochrone, then point in polygon  | Routing         |
| Which area each point belongs to              | Spatial join (point in polygon)   | No              |
| Count, sum, or average per area               | Join, then aggregate              | No              |
| Closest location to each point                | Nearest by straight-line distance | No              |
| Closest by travel time                        | Routing matrix                    | Routing         |
| Density without official areas                | Regular grid (hexagons), then count | No            |
| Totals per municipality, region, or country   | Join rows to Boundaries by code   | No              |

"Near" means straight-line distance; "reachable" means travel time over roads. Do not answer a
travel-time question with a circle, and do not call Routing for a straight-line question. When the
request is ambiguous, ask which one the user means.

If a value already joins to official areas by code, such as sales per municipality code, use
Boundaries instead of polygons of your own: read the Boundary areas guide from
<https://tileflow.dev/llms.txt>.

## 2. Choose where it runs

- **Turf** in the browser or Node: up to roughly tens of thousands of features, or anything that must
  react to the user, such as a moving radius. See [Turf recipes](references/turf.md).
- **DuckDB** with its spatial extension on a server, in CI, or locally: large files, CSV, GeoPackage,
  or Parquet, and results that change only when the data changes. See
  [DuckDB recipes](references/duckdb.md).
- **Interactive selection** of points inside a shape the user draws on the map: follow the Clusters
  and heatmaps guide, which draws the shape and selects the points with Turf in the application.

Pin the exact versions you install, as for any other dependency.

## 3. Avoid the common errors

- **Coordinate order.** GeoJSON and Tileflow use longitude, latitude. Some libraries take latitude
  first: H3's `latLngToCell(lat, lng, res)` does, and DuckDB 2.0 needs
  `SET geometry_always_xy = true` before spheroid distances.
- **Units.** Distances on longitude and latitude in degrees are wrong. Turf takes a `units` option;
  in DuckDB, transform to a metric CRS for the area, or use the sphere or spheroid functions.
- **Boundaries.** A point exactly on an edge is inside for Turf, and outside for DuckDB's
  `ST_Contains`. Use `ST_Covers` in DuckDB when edge points must count, and say which rule you used.
- **Unmatched rows.** After a join, report the points that fell in no area and the rows that matched
  no code. Do not drop them silently.
- **Search results are temporary.** Do not geocode a list with Tileflow Search and store the
  coordinates. Locations must come from the user's own data.
- **Routing costs units.** A matrix or isochrone per row of a large table can be expensive. Read the
  Routing guide's units and limits, and batch requests.

## 4. Show the result on the map

- **Small result, computed in the app:** pass it as a GeoJSON runtime source with a data overlay, as
  in the Clusters and heatmaps guide. Use `heatmapOverlay` for raw density, and `maplibreOverlay`
  with a fill layer for aggregated areas.
- **Large or shared result:** write GeoJSON, CSV, or GeoPackage, then run
  `npx tileflow tileset build <file>` to produce a PMTiles archive the map can read. Keep the build
  settings in a recipe file. Publishing it to a Team is a separate, authorized step.
- **Data that changes on a schedule:** rerun the export and the tileset build in CI. See the Build a
  tileset guide's CI page.

Use the `tileflow-map-builder` skill, when installed, for the map changes and their verification.

## 5. Check the answer

1. Recompute one or two results by hand or with a second method, such as a point you know is inside
   an area, before trusting a whole table.
2. Report counts: inputs, matched, unmatched, and output features.
3. Look at the result on the map, in preview or a capture: a correct table with a wrong coordinate
   order draws in the wrong place.
4. State the method: straight-line or travel time, the distance units, the edge rule, and the data
   date.
