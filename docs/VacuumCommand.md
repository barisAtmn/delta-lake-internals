= VacuumCommand Utility -- Garbage Collecting Delta Table
:navtitle: VacuumCommand

*VacuumCommand* is a concrete <<VacuumCommandImpl.adoc#, VacuumCommandImpl>> for <<gc, gc>>.

== [[gc]] Garbage Collecting Of Delta Table -- gc Utility

[source, scala]
----
gc(
  spark: SparkSession,
  deltaLog: DeltaLog,
  dryRun: Boolean = true,
  retentionHours: Option[Double] = None,
  clock: Clock = new SystemClock): DataFrame
----

gc requests the given `DeltaLog` to <<DeltaLog.adoc#update, update>> (and give the latest <<Snapshot.adoc#, Snapshot>> of the delta table).

[[gc-deleteBeforeTimestamp]]
gc...FIXME (deleteBeforeTimestamp)

gc prints out the following INFO message to the logs:

```
Starting garbage collection (dryRun = [dryRun]) of untracked files older than [deleteBeforeTimestamp] in [path]
```

[[gc-validFiles]]
gc requests the `Snapshot` for the <<Snapshot.adoc#state, state dataset>> and defines a function for every action (in a partition) that does the following:

. FIXME

gc converts the mapped state dataset (of actions) into a `DataFrame` with a single `path` column.

[[gc-allFilesAndDirs]]
gc...FIXME

gc caches the <<gc-allFilesAndDirs, allFilesAndDirs>> dataset.

gc prints out the following INFO message to the logs:

```
Deleting untracked files and empty directories in [path]
```

gc...FIXME

gc prints out the following message to standard output:

```
Deleted [filesDeleted] files and directories in a total of [dirCounts] directories.
```

gc...FIXME

In the end, gc unpersists the <<gc-allFilesAndDirs, allFilesAndDirs>> dataset.

[NOTE]
====
gc is used when:

* `DeltaTableOperations` is requested to <<DeltaTableOperations.adoc#executeVacuum, execute vacuum command>> (for <<DeltaTable.adoc#vacuum, DeltaTable.vacuum>> operator)

* <<VacuumTableCommand.adoc#, VacuumTableCommand>> is executed (for delta-sql.adoc#VACUUM[VACUUM] SQL command)
====

== [[checkRetentionPeriodSafety]] `checkRetentionPeriodSafety` Method

[source, scala]
----
checkRetentionPeriodSafety(
  spark: SparkSession,
  retentionMs: Option[Long],
  configuredRetention: Long): Unit
----

`checkRetentionPeriodSafety`...FIXME

NOTE: `checkRetentionPeriodSafety` is used exclusively when VacuumCommand utility is requested to <<gc, gc>>.

== [[logging]] Logging

Enable `ALL` logging level for `org.apache.spark.sql.delta.commands.VacuumCommand` logger to see what happens inside.

Add the following line to `conf/log4j.properties`:

[source,plaintext]
----
log4j.logger.org.apache.spark.sql.delta.commands.VacuumCommand=ALL
----

Refer to logging.adoc[].
