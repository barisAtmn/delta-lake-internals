= OptimisticTransaction

*OptimisticTransaction* is an OptimisticTransactionImpl.adoc[] (which _seems_ more of a class name change than anything more important).

OptimisticTransaction is <<creating-instance, created>> for changes to a <<deltaLog, delta table>> at a given <<snapshot, version>>.

When OptimisticTransaction (as a <<OptimisticTransactionImpl.adoc#, OptimisticTransactionImpl>>) is attempted to be <<OptimisticTransactionImpl.adoc#commit, committed>> (that does <<OptimisticTransactionImpl.adoc#doCommit, doCommit>> internally), the <<LogStore.adoc#, LogStore>> (of the <<deltaLog, DeltaLog>>) is requested to <<LogStore.adoc#write, write actions to a delta file>>, e.g. `_delta_log/00000000000000000001.json` for the attempt version `1`. Only when a `FileAlreadyExistsException` is thrown a commit is considered unsuccessful and <<OptimisticTransactionImpl.adoc#checkAndRetry, retried>>.

OptimisticTransaction can be associated with a thread as an <<active, active transaction>>.

== [[creating-instance]] Creating Instance

OptimisticTransaction takes the following to be created:

* [[deltaLog]] DeltaLog.adoc[]
* [[snapshot]] Snapshot.adoc[]
* [[clock]] Clock

NOTE: The <<deltaLog, DeltaLog>> and <<snapshot, Snapshot>> are part of the <<OptimisticTransactionImpl.adoc#, OptimisticTransactionImpl>> contract (which in turn inherits them as a TransactionalWrite.adoc[] and changes to `val` from `def`).

OptimisticTransaction is created when DeltaLog is used for the following:

* DeltaLog.adoc#startTransaction[Starting a new transaction]

* DeltaLog.adoc#withNewTransaction[Executing a single-threaded operation (in a new transaction)] (for <<DeleteCommand.adoc#, DeleteCommand>>, <<MergeIntoCommand.adoc#, MergeIntoCommand>>, <<UpdateCommand.adoc#, UpdateCommand>>, and <<WriteIntoDelta.adoc#, WriteIntoDelta>> commands as well as for <<DeltaSink.adoc#, DeltaSink>> for <<DeltaSink.adoc#addBatch, adding a streaming micro-batch>>)

== [[active]] Active Thread-Local OptimisticTransaction

[source, scala]
----
active: ThreadLocal[OptimisticTransaction]
----

`active` is a Java https://docs.oracle.com/javase/8/docs/api/java/lang/ThreadLocal.html[ThreadLocal] with the <<OptimisticTransaction.adoc#, OptimisticTransaction>> of the current thread.

> *ThreadLocal* provides thread-local variables. These variables differ from their normal counterparts in that each thread that accesses one (via its get or set method) has its own, independently initialized copy of the variable.

> *ThreadLocal* instances are typically private static fields in classes that wish to associate state with a thread (e.g., a user ID or Transaction ID).

`active` is assigned to the current thread using <<setActive, setActive>> utility and cleared in <<clearActive, clearActive>>.

`active` is available using <<getActive, getActive>> utility.

There can only be one active OptimisticTransaction (or an `IllegalStateException` is thrown).

== [[utilities]] Utilities

=== [[setActive]] setActive

[source, scala]
----
setActive(
  txn: OptimisticTransaction): Unit
----

setActive simply associates the given OptimisticTransaction as <<active, active>> with the current thread.

setActive throws an `IllegalStateException` if there is an active OptimisticTransaction already associated:

```
Cannot set a new txn as active when one is already active
```

setActive is used when `DeltaLog` is requested to <<DeltaLog.adoc#withNewTransaction, execute an operation in a new transaction>>.

=== [[clearActive]] clearActive

[source, scala]
----
clearActive(): Unit
----

clearActive simply clears the <<active, active>> transaction (so no transaction is associated with a thread).

clearActive is used when `DeltaLog` is requested to <<DeltaLog.adoc#withNewTransaction, execute an operation in a new transaction>>.

=== [[getActive]] getActive

[source, scala]
----
getActive(): Option[OptimisticTransaction]
----

getActive simply returns the <<active, active>> transaction.

getActive _seems_ unused.

== [[logging]] Logging

Enable `ALL` logging level for `org.apache.spark.sql.delta.OptimisticTransaction` logger to see what happens inside.

Add the following line to `conf/log4j.properties`:

[source,plaintext]
----
log4j.logger.org.apache.spark.sql.delta.OptimisticTransaction=ALL
----

Refer to ROOT:logging.adoc[].

== [[demo]] Demo

[source,scala]
----
import org.apache.spark.sql.delta.DeltaLog
val dir = "/tmp/delta/users"
val log = DeltaLog.forTable(spark, dir)

val txn = log.startTransaction()

// ...changes to a delta table...
val addFile = AddFile("foo", Map.empty, 1L, System.currentTimeMillis(), dataChange = true)
val removeFile = addFile.remove
val actions = addFile :: removeFile :: Nil

txn.commit(actions, op)

// You could do the following instead

deltaLog.withNewTransaction { txn =>
  // ...transactional changes to a delta table
}
----
