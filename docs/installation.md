= Installation

Delta Lake is a Spark data source and as such installation boils down to using spark-submit's `--packages` command-line option.

Delta Lake also requires DeltaSparkSessionExtension.adoc[] and DeltaCatalog.adoc[] to be registered (using respective configuration properties).

== [[application]] Spark SQL Application

[source,scala]
----
import org.apache.spark.sql.SparkSession
val spark = SparkSession
  .builder()
  .appName("...")
  .config("spark.sql.extensions", "io.delta.sql.DeltaSparkSessionExtension")
  .config("spark.sql.catalog.spark_catalog", "org.apache.spark.sql.delta.catalog.DeltaCatalog")
  .getOrCreate
----

== [[spark-shell]] Spark Shell

[source,plaintext]
----
./bin/spark-submit \
  --packages io.delta:delta-core_2.12:0.7.0 \
  --conf spark.sql.extensions=io.delta.sql.DeltaSparkSessionExtension \
  --conf spark.sql.catalog.spark_catalog=org.apache.spark.sql.delta.catalog.DeltaCatalog
----
