= [[TahoeLogFileIndex]] TahoeLogFileIndex

`TahoeLogFileIndex` is a concrete <<TahoeFileIndex.adoc#, file index>>.

`TahoeLogFileIndex` is <<creating-instance, created>> when `DeltaLog` is requested for a <<DeltaLog.adoc#createRelation, relation>> (when `DeltaDataSource` is requested for one as a <<DeltaDataSource.adoc#CreatableRelationProvider, CreatableRelationProvider>> and a <<DeltaDataSource.adoc#RelationProvider, RelationProvider>>).

```
val q = spark.read.format("delta").load("/tmp/delta/users")
val plan = q.queryExecution.executedPlan

import org.apache.spark.sql.execution.FileSourceScanExec
val scan = plan.collect { case e: FileSourceScanExec => e }.head

import org.apache.spark.sql.delta.files.TahoeLogFileIndex
val index = scan.relation.location.asInstanceOf[TahoeLogFileIndex]
scala> println(index)
Delta[version=1, file:/tmp/delta/users]
```

== [[creating-instance]] Creating TahoeLogFileIndex Instance

`TahoeLogFileIndex` takes the following to be created:

* [[spark]] `SparkSession`
* [[deltaLog]] <<DeltaLog.adoc#, DeltaLog>>
* [[dataPath]] Data directory of the delta table (as Hadoop https://hadoop.apache.org/docs/r2.6.5/api/org/apache/hadoop/fs/Path.html[Path])
* [[partitionFilters]] Partition filters (default: `empty`) (as Catalyst expressions, i.e. `Seq[Expression]`)
* [[versionToUse]] Snapshot version (default: `undefined`) (`Option[Long]`)

`TahoeLogFileIndex` initializes the <<internal-properties, internal properties>>.

== [[partitionSchema]] Schema of Partition Columns -- `partitionSchema` Method

[source, scala]
----
partitionSchema: StructType
----

NOTE: `partitionSchema` is part of the `FileIndex` contract (Spark SQL) to get the schema of the partition columns (if used).

`partitionSchema`...FIXME

== [[matchingFiles]] `matchingFiles` Method

[source, scala]
----
matchingFiles(
  partitionFilters: Seq[Expression],
  dataFilters: Seq[Expression],
  keepStats: Boolean = false): Seq[AddFile]
----

NOTE: `matchingFiles` is part of the <<TahoeFileIndex.adoc#matchingFiles, TahoeFileIndex Contract>> for the AddFile.adoc[files] matching given predicates.

`matchingFiles` <<getSnapshot, gets the snapshot>> (with `stalenessAcceptable` flag off) and requests it for the <<PartitionFiltering.adoc#filesForScan, files to scan>> (for the index's <<partitionFilters, partition filters>>, the given `partitionFilters` and `dataFilters`).

NOTE: <<inputFiles, inputFiles>> and <<matchingFiles, matchingFiles>> are similar. Both <<getSnapshot, get the snapshot>> (of the delta table), but they use different filtering expressions and return value types.

== [[inputFiles]] `inputFiles` Method

[source, scala]
----
inputFiles: Array[String]
----

NOTE: `inputFiles` is part of the `FileIndex` contract to...FIXME

`inputFiles` <<getSnapshot, gets the snapshot>> (with `stalenessAcceptable` flag off) and requests it for the <<PartitionFiltering.adoc#filesForScan, files to scan>> (for the index's <<partitionFilters, partition filters>>).

NOTE: <<inputFiles, inputFiles>> and <<matchingFiles, matchingFiles>> are similar. Both <<getSnapshot, get the snapshot>> (of the delta table), but they use different filtering expressions and return value types.

== [[getSnapshot]] Historical Or Latest Snapshot -- `getSnapshot` Method

[source, scala]
----
getSnapshot(
  stalenessAcceptable: Boolean): Snapshot
----

`getSnapshot` returns a <<Snapshot.adoc#, Snapshot>> that is either the <<historicalSnapshotOpt, historical snapshot>> (for the <<versionToUse, snapshot version>> if defined) or requests the <<deltaLog, DeltaLog>> to <<DeltaLog.adoc#update, update>> (and give one).

NOTE: `getSnapshot` is used when `TahoeLogFileIndex` is requested for the <<matchingFiles, matching files>> and the <<inputFiles, input files>>.

== [[sizeInBytes]] `sizeInBytes` Property

[source, scala]
----
sizeInBytes: Long
----

NOTE: `sizeInBytes` is part of the `FileIndex` contract for the table size (in bytes).

`sizeInBytes`...FIXME

== [[internal-properties]] Internal Properties

[cols="30m,70",options="header",width="100%"]
|===
| Name
| Description

| historicalSnapshotOpt
a| [[historicalSnapshotOpt]] *Historical snapshot*, i.e. <<Snapshot.adoc#, Snapshot>> for the <<versionToUse, versionToUse>> (if defined)

Used when `TahoeLogFileIndex` is requested for the <<getSnapshot, (historical or latest) snapshot>> and the <<partitionSchema, schema of the partition columns>>

|===
