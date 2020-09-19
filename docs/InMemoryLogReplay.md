= InMemoryLogReplay -- Delta Log Replay

`InMemoryLogReplay` is used at the very last phase of <<Snapshot.adoc#stateReconstruction, state reconstruction>> (of a <<Snapshot.adoc#cachedState, cached delta state>>).

`InMemoryLogReplay` is <<creating-instance, created>> for every partition of the <<Snapshot.adoc#stateReconstruction, stateReconstruction>> dataset (`Dataset[SingleAction]`) that is based on the <<DeltaSQLConf.adoc#DELTA_SNAPSHOT_PARTITIONS, spark.databricks.delta.snapshotPartitions>> configuration property (default: `50`).

The lifecycle of `InMemoryLogReplay` is as follows:

. <<creating-instance, Created>> (with <<Snapshot.adoc#minFileRetentionTimestamp, Snapshot.minFileRetentionTimestamp>>)

. <<append, Append>> (with all <<SingleAction.adoc#, SingleActions>> of a partition)

. <<checkpoint, Checkpoint>>

== [[creating-instance]] Creating InMemoryLogReplay Instance

`InMemoryLogReplay` takes the following to be created:

* [[minFileRetentionTimestamp]] `minFileRetentionTimestamp` (that is exactly <<Snapshot.adoc#minFileRetentionTimestamp, Snapshot.minFileRetentionTimestamp>>)

`InMemoryLogReplay` initializes the <<internal-properties, internal properties>>.

== [[append]] Appending Actions -- `append` Method

[source, scala]
----
append(
  version: Long,
  actions: Iterator[Action]): Unit
----

`append` sets the <<currentVersion, currentVersion>> as the given `version`.

`append` adds <<Action.adoc#, actions>> to respective registries:

* Every <<SetTransaction.adoc#, SetTransaction>> is registered in the <<transactions, transactions>> by <<SetTransaction.adoc#appId, appId>>

* <<Metadata.adoc#, Metadata>> is registered as the <<currentMetaData, currentMetaData>>

* <<Protocol.adoc#, Protocol>> is registered as the <<currentProtocolVersion, currentProtocolVersion>>

* Every <<AddFile.adoc#, AddFile>> is registered as follows:
** Added to <<activeFiles, activeFiles>> by `pathAsUri` (with `dataChange` flag turned off)
** Removed from <<tombstones, tombstones>> by `pathAsUri`

* Every <<FileAction.adoc#RemoveFile, RemoveFile>> is registered as follows:
** Removed from <<activeFiles, activeFiles>> by `pathAsUri`
** Added to <<tombstones, tombstones>> by `pathAsUri` (with `dataChange` flag turned off)

* <<CommitInfo.adoc#, CommitInfos>> are ignored

`append` throws an `AssertionError` when the <<currentVersion, currentVersion>> is neither `-1` (the default) nor one before the given `version`:

```
Attempted to replay version [version], but state is at [currentVersion]
```

NOTE: `append` is used when `Snapshot` is created (and initializes the <<Snapshot.adoc#stateReconstruction, stateReconstruction>> for the <<Snapshot.adoc#cachedState, cached delta state>>).

== [[checkpoint]] Current State Of Delta Table -- `checkpoint` Method

[source, scala]
----
checkpoint: Iterator[Action]
----

`checkpoint` simply builds a sequence (`Iterator[Action]`) of the following (in that order):

* <<currentProtocolVersion, currentProtocolVersion>> if defined (non-``null``)

* <<currentMetaData, currentMetaData>> if defined (non-``null``)

* <<transactions, SetTransactions>>

* <<activeFiles, AddFiles>> and <<getTombstones, RemoveFiles>> (after the <<minFileRetentionTimestamp, minFileRetentionTimestamp>>) sorted by <<FileAction.adoc#path, path>> (lexicographically)

NOTE: `checkpoint` is used when `Snapshot` is created (and initializes the <<Snapshot.adoc#stateReconstruction, stateReconstruction>> for the <<Snapshot.adoc#cachedState, cached delta state>>).

== [[getTombstones]] `getTombstones` Internal Method

[source, scala]
----
getTombstones: Iterable[FileAction]
----

`getTombstones` returns <<FileAction.adoc#RemoveFile, RemoveFiles>> (from the <<tombstones, tombstones>>) with their `delTimestamp` after the <<minFileRetentionTimestamp, minFileRetentionTimestamp>>.

NOTE: `getTombstones` is used when `InMemoryLogReplay` is requested to <<checkpoint, checkpoint>>.

== [[internal-properties]] Internal Properties

[cols="30m,70",options="header",width="100%"]
|===
| Name
| Description

| currentProtocolVersion
a| [[currentProtocolVersion]] <<Protocol.adoc#, Protocol>> (default: `null`)

Used when...FIXME

| currentVersion
a| [[currentVersion]] Version (default: `-1`)

Used when...FIXME

| currentMetaData
a| [[currentMetaData]] <<Metadata.adoc#, Metadata>> (default: `null`)

Used when...FIXME

| transactions
a| [[transactions]] <<SetTransaction.adoc#, SetTransactions>> per ID (`HashMap[String, SetTransaction]`)

Used when...FIXME

| activeFiles
a| [[activeFiles]] <<AddFile.adoc#, AddFile>> per URI (`HashMap[URI, AddFile]`)

Used when...FIXME

| tombstones
a| [[tombstones]] <<FileAction.adoc.adoc#RemoveFile, RemoveFile>> per URI (`HashMap[URI, RemoveFile]`)

Used when...FIXME

|===
