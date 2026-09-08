# Asendra Space Travels — map data

`space-objects.json` is the object catalogue for Asendra Space Travels, a 3D map of
space. The application downloads this file at startup; nothing else lives here.

**Generated, not edited.** A scheduled job rebuilds this file from the
[NASA Exoplanet Archive](https://exoplanetarchive.ipac.caltech.edu/) and overwrites it,
so changes made here are lost on the next run. The application code lives in a separate
private repository.

## What is in it

Every object is one place you can travel to: a planetary system, a star, a galaxy. The
hand-picked landmarks come first, then every known planetary system within 100 light-years,
sorted by distance.

Positions are **catalogue values, not Cartesian coordinates** — right ascension in hours,
declination in degrees, distance in light-years. The application derives XYZ from those at
load time, in astronomical units, with Earth at the origin. Nothing is pre-projected or
compressed, and there is never a second set of coordinates that can drift out of step with
the first.

| Field | Meaning |
|---|---|
| `id` | Stable key. Never reused, never renamed. |
| `rightAscensionHours`, `declinationDegrees`, `distanceLightYears` | Position, ICRS. |
| `radiusAstronomicalUnits` | Real physical radius of `primaryBody`. |
| `markerAngularSizeDegrees` | Floor on how small the object may appear on screen. |
| `bodies` | Known planets, innermost first. |
| `firstSeen` | The day the sync first saw this object. Set once, never changed. |
| `discoveryDate` | The year the object itself was discovered. Not the same thing. |

`firstSeen` is what drives the NEW badge in the application's library panel: it compares
that date against the last time you opened the app. There is no diffing on the client.

## Acknowledgement

This research has made use of the NASA Exoplanet Archive, which is operated by the
California Institute of Technology, under contract with the National Aeronautics and
Space Administration under the Exoplanet Exploration Program.
