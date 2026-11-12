# Turf recipes

Turf works on GeoJSON in longitude, latitude order. Install `@turf/turf`, or the single
`@turf/<name>` packages you use, at an exact version. These recipes were checked with Turf 7.

```js
import * as turf from '@turf/turf';
// stores, customers: Point FeatureCollections. districts: Polygon FeatureCollection.
```

## Within a distance

```js
const zones = turf.featureCollection(
  stores.features.map((store) => turf.buffer(store, 500, {units: 'meters'})),
);
const nearby = turf.pointsWithinPolygon(customers, zones);
```

For a single distance check, `turf.distance(a, b, {units: 'kilometers'})` is cheaper than a buffer.

## Which area each point is in

```js
const tagged = turf.tag(customers, districts, 'code', 'district');
const unmatched = tagged.features.filter((point) => point.properties.district === undefined);
```

`tag` copies the area's `code` into each point's `district`. Points on an edge count as inside.

## Count or sum per area

```js
const totals = turf.collect(districts, customers, 'spend', 'spends');
for (const area of totals.features) {
  const values = area.properties.spends;
  area.properties.customers = values.length;
  area.properties.spend = values.reduce((sum, value) => sum + value, 0);
}
```

Pass `totals` to a fill overlay and color areas by `spend` or `customers`.

## Nearest location

```js
const nearest = turf.nearestPoint(customer, stores);
// nearest.properties.distanceToPoint is in kilometers.
```

This is straight-line distance. For travel time, use a Routing matrix.

## Reachable in a given time

```js
import {isochrone} from '@tileflow/routing';

const answer = await isochrone(
  {location: [-3.7038, 40.4168], mode: 'walking', minutes: [10, 20]},
  {apiKey},
);
const tenMinutes = turf.featureCollection(
  answer.isochrones.features.filter((area) => area.properties.minutes === 10),
);
const reachable = turf.pointsWithinPolygon(customers, tenMinutes);
```

Run this where the credential belongs: a publishable credential restricted to the site in the
browser, or a Team credential on the server. Show `answer.attribution` with the areas.

## Density on a grid

```js
const grid = turf.hexGrid(turf.bbox(customers), 1, {units: 'kilometers'});
const density = turf.collect(grid, customers, 'spend', 'spends');
density.features = density.features.filter((cell) => cell.properties.spends.length > 0);
```

For a grid shared across datasets or zoom levels, use H3 cells instead: `h3-js` takes latitude
first in `latLngToCell(lat, lng, resolution)`, and `cellToBoundary(cell, true)` returns GeoJSON
order.
