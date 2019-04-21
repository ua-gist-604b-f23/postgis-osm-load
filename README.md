# Assignment: PostGIS II - OSM Data Load - PostGIS
## Worth: 40 points
## Due: Saturday, April 27, 11:59pm

## Background
_[OpenStreetMap](https://www.openstreetmap.org) (aka OSM) is a map of the world, created by people like you and free to use under an open license._ In this lab you are going to download the OSM data for the state of Arizona and load it into a
PostGIS Database. 

### OpenStreetMap Data Model
Read about the OSM Data Model at [https://labs.mapbox.com/mapping/osm-data-model/](https://labs.mapbox.com/mapping/osm-data-model/). OSM Treats the world as vectors, specifically using the terminology `nodes`, `ways`, and `relations`. It does not 
map perfectly to the `points`, `lines`, and `polygons` models that you are used to. The model is also somewhat loosely defined and classes of entities such as roads are separated logically into different groups. Instead, they are represented by special attributes. Translating these entities to spatial layers requires a bit of work.

## Assignment
Deliverables: 

1) A file named `import.cmd` in a `osm` branch with a Pull Request to merge with master.
2) A screenshot of QGIS showing the OSM layers loaded from PostGIS, zoomed into Tucson.

`import.cmd` should contain all commands used to import the data into PostgreSQL. In practice,
this file would be a functioning shell script that could be re-used to perform the full data import from the 
unzipped shapefile to having fully populated tables in PostgreSQL.

### Download OpenStreetMap Arizona data

Download the Arizona _shapefile_ (not the pbf file) for OpenStreetMap from [http://download.geofabrik.de/north-america/us/arizona.html](http://download.geofabrik.de/north-america/us/arizona.html).

Unzip and take note of the projection:

```GEOGCS["GCS_WGS_1984",DATUM["D_WGS_1984",SPHEROID["WGS_1984",6378137,298.257223563]],PRIMEM["Greenwich",0],UNIT["Degree",0.017453292519943295]]```

This is `EPSG:4326`.

### Extract the OSM data and load it into postgresql

The command to load this data into PostGIS is called `shp2psql`. It should already be installed as part of the PostGIS bundle. It is a command that takes a shapefile and turns into the PostgreSQL variant of SQL. When you run it you
you will provide the name of a shapefile. By default the output will be printed to your screen (aka `STDOUT`)
but you want to redirect the output to a file. 

Open a Unix shell or DOS command window and navigate to the directory where you unzipped the arizona 


```
shp2pgsql -s 4326 gis_osm_places_free_1 > gis_osm_places_free_1.sql
```

This creates a SQL file that you can use to load the data into postgresql. Loading data via the command line is pretty simple:

```
psql -d arizona -h localhost -U postgres -f gis_osm_places_free_1.sql
```

The above two commands will create and populate a table for `places` based on OSM data. 

You will be prompted for your password each time. To avoid being asked repeatedly, type the following command to store
your password in your local shell environment, replacing `postgres` with the password you selected (if you did) for your
PostgreSQL installation.

```
set PGPASSWORD=postgres
```
Note that if you close the window you will lose that environment. Thus, if you close and re-open a command window you will
need to re-issue the above command if you want to avoid being asked for the password every time you run a `psql` command. Savvy users can save this as a USER environment variable and not have to be asked again.

Repeat the steps for the additional data files. Refresh your pgadmin table list to see that the tables were created.

### Rename the tables
The names are pretty obnoxious since they all start with the same 8 characters. To change a table name in SQL: 


```
ALTER TABLE my_table RENAME TO new_name;
```


Use it with psql to run it from the command line:


```
psql -d arizona -h localhost -U postgres -c "ALTER TABLE gs_osm_buildings_a_free_1 RENAME TO buildings;
```


Do this for all the OSM layers. I didn’t do it for the rest of this tutorial but it will make things a lot easier for you.


### Open PostGIS Tables as Layers in QGIS
View in QGIS
Open GGIS and select the “Layer -> Add PostGIS Layers” option. 
Open all the OSM Arizona layers. Take a screenshot and save it to your github `osm` branch with the name `osm_qgis_screenshot.png`

### Deliverables:
The following two files in a branch named `osm`, submitted as a Pull Request to be merged with master:
1) File named `import.cmd` containing:
- all commands used to extract shapefile data into sql files (i.e., `shp2pgsql...`)
- all commands used to import sql files into postgresql (i.e., `psql...`)
- all commands used to rename tables (i.e., `psql.... ALTER TABLE...`)
2) Screenshot named `osm_qgis_screenshot.png` showing all OSM PostGIS tables visible in QGIS, zoomed into Tucson
