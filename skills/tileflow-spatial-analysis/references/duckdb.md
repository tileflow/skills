# DuckDB recipes

Use DuckDB for files too large for the browser, and for results you rebuild when the data changes.
These recipes were checked with DuckDB 2.0 and its spatial extension, through the DuckDB CLI or
`@duckdb/node-api`.

```sql
INSTALL spatial;
LOAD spatial;
SET geometry_always_xy = true; -- longitude, latitude, as in GeoJSON

CREATE TABLE customers AS SELECT * FROM ST_Read('customers.geojson');
CREATE TABLE districts AS SELECT * FROM ST_Read('districts.geojson');
CREATE TABLE stores AS SELECT * FROM ST_Read('stores.geojson');
```

`ST_Read` also reads GeoPackage, Shapefile, and other GDAL formats. For a CSV with coordinate
columns, build the point yourself: `ST_Point(lon, lat)`.

## Count or sum per area

```sql
SELECT d.code, count(c.id) AS customers, coalesce(sum(c.spend), 0) AS spend
FROM districts d
LEFT JOIN customers c ON ST_Covers(d.geom, c.geom)
GROUP BY d.code;
```

`ST_Covers` counts points on an edge; `ST_Contains` does not. Find unmatched points with an anti-join
on the same condition.

## Within a distance

Transform to a metric CRS that covers the area, then compare in meters. This example uses
ETRS89 / UTM zone 30N for Spain; choose the zone or national CRS for the user's region.

```sql
SELECT DISTINCT c.id
FROM customers c, stores s
WHERE ST_DWithin(
  ST_Transform(c.geom, 'EPSG:4326', 'EPSG:25830', always_xy := true),
  ST_Transform(s.geom, 'EPSG:4326', 'EPSG:25830', always_xy := true),
  500
);
```

## Nearest location

```sql
SELECT c.id, arg_min(s.id, ST_Distance_Spheroid(c.geom::GEOMETRY, s.geom::GEOMETRY)) AS store
FROM customers c, stores s
GROUP BY c.id;
```

`ST_Distance_Spheroid` returns meters and needs `geometry_always_xy`. The cast drops the CRS type
that `ST_Read` attaches, which the function does not accept. This compares every pair, so prefilter
with `ST_DWithin` for large tables.

## Density on a grid

The community `h3` extension adds H3 cells: `INSTALL h3 FROM community; LOAD h3;`. Its
`h3_latlng_to_cell(lat, lng, resolution)` takes latitude first.

## Write the result for the map

```sql
COPY (
  SELECT d.code, count(c.id) AS customers, d.geom
  FROM districts d LEFT JOIN customers c ON ST_Covers(d.geom, c.geom)
  GROUP BY d.code, d.geom
) TO 'by-district.geojson' WITH (FORMAT GDAL, DRIVER 'GeoJSON');
```

Then run `npx tileflow tileset build by-district.geojson`. For a large output, write
`GeoJSONSeq` or GeoPackage instead, which the tileset build also reads.
