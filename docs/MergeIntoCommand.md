# MergeIntoCommand

MergeIntoCommand is a DeltaCommand.adoc[] that can...FIXME

MergeIntoCommand is a logical command (Spark SQL's `RunnableCommand`).

TIP: Read up on https://jaceklaskowski.gitbooks.io/mastering-spark-sql/spark-sql-LogicalPlan-RunnableCommand.html[RunnableCommand] in https://bit.ly/spark-sql-internals[The Internals of Spark SQL] online book.

== [[creating-instance]] Creating Instance

MergeIntoCommand takes the following to be created:

* [[source]] Source Data (`LogicalPlan`)
* [[target]] Target Data (`LogicalPlan`)
* [[targetFileIndex]] TahoeFileIndex.adoc[]
* [[condition]] Condition Expression
* [[matchedClauses]] Matched Clauses (`Seq[DeltaMergeIntoMatchedClause]`)
* [[notMatchedClause]] Optional Non-Matched Clause (`Option[DeltaMergeIntoInsertClause]`)
* [[migratedSchema]] Optional Migrated Schema

MergeIntoCommand is created when PreprocessTableMerge logical resolution rule is requested to PreprocessTableMerge.adoc#apply[execute].

== [[run]] Running Command

[source, scala]
----
run(
  spark: SparkSession): Seq[Row]
----

run...FIXME

run is part of the RunnableCommand (Spark SQL) abstraction.

== [[writeAllChanges]] writeAllChanges Internal Method

[source, scala]
----
writeAllChanges(
  spark: SparkSession,
  deltaTxn: OptimisticTransaction,
  filesToRewrite: Seq[AddFile]): Seq[AddFile]
----

writeAllChanges...FIXME

writeAllChanges is used when MergeIntoCommand is requested to <<run, run>>.

== [[findTouchedFiles]] findTouchedFiles Internal Method

[source, scala]
----
findTouchedFiles(
  deltaTxn: OptimisticTransaction,
  files: Seq[AddFile]): LogicalPlan
----

findTouchedFiles...FIXME

findTouchedFiles is used when MergeIntoCommand is requested to <<run, run>>.

== [[buildTargetPlanWithFiles]] buildTargetPlanWithFiles Internal Method

[source, scala]
----
buildTargetPlanWithFiles(
  deltaTxn: OptimisticTransaction,
  files: Seq[AddFile]): LogicalPlan
----

buildTargetPlanWithFiles...FIXME

buildTargetPlanWithFiles is used when MergeIntoCommand is requested to <<run, run>> (via <<findTouchedFiles, findTouchedFiles>> and <<writeAllChanges, writeAllChanges>>).
