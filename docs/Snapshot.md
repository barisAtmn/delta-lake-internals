# Snapshot

**Snapshot** is an immutable snapshot of the <<state, state>> of a <<deltaLog, delta table>> at <<version, some version>>.

Snapshot is <<creating-instance, created>> when `DeltaLog` is requested for the <<DeltaLog.adoc#currentSnapshot, current snapshot>> or <<DeltaLog.adoc#getSnapshotAt, at a given version>>, and to <<DeltaLog.adoc#update, update>>.

Snapshot can be requested for <<allFiles, all data files>>.

[source, scala]
----
scala> deltaLog.snapshot.allFiles.show(false)
+-------------------------------------------------------------------+---------------+----+----------------+----------+-----+----+
|path                                                               |partitionValues|size|modificationTime|dataChange|stats|tags|
+-------------------------------------------------------------------+---------------+----+----------------+----------+-----+----+
|part-00000-4050db39-e0f5-485d-ab3b-3ca72307f621-c000.snappy.parquet|[]             |262 |1578083748000   |false     |null |null|
|part-00000-ba39f292-2970-4528-a40c-8f0aa5f796de-c000.snappy.parquet|[]             |262 |1578083570000   |false     |null |null|
|part-00003-99f9d902-24a7-4f76-a15a-6971940bc245-c000.snappy.parquet|[]             |429 |1578083748000   |false     |null |null|
|part-00007-03d987f1-5bb3-4b5b-8db9-97b6667107e2-c000.snappy.parquet|[]             |429 |1578083748000   |false     |null |null|
|part-00011-a759a8c2-507d-46dd-9da7-dc722316214b-c000.snappy.parquet|[]             |429 |1578083748000   |false     |null |null|
|part-00015-2e685d29-25ed-4262-90a7-5491847fd8d0-c000.snappy.parquet|[]             |429 |1578083748000   |false     |null |null|
|part-00015-ee0ac1af-e1e0-4422-8245-12da91ced0a2-c000.snappy.parquet|[]             |429 |1578083570000   |false     |null |null|
+-------------------------------------------------------------------+---------------+----+----------------+----------+-----+----+
----

Snapshot can be requested for <<tombstones, removed data files>> (aka _tombstones_).

[source, scala]
----
scala> deltaLog.snapshot.tombstones.show(false)
+----+-----------------+----------+
|path|deletionTimestamp|dataChange|
+----+-----------------+----------+
+----+-----------------+----------+
----

Snapshot uses the <<DeltaSQLConf.adoc#DELTA_SNAPSHOT_PARTITIONS, spark.databricks.delta.snapshotPartitions>> internal configuration property (default: `50`) for the number of partition for <<stateReconstruction, state reconstruction>>.

== [[creating-instance]] Creating Snapshot Instance

Snapshot takes the following to be created:

