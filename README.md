# Webinaire_CASD_GeoParquet_Sedona

In this webinaire, we will learn
- What is apache sedona
- how to create a sedona session(via pyspark)
- how to read geospatial data(e.g vector and raster)
- how to do geospatial calculation
- how to write result in geoparquet

You can find all programing tutorials [here](./notebooks)

## 1. What is apache sedona?

[Apache Sedona](https://sedona.apache.org/latest/) is a cluster computing system for processing `large-scale spatial data`. In this tutorial, we will learn
how to use sedona in a `pyspark environment`. It supports other `cluster computing systems`, such as `Apache Flink, and Snowflake`. But we will not cover them in this tutorial.

### 1.1 Sedona Core (Scala)

Sedona adds support for spatial data types and operations to Spark.

It includes:

- Spatial RDDs
- Geometry types (Point, Polygon, etc.)
- Spatial indexes (QuadTree, RTree)
- Spatial operations (joins, predicates)

### 1.2 Sedona workflow

Sedona functions you call in Python (e.g., `ST_Point`, `ST_Within`) are just Python wrappers that translate calls to JVM methods.

The workflow is sedona->python->py4j->spark->jvm



