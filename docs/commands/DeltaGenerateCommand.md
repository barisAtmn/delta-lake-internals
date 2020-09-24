= DeltaGenerateCommand -- Executing Generation Functions On Delta Tables

`DeltaGenerateCommand` is a concrete <<DeltaGenerateCommandBase.md#, DeltaGenerateCommandBase>> (and so can <<DeltaGenerateCommandBase.md#getPath, get the path of a delta table from a table identifier>>) that can <<run, execute a generate function on a delta table>>.

[[symlink_format_manifest]]
`DeltaGenerateCommand` supports `symlink_format_manifest` only for the <<modeName, mode>>.

[source,text]
----
val generateQ = """GENERATE symlink_format_manifest for table delta.`/tmp/delta/t1`"""
scala> sql(generateQ).foreach(_ => ())
----

[[modeNameToGenerationFunc]]
`DeltaGenerateCommand` uses a lookup table for generation functions per mode:

* Uses <<GenerateSymlinkManifest.md#generateFullManifest, generateFullManifest>> for the only-supported <<symlink_format_manifest, symlink_format_manifest>>

`DeltaGenerateCommand` is <<creating-instance, created>> when:

* `DeltaSqlAstBuilder` is requested to <<DeltaSqlAstBuilder.md#visitGenerate, parse GENERATE SQL command>>

* <<DeltaTable.md#generate, DeltaTable.generate>> operator is used (that <<DeltaTableOperations.md#executeGenerate, executeGenerate>>)

== [[creating-instance]] Creating DeltaGenerateCommand Instance

`DeltaGenerateCommand` takes the following to be created:

* [[modeName]] Mode (<<symlink_format_manifest, symlink_format_manifest>> is the only supported mode)
* [[tableId]] Delta table (`TableIdentifier`)

== [[run]] Running Command -- `run` Method

[source, scala]
----
run(sparkSession: SparkSession): Seq[Row]
----

NOTE: `run` is part of the `RunnableCommand` contract to...FIXME.

`run` <<DeltaLog.md#forTable, creates a DeltaLog>> for the <<getPath, path of the delta table>> (from the <<tableId, table identifier>>).

`run` finds the generate function for the mode (in the <<modeNameToGenerationFunc, modeNameToGenerationFunc>> registry) and applies (_executes_) it to the `DeltaLog`.

`run` throws an `AnalysisException` when the <<Snapshot.md#version, version>> of the <<DeltaLog.md#snapshot, snapshot>> of the `DeltaLog` is negative (less than `0`):

```
Delta table not found at [tablePath].
```

`run` throws an `IllegalArgumentException` for unsupported <<modeName, mode>>:

```
Specified mode '[modeName]' is not supported. Supported modes are: symlink_format_manifest
```
