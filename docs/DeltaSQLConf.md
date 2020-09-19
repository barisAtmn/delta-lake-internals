# DeltaSQLConf &mdash; spark.databricks.delta Configuration Properties

**DeltaSQLConf** contains **spark.databricks.delta**-prefixed configuration properties to configure behaviour of Delta Lake.

== [[alterLocation.bypassSchemaCheck]][[DELTA_ALTER_LOCATION_BYPASS_SCHEMA_CHECK]] alterLocation.bypassSchemaCheck

*spark.databricks.delta.alterLocation.bypassSchemaCheck* enables Alter Table Set Location on Delta to go through even if the Delta table in the new location has a different schema from the original Delta table

Default: `false`

## <span id="checkLatestSchemaOnRead"><span id="DELTA_SCHEMA_ON_READ_CHECK_ENABLED"> checkLatestSchemaOnRead

**spark.databricks.delta.checkLatestSchemaOnRead** (internal) enables a check that ensures that users won't read corrupt data if the source schema changes in an incompatible way.

Default: `true`

In Delta, we always try to give users the latest version of their data without having to call REFRESH TABLE or redefine their DataFrames when used in the context of streaming. There is a possibility that the schema of the latest version of the table may be incompatible with the schema at the time of DataFrame creation.

== [[checkpoint.partSize]][[DELTA_CHECKPOINT_PART_SIZE]] checkpoint.partSize

*spark.databricks.delta.checkpoint.partSize* (internal) is the limit at which we will start parallelizing the checkpoint. We will attempt to write maximum of this many actions per checkpoint.

Default: `5000000`

== [[commitInfo.enabled]][[DELTA_COMMIT_INFO_ENABLED]] commitInfo.enabled

*spark.databricks.delta.commitInfo.enabled* controls whether to OptimisticTransactionImpl.adoc#commitInfo[log commit information into a Delta log].

Default: `true`

== [[commitInfo.userMetadata]][[DELTA_USER_METADATA]] commitInfo.userMetadata

*spark.databricks.delta.commitInfo.userMetadata* is an arbitrary user-defined metadata to include in CommitInfo.adoc#userMetadata[CommitInfo] (requires <<commitInfo.enabled, spark.databricks.delta.commitInfo.enabled>>).

Default: (empty)

== [[commitValidation.enabled]][[DELTA_COMMIT_VALIDATION_ENABLED]] commitValidation.enabled

*spark.databricks.delta.commitValidation.enabled* (internal) controls whether to perform validation checks before commit or not

Default: `true`

== [[convert.metadataCheck.enabled]][[DELTA_CONVERT_METADATA_CHECK_ENABLED]] convert.metadataCheck.enabled

*spark.databricks.delta.convert.metadataCheck.enabled* enables validation during convert to delta, if there is a difference between the catalog table's properties and the Delta table's configuration, we should error.

If disabled, merge the two configurations with the same semantics as update and merge

Default: `true`

== [[dummyFileManager.numOfFiles]][[DUMMY_FILE_MANAGER_NUM_OF_FILES]] dummyFileManager.numOfFiles

*spark.databricks.delta.dummyFileManager.numOfFiles* (internal) controls how many dummy files to write in DummyFileManager

Default: `3`

== [[dummyFileManager.prefix]][[DUMMY_FILE_MANAGER_PREFIX]] dummyFileManager.prefix

*spark.databricks.delta.dummyFileManager.prefix* (internal) is the file prefix to use in DummyFileManager

Default: `.s3-optimization-`

== [[history.maxKeysPerList]][[DELTA_HISTORY_PAR_SEARCH_THRESHOLD]] history.maxKeysPerList

*spark.databricks.delta.history.maxKeysPerList* (internal) controls how many commits to list when performing a parallel search.

The default is the maximum keys returned by S3 per list call. Azure can return 5000, therefore we choose 1000.

Default: `1000`

## <span id="history.metricsEnabled"><span id="DELTA_HISTORY_METRICS_ENABLED"> history.metricsEnabled

**spark.databricks.delta.history.metricsEnabled** enables Metrics reporting in Describe History. [CommitInfo](CommitInfo.md) will now record the Operation Metrics.

Default: `true`

== [[import.batchSize.schemaInference]][[DELTA_IMPORT_BATCH_SIZE_SCHEMA_INFERENCE]] import.batchSize.schemaInference

*spark.databricks.delta.import.batchSize.schemaInference* (internal) is the number of files per batch for schema inference during <<ConvertToDeltaCommand.adoc#performConvert-schemaBatchSize, import>>.

Default: `1000000`

== [[import.batchSize.statsCollection]][[DELTA_IMPORT_BATCH_SIZE_STATS_COLLECTION]] import.batchSize.statsCollection

*spark.databricks.delta.import.batchSize.statsCollection* (internal) is the number of files per batch for stats collection during <<ConvertToDeltaCommand.adoc#performConvert-schemaBatchSize, import>>.

Default: `50000`

== [[maxSnapshotLineageLength]][[DELTA_MAX_SNAPSHOT_LINEAGE_LENGTH]] maxSnapshotLineageLength

*spark.databricks.delta.maxSnapshotLineageLength* (internal) is the maximum lineage length of a Snapshot before Delta forces to build a Snapshot from scratch

Default: `50`

== [[merge.maxInsertCount]][[MERGE_MAX_INSERT_COUNT]] merge.maxInsertCount

*spark.databricks.delta.merge.maxInsertCount* (internal) is the maximum row count of inserts in each MERGE execution

Default: `10000L`

