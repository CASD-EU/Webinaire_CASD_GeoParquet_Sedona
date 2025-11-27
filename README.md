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


## 2. Sedona Raster data processing limits

Sedona does not implement all the advance processing for now (v1.7.1). Below is a list of the features which sedona does not support.
- Advanced Resampling Methods:  Sedona only supports **Nearest Neighbor, Bilinear, and Bicubic**. _Not supported methods: Lanczos, Mode, Average, and Cubic Spline_
- Specialized Terrain & Hydrology Analysis: Sedona does not implement the algorithm like NDVI as one function; the user must use RS_MapAlgebra functions to implement the algo. For example, NDVI algo in Sedona: RS_Divide(RS_Subtract(NIR−RED), RS_Add(NIR+RED)). 
- Other not supported functions :
      - Viewshed Analysis (what is visible from a point). 
      - Flow Accumulation/Direction (for river network mapping). 
      - Solar Radiation calculations. 
      - Cost Distance / Least Cost Path analysis.
- Complex Multi-Band "Composite" operations: Sedona can only handle basic band-specific operations. For example, 
   to apply a complex color correction across multi bands, you have to manually extract arrays, process them, 
     and re-assemble them by using the basic operations. For example, Sedona can’t convert _RGB_ to _YCbCr_ or _pan-sharpening_ images automatically


## 3. Sedona storing large raster geometries in GeoParquet or Parquet file

When processing and storing large raster datasets in Parquet format, you hit a performance and memory limit due to how 
Parquet handles "pages" (the internal units of storage).

- The Limit: The default page size in Spark/Parquet is 1 MB.
- Why it exists: Parquet is designed for columnar data, not large binary blobs (like images/rasters). When you save large raster geometries, they can easily exceed this 1 MB limit.
- The Consequence: If a single raster image is larger than 1 MB (e.g., 5 MB), Parquet cannot split it. It creates a "huge page" (e.g., 500 MB if you have 100 such rows).
- The Result: Reading these files becomes extremely slow and requires a massive amount of memory, often causing crashes or performance degradation.
- The Fix: You must tune the spark.hadoop.parquet.page.size.row.check.min and max settings, or use the RS_AsXXX functions (like RS_AsGeoTiff) to convert rasters to standard formats rather than raw internal bytes.

## 4. Internal Format Stability Limit

- The Limit: You should not store the raw bytes of Sedona raster objects directly if you expect long-term compatibility.
- Why: The internal binary format Sedona uses to represent rasters in memory is not guaranteed to be stable across different versions of Sedona.
- The Consequence: If you upgrade Sedona, you might not be able to read back the raw raster data you saved in a previous version.

## 5. GeoPackage Data Limits
If you are using `GeoPackage files` to store or process rasters, there are specific format limitations:
- WebP Rasters: Not supported.
- EWKB Geometries: Not supported.
- Filtering: You cannot filter rasters based on geometry envelopes (bounding boxes) directly in GeoPackage reading.