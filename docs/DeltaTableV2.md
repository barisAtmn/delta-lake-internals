= DeltaTableV2

*DeltaTableV2* is a logical representation of a writable Delta table (Spark SQL 3.0.0).

== [[creating-instance]] Creating Instance

DeltaTableV2 takes the following to be created:

* [[spark]] SparkSession
* [[path]] Hadoop Path
* [[catalogTable]] Optional Catalog Metadata (`Option[CatalogTable]`) (default: unspecified)
* [[tableIdentifier]] Optional Table ID (`Option[String]`) (default: unspecified)
* [[timeTravelOpt]] Optional Time Travel Specification (`Option[DeltaTimeTravelSpec]`) (default: unspecified)

DeltaTableV2 is created when:

* DeltaCatalog is requested to DeltaCatalog.adoc#loadTable[load a table]

* DeltaDataSource is requested to DeltaDataSource.adoc#getTable[load a table] or DeltaDataSource.adoc#RelationProvider-createRelation[create a table relation]
