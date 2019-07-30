## Introduction

This repository contains topojson limits for all municipalities in Italy, 
by regions and provinces.

Definitions:
- `oc_comuni.geo.json`  - original data, produced elsewhere, ~32MB, no preview
- `limits.topo.json`    - all municipalities, provinces and regions (3 layers), ~2MB, no preview
- `limits_it.topo.json` - all italian provinces and regions (2 layers)
- `limits_R*.topo.json` - all municipalities in a region, by ISTAT code (1-20)
- `limits_P*.topo.json` - all munitipalities in a province, by ISTAT code (1-111)

Please use [mapshaper](https://mapshaper.org), in order to see the content of big files.

The limits are highly simplified and thought for use in web-based solutions, where a high number of vectors in a page
could be a problem (svg-based visualisers).

The files  are upgraded periodically, and refer to the latest administrative subdivisions. 

Latest upgrade: june 2019


## Topojson limits generation procedure
All topojson files can be generated, starting from the original `oc_comuni.geo.json`, 
or another similar in format, produced by you.

As a *prerequisite*, install the [mapshaper client](https://github.com/mbloch/mapshaper))

### Transformation into simplified topojson

```
    mapshaper\
        -i oc_comuni.geo.json -clean encoding=utf8 \
        -simplify 5% weighted \
        -o oc_comuni.simplified.topo.json bbox format=topojson
```
Increase the percentage to increase limits *precision*.

### Generation of aggregated layers

```
    mapshaper\
        -i oc_comuni.simplified.topo.json \
        -rename-layers comuni \
        -dissolve cod_pro + copy-fields=cod_reg,cod_pro name=province \
        -target 1 \
        -dissolve cod_reg + name=regioni \
        -target 1  \
        -o limits.topo.json bbox target=* format=topojson \
        -info
```

