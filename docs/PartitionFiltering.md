= [[PartitionFiltering]] PartitionFiltering -- Snapshots With Partition Filtering

[[self]]
`PartitionFiltering` is an abstraction of <<implementations, snapshots>> with <<filesForScan, partition filtering for scan>>.

[[implementations]]
NOTE: Snapshot.adoc[Snapshot] is the default and only known `PartitionFiltering` in Delta Lake.

== [[filesForScan]] Files to Scan (Matching Projection Attributes and Predicates) -- `filesForScan` Method

[source, scala]
----
filesForScan(
  projection: Seq[Attribute],
  filters: Seq[Expression],
  keepStats: Boolean = false): DeltaScan
----

`filesForScan`...FIXME

[NOTE]
====
`filesForScan` is used when:

* `OptimisticTransactionImpl` is requested for the OptimisticTransactionImpl.adoc#filterFiles[files to scan matching given predicates]

* `TahoeLogFileIndex` is requested for the TahoeLogFileIndex.adoc#matchingFiles[files matching predicates] and the TahoeLogFileIndex.adoc#inputFiles[input files]
====
