= DeltaSqlAstBuilder

`DeltaSqlAstBuilder` is a command builder for the <<commands, Delta SQL commands>> described in *DeltaSqlBase.g4* ANTLR grammar.

[[commands]]
.DeltaSqlAstBuilder's Visit Methods
[cols="30,70",options="header",width="100%"]
|===
| Name
| Description

| delta-sql.adoc#CONVERT-TO-DELTA[CONVERT TO DELTA]
| [[visitConvert]] Creates a <<ConvertToDeltaCommand.adoc#, ConvertToDeltaCommand>>

| delta-sql.adoc#DESCRIBE-DETAIL[DESCRIBE DETAIL]
| [[visitDescribeDeltaDetail]] Creates a <<DescribeDeltaDetailCommand.adoc#, DescribeDeltaDetailCommand>>

| delta-sql.adoc#DESCRIBE-HISTORY[DESCRIBE HISTORY]
| [[visitDescribeDeltaHistory]] Creates a <<DescribeDeltaHistoryCommand.adoc#, DescribeDeltaHistoryCommand>>

| delta-sql.adoc#GENERATE[GENERATE]
| [[visitGenerate]] Creates a <<DeltaGenerateCommand.adoc#, DeltaGenerateCommand>>

| delta-sql.adoc#VACUUM[VACUUM]
| [[visitVacuumTable]] Creates a <<VacuumTableCommand.adoc#, VacuumTableCommand>>

|===

`DeltaSqlParser` is used exclusively by <<DeltaSqlParser.adoc#builder, DeltaSqlParser>>.
