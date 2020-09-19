= Delta SQL

Delta Lake registers custom <<commands, SQL commands>> using DeltaSparkSessionExtension.adoc[].

The SQL commands support `table` of the format `++delta.`path`++` (with backticks), e.g. `++delta.`/tmp/delta/t1`++` while `path` is between single quotes, e.g. `'/tmp/delta/t1'`.

== [[CONVERT-TO-DELTA]] CONVERT TO DELTA

[source,plaintext]
----
CONVERT TO DELTA table
  (PARTITIONED BY '(' colTypeList ')')?
----

Runs a <<ConvertToDeltaCommand.adoc#, ConvertToDeltaCommand>>

== [[DESCRIBE-DETAIL]] DESCRIBE DETAIL

[source,plaintext]
----
(DESC | DESCRIBE) DETAIL (path | table)
----

Runs a <<DescribeDeltaDetailCommand.adoc#, DescribeDeltaDetailCommand>>

== [[DESCRIBE-HISTORY]] DESCRIBE HISTORY

[source,plaintext]
----
(DESC | DESCRIBE) HISTORY (path | table)
  (LIMIT limit)?
----

Runs a <<DescribeDeltaHistoryCommand.adoc#, DescribeDeltaHistoryCommand>>

== [[GENERATE]] GENERATE

[source,plaintext]
----
GENERATE modeName FOR TABLE table
----

Runs a <<DeltaGenerateCommand.adoc#, DeltaGenerateCommand>>

== [[VACUUM]] VACUUM

[source,plaintext]
----
VACUUM (path | table)
  (RETAIN number HOURS)? (DRY RUN)?
----

Runs a <<VacuumTableCommand.adoc#, VacuumTableCommand>>
