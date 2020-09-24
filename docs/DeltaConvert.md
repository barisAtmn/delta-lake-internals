= DeltaConvert Utility
:navtitle: DeltaConvert

`DeltaConvert` utility is used exclusively for <<executeConvert, importing a parquet table into Delta Lake (DeltaConvert.executeConvert)>>.

`DeltaConvert` utility can be used directly or indirectly via <<DeltaTable.md#convertToDelta, DeltaTable.convertToDelta>> utility.

.Demo of DeltaConvert
[source,scala]
----
import org.apache.spark.sql.SparkSession
assert(spark.isInstanceOf[SparkSession])

// CONVERT TO DELTA only supports parquet tables
// TableIdentifier should be parquet.`users`
import org.apache.spark.sql.catalyst.TableIdentifier
val table = TableIdentifier(table = "users", database = Some("parquet"))

import org.apache.spark.sql.types.{StringType, StructField, StructType}
val partitionSchema: Option[StructType] = Some(
  new StructType().add(StructField("country", StringType)))

val deltaPath: Option[String] = None

// Use web UI to monitor execution, e.g. http://localhost:4040
import io.delta.tables.execution.DeltaConvert
DeltaConvert.executeConvert(spark, table, partitionSchema, deltaPath)
----

[[DeltaConvertBase]]
`DeltaConvert` utility is a concrete `DeltaConvertBase`.

== [[executeConvert]] Importing Parquet Table Into Delta Lake (Converting Parquet Table To Delta Format) -- `executeConvert` Method

[source, scala]
----
executeConvert(
  spark: SparkSession,
  tableIdentifier: TableIdentifier,
  partitionSchema: Option[StructType],
  deltaPath: Option[String]): Unit
----

`executeConvert` simply creates a new <<ConvertToDeltaCommand.md#, ConvertToDeltaCommand>> and <<ConvertToDeltaCommand.md#run, executes it>>.

NOTE: `executeConvert` is used when `DeltaTable` utility is requested to <<DeltaTable.md#convertToDelta, convert a parquet table to delta format (DeltaTable.convertToDelta)>>.
