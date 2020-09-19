= Checkpoints

`Checkpoints` is an <<contract, abstraction>> of <<implementations, DeltaLogs>> that can <<checkpoint, checkpoint>> the current state of a delta table (represented by the <<self, DeltaLog>>).

[[contract]]
.Checkpoints Contract (Abstract Methods Only)
[cols="30m,70",options="header",width="100%"]
|===
| Method
| Description

| logPath
a| [[logPath]]

[source, scala]
----
logPath: Path
----

Used when...FIXME

| dataPath
a| [[dataPath]]

[source, scala]
----
dataPath: Path
----

Used when...FIXME

| snapshot
a| [[snapshot]]

[source, scala]
----
snapshot: Snapshot
----

Used when...FIXME

| store
a| [[store]]

[source, scala]
----
store: LogStore
----

Used when...FIXME

| metadata
a| [[metadata]]

[source, scala]
----
metadata: Metadata
----

<<Metadata.adoc#, Metadata>> of (the current state of) the <<self, delta table>>

Used when...FIXME

| doLogCleanup
a| [[doLogCleanup]]

[source, scala]
----
doLogCleanup(): Unit
----

Used when...FIXME

|===

[[implementations]][[self]]
NOTE: <<DeltaLog.adoc#, DeltaLog>> is the default and only known `Checkpoints` in Delta Lake.

== [[LAST_CHECKPOINT]][[_last_checkpoint]] _last_checkpoint Metadata File

`Checkpoints` uses *_last_checkpoint* metadata file (under the <<DeltaLog.adoc#logPath, log path>>) for the following:

* <<checkpoint, Writing checkpoint metadata out>>

* <<loadMetadataFromFile, Loading checkpoint metadata in>>

== [[checkpoint]] Checkpointing -- `checkpoint` Method

[source, scala]
----
checkpoint(): Unit
checkpoint(
  snapshotToCheckpoint: Snapshot): CheckpointMetaData
----

`checkpoint`...FIXME

NOTE: `checkpoint` is used when...FIXME

== [[lastCheckpoint]] `lastCheckpoint` Method

[source, scala]
----
lastCheckpoint: Option[CheckpointMetaData]
----

`lastCheckpoint` simply <<loadMetadataFromFile, loadMetadataFromFile>> (with 0 `tries`).

NOTE: `lastCheckpoint` is used when...FIXME

== [[manuallyLoadCheckpoint]] `manuallyLoadCheckpoint` Method

[source, scala]
----
manuallyLoadCheckpoint(cv: CheckpointInstance): CheckpointMetaData
----

`manuallyLoadCheckpoint`...FIXME

NOTE: `manuallyLoadCheckpoint` is used when...FIXME

== [[findLastCompleteCheckpoint]] `findLastCompleteCheckpoint` Method

[source, scala]
----
findLastCompleteCheckpoint(cv: CheckpointInstance): Option[CheckpointInstance]
----

`findLastCompleteCheckpoint`...FIXME

NOTE: `findLastCompleteCheckpoint` is used when...FIXME

== [[getLatestCompleteCheckpointFromList]] `getLatestCompleteCheckpointFromList` Method

[source, scala]
----
getLatestCompleteCheckpointFromList(
  instances: Array[CheckpointInstance],
  notLaterThan: CheckpointInstance): Option[CheckpointInstance]
----

`getLatestCompleteCheckpointFromList`...FIXME

NOTE: `getLatestCompleteCheckpointFromList` is used when...FIXME

== [[writeCheckpoint]] `writeCheckpoint` Utility

[source, scala]
----
writeCheckpoint(
  spark: SparkSession,
  deltaLog: DeltaLog,
  snapshot: Snapshot): CheckpointMetaData
----

`writeCheckpoint`...FIXME

NOTE: `writeCheckpoint` is used when `Checkpoints` is requested to <<checkpoint, checkpoint>>.

== [[loadMetadataFromFile]] `loadMetadataFromFile` Internal Method

[source, scala]
----
loadMetadataFromFile(tries: Int): Option[CheckpointMetaData]
----

`loadMetadataFromFile`...FIXME

NOTE: `loadMetadataFromFile` is used when...FIXME
