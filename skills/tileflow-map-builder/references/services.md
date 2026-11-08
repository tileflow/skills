# Hosted services

Use this to choose a service and its access. Check availability in `docs-manifest.json` first, then
read the service's guide for its package, methods, and limits. Most calls consume API units, so
read Usage and limits before designing anything that calls a service often.

## Choose a service

| The user needs                                       | Use                                  |
| ---------------------------------------------------- | ------------------------------------ |
| An address box, a place from coordinates, nearby POIs | Search                               |
| A line between stops, with distance and duration     | Routing, directions                  |
| The area reachable in a given time                   | Routing, isochrones                  |
| Travel times between many origins and destinations   | Routing, matrix                      |
| A GPS trace snapped to roads                         | Routing, map matching                |
| A height, or a climb profile along a line            | Elevation                            |
| A first view near the visitor, without a prompt      | GeoIP                                |
| An image for email, a PDF, or a link preview         | Static Maps                          |
| Conversion between coordinate reference systems      | Coordinates                          |

Straight-line distance between two points needs no service. Compute it in the application, and do
not present it as travel time.

## Choose access

Local preview, build, and capture need no credential. For everything else:

| Where the code runs                               | Access                                                       |
| ------------------------------------------------- | ------------------------------------------------------------ |
| Browser map                                       | Public resources and the Map's Allowed websites; no key      |
| Search, Routing, or Elevation in a website or app | Publishable credential limited to those services and clients |
| Server or CI                                      | Team credential with the narrowest scopes                    |
| Static Maps and Coordinates                       | Server only; browser code calls the application's own route  |
| GeoIP                                             | None for anonymous use; a Map ID for managed use             |

The user creates credentials in the Team dashboard. Ask them to provide the value through the
project's environment configuration; do not create or request a key in chat. A publishable
credential can be copied, so it also needs per-minute and daily quotas. A native app identifies
itself with its bundle ID on iOS, and its package name and signing certificate on Android.

## Rules that are easy to miss

- **Search results are temporary.** Do not save or cache them, and do not build a place catalog from
  them. Geocoding a customer's store list and storing the coordinates is not allowed; the locations
  must come from the customer's own data.
- **Keep attribution.** Show the attribution returned by Search, Routing, and Elevation with the
  result, and keep it on every static image.
- **Search box.** Debounce input, cancel superseded requests, and ignore stale responses. Send the
  map center as proximity at city zoom or closer, and omit it at country scale. Keep bounds for an
  explicit "search this area" action: a box drawn from the view hides better matches outside it.
- **GeoIP is approximate.** Call it from the browser, because a backend call locates the backend.
  Keep a fallback view and never move a map the user has already moved.
- **Routes exclude live traffic.** Do not describe durations as real-time. On a routing failure, keep
  the stops and offer a retry rather than drawing a straight line.
- **Elevation can be missing.** A `null` height means no sample there; keep the gap in a chart.
- **Positions are longitude first** in Search, Routing, Elevation, and markers.
- **Do not auto-retry** an error whose code ends in `UNCONFIRMED`: the usage may already count.
