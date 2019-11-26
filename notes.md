curl 'http://www2.census.gov/geo/tiger/GENZ2014/shp/cb_2014_25_tract_500k.zip' -o cb_2014_25_tract_500k.zip

ma-tracts.json
geoproject 'd3.geoConicConformal().parallels([41 + 43 / 60, 42 + 41 / 60]).rotate([71 + 30 / 60, 0]).fitSize([960,960], d)' < ma-tracts.json > ma-stateplane.json

geo2svg -w 960 -h 960 < ma-stateplane.json > ma-stateplane.svg


ma-munis.json
geoproject 'd3.geoConicConformal().parallels([41 + 43 / 60, 42 + 41 / 60]).rotate([71 + 30 / 60, 0]).fitSize([960,960], d)' < ma-munis.json > ma-munis-stateplane.json

geo2svg -w 960 -h 960 < ma-munis-stateplane.json > ma-munis-stateplane.svg


census.json
geoproject 'd3.geoConicConformal().parallels([41 + 43 / 60, 42 + 41 / 60]).rotate([71 + 30 / 60, 0]).fitSize([960,960], d)' < census.json > census-stateplane.json

geo2svg -w 960 -h 960 < census-stateplane.json > census-stateplane.svg
