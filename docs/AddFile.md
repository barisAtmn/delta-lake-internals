= AddFile

`AddFile` is a <<FileAction.adoc#, file action>> to denote a <<path, file>> added to a <<DeltaLog.adoc#, delta table>>.

`AddFile` is <<creating-instance, created>> when:

* <<ConvertToDeltaCommand.adoc#, ConvertToDeltaCommand>> is executed (for <<ConvertToDeltaCommand.adoc#createAddFile, every data file to import>>)

* `DelayedCommitProtocol` is requested to <<DelayedCommitProtocol.adoc#commitTask, commit a task (after successful write)>> (for <<TransactionalWrite.adoc#, optimistic transactional writers>>)

== [[creating-instance]] Creating AddFile Instance

`AddFile` takes the following to be created:

* [[path]] Path
* [[partitionValues]] Partition values (`Map[String, String]`)
* [[size]] Size (in bytes)
* [[modificationTime]] Modification time
* [[dataChange]] `dataChange` flag
* [[stats]] Stats (default: `null`)
* [[tags]] Tags (`Map[String, String]`) (default: `null`)

== [[wrap]] `wrap` Method

[source, scala]
----
wrap: SingleAction
----

NOTE: `wrap` is part of the <<Action.adoc#wrap, Action>> contract to wrap the action into a <<SingleAction.adoc#, SingleAction>> for serialization.

`wrap` simply creates a new <<SingleAction.adoc#, SingleAction>> with the `add` field set to this `AddFile`.

== [[remove]] Creating RemoveFile Instance With Current Timestamp -- `remove` Method

[source, scala]
----
remove: RemoveFile
----

`remove` <<removeWithTimestamp, creates a RemoveFile action>> with the current timestamp and `dataChange` flag enabled.

[NOTE]
====
`remove` is used when:

* <<MergeIntoCommand.adoc#, MergeIntoCommand>> is executed

* <<WriteIntoDelta.adoc#, WriteIntoDelta>> is executed (with `Overwrite` mode)

* `DeltaSink` is requested to <<DeltaSink.adoc#addBatch, add a streaming micro-batch>> (with `Complete` mode)
====

== [[removeWithTimestamp]] Creating RemoveFile Instance For Given Timestamp -- `removeWithTimestamp` Method

[source, scala]
----
removeWithTimestamp(
  timestamp: Long = System.currentTimeMillis(),
  dataChange: Boolean = true): RemoveFile
----

`removeWithTimestamp` creates a <<RemoveFile.adoc#, RemoveFile>> action for the <<path, path>>, and the given `timestamp` and `dataChange` flag.

[NOTE]
====
`removeWithTimestamp` is used when:

* `AddFile` is requested to <<remove, create a RemoveFile action with the current timestamp>>

* <<DeleteCommand.adoc#, DeleteCommand>> and <<UpdateCommand.adoc#, UpdateCommand>> are executed
====
