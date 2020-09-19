== [[LogStoreProvider]] LogStoreProvider

`LogStoreProvider` is an abstraction of <<implementations, providers>> of <<createLogStore, LogStores>>.

[[logStoreClassConfKey]][[defaultLogStoreClass]][[spark.delta.logStore.class]]
`LogStoreProvider` uses the *spark.delta.logStore.class* configuration property (default: <<HDFSLogStore.adoc#, HDFSLogStore>>) for the fully-qualified class name of the <<LogStore.adoc#, LogStore>> to <<createLogStore, create>> (for a <<DeltaLog.adoc#, DeltaLog>>, a <<DeltaHistoryManager.adoc#, DeltaHistoryManager>>, and <<DeltaFileOperations.adoc#, DeltaFileOperations>>).

== [[createLogStore]] Creating LogStore -- `createLogStore` Method

[source, scala]
----
createLogStore(
  spark: SparkSession): LogStore
createLogStore(
  sparkConf: SparkConf,
  hadoopConf: Configuration): LogStore
----

`createLogStore`...FIXME

[NOTE]
====
`createLogStore` is used when:

* <<DeltaLog.adoc#store, DeltaLog>> is created

* <<LogStore#apply, LogStore.apply>> utility is used (to create a <<LogStore.adoc#, LogStore>>)
====
