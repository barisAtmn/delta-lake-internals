= Operation

*Operation* is an <<contract, abstraction>> of <<implementations, operations>> that can be performed on a Delta table.

Operation has a <<name, name>> and <<parameters, parameters>> (that are simply used to create a CommitInfo.adoc[] for OptimisticTransactionImpl being OptimisticTransactionImpl.adoc#commit[committed] and, as a way to bypass a transaction, ConvertToDeltaCommand.adoc[]).

== [[contract]] Contract

=== [[name]] name

[source,scala]
----
name: String
----

Name of the operation (to create a CommitInfo.adoc[])

Used when:

* OptimisticTransactionImpl is requested to OptimisticTransactionImpl.adoc#commit[commit]

* ConvertToDeltaCommand command is requested to ConvertToDeltaCommand.adoc#streamWrite[streamWrite] (when executed)

=== [[parameters]] parameters

[source,scala]
----
parameters: Map[String, Any]
----

Parameters of the operation (to create a CommitInfo.adoc[] with the <<jsonEncodedValues, JSON-encoded values>>)

Used when Operation is requested for <<jsonEncodedValues, parameters with the values in JSON format>>.

== [[implementations]] Operations

Operation is a Scala *sealed trait* which means that all the implementations are in the same compilation unit (a single file).

=== [[AddColumns]] AddColumns

=== [[ChangeColumn]] ChangeColumn

=== [[ComputeStats]] ComputeStats

=== [[Convert]] Convert

=== [[CreateTable]] CreateTable

=== [[Delete]] Delete

=== [[FileNotificationRetention]] FileNotificationRetention

=== [[Fsck]] Fsck

=== [[ManualUpdate]] ManualUpdate

<<name, name>>: *Manual Update*

<<parameters, parameters>>: Empty

=== [[Merge]] Merge

=== [[Optimize]] Optimize

=== [[ReplaceColumns]] ReplaceColumns

=== [[ReplaceTable]] ReplaceTable

=== [[ResetZCubeInfo]] ResetZCubeInfo

=== [[SetTableProperties]] SetTableProperties

=== [[StreamingUpdate]] StreamingUpdate

=== [[Truncate]] Truncate

=== [[UnsetTableProperties]] UnsetTableProperties

=== [[Update]] Update

=== [[UpdateColumnMetadata]] UpdateColumnMetadata

=== [[UpdateSchema]] UpdateSchema

=== [[UpgradeProtocol]] UpgradeProtocol

=== [[Write]] Write

== [[jsonEncodedValues]] Serializing Parameter Values (to JSON Format)

[source,scala]
----
jsonEncodedValues: Map[String, String]
----

jsonEncodedValues converts the values of the <<parameters, parameters>> to JSON format.

jsonEncodedValues is used when:

* OptimisticTransactionImpl is requested to OptimisticTransactionImpl.adoc#commit[commit] (to create a CommitInfo.adoc[])

* ConvertToDeltaCommand command is requested to ConvertToDeltaCommand.adoc#streamWrite[streamWrite] (when executed and creates a CommitInfo.adoc[])

== [[operationMetrics]] operationMetrics Method

[source,scala]
----
operationMetrics: Set[String]
----

operationMetrics...FIXME

operationMetrics is used when...FIXME

== [[transformMetrics]] transformMetrics Method

[source,scala]
----
transformMetrics(
  metrics: Map[String, SQLMetric]): Map[String, String]
----

transformMetrics...FIXME

transformMetrics is used when...FIXME

== [[userMetadata]] userMetadata Method

[source,scala]
----
userMetadata: Option[String] = None
----

userMetadata of the operation.

userMetadata is used when OptimisticTransactionImpl is requested to OptimisticTransactionImpl.adoc#getUserMetadata[getUserMetadata].
