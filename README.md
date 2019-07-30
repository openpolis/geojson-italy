This repository contains topojson limits for all municipalities in Italy, 
by regions and provinces.

Definitions:
- `oc_comuni.geo.json`  - original data, produced elsewhere, ~32MB, no preview
- `limits.topo.json`    - all municipalities, ~2MB, no preview
- `limits_it.topo.json` - all italian provinces
- `limits_R*.topo.json` - all municipalities in a region, by ISTAT code (1-20)
- `limits_P*.topo.json` - all munitipalities in a province, by ISTAT code (1-111)

Please use [mapshaper](https://mapshaper.org), in order to see the content of big files.

The limits are highly simplified and thought for use in web-based solutions, where a high number of vectors in a page
could be a problem (svg-based visualisers).

The files  are upgraded periodically, and refer to the latest administrative subdivisions. 

Latest upgrade: june 2019
