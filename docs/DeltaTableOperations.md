= [[DeltaTableOperations]] DeltaTableOperations -- Delta DML Operations

[[self]]
`DeltaTableOperations` is the <<contract, abstraction>> of <<implementations, management services>> of a <<DeltaTable.adoc#, DeltaTable>> for executing <<executeDelete, delete>>, <<executeHistory, history>>, <<executeUpdate, update>>, and <<executeVacuum, vacuum>> commands.

[[implementations]]
NOTE: <<DeltaTable.adoc#, DeltaTable>> is the default and only known `DeltaTableOperations` in Delta Lake.

NOTE: <<executeDelete, Delete>> and <<executeUpdate, Update>> operations do not support subqueries (and <<subqueryNotSupportedCheck, throw an AnalysisException>> otherwise).

== [[executeGenerate]] Executing Generate Command -- `executeGenerate` Method

[source, scala]
----
executeGenerate(
  tblIdentifier: String,
  mode: String): Unit
----

`executeGenerate` requests the SQL parser (of the `SparkSession`) to parse the given table identifier, creates a <<DeltaGenerateCommand.adoc#, DeltaGenerateCommand>> and runs it.

NOTE: `executeGenerate` is used in <<DeltaTable.adoc#generate, DeltaTable.generate>> operator.

== [[executeDelete]] Executing Delete Command -- `executeDelete` Method

[source, scala]
----
executeDelete(
  condition: Option[Expression]): Unit
----

`executeDelete` creates a `QueryExecution` for the `Delete` logical operator with (the analyzed logical plan of) the <<self, DeltaTable>>. `executeDelete` requests the `QueryExecution` for the analyzed logical plan that is (_again?!_) a `Delete` unary node.

NOTE: FIXME What's the purpose of all this resolutions?

In the end, `executeDelete` simply creates a <<DeleteCommand.adoc#, DeleteCommand>> (for the resolved delete) and <<DeleteCommand.adoc#run, executes it>>.

`executeDelete` <<subqueryNotSupportedCheck, throws an AnalysisException>> when the `condition` expression contains subqueries.

NOTE: `executeDelete` is used in <<DeltaTable.adoc#delete, DeltaTable.delete>> operator.

== [[executeHistory]] `executeHistory` Method

[source, scala]
----
executeHistory(
  deltaLog: DeltaLog,
  limit: Option[Int]): DataFrame
----

`executeHistory`...FIXME

NOTE: `executeHistory` is used when...FIXME

== [[executeUpdate]] `executeUpdate` Method

[source, scala]
----
executeUpdate(
  set: Map[String, Column],
  condition: Option[Column]): Unit
----

`executeUpdate`...FIXME

NOTE: `executeUpdate` is used when...FIXME

== [[executeVacuum]] Executing Vacuum Command -- `executeVacuum` Method

[source, scala]
----
executeVacuum(
  deltaLog: DeltaLog,
  retentionHours: Option[Double]): DataFrame
----

`executeVacuum` simply uses the `VacuumCommand` utility to <<VacuumCommand.adoc#gc, gc>> (with the `dryRun` flag off) and returns an empty `DataFrame`.

NOTE: `executeVacuum` returns an empty `DataFrame` not the one from <<VacuumCommand.adoc#gc, VacuumCommand.gc>>.

NOTE: `executeVacuum` is used exclusively in <<DeltaTable.adoc#vacuum, DeltaTable.vacuum>> operator.

== [[makeUpdateTable]] `makeUpdateTable` Method

[source, scala]
----
makeUpdateTable(
  target: DeltaTable,
  onCondition: Option[Column],
  setColumns: Seq[(String, Column)]): UpdateTable
----

`makeUpdateTable`...FIXME

NOTE: `makeUpdateTable` is used when...FIXME

== [[subqueryNotSupportedCheck]] `subqueryNotSupportedCheck` Internal Method

[source, scala]
----
subqueryNotSupportedCheck(
  condition: Option[Expression],
  op: String): Unit
----

`subqueryNotSupportedCheck` traverses the `condition` expression and throws an `AnalysisException` when it finds a `SubqueryExpression`:

```
Subqueries are not supported in the [op] (condition = [sql]).
```

NOTE: `subqueryNotSupportedCheck` is used when `DeltaTableOperations` is requested to execute <<executeDelete, delete>> and <<executeUpdate, update>> operations.
