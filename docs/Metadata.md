= Metadata

*Metadata* is an <<Action.adoc#, action>> that describes metadata (change) of a <<DeltaLog.adoc#metadata, delta table>> (indirectly via <<Snapshot.adoc#metadata, Snapshot>>).

[source,plaintext]
----
import org.apache.spark.sql.delta.DeltaLog
val deltaLog = DeltaLog.forTable(spark, "/tmp/delta/users")
scala> :type deltaLog.snapshot.metadata
org.apache.spark.sql.delta.actions.Metadata
----

Metadata contains all the non-data information (_metadata_) like <<name, name>>, <<description, description>>, <<format, format>>, <<schemaString, schema>>, <<partitionColumns, partition columns>>, <<configuration, table properties>> and <<createdTime, created time>>. These can be changed (e.g., schema evolution).

TIP: Use <<DescribeDeltaDetailCommand.adoc#, DescribeDeltaDetailCommand>> to review the metadata of a delta table.

Metadata uses <<id, id>> to uniquely identify a delta table. The ID is never going to change through the history of the table (unless the entire directory, along with the transaction log is deleted). It is known as *tableId* or <<DeltaSourceOffset.adoc#reservoirId, reservoirId>>.

[NOTE]
====
When I asked the question https://groups.google.com/forum/#!topic/delta-users/5OKEFvVKiew[tableId and reservoirId - Why two different names for metadata ID?] on delta-users mailing list, Tathagata Das wrote:

> Any reference to "reservoir" is just legacy code. In the early days of this project, the project was called "Tahoe" and each table is called a "reservoir" (Tahoe is one of the 2nd deepest lake in US, and is a very large reservoir of water ;) ). So you may still find those two terms all around the codebase.

> In some cases, like DeltaSourceOffset, the term `reservoirId` is in the json that is written to the streaming checkpoint directory. So we cannot change that for backward compatibility.

====

Metadata can be <<OptimisticTransactionImpl.adoc#updateMetadata, updated>> in a OptimisticTransactionImpl.adoc[transaction] once (and only when created for an uninitialized table, when <<OptimisticTransactionImpl.adoc#readVersion, readVersion>> is `-1`).

[source,scala]
----
txn.metadata
----

Metadata is <<creating-instance, created>> when:

* `DeltaLog` is requested for the <<DeltaLog.adoc#metadata, metadata>>

* `OptimisticTransactionImpl` is requested for the <<OptimisticTransactionImpl.adoc#snapshotMetadata, snapshotMetadata>>

* `ConvertToDeltaCommand` is requested to <<ConvertToDeltaCommand.adoc#performConvert, performConvert>>

* `ImplicitMetadataOperation` is requested to <<ImplicitMetadataOperation.adoc#updateMetadata, update a Metadata>>

== [[creating-instance]] Creating Metadata Instance

Metadata takes the following to be created:

* [[id]] Table ID (default: a random UUID)
* [[name]] Name of the delta table (default: `null`)
* [[description]] Description (default: `null`)
* [[format]] `Format`
* [[schemaString]] Schema (default: `null`)
* [[partitionColumns]] Partition columns (default: `Nil`)
* [[configuration]] Configuration (default: `empty`)
* [[createdTime]] Created time (in millis since the epoch)

== [[wrap]] `wrap` Method

[source, scala]
----
wrap: SingleAction
----

NOTE: `wrap` is part of the <<Action.adoc#wrap, Action>> contract to wrap the action into a <<SingleAction.adoc#, SingleAction>>.

`wrap` simply creates a new <<SingleAction.adoc#, SingleAction>> with the Metadata field set to this Metadata.

== [[partitionSchema]] `partitionSchema` (Lazy) Property

[source, scala]
----
partitionSchema: StructType
----

`partitionSchema` is the <<partitionColumns, partition columns>> as `StructFields` (and defined in the <<schemaString, schema>>).

NOTE: `partitionSchema` throws an `IllegalArgumentException` for undefined fields that were used for the <<partitionColumns, partition columns>> but not defined in the <<schemaString, schema>>.

NOTE: `partitionSchema` is used when...FIXME

== [[dataSchema]] `dataSchema` (Lazy) Property

[source, scala]
----
dataSchema: StructType
----

`dataSchema`...FIXME

NOTE: `dataSchema` is used when...FIXME

== [[schema]] `schema` (Lazy) Property

[source, scala]
----
schema: StructType
----

`schema` is a deserialized <<schemaString, schema>> (from JSON format) to `StructType`.

[NOTE]
====
`schema` is used when:

* Metadata is requested for the schema of the <<partitionSchema, partitions>> and the <<dataSchema, data>>

* `DeltaLog` is requested for an DeltaLog.adoc#createRelation[insertable HadoopFsRelation for batch queries] (for the data schema), to DeltaLog.adoc#upgradeProtocol[upgrade protocol], a DeltaLog.adoc#createDataFrame[DataFrame for given AddFiles]

* `DeltaTableUtils` utility is used to DeltaTableUtils.adoc#combineWithCatalogMetadata[combineWithCatalogMetadata]

* `OptimisticTransactionImpl` is requested to OptimisticTransactionImpl.adoc#verifyNewMetadata[verifyNewMetadata]

* ...FIXME (there are other uses)
====
