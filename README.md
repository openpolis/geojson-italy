This repository contains topojson limits for all municipalities in Italy, 
by regions and provinces.

Definitions:
- `oc_comuni.geo.json`  - original data, produced elsewhere, 32MB, no preview
- `limits.topo.json`    - all municipalities (no preview on github, sorry)
- `limits_it.topo.json` - all italian provinces
- `limits_R*.topo.json` - all municipalities in a region, by ISTAT code (1-20)
- `limits_P*.topo.json` - all munitipalities in a province, by ISTAT code (1-111)

Please use [mapshaper](https://mapshaper.org), in order to see the content of big files.

The limits are highly simplified and thought for use in web-based solutions, where the number of vectors in a pag
could be a problem (svg-based visualisers).

The limits are upgraded periodically, and refer to the latest administrative subdivisions.
