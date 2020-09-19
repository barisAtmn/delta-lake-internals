= VacuumTableCommand

`VacuumTableCommand` is a logical command (`RunnableCommand`) for delta-sql.adoc#VACUUM[VACUUM] SQL command.

TIP: Read up on https://jaceklaskowski.gitbooks.io/mastering-spark-sql/spark-sql-LogicalPlan-RunnableCommand.html[RunnableCommand] in https://bit.ly/spark-sql-internals[The Internals of Spark SQL] online book.

`VacuumTableCommand` is <<creating-instance, created>> exclusively when `DeltaSqlAstBuilder` is requested to <<DeltaSqlAstBuilder.adoc#visitVacuumTable, parse VACUUM SQL command>>.

`VacuumTableCommand` <<run, requires>> that either the <<table, table>> or the <<path, path>> is defined and it is the root directory of a delta table. Partition directories are not supported.

[[output]]
The output of `VacuumTableCommand` is a single `path` column (of type `StringType`).

== [[creating-instance]] Creating VacuumTableCommand Instance

`VacuumTableCommand` takes the following to be created:

* [[path]] Path (optional)
* [[table]] `TableIdentifier` (optional)
* [[horizonHours]] Optional `horizonHours`
* [[dryRun]] `dryRun` flag

== [[run]] Running Command -- `run` Method

[source, scala]
----
run(sparkSession: SparkSession): Seq[Row]
----

NOTE: `run` is part of the `RunnableCommand` contract to...FIXME.

`run` takes the path to vacuum (i.e. either the <<table, table>> or the <<path, path>>) and <<DeltaTableUtils.adoc#findDeltaTableRoot, finds the root directory of the delta table>>.

`run` <<DeltaLog.adoc#forTable, creates a DeltaLog instance>> for the delta table and executes <<VacuumCommand.adoc#gc, VacuumCommand.gc>> utility (passing in the `DeltaLog` instance, the <<dryRun, dryRun>> and the <<horizonHours, horizonHours>> options).

`run` throws an `AnalysisException` when executed for a non-root directory of a delta table:

```
Please provide the base path ([baseDeltaPath]) when Vacuuming Delta tables. Vacuuming specific partitions is currently not supported.
```

`run` throws an `AnalysisException` when executed for a `DeltaLog` with the snapshot version being `-1`:

```
[deltaTableIdentifier] is not a Delta table. VACUUM is only supported for Delta tables.
```