== [[merge.optimizeInsertOnlyMerge.enabled]][[MERGE_INSERT_ONLY_ENABLED]] merge.optimizeInsertOnlyMerge.enabled

*spark.databricks.delta.merge.optimizeInsertOnlyMerge.enabled* (internal) controls merge without any matched clause (i.e., insert-only merge) will be optimized by avoiding rewriting old files and just inserting new files

Default: `true`

== [[merge.optimizeMatchedOnlyMerge.enabled]][[MERGE_MATCHED_ONLY_ENABLED]] merge.optimizeMatchedOnlyMerge.enabled

*spark.databricks.delta.merge.optimizeMatchedOnlyMerge.enabled* (internal) controls merge without 'when not matched' clause will be optimized to use a right outer join instead of a full outer join

Default: `true`

== [[merge.repartitionBeforeWrite.enabled]][[MERGE_REPARTITION_BEFORE_WRITE]] merge.repartitionBeforeWrite.enabled

*spark.databricks.delta.merge.repartitionBeforeWrite.enabled* (internal) controls merge will repartition the output by the table's partition columns before writing the files

Default: `false`

== [[partitionColumnValidity.enabled]][[DELTA_PARTITION_COLUMN_CHECK_ENABLED]] partitionColumnValidity.enabled

*spark.databricks.delta.partitionColumnValidity.enabled* (internal) enables validation of the partition column names (just like the data columns)

Default: `true`

== [[retentionDurationCheck.enabled]][[DELTA_VACUUM_RETENTION_CHECK_ENABLED]] retentionDurationCheck.enabled

*spark.databricks.delta.retentionDurationCheck.enabled* adds a check preventing users from running vacuum with a very short retention period, which may end up corrupting a Delta log.

Default: `true`

== [[sampling.enabled]][[DELTA_SAMPLE_ESTIMATOR_ENABLED]] sampling.enabled

*spark.databricks.delta.sampling.enabled* (internal) enables sample-based estimation

Default: `false`

## <span id="schema.autoMerge.enabled"><span id="DELTA_SCHEMA_AUTO_MIGRATE"> schema.autoMerge.enabled

**spark.databricks.delta.schema.autoMerge.enabled** enables schema merging on appends and overwrites.

Default: `false`

Equivalent DataFrame option: [mergeSchema](DeltaOptions.adoc#mergeSchema)

== [[snapshotIsolation.enabled]][[DELTA_SNAPSHOT_ISOLATION]] snapshotIsolation.enabled

*spark.databricks.delta.snapshotIsolation.enabled* (internal) controls whether queries on Delta tables are guaranteed to have snapshot isolation

Default: `true`

== [[snapshotPartitions]][[DELTA_SNAPSHOT_PARTITIONS]] snapshotPartitions

*spark.databricks.delta.snapshotPartitions* (internal) is the number of partitions to use for *state reconstruction* (when <<Snapshot.adoc#stateReconstruction, building a snapshot>> of a Delta table).

Default: `50`

== [[stalenessLimit]][[DELTA_ASYNC_UPDATE_STALENESS_TIME_LIMIT]] stalenessLimit

*spark.databricks.delta.stalenessLimit* (in millis) allows you to query the last loaded state of the Delta table without blocking on a table update. You can use this configuration to reduce the latency on queries when up-to-date results are not a requirement. Table updates will be scheduled on a separate scheduler pool in a FIFO queue, and will share cluster resources fairly with your query. If a table hasn't updated past this time limit, we will block on a synchronous state update before running the query.

Default: `0` (no tables can be stale)

== [[state.corruptionIsFatal]][[DELTA_STATE_CORRUPTION_IS_FATAL]] state.corruptionIsFatal

*spark.databricks.delta.state.corruptionIsFatal* (internal) throws a fatal error when the recreated Delta State doesn't
match committed checksum file

Default: `true`

== [[stateReconstructionValidation.enabled]][[DELTA_STATE_RECONSTRUCTION_VALIDATION_ENABLED]] stateReconstructionValidation.enabled

*spark.databricks.delta.stateReconstructionValidation.enabled* (internal) controls whether to perform validation checks on the reconstructed state

Default: `true`

== [[stats.collect]][[DELTA_COLLECT_STATS]] stats.collect

*spark.databricks.delta.stats.collect* (internal) enables statistics to be collected while writing files into a Delta table

Default: `true`

== [[stats.limitPushdown.enabled]][[DELTA_LIMIT_PUSHDOWN_ENABLED]] stats.limitPushdown.enabled

*spark.databricks.delta.stats.limitPushdown.enabled* (internal) enables using the limit clause and file statistics to prune files before they are collected to the driver

Default: `true`

== [[stats.localCache.maxNumFiles]][[DELTA_STATS_SKIPPING_LOCAL_CACHE_MAX_NUM_FILES]] stats.localCache.maxNumFiles

*spark.databricks.delta.stats.localCache.maxNumFiles* (internal) is the maximum number of files for a table to be considered a *delta small table*. Some metadata operations (such as using data skipping) are optimized for small tables using driver local caching and local execution.

Default: `2000`

== [[stats.skipping]][[DELTA_STATS_SKIPPING]] stats.skipping

*spark.databricks.delta.stats.skipping* (internal) enables statistics used for skipping

Default: `true`

== [[timeTravel.resolveOnIdentifier.enabled]][[RESOLVE_TIME_TRAVEL_ON_IDENTIFIER]] timeTravel.resolveOnIdentifier.enabled

*spark.databricks.delta.timeTravel.resolveOnIdentifier.enabled* (internal) controls whether to resolve patterns as `@v123` in identifiers as time-travel.adoc[time travel] nodes.

Default: `true`
