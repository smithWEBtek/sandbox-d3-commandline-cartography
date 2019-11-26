geoproject 'd3.geoConicEqualArea().parallels([34, 40.5]).rotate([120, 0]).fitSize([960, 960], d)' < ma.json > ma-albers.json

geoproject 'd3.geoConicConformal().parallels([41 + 43 / 60, 42 + 41 / 60]).rotate([71 + 30 / 60, 0]).fitSize([960, 960], d)' < ma.json > ma-stateplane.json



Upgrade
Brad Smith
Top highlight
Command-Line Cartography, Part 1
A tour of d3-geo’s new command-line interface.
Mike Bostock
Mike Bostock
Follow
Dec 9, 2016 · 5 min read

[This is Part 1 of a tutorial on making thematic maps. Read Part 2 here.]
This multipart tutorial will teach you to make a thematic map from the command line using d3-geo, TopoJSON and ndjson-cli—free, open-source tools written in JavaScript. We’ll make a choropleth of California’s population density. (For added challenge, substitute your state of choice!)


Source: American Community Survey, 2014 5-Year Estimate
The first part of this tutorial focuses on getting geometry (polygons) and converting this geometry into a format that can be easily manipulated on the command-line and displayed in a web browser.
The U.S. Census Bureau regularly publishes cartographic boundary shapefiles. Unlike TIGER—the Census Bureau’s most-detailed and comprehensive geometry product—the “cartographic boundary files are simplified representations… specifically designed for small scale thematic mapping.” In other words, they’re perfect for a humble choropleth.
The Census Bureau, as you might guess, also publishes data from their decennial census, the more frequent American Community Survey, and other surveys. To get a sense of the wealth of data the Census Bureau provides, visit the American FactFinder or the friendly Census Reporter. Now we must choose a few parameters:
A metric (e.g., population density)
A geographic entity (e.g., census tract)
A source (e.g., ACS 2014 5-year estimate)
It’s necessary to determine these parameters first because the geometry must match the data: if our population estimates are per census tract, we’ll need census tract polygons. More subtly, the year of the survey should match the geometry: while boundaries change relatively infrequently, they do change, especially with smaller entities such as tracts.
The Census Bureau helpfully provides guidance on picking the right data. Census tracts are small enough to produce a detailed map, but big enough to be easy to work with. We’ll use 5-year estimates, which are recommended for smaller entities and favor precision over currency. 2014 is the most recent release at the time of writing.

The Census Bureau’s standard hierarchy of geographic entities.
Now we need a URL! That URL can be found through a series of clicks from the Census Bureau website. But forget that, and just browse the 2014 cartographic boundary files here:
http://www2.census.gov/geo/tiger/GENZ2014/shp/
Given a state’s FIPS code (06 for California), you can now use curl to download the corresponding census tract polygons:
curl 'http://www2.census.gov/geo/tiger/GENZ2014/shp/cb_2014_06_tract_500k.zip' -o cb_2014_06_tract_500k.zip
Next, unzip the archive to extract the shapefile (.shp), and some other junk:
unzip -o cb_2014_06_tract_500k.zip
(You should already have curl and unzip installed, as these are included in most operating systems. You will also need node and npm; on macOS, I recommend Homebrew to install software.)
A quick way to check what’s in a shapefile is to visit mapshaper.org and drag the shapefile into your browser. If you do that with the downloaded cb_2014_06_tract_500k.shp, you should see something like this:

cb_2014_06_tract_500k.shp on mapshaper.org
As Mapshaper demonstrates, it’s possible to view shapefiles directly in your browser. But binary shapefiles can be difficult to work with, so we’ll convert to GeoJSON: a web-friendly, human-readable format. My shapefile parser has a command-line interface, shp2json, for this purpose. (Warning: there’s an unrelated package of the same name on npm.) To install:
npm install -g shapefile
Now use shp2json to convert to GeoJSON:
shp2json cb_2014_06_tract_500k.shp -o ca.json
Note that this also reads cb_2014_06_tract_500k.dbf, a dBASE file, defining feature properties on the resulting GeoJSON. The glorious result:

Hey, it’s more “human-readable” than binary data.
We could now display this in a browser using D3, but first we should apply a geographic projection. By avoiding expensive trigonometric operations at runtime, the resulting GeoJSON renders much faster, especially on mobile devices. Pre-projecting also improves the efficacy of simplification, which we’ll cover in part 3. To install d3-geo-projection’s command-line interface:
npm install -g d3-geo-projection
Now use geoproject:
geoproject 'd3.geoConicEqualArea().parallels([34, 40.5]).rotate([120, 0]).fitSize([960, 960], d)' < ca.json > ca-albers.json
This d3.geoConicEqualArea projection is California Albers, and as its name suggests, is appropriate for showing California. It’s also equal-area, which is strongly recommended for choropleth maps as the projection will not distort the data. If you’re not sure what projection to use, try d3-stateplane or search spatialreference.org.
The projection you specify to geoproject is an arbitrary JavaScript expression. That means that you can use projection.fitSize to fit the input geometry (represented by d) to the desired 960×960 bounding box!
To preview the projected geometry, use d3-geo-projection’s geo2svg:
geo2svg -w 960 -h 960 < ca-albers.json > ca-albers.svg

ca-albers.svg
If you followed along on the command line, you hopefully learned how to download and prepare geometry from the U.S. Census Bureau.
In part 2 of this tutorial, I’ll cover using ndjson-cli to join the geometry with population estimates from the Census Bureau API and to compute population density.
In part 3, I’ll cover simplifying geometry and merging features using topojson-server, topojson-simplify and topojson-client.
In part 4, I’ll cover implementing effective color encodings using d3-scale, and rendering the choropleth to SVG using d3-geo.
Ready for more? Continue to Part 2.
Questions or comments? Reply below or on Twitter. Thank you for reading!
1.5K

JavaScript
Maps
D3
Data Visualization
Programming
1.5K claps


Mike Bostock
WRITTEN BY

Mike Bostock
Follow
Building a better computational medium. Founder @observablehq. Creator #d3js. Former @nytgraphics. Pronounced BOSS-tock.
See responses (29)
More From Medium
Also tagged JavaScript
Safely Extending The JavaScript Set Object Using Proxies
Justin Travis Waith-Mair
Justin Travis Waith-Mair in The Non-Traditional Developer
Nov 25 · 6 min read
5

Related reads
Charting the waters (pt. 2): a comparison of JavaScript charting libraries
Mandi Cai
Mandi Cai in freeCodeCamp.org
Jan 24 · 8 min read
243

Also tagged Programming
5 Takeaways from 5 Months as a Software Engineer
Maria Mahin
Maria Mahin in Level Up Coding
Nov 25 · 7 min read
83

Discover Medium
Welcome to a place where words matter. On Medium, smart voices and original ideas take center stage - with no ads in sight. Watch
Make Medium yours
Follow all the topics you care about, and we’ll deliver the best stories for you to your homepage and inbox. Explore
Become a member
Get unlimited access to the best stories on Medium — and support writers while you’re at it. Just $5/month. Upgrade
About
Help
Legal
