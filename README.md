## Introduction

This repository contains geo-referenced limits for all municipalities in Italy, 
by regions and provinces.

Definitions:
- `oc_comuni.geo.json`  - original data, produced elsewhere, ~32MB, no preview
- `limits.topo.json`    - all municipalities, provinces and regions (3 layers), ~2MB, no preview
- `limits_it.topo.json` - all italian provinces and regions (2 layers)
- `limits_R*.topo.json` - all municipalities in a region, by ISTAT code (1-20)
- `limits_P*.topo.json` - all munitipalities in a province, by ISTAT code (1-111)

The [geographic projection](https://github.com/d3/d3-geo) used is WGS84.

Please use [mapshaper](https://mapshaper.org), in order to see the content of big files.

The limits are in [topojson](https://github.com/topojson/topojson) format, 
with a high simplification rate (5%). 
They are thought for simple web-based solutions, where a high number of vectors in a page
could be a problem (svg-based visualisers).

The files are upgraded periodically, and refer to the latest administrative subdivisions. 

Latest upgrade: june 2019


## Topojson limits generation procedure
All topojson files can be generated, starting from the original `oc_comuni.geo.json`, 
or another similar in format, produced by you.

As a *prerequisite*, install the [mapshaper client](https://github.com/mbloch/mapshaper)

The `oc_comuni.geo.json` file is a [geojson](https://geojson.org/) file, with a single layer:
- comuni (`cod_com`, `cod_pro`, `cod_reg`, `denominazione`)

### Transformation into simplified topojson

| origin             | destination                    |
| ------------------ | ------------------------------ |
| oc_comuni.geo.json | oc_comuni.simplified.topo.json |

```
mapshaper\
    -i oc_comuni.geo.json -clean encoding=utf8 \
    -simplify 5% weighted \
    -o oc_comuni.simplified.topo.json bbox format=topojson
```
Increase the percentage to increase limits *precision*.

Layers and fields do not change.

### Generation of complete, aggregated layers

| origin                         | destination      |
| ------------------------------ | ---------------- |
| oc_comuni.simplified.topo.json | limits.topo.json |

```
mapshaper\
    -i oc_comuni.simplified.topo.json \
    -rename-layers comuni \
    -dissolve cod_pro + copy-fields=cod_reg name=province \
    -target 1 \
    -dissolve cod_reg + name=regioni \
    -target 1  \
    -o limits.topo.json bbox target=* format=topojson \
    -info
```

The `limits.topo.json` file has the following layers:
- comuni (`cod_com`, `cod_pro`, `cod_reg`, `denominazione`)
- province (`cod_pro`, `cod_reg`)
- regioni (`cod_reg`)

### Generation of national layers (regions and provinces)

| origin           | destination         |
| ---------------- | ------------------- |
| limits.topo.json | limits_it.topo.json |

```
mapshaper\
    -i limits.topo.json \
    -drop target=comuni  \
    -o limits_it.topo.json bbox format=topojson  target=*
```

The `limits_it.topo.json` file has the following layers:
- province (`cod_pro`, `cod_reg`)
- regioni (`cod_reg`)

### Production of the 20 regional limits

| origin                         | destination         |
| ------------------------------ | ------------------- |
| oc_comuni.simplified.topo.json | limits_R*.topo.json |

```
for REG in `seq 1 20`
do
mapshaper\
    -i oc_comuni.simplified.topo.json \
    -filter cod_reg==$REG \
    -dissolve cod_pro + \
    -rename-layers comuni,province target=1,2 \
    -filter-fields cod_pro,cod_com,denominazione target=comuni \
    -o limits_R${REG}.topo.json bbox format=topojson target=comuni,province
done
```
The `limits_R*.topo.json` files have the following layers:
- comuni (`cod_com`, `cod_pro`, `cod_reg`, `denominazione`)
- province (`cod_pro`, `cod_reg`)

### Production of the 111 provincial limits

| origin                         | destination         |
| ------------------------------ | ------------------- |
| oc_comuni.simplified.topo.json | limits_P*.topo.json |

```
for PROV in `seq 1 111`
do
mapshaper\
    -i oc_comuni.simplified.topo.json \
    -filter cod_pro==$PROV \
    -dissolve cod_pro + \
    -rename-layers comuni target=1 \
    -filter-fields cod_com,denominazione target=comuni \
    -o limits_P${PROV}.topo.json bbox format=topojson target=comuni
done
```
The `limits_P*.topo.json` files have a single layer:
- comuni (`cod_com`, `cod_pro`, `cod_reg`, `denominazione`)
