# Introduction

This repository contains geo-referenced limits for all municipalities in Italy, 
and their breakdown by regions and provinces.

The [geographic projection](https://github.com/d3/d3-geo) used is WGS84.

Please use [mapshaper](https://mapshaper.org), in order to see the content of big files.

The limits are released both in [topojson](https://github.com/topojson/topojson) with a high simplification rate (5%),
and the non-simplified [geojson](https://geojson.org/) format.

Topojson files are thought for simple web-based solutions, where a high number of vectors in a page
could be a problem (svg-based visualisers).

The files are upgraded periodically, and refer to the latest administrative subdivisions, as published by [ISTAT](https://www.istat.it/) in [this permalink](https://www.istat.it/it/archivio/222527) (hoping it's actually a **permalink**).

**Latest upgrade**: Jan 2019

# Geojson files
This files are **not simplified**, contains a large number of vectors, and can only contain one layer.
They are compatible with almost all visualisers and applications can be used to integrate geographic information, 
almost as ubiquitously as shp files.

The following files are available:
- `geojson/limits_IT_municipalities.geojson` - all italian municipalities, ~40MB
- `geojson/limits_IT_provinces.geojson` - all italian provinces
- `geojson/limits_IT_regions.geojson` - all italian regions
- `geojson/limits_R_{code}.geojson` - all municipalities in a region (R is the ISTAT numerical code of the region)
- `geojson/limits_P_{code}.geojson` - all munitipalities in a province (R is the ISTAT numerical code of the region)

# Topojson files
These files are **simplified**, **smaller**, but **less precise**, and contains **a lot less vectors** than the corresponding `geojson` files, can contain **many layers**, and can be used in compatible map visualisers ([leaflet](https://webkid.io/blog/maps-with-leaflet-and-topojson/), [d3](https://bl.ocks.org/almccon/410b4eb5cad61402c354afba67a878b8), mapshaper).

The following `topojson` files are available:
- `topojson/limits_IT_all.topo.json` - all municipalities, provinces and regions (3 layers), ~4MB
- `topojson/limits_IT_municipalities.topo.json` - all italian municipalities (1 layer), ~4MB
- `topojson/limits_IT_provinces.topo.json` - all italian provinces (1 layer)
- `topojson/limits_IT_regions.topo.json` - all italian regions (1 layer)
- `topojson/limits_R_{code}.topo.json` - all municipalities in a region (R is the ISTAT numerical code of the region)
- `topojson/limits_P_{code}.topo.json` - all munitipalities in a province (R is the ISTAT numerical code of the region)


# Metadata
Each geographic area has the following metadata:
- `name` (M) - the name of the municipality
- `com_catasto_code` (M) - the cadraste code (H501)
- `com_istat_code` (M) - the ISTAT code, as text (zero-padded)
- `com_istat_code_num` (M) - the ISTAT code, as integer
- `op_id` (M) - the openpolis ID (for integration with legacy OP data)
- `opdm_id` (M) - the opdm ID (for integration with OPDM data)
- `minint_elettorale` (M) - interior minister ID
- `prov_name` (M,P) - parent province name
- `prov_istat_code` (M,P) - parent province ISTAT code, as text (zero-padded)
- `prov_istat_code_num` (M,P) - parent province ISTAT code, as integer
- `prov_acr` (M,P,R) - parent province acronym (ex: RM)
- `reg_name` (M,P,R) - parent region full name
- `reg_istat_code` (M,P,R) - parent region ISTAT code, as text (zero padded)
- `reg_istat_code_num` (M,P,R) - parent region ISTAT code, as number

In parenthesis, the contexts where these properties can be found: 
- M: Municipalities,
- P: Provinces,
- R: Regions

# Developers
The procedure to produce the files is described in [this wiki page](https://github.com/openpolis/geojson-italy/wiki/How-to-generate-the-limits-files).
