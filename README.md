# Asendra Space Travels — map data

The map data for Asendra Space Travels, a 3D map of space. The application downloads
these files at startup and as you travel; no code lives here.

**Generated, not edited.** Scheduled jobs rebuild these files and overwrite them, so
changes made here are lost on the next run. The application code lives in a separate
private repository.

## The files

| File | What it is | Rebuilt |
|---|---|---|
| `space-objects.json` | Everything you can click on and travel to. | Nightly |
| `space-points.txt` | The asteroid belt, as a point cloud. | Nightly |
| `gaia/sectors.json` | Index of the star sectors below. | Monthly |
| `gaia/s_<x>_<y>_<z>.txt` | Real nearby stars, one file per cube of space. | Monthly |

## Positions

Positions everywhere are **catalogue values, not Cartesian coordinates** — right
ascension in hours, declination in degrees, distance in light-years. The application
derives XYZ from those at load time, in astronomical units, with Earth at the origin.
Nothing is pre-projected or compressed, and there is never a second set of coordinates
that can drift out of step with the first.

One thing here does move. Asteroids and dwarf planets orbit the Sun, so their positions
are a **snapshot** computed for the `generated` date in the file — from the orbital
elements JPL publishes, since a catalogue cannot hand out a fixed direction for something
that changes every night. A day of drift is about an arcminute, which is nothing on a map
that spans light-years.

## space-objects.json

Every object is one place you can travel to: a planetary system, an asteroid, a black
hole, a galaxy. The hand-picked landmarks come first, then everything else from nearest
to farthest.

| Field | Meaning |
|---|---|
| `id` | Stable key. Never reused, never renamed. |
| `rightAscensionHours`, `declinationDegrees`, `distanceLightYears` | Position, ICRS. |
| `radiusAstronomicalUnits` | Real physical radius of `primaryBody`. For black holes this is the Schwarzschild radius, so the sphere you fly up to is the event horizon at true scale. |
| `markerAngularSizeDegrees` | Floor on how small the object may appear on screen. |
| `bodies` | Known planets, innermost first. |
| `firstSeen` | The day the sync first saw this object. Set once, never changed. |
| `discoveryDate` | The year the object itself was discovered. Not the same thing. |

`firstSeen` is what drives the NEW badge in the application's library panel: it compares
that date against the last time you opened the app. There is no diffing on the client.

## The point files

`space-points.txt` and the Gaia sectors hold objects that are too numerous to be
catalogue entries — 26,000 named minor planets, 331,000 nearby stars. They are drawn as
point clouds and cannot be clicked. The format is one record per line, fields separated
by a vertical bar, `#` for comments:

    ra_hours|dec_degrees|distance_light_years|colour|brightness[|id|name]

Plain text rather than JSON because the application parses these at runtime, on the main
thread, while the camera is moving. The last two fields exist only in the Gaia sectors,
where a star close to where you are standing gets promoted to a real, clickable object.

## The Gaia sectors

Space is cut into cubes 100 light-years on a side. A point belongs to the sector
`floor(coordinate / 100)` on each axis, in the same map coordinates described above.
`sectors.json` lists which cubes exist and what each one is called; cubes not listed are
empty. The application loads only the cubes near wherever you are docked.

The source is the Gaia Catalogue of Nearby Stars — everything within 100 parsecs, about
390 light-years. Beyond that the application draws its own procedural starfield.

## Acknowledgements

This research has made use of the NASA Exoplanet Archive, which is operated by the
California Institute of Technology, under contract with the National Aeronautics and
Space Administration under the Exoplanet Exploration Program.

This research has made use of data and services provided by the NASA/JPL Solar System
Dynamics group, including the Small-Body Database and the Close Approach Data API.

This research has made use of the SIMBAD database and the VizieR catalogue access tool,
operated at CDS, Strasbourg, France.

This work has made use of data from the European Space Agency (ESA) mission
[Gaia](https://www.cosmos.esa.int/gaia), processed by the Gaia Data Processing and
Analysis Consortium ([DPAC](https://www.cosmos.esa.int/web/gaia/dpac/consortium)).
Funding for the DPAC has been provided by national institutions, in particular the
institutions participating in the Gaia Multilateral Agreement.
