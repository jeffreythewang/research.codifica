# Research @ Codifica

Yo

# Want to contribute to the research?

Good, checkout the README inside the `data/` directory and send me a pull request.

Check to make sure it doesn't break the site before submitting. Or else @kenzshi will drop you a visit when you're sleeping.

Don't know how to check? Just run this from the root research.codifica directory:

    python -m SimpleHTTPServer 1337

Fire up a web browser and navigate to `localhost:1337`

If you don't have Python and SimpleHTTPServer installed, go figure out how to run your own local web server...

# Want to compile the data from scratch?

You fucking masochist.

## Prerequisites

- [Geospatial Data Abstraction Library](http://trac.osgeo.org/gdal/wiki/DownloadingGdalBinaries)
    - `brew install gdal` if you're on a Mac and have homebrew installed
- [TopoJSON](https://github.com/mbostock/topojson)
    - `npm install -g topojson` (make sure you have Node and NPM installed)

## Generating the dataset from Natural Earth data.

Download the datasets from [Natural Earth](http://www.naturalearthdata.com/)

- [States and Provinces Dataset](http://www.naturalearthdata.com/downloads/10m-cultural-vectors/10m-admin-1-states-provinces/)
- [Populated Places Dataset](http://www.naturalearthdata.com/http//www.naturalearthdata.com/download/10m/cultural/ne_10m_populated_places.zip)

Compile the dataset for the US (the ne_10m_admin_1_states_provinces dataset includes data for each US state)

    ogr2ogr \
      -f GeoJSON \
      -where "ADM0_A3 = 'USA'" \
      subunits.json \
      {DATASETS_DIR}/ne_10m_admin_1_states_provinces/ne_10m_admin_1_states_provinces.shp

Compile the dataset for major populated cities in the US

    ogr2ogr \
      -f GeoJSON \
      -where "ISO_A2 = 'US' AND SCALERANK < 8" \
      places.json \
      {DATASETS_DIR}/ne_10m_populated_places/ne_10m_populated_places.shp

Combining the two datasets

    topojson \
      -o us.json \
      --id-property SU_A3 \
      --properties name=NAME \
      -- \
      subunits.json \
      places.json