* [[path]] Hadoop https://hadoop.apache.org/docs/r2.6.5/api/org/apache/hadoop/fs/Path.html[Path] to the <<DeltaLog.adoc#logPath, log directory>>
* [[version]] Version
* [[previousSnapshot]] Previous snapshot (`Option[Dataset[SingleAction]]`)
* [[files]] Files (`Seq[Path]`)
* [[minFileRetentionTimestamp]] `minFileRetentionTimestamp` (that is exactly <<DeltaLog.adoc#minFileRetentionTimestamp, DeltaLog.minFileRetentionTimestamp>>)
* [[deltaLog]] <<DeltaLog.adoc#, DeltaLog>>
* [[timestamp]] Timestamp
* [[lineageLength]] Length of the lineage (default: `1`)

Snapshot initializes the <<internal-properties, internal properties>>.

While being created, Snapshot requests the <<deltaLog, DeltaLog>> to <<DeltaLog.adoc#protocolRead, protocolRead>> with the <<protocol, protocol>>.

== [[state]] `state` Method

[source, scala]
----
state: Dataset[SingleAction]
----

`state` simply requests the <<cachedState, cached delta state>> to <<CachedDS.adoc#getDS, get the delta state from the cache>>.

[NOTE]
====
`state` is used when:

* `DeltaLog` is requested to <<DeltaLog.adoc#update, update>> (and in turn <<DeltaLog.adoc#updateInternal, updateInternal>>)

* `Checkpoints` is requested to <<Checkpoints.adoc#checkpoint, checkpoint>> (and in turn <<Checkpoints.adoc#writeCheckpoint, writeCheckpoint>>)

* Snapshot is created, and requested for <<allFiles, allFiles>> and <<tombstones, tombstones>>

* `VacuumCommand` is requested for <<VacuumCommand.adoc#gc, garbage collecting of a delta table>>
====

== [[allFiles]] All AddFiles -- `allFiles` Method

[source, scala]
----
allFiles: Dataset[AddFile]
----

`allFiles` simply takes the <<state, state dataset>> and selects AddFile.adoc[AddFiles] (adds `where` clause for `add IS NOT NULL` and `select` over the fields of AddFile.adoc[AddFiles]).

NOTE: `allFiles` simply adds `where` and `select` clauses. No computation as it is (a description of) a distributed computation as a `Dataset[AddFile]`.

[source, scala]
----
import org.apache.spark.sql.delta.DeltaLog
val deltaLog = DeltaLog.forTable(spark, "/tmp/delta/users")
val files = deltaLog.snapshot.allFiles

scala> :type files
org.apache.spark.sql.Dataset[org.apache.spark.sql.delta.actions.AddFile]

scala> files.show
+--------------------+---------------+----+----------------+----------+-----+----+
|                path|partitionValues|size|modificationTime|dataChange|stats|tags|
+--------------------+---------------+----+----------------+----------+-----+----+
|part-00000-68a7ce...|             []| 875|   1579789902000|     false| null|null|
|part-00000-73e140...|             []| 419|   1579723382000|     false| null|null|
|part-00000-7a01b0...|             []| 875|   1579877119000|     false| null|null|
|part-00001-8a2ece...|             []| 875|   1579789902000|     false| null|null|
|part-00002-0fc3da...|             []| 866|   1579789902000|     false| null|null|
|part-00003-c0fc5f...|             []| 884|   1579789902000|     false| null|null|
+--------------------+---------------+----+----------------+----------+-----+----+
----

[NOTE]
====
`allFiles` is used when:

* `PartitionFiltering` is requested for the <<PartitionFiltering.adoc#filesForScan, files to scan (matching projection attributes and predicates)>>

* `DeltaSourceSnapshot` is requested for the <<DeltaSourceSnapshot.adoc#initialFiles, initial files>> (indexed AddFile.adoc[AddFiles])

* `GenerateSymlinkManifestImpl` is requested to <<GenerateSymlinkManifest.adoc#generateIncrementalManifest, generateIncrementalManifest>> and <<GenerateSymlinkManifest.adoc#generateFullManifest, generateFullManifest>>

* `DeltaDataSource` is requested for an <<DeltaDataSource.adoc#RelationProvider-createRelation, insertable HadoopFsRelation for batch queries>>
====

== [[stateReconstruction]] `stateReconstruction` Internal Property

[source, scala]
----
stateReconstruction: Dataset[SingleAction]
----

`stateReconstruction` is a dataset of <<SingleAction.adoc#, SingleActions>> (that is the <<CachedDS.adoc#ds, dataset>> part) of the <<cachedState, cachedState>>.

== [[emptyActions]] `emptyActions` Internal Method

[source, scala]
----
emptyActions: Dataset[SingleAction]
----

`emptyActions` is an empty dataset of <<SingleAction.adoc#, SingleActions>> for <<stateReconstruction, stateReconstruction>> and <<load, load>>.

== [[load]] `load` Internal Method

[source, scala]
----
load(
  files: Seq[DeltaLogFileIndex]): Dataset[SingleAction]
----

`load`...FIXME

NOTE: `load` is used when Snapshot is created (and initializes <<stateReconstruction, stateReconstruction>>).

== [[transactions]] Transaction Version By App ID -- `transactions` Lookup Table

[source, scala]
----
transactions: Map[String, Long]
----

`transactions` takes the <<setTransactions, SetTransaction>> actions (from the <<state, state>> dataset) and makes them a lookup table of <<SetTransaction.adoc#version, transaction version>> by <<SetTransaction.adoc#appId, appId>>.

NOTE: `transactions` is a Scala lazy value and is not initialized until the first access.

NOTE: `transactions` is used when `OptimisticTransactionImpl` is requested for the <<OptimisticTransactionImpl.adoc#txnVersion, transaction version for a given (streaming query) id>>.

== [[tombstones]] `tombstones` Method

[source, scala]
----
tombstones: Dataset[RemoveFile]
----

`tombstones`...FIXME

NOTE: `tombstones` seems to be used for testing only.

== [[redactedPath]] `redactedPath` Method

[source, scala]
----
redactedPath: String
----

`redactedPath`...FIXME

NOTE: `redactedPath` is used...FIXME

== [[numIndexedCols]] dataSkippingNumIndexedCols Table Property -- `numIndexedCols` Value

[source, scala]
----
numIndexedCols: Int
----

`numIndexedCols` simply reads the <<DeltaConfigs.adoc#DATA_SKIPPING_NUM_INDEXED_COLS, dataSkippingNumIndexedCols>> table property <<DeltaConfigs.adoc#fromMetaData, from>> the <<metadata, Metadata>>.

NOTE: `numIndexedCols` seems unused.

== [[internal-properties]] Internal Properties

[cols="30m,70",options="header",width="100%"]
|===
| Name
| Description

| cachedState
a| [[cachedState]] <<CachedDS.adoc#, Cached Delta State>> that is made up of the following:

* The <<CachedDS.adoc#ds, dataset>> part is the <<stateReconstruction, stateReconstruction>> dataset of <<SingleAction.adoc#, SingleActions>>

* The <<CachedDS.adoc#name, name>> in the format *Delta Table State #version - [redactedPath]* (with the <<version, version>> and the <<redactedPath, redacted path>>)

Used when Snapshot is requested for the <<state, state>> (i.e. `Dataset[SingleAction]`)

| metadata
a| [[metadata]] <<Metadata.adoc#, Metadata>> of the current <<state, state>> of the <<deltaLog, delta table>>

| protocol
a| [[protocol]] <<Protocol.adoc#, Protocol>> of the current <<state, state>> of the <<deltaLog, delta table>>

| setTransactions
a| [[setTransactions]] <<SetTransaction.adoc#, SetTransactions>> of the current <<state, state>> of the <<deltaLog, delta table>>

|===
