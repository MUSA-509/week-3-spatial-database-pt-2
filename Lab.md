# Week 3 Lab - JOINs and more Spatial SQL

## Topics

1. GitHub Desktop workflow demo
2. Introduction to our datasets
3. JOINs
4. Spatial JOINs

## GitHub Desktop

If you're not comfortable with the command line, [GitHub Desktop](https://desktop.github.com/) is a good way to use GitHub.

Download here: https://desktop.github.com/

## Datasets

### 1. Census block groups of Philadelphia County

**Dataset link:** `TBD`

This dataset contains polygon geometries for Philadelphia County (FIPS 42101). The data originally came from [TIGER/Line with Selected Demographic and Economic Data](https://www.census.gov/geographies/mapping-files/2010/geo/tiger-data.html) but reduced to only population, geoid, and geometry. The full dataset has hundreds of census variables.

![](images/philadelphia-cbgs.png)
/Users/petereschbacher/Documents/GitHub/week-3-spatial-database-pt-2
Block Groups (per [US Census](https://www.census.gov/programs-surveys/geography/about/glossary.html#par_textimage_4))

> Block Groups (BGs) are statistical divisions of census tracts, are generally defined to contain between 600 and 3,000 people, and are used to present data and control block numbering.  A block group consists of clusters of blocks within the same census tract that have the same first digit of their four-digit census block number.  For example, blocks 3001, 3002, 3003, . . ., 3999 in census tract 1210.02 belong to BG 3 in that census tract.

The two most important columns in this dataset for us are:

* `geoid`: Census block group identifier; a concatenation of the current state Federal Information Processing Series (FIPS) code, county FIPS code, census tract code, and block group number
* `geometry`: Polygon geometries of the block groups

### 2. SafeGraph Neighborhood Patterns (see link on Canvas for download)

**Dataset link:** `TBD`

More on [SafeGraph Neighborhood Patterns](https://docs.safegraph.com/docs/neighborhood-patterns-2020).

There's a lot to unpack here. First, notice that the dataset doesn't have explicit geospatial data. It has the Census Block Group ID, though. If only we could match that to a geometry...

  * `median_dwell`: Median dwell time in minutes. Note that we are only including stops that have a dwell of at least 1 minute.
  * `distance_from_primary_daytime_location`: Median distance from device_daytime_areas traveled to the stopping point(s) within the area by devices from their device_daytime_area (of devices whose device_daytime_area we have identified) in meters.
  * `distance_from_home`: Median distance from home traveled to the stopping point(s) within the area by devices from their home area (of devices whose home area we have identified) in meters.
  * `raw_device_counts`: Number of unique devices in our panel that stopped in this area during the date range. This includes devices whose home area is the same as this area.
  * `raw_stop_counts`: Number of stops by devices in our panel to this area during the date range. A stop must have a minimum duration of 1 minute to be included. The count includes stops by devices whose home area is the same as this area.

We will explore the other attributes in future classes.

### 3. OpenStreetMap Building Footprints

We'll use this OSM file from last week too.

**Dataset link:** `https://raw.githubusercontent.com/MUSA-509/week-2-digging-into-databases/master/data/university_city_osm_buildings.geojson`

## JOINs

Our template for a relational JOIN is as follows:

```SQL
SELECT <columns>
FROM <table1>
JOIN <table2>
ON <matching condition>
```

For example, from our lecture we saw a join with Indego data that allows us to connect station information with the trip data:

```SQL
SELECT
  t.start_station,
  t.duration,
  t.start_time,
  t.end_time,
  stations.the_geom,
  stations.id,
  stations.name
FROM andyepenn.indego_trips_2020_q2 AS trips
JOIN andyepenn.indego_station_status AS stations
 ON trips.start_station = stations.id
```


**1. Write a query that matches the Census Block Group Polygons to the SafeGraph dataset**

```SQL
-- fill in your query here
```

**2. Using your answer from the previous question, modify it to find the number of unique devices per square kilometer for each block group.**

_Remember that ST_Area(geography) returns an answer in square meters._

```SQL
-- fill in your query here
```

**3. **

## Spatial Joins
