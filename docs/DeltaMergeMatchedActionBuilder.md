= DeltaMergeMatchedActionBuilder

*DeltaMergeMatchedActionBuilder* is a merge action builder for ROOT:DeltaMergeBuilder.adoc#whenMatched[DeltaMergeBuilder.whenMatched] operator.

[source,plaintext]
----
scala> :type mergeBuilder
io.delta.tables.DeltaMergeBuilder

val mergeMatchedBuilder = mergeBuilder.whenMatched

scala> :type mergeMatchedBuilder
io.delta.tables.DeltaMergeMatchedActionBuilder
----

== [[creating-instance]] Creating Instance

DeltaMergeMatchedActionBuilder takes the following to be created:

* [[mergeBuilder]] ROOT:DeltaMergeBuilder.adoc[]
* [[matchCondition]] Optional match condition (`Column` expression)

DeltaMergeMatchedActionBuilder is created using <<apply, apply>> factory method.

== [[apply]] apply Factory Method

[source,scala]
----
apply(
  mergeBuilder: DeltaMergeBuilder,
  matchCondition: Option[Column]): DeltaMergeMatchedActionBuilder
----

apply creates a DeltaMergeMatchedActionBuilder (for the arguments given).

apply is used when DeltaMergeBuilder is requested to ROOT:DeltaMergeBuilder.adoc#whenMatched[whenMatched].
