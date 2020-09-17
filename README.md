# Week 3 - Spatial Databases, Part II

* [Homework (optional)](#Homework)
* Lecture
  - [Slide Deck](https://docs.google.com/presentation/d/1EovplVMnKfdNlxtVx7JJZJsE8f5BJyg_yd6VPTKj9L8/edit?usp=sharing)
  - [Code snippets](#Lecture)
* Lab - will be posted soon
* [Recommended Reading](#recommended-reading)

## Homework

* SQL practice at codecademy:
  - [Aggregate Functions](https://www.codecademy.com/courses/learn-sql/lessons/aggregate-functions)
  - [Multiple Tables](https://www.codecademy.com/courses/learn-sql/lessons/multiple-tables/)

## Lecture

### PostGIS Examples

You can reproduce the maps shown in lecture using the following queries. Be sure to change your schema/user name.

**ST_Centroid**

```SQL
SELECT ST_Transform(g, 3857) as the_geom_webmercator, g as the_geom, cartodb_id
FROM (
  SELECT ST_Centroid(the_geom) as g, cartodb_id
  FROM andyepenn.university_city_osm_buildings
) as _w
```

**ST_PointOnSurface**

```SQL
SELECT ST_Transform(g, 3857) as the_geom_webmercator, g as the_geom, cartodb_id
FROM (
  SELECT ST_PointOnSurface(the_geom) as g, cartodb_id
  FROM andyepenn.university_city_osm_buildings
) as _w
```

**ST_GeneratePoints**

```SQL
SELECT ST_Transform(g, 3857) as the_geom_webmercator, g as the_geom, cartodb_id
FROM (
  SELECT ST_GeneratePoints(the_geom, 15) as g, cartodb_id
  FROM andyepenn.university_city_osm_buildings
) as _w
```

**ST_Buffer**

```SQL
SELECT ST_Transform(g, 3857) as the_geom_webmercator, g as the_geom, cartodb_id
FROM (
  SELECT ST_Buffer(the_geom::geography, 25)::geometry as g, cartodb_id
  FROM andyepenn.university_city_osm_buildings
) _w
```

**ST_Intersection**

```SQL
SELECT the_geom, ST_Transform(the_geom, 3857) as the_geom_webmercator, row_number() over() as cartodb_id
FROM (
  SELECT ST_Intersection(o.the_geom, b.the_geom) as the_geom
  FROM (
    SELECT ST_Transform(g, 3857) as the_geom_webmercator, g as the_geom, cartodb_id
    FROM (
      SELECT ST_Buffer(the_geom::geography, 25)::geometry as g, cartodb_id
      FROM andyepenn.university_city_osm_buildings
    ) _w
  ) as b
  JOIN andyepenn.university_city_osm_buildings as o
  ON ST_Intersects(o.the_geom, b.the_geom) and o.cartodb_id <> b.cartodb_id
) as _abc
```

### JOIN query to try out


I used the following query to generate the data in the lecture

**Trip Data**

```SQL
SELECT start_station, duration, start_time, end_time
FROM andyepenn.indego_trips_2020_q2
where start_station in (3007, 3125, 3052)
LIMIT 5
```

**Station data**

```SQL
SELECT ST_AsText(the_geom) as geom, id, name
FROM andyepenn.indego_station_status
where id in (3007, 3125, 3052)
```

**Joined data**

```SQL
SELECT start_station, duration, start_time, end_time, geom, id, name
FROM (
  SELECT start_station, duration, start_time, end_time
  FROM andyepenn.indego_trips_2020_q2
  where start_station in (3007, 3125, 3052)
  LIMIT 5
  ) as trips
JOIN (
  SELECT ST_AsText(the_geom) as geom, id, name
  FROM andyepenn.indego_station_status
  where id in (3007, 3125, 3052)
 ) AS stations
 ON trips.start_station = stations.id
```

**Cover Slide**

```SQL
SELECT the_geom, ST_Transform(the_geom, 3857) as the_geom_webmercator, start_station, end_station, count(*) as num_trips, row_number() over () as cartodb_id
FROM (
  SELECT ST_MakeLine(ST_SetSRID(ST_MakePoint(start_lon, start_lat), 4326), ST_SetSRID(ST_MakePoint(end_lon, end_lat), 4326)) as the_geom, cartodb_id, start_station, end_station
  FROM andyepenn.indego_trips_2019_q2
  ) as _w
GROUP BY 1, 2, 3, 4
```

## Recommended Reading

* [Geography type](https://postgis.net/workshops/postgis-intro/geography.html)
* [PostGIS Geometry Returning Functions](https://postgis.net/workshops/postgis-intro/geometry_returning.html)
* [SafeGraph Patterns blog post](https://carto.com/blog/visit-pattern-footfall-data-safegraph/) -- skip the first couple of paragraphs ;)
