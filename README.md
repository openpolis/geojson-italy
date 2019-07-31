## Introduction

This repository contains geo-referenced limits for all municipalities in Italy, 
by regions and provinces.

The [geographic projection](https://github.com/d3/d3-geo) used is WGS84.

Please use [mapshaper](https://mapshaper.org), in order to see the content of big files.

Files:
- `comuni.geojson`               - original data, produced elsewhere, ~40MB, no preview
- `geojson/limits.geojson`       - all municipalities, provinces and regions (3 layers)
- `geojson/limits_it.geojson`    - all italian provinces and regions (2 layers)
- `geojson/limits_R*.geojson`    - all municipalities in a region, by ISTAT code (1-20)
- `geojson/limits_P*.geojson`    - all munitipalities in a province, by ISTAT code (1-111)
- `topojson/limits.topo.json`    - all municipalities, provinces and regions (3 layers), ~2MB, no preview
- `topojson/limits_it.topo.json` - all italian provinces and regions (2 layers)
- `topojson/limits_R*.topo.json` - all municipalities in a region, by ISTAT code (1-20)
- `topojson/limits_P*.topo.json` - all munitipalities in a province, by ISTAT code (1-111)

The limits are released both in [topojson](https://github.com/topojson/topojson) with a high simplification rate (5%),
and the non-simplified [geojson](https://geojson.org/) format.

Topojson files are thought for simple web-based solutions, where a high number of vectors in a page
could be a problem (svg-based visualisers).

The files are upgraded periodically, and refer to the latest administrative subdivisions. 

Latest upgrade: june 2019


## Limits generation procedure
All files can be generated, starting from the original `comuni.geojson`, 
or another similar in format, produced by you.

As a *prerequisite*, install the [mapshaper client](https://github.com/mbloch/mapshaper)

The `comuni.geojson` file is a geojson file (one layer), each feature has these properties:
- `name` - the name of the municipality
- `com_catasto_code` - the cadraste code (H501)
- `com_istat_code` - the ISTAT code, as text (zero-padded)
- `com_istat_code_num` - the ISTAT code, as integer
- `op_id` - the openpolis ID (for integration with legacy OP data)
- `opdm_id` - the opdm ID (for integration with OPDM data)
- `minint_elettorale` - interior minister ID
- `prov_name` - parent province name
- `prov_istat_code` - parent province ISTAT code, as text (zero-padded)
- `prov_istat_code_num` - parent province ISTAT code, as integer
- `prov_acr` - parent province acronym (ex: RM)
- `reg_name` - parent region full name
- `reg_istat_code` - parent region ISTAT code, as text (zero padded)
- `reg_istat_code_num` - parent region ISTAT code, as number

### Topojson
This will generate `topojson` files. These files are simplified (**smaller, but less precise**), contains **a lot less vectors** than the corresponding geojson files, can contain **many layers**, and can be used in compatible map visualisers ([leaflet](https://webkid.io/blog/maps-with-leaflet-and-topojson/), [d3](https://bl.ocks.org/almccon/410b4eb5cad61402c354afba67a878b8), mapshaper).

#### Transformation into simplified topojson
The following command will transform the original file into `topojson`, 
with a **simplification** of 5%, and retaining all the meta information.

| origin             | destination                 |
| ------------------ | ---------------------------:|
| comuni.geojson     | limits_it_comuni.topo.json  |

```
mapshaper\
    -i comuni.geojson -clean encoding=utf8 \
    -simplify 5% weighted \
    -o topojson/limits_it_comuni.topo.json bbox format=topojson
```
Increase the percentage to increase limits *precision* (and size, accordingly).


### Generation of national layers
This will generate a single file containing three layers (comuni, province, regioni), 
plus two layers with provinces and regions.

Each layer (and file) will have the useful metadata.

| origin                     | destination                    |
| -------------------------- | ------------------------------:|
| limits_it_comuni.topo.json | limits_it_all_layers.topo.json |

```
mapshaper \
    -i topojson/limits_it_comuni.topo.json \
    -rename-layers comuni \
    -dissolve prov_istat_code + \
      copy-fields=prov_name,prov_istat_code_num,prov_acr,reg_name,reg_istat_code,reg_istat_code_num name=province \
    -target 1 \
    -dissolve reg_istat_code + \
      copy-fields=reg_name,reg_istat_code_num name=regioni \
    -target 1  \
    -o topojson/limits_it_all_layers.topo.json bbox format=topojson target=regioni,province,comuni \
    -o topojson/limits_it_regioni.topo.json bbox format=topojson target=regioni \
    -o topojson/limits_it_province.topo.json bbox format=topojson target=province 
```

The metadata in each layer/file:
- comuni (same fields as the original `comuni.geojson`)
- province (`prov_name`, `prov_istat_code`, `prov_istat_code_num`, `prov_acr`, 
  `reg_name`, `reg_istat_code`, `reg_istat_code_num`)
- regioni (`reg_name`, `reg_istat_code_num`, `reg_code`)

### Generation of national limits (regions and provinces)
This will produce
```
mapshaper\
    -i topojson/limits.topo.json \
    -o topojson/limits_it_regioni.topo.json bbox format=topojson target=regioni \
    -o topojson/limits_it_province.topo.json bbox format=topojson target=province \    
```

### Generation of national limits (regions and provinces)

| origin             | destination             |
| ------------------ | -----------------------:|
| limits.topo.json   | limits_it.topo.json     |
| oc_comuni.geojson  | limits_it_province.json |
| oc_comuni.geojson  | limits_it_regioni.json  |

```
# geojson
for LAYER in comuni province regioni
do
  mapshaper \
    -i oc_comuni.geojson -clean encoding=utf8 \
    -rename-layers comuni \
    -dissolve cod_pro + copy-fields=cod_reg name=province \
    -target comuni \
    -dissolve cod_reg + name=regioni \
    -o geojson/limits_it_$LAYER.geojson bbox format=geojson target=$LAYER
done


The `limits_it.topo.json` file has the following layers:
- province (`cod_pro`, `cod_reg`)
- regioni (`cod_reg`)

### Production of the 20 regional limits

| origin                         | destination                |
| ------------------------------ | --------------------------:|
| oc_comuni.simplified.topo.json | limits_R*.topo.json        |
| oc_comuni.geojson              | limits_R*_province.geojson |
| oc_comuni.geojson              | limits_R*_regioni.geojson  |

```
# geojson
for LAYER in province comuni
do 
  for REG in `seq 1 20`
  do mapshaper \
    -i oc_comuni.geojson \
    -filter cod_reg==$REG \
    -dissolve cod_pro + \
    -rename-layers comuni,province target=1,2 \
    -filter-fields cod_pro,cod_com,denominazione target=comuni \
    -o geojson/limits_R${REG}_${LAYER}.geojson bbox format=geojson target=${LAYER}
  done
done

# topojson
for REG in `seq 1 20`
do
mapshaper\
    -i topojson/oc_comuni.simplified.topo.json \
    -filter cod_reg==$REG \
    -dissolve cod_pro + \
    -rename-layers comuni,province target=1,2 \
    -filter-fields cod_pro,cod_com,denominazione target=comuni \
    -o topojson/limits_R${REG}.topo.json bbox format=topojson target=province,comuni
done
```

The `limits_R*.topo.json` files have the following layers:
- comuni (`cod_com`, `cod_pro`, `cod_reg`, `denominazione`)
- province (`cod_pro`, `cod_reg`)

### Production of the 111 provincial limits

| origin                         | destination         |
| ------------------------------ | -------------------:|
| oc_comuni.simplified.topo.json | limits_P*.topo.json |
| oc_comuni.geojson              | limits_P*.geojson   |

```
# geojson
for PROV in `seq 1 111`
do 
  mapshaper \
    -i oc_comuni.geojson \
    -filter cod_pro==$PROV \
    -dissolve cod_pro + \
    -rename-layers comuni target=1 \
    -filter-fields cod_pro,cod_com,denominazione target=comuni \
    -o geojson/limits_P${PROV}.geojson bbox format=geojson target=comuni
done

# topojson
for PROV in `seq 1 111`
do
mapshaper\
    -i topojson/oc_comuni.simplified.topo.json \
    -filter cod_pro==$PROV \
    -dissolve cod_pro + \
    -rename-layers comuni target=1 \
    -filter-fields cod_com,denominazione target=comuni \
    -o topojson/limits_P${PROV}.topo.json bbox format=topojson target=comuni
done
```
The `limits_P*.topo.json` files have a single layer:
- comuni (`cod_com`, `cod_pro`, `cod_reg`, `denominazione`)
