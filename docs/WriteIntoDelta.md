= [[WriteIntoDelta]] WriteIntoDelta Command -- Writing Data(Frame) Transactionally Into Delta Table

`WriteIntoDelta` is a <<DeltaCommand.adoc#, Delta command>> that can write <<data, data(frame)>> transactionally into a <<deltaLog, delta table>>.

[[demo]]
.Demo
[source, scala]
----
import org.apache.spark.sql.delta.commands.WriteIntoDelta
import org.apache.spark.sql.delta.DeltaLog
import org.apache.spark.sql.SaveMode
import org.apache.spark.sql.delta.DeltaOptions
val tableName = "/tmp/delta/t1"
val data = spark.range(5).toDF
val writeCmd = WriteIntoDelta(
  deltaLog = DeltaLog.forTable(spark, tableName),
  mode = SaveMode.Overwrite,
  options = new DeltaOptions(Map.empty[String, String], spark.sessionState.conf),
  partitionColumns = Seq.empty[String],
  configuration = Map.empty[String, String],
  data)

// Review web UI @ http://localhost:4040

writeCmd.run(spark)
----

[[ImplicitMetadataOperation]]
`WriteIntoDelta` is an <<ImplicitMetadataOperation.adoc#, operation that can update metadata (schema and partitioning)>> of a <<deltaLog, delta table>>.

[[RunnableCommand]]
`WriteIntoDelta` is a logical command (`RunnableCommand`).

TIP: Read up on https://jaceklaskowski.gitbooks.io/mastering-spark-sql/spark-sql-LogicalPlan-RunnableCommand.html[RunnableCommand] in https://bit.ly/spark-sql-internals[The Internals of Spark SQL] online book.

`WriteIntoDelta` is <<creating-instance, created>> when:

* `DeltaLog` is requested to <<DeltaLog.adoc#createRelation, create an insertable HadoopFsRelation>> (when `DeltaDataSource` is requested to create a relation as a <<DeltaDataSource.adoc#CreatableRelationProvider, CreatableRelationProvider>> or a <<DeltaDataSource.adoc#RelationProvider, RelationProvider>>)

* `DeltaDataSource` is requested to <<DeltaDataSource.adoc#CreatableRelationProvider-createRelation, create a relation (for writing)>> (as a <<DeltaDataSource.adoc#CreatableRelationProvider, CreatableRelationProvider>>)

== [[creating-instance]] Creating WriteIntoDelta Instance

`WriteIntoDelta` takes the following to be created:

* [[deltaLog]] <<DeltaLog.adoc#, DeltaLog>>
* [[mode]] `SaveMode`
* [[options]] <<DeltaOptions.adoc#, DeltaOptions>>
* [[partitionColumns]] Names of the partition columns (`Seq[String]`)
* [[configuration]] Configuration (`Map[String, String]`)
* [[data]] Data (`DataFrame`)

== [[run]] Running Command -- `run` Method

[source, scala]
----
run(
  sparkSession: SparkSession): Seq[Row]
----

NOTE: `run` is part of the `RunnableCommand` contract to run a command.

`run` requests the <<deltaLog, DeltaLog>> to <<DeltaLog.adoc#withNewTransaction, start a new transaction>>.

`run` <<write, writes>> and requests the `OptimisticTransaction` to <<OptimisticTransactionImpl.adoc#commit, commit>>.

== [[write]] `write` Method

[source, scala]
----
write(
  txn: OptimisticTransaction,
  sparkSession: SparkSession): Seq[Action]
----

`write` checks out whether the write operation is to a delta table that already exists. If so (i.e. the <<OptimisticTransactionImpl.adoc#readVersion, readVersion>> of the transaction is above `-1`), `write` branches per the <<mode, SaveMode>>:

* For `ErrorIfExists`, `write` throws an `AnalysisException`:
+
```
[path] already exists.
```

* For `Ignore`, `write` does nothing

* For `Overwrite`, `write` requests the <<deltaLog, DeltaLog>> to <<DeltaLog.adoc#assertRemovable, assertRemovable>>

`write` <<ImplicitMetadataOperation.adoc#updateMetadata, updateMetadata>>.

`write`...FIXME

NOTE: `write` is used exclusively when `WriteIntoDelta` is requested to <<run, run>>.
