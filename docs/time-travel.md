# Time Travel

<<DeltaOptions.adoc#path, path>> option may optionally specify *time travel*.

The format is defined per the following regular expressions:

* `.*@(\\d&#123;17})$$` (_TIMESTAMP_URI_FOR_TIME_TRAVEL_), e.g. `@(yyyyMMddHHmmssSSS)`

* `.*@[vV](\d+)$` (_VERSION_URI_FOR_TIME_TRAVEL_), e.g. `@v123`

The <<DeltaOptions.adoc#versionAsOf, versionAsOf>> or <<DeltaOptions.adoc#timestampAsOf, timestampAsOf>>...FIXME

== [[DeltaTimeTravelSpec]] DeltaTimeTravelSpec -- Metadata of Time Travel Node

`DeltaTimeTravelSpec` describes a time travel node:

* `timestamp` or `version`

* Optional `creationSource`
