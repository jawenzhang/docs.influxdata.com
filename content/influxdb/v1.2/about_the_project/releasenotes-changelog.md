---
title: Release Notes/Changelog
menu:
  influxdb_1_2:
    weight: 0
    parent: about_the_project
---

## v1.2.4 [2017-05-08]

### Bugfixes

- Prefix partial write errors with `partial write:` to generalize identification in other subsystems.

## v1.2.3 [2017-04-17]

### Bugfixes

- Redact passwords before saving them to the history file.
- Add the missing DefaultDatabase method to several InfluxQL statements.
- Fix segment violation in models.Tags.Get.
- Simplify the admin user check.
- Fix a regression when math was used with selectors.
- Ensure the input for certain functions in the query engine are ordered.
- Fix issue where deleted `time` field keys created unparseable points.

## v1.2.2 [2017-03-14]

### Release Notes

### Configuration Changes

#### `[http]` Section

* [`max-row-limit`](/influxdb/v1.2/administration/config/#max-row-limit-0) now defaults to `0`.
In versions 1.0 and 1.1, the default setting was `10000`, but due to a bug, the value in use in versions 1.0 and 1.1 was effectively `0`.
In versions 1.2.0 through 1.2.1, we fixed that bug, but the fix caused a breaking change for Grafana and Kapacitor users; users who had not set `max-row-limit` to `0` experienced truncated/partial data due to the `10000` row limit.
In version 1.2.2, we've changed the default `max-row-limit` setting to `0` to match the behavior in versions 1.0 and 1.1.

### Bugfixes

- Change the default [`max-row-limit`](/influxdb/v1.2/administration/config/#max-row-limit-0) setting from `10000` to `0` to prevent the absence of data in Grafana or Kapacitor.

## v1.2.1 [2017-03-08]

### Release Notes

### Bugfixes

-	 Treat non-reserved measurement names with underscores as normal measurements.
-	 Reduce the expression in a subquery to avoid a panic.
-	 Properly select a tag within a subquery.
-	 Prevent a panic when aggregates are used in an inner query with a raw query.
-	 Points missing after compaction.
-	 Point.UnmarshalBinary() bounds check.
-	 Interface conversion: tsm1.Value is tsm1.IntegerValue, not tsm1.FloatValue.
-	 Map types correctly when using a regex and one of the measurements is empty.
-	 Map types correctly when selecting a field with multiple measurements where one of the measurements is empty.
-	 Include IsRawQuery in the rewritten statement for meta queries.
-	 Fix race in WALEntry.Encode and Values.Deduplicate
-	 Fix panic in collectd when configured to read types DB from directory.
-	 Fix ORDER BY time DESC with ordering series keys.
-	 Fix mapping of types when the measurement uses a regular expression.
-	 Fix LIMIT and OFFSET when they are used in a subquery.
-	 Fix incorrect math when aggregates that emit different times are used.
-	 Fix EvalType when a parenthesis expression is used.
-	 Fix authentication when subqueries are present.
-	 Expand query dimensions from the subquery.
-	 Dividing aggregate functions with different outputs doesn't panic.
-	 Anchors not working as expected with case-insensitive regular expression.
 
## v1.2.0 [2017-01-24]

### Release Notes

This release introduces a major new querying capability in the form of sub-queries, and provides several performance improvements, including a 50% or better gain in write performance on larger numbers of cores. The release adds some stability and memory-related improvements, as well as several CLI-related bug fixes. If upgrading from a prior version, please read the configuration changes in the following section before upgrading.

### Configuration Changes

The following new configuration options are available, if upgrading to `1.2.0` from prior versions.

#### `[[collectd]]` Section

* `security-level` which defaults to `"none"`. This field also accepts `"sign"` and `"encrypt"` and enables different levels of transmission security for the collectd plugin.
* `auth-file` which defaults to `"/etc/collectd/auth_file"`. Specifies where to locate the authentication file used to authenticate clients when using signed or encrypted mode.

### Deprecations

The stress tool `influx_stress` will be removed in a subsequent release. We recommend using [`influx-stress`](https://github.com/influxdata/influx-stress) as a replacement.

### Features

- Remove the override of GOMAXPROCS.
- Uncomment section headers from the default configuration file.
- Improve write performance significantly.
- Prune data in meta store for deleted shards.
- Update latest dependencies with Godeps.
- Introduce syntax for marking a partial response with chunking.
- Use X-Forwarded-For IP address in HTTP logger if present.
- Add support for secure transmission via collectd.
- Switch logging to use structured logging everywhere.
- [CLI feature request] USE retention policy for queries.
- Add clear command to CLI.
- Adding ability to use parameters in queries in the v2 client using the `Parameters` map in the `Query` struct.
- Allow add items to array config via ENV.
- Support subquery execution in the query language.
- Verbose output for SSL connection errors.
- Cache snapshotting performance improvements

### Bugfixes

- Fix potential race condition in correctness of tsm1_cache memBytes statistic.
- Fix broken error return on meta client's UpdateUser and DropContinuousQuery methods.
- Fix string quoting and significantly improve performance of `influx_inspect export`.
- CLI was caching db/rp for insert into statements.
- Fix CLI import bug when using self-signed SSL certificates.
- Fix cross-platform backup/restore.
- Ensures that all user privileges associated with a database are removed when the database is dropped.
- Return the time from a percentile call on an integer.
- Expand string and boolean fields when using a wildcard with `sample()`.
- Fix chuid argument order in init script.
- Reject invalid subscription URLs. 
- CLI should use spaces for alignment, not tabs.
- 0.12.2 InfluxDB CLI client PRECISION returns "Unknown precision...".
- Fix parse key panic when missing tag value.
- Rentention Policy should not allow `INF` or `0` as a shard duration.
- Return Error instead of panic when decoding point values.
- Fix slice out of bounds panic when pruning shard groups. 
- Drop database will delete /influxdb/data directory.
- Ensure Subscriber service can be disabled.
- Fix race in storage engine.
- InfluxDB should do a partial write on mismatched type errors.

## v1.1.5 [2017-05-08]

### Bugfixes

- Redact passwords before saving them to the history file.
- Add the missing DefaultDatabase method to several InfluxQL statements.

## v1.1.4 [2017-02-27]

### Bugfixes

- Backport from 1.2.0: Reduce GC allocations.

## v1.1.3 [2017-02-17]

### Bugfixes

- Remove Tags.shouldCopy, replace with forceCopy on series creation.

## v1.1.2 [2017-02-16]

### Bugfixes

- Fix memory leak when writing new series over HTTP.
- Fix series tag iteration segfault. 
- Fix tag dereferencing panic.

## v1.1.1 [2016-12-06]

### Features

- Update Go version to 1.7.4.

### Bugfixes

- Fix string fields w/ trailing slashes.
- Quote the empty string as an ident.
- Fix incorrect tag value in error message.

### Security

[Go 1.7.4](https://golang.org/doc/devel/release.html#go1.7.minor) was released to address two security issues.  This release includes these security fixes.

## v1.1.0 [2016-11-14]

### Release Notes

This release is built with GoLang 1.7.3 and provides many performance optimizations, stability changes and a few new query capabilities.  If upgrading from a prior version, please read the configuration changes below section before upgrading.

### Deprecations

The admin interface is deprecated and will be removed in a subsequent release. 
The configuration setting to enable the admin UI is now disabled by default, but can be enabled if necessary.  
We recommend using [Chronograf](https://github.com/influxdata/chronograf) or [Grafana](https://github.com/grafana/grafana) as a replacement.

### Configuration Changes

The following configuration changes may need to changed before upgrading to `1.1.0` from prior versions.

#### `[admin]` Section

* `enabled` now default to false.  If you are currently using the admin interaface, you will need to change this value to `true` to re-enable it.  The admin interface is currently deprecated and will be removed in a subsequent release.

#### `[data]` Section

* `max-values-per-tag` was added with a default of 100,000, but can be disabled by setting it to `0`.  Existing measurements with tags that exceed this limit will continue to load, but writes that would cause the tags cardinality to increase will be dropped and a `partial write` error will be returned to the caller.  This limit can be used to prevent high cardinality tag values from being written to a measurement.
* `cache-max-memory-size` has been increased to from `524288000` to `1048576000`.  This setting is the maximum amount of RAM, in bytes, a shard cache can use before it rejects writes with an error.  Setting this value to `0` disables the limit.
* `cache-snapshot-write-cold-duration` has been decreased from `1h` to `10m`.  This setting determines how long values will stay in the shard cache while the shard is cold for writes.
* `compact-full-write-cold-duration` has been decreased from `24h` to `4h`.  The shorter duration allows cold shards to be compacted to an optimal state more quickly.

### Features

The query language has been extended with a few new features:

- Support regular expressions on fields keys in select clause.
- New `linear` fill option.
- New `cumulative_sum` function.
- Support `ON` for `SHOW` commands.

All Changes:

- Filter out series within shards that do not have data for that series.
- Rewrite regular expressions of the form host = /^server-a$/ to host = 'server-a', to take advantage of the tsdb index.
- Improve compaction planning performance by caching tsm file stats.
- Align binary math expression streams by time.
- Reduce map allocations when computing the TagSet of a measurement.
- Make input plugin services open/close idempotent.
- Speed up shutdown by closing shards concurrently.
- Add sample function to query language.
- Add `fill(linear)` to query language.
- Implement cumulative_sum() function.
- Update defaults in config for latest best practices.
- UDP Client: Split large points. 
- Add stats for active compactions, compaction errors.
- More man pages for the other tools we package and compress man pages fully.
- Add max-values-per-tag to limit high tag cardinality data.
- Update jwt-go dependency to version 3.
- Support enable HTTP service over unix domain socket.
- Add additional statistics to query executor.
- Feature request: `influx inspect -export` should dump WAL files.
- Implement text/csv content encoding for the response writer.
- Support tools for running async queries.
- Support ON and use default database for SHOW commands.
- Correctly read in input from a non-interactive stream for the CLI.
- Support `INFLUX_USERNAME` and `INFLUX_PASSWORD` for setting username/password in the CLI.
- Optimize first/last when no group by interval is present.
- Make regular expressions work on field and dimension keys in SELECT clause.
- Change default time boundaries for raw queries.
- Support mixed duration units.

### Bugfixes

- Avoid deadlock when `max-row-limit` is hit.
- Fix incorrect grouping when multiple aggregates are used with sparse data.
- Fix output duration units for SHOW QUERIES.
- Truncate the version string when linking to the documentation.
- influx_inspect: export does not escape field keys.
- Fix issue where point would be written to wrong shard.
- Fix retention policy inconsistencies.
- Remove accidentally added string support for the stddev call.
- Remove /data/process_continuous_queries endpoint.
- Enable https subscriptions to work with custom CA certificates.
- Reduce query planning allocations.
- Shard stats include WAL path tag so disk bytes make more sense.
- Panic with unread show series iterators during drop database.
- Use consistent column output from the CLI for column formatted responses.
- Correctly use password-type field in Admin UI. 
- Duplicate parsing bug in ALTER RETENTION POLICY.
- Fix database locked up when deleting shards.
- Fix mmap dereferencing.
- Fix base64 encoding issue with /debug/vars stats.
- Drop measurement causes cache max memory exceeded error.
- Decrement number of measurements only once when deleting the last series from a measurement.
- Delete statement returns an error when retention policy or database is specified.
- Fix the dollar sign so it properly handles reserved keywords.
- Exceeding max retention policy duration gives incorrect error message.
- Drop time when used as a tag or field key.

## v1.0.2 [2016-10-05]

### Bugfixes

- Fix RLE integer decoding producing negative numbers.
- Avoid stat syscall when planning compactions.
- Subscription data loss under high write load.
- Do not automatically reset the shard duration when using ALTER RETENTION POLICY.
- Ensure correct shard groups created when retention policy has been altered.

## v1.0.1 [2016-09-26]

### Bugfixes

- Prevent users from manually using system queries since incorrect use would result in a panic.
- Ensure fieldsCreated stat available in shard measurement.
- Report cmdline and memstats in /debug/vars.
- Fixing typo within example configuration file. 
- Implement time math for lazy time literals.
- Fix database locked up when deleting shards.
- Skip past points at the same time in derivative call within a merged series.
- Read an invalid JSON response as an error in the Influx client.

## v1.0.0 [2016-09-08]

### Release Notes
Inital release of InfluxDB.

### Breaking changes

* `max-series-per-database` was added with a default of 1M but can be disabled by setting it to `0`. Existing databases with series that exceed this limit will continue to load but writes that would create new series will fail.
* Config option `[cluster]` has been replaced with `[coordinator]`.
* Support for config options `[collectd]` and `[opentsdb]` has been removed; use `[[collectd]]` and `[[opentsdb]]` instead.
* Config option `data-logging-enabled` within the `[data]` section, has been renamed to `trace-logging-enabled`, and defaults to `false`.
* The keywords `IF`, `EXISTS`, and `NOT` where removed for this release.  This means you no longer need to specify `IF NOT EXISTS` for `DROP DATABASE` or `IF EXISTS` for `CREATE DATABASE`.  If these are specified, a query parse error is returned.
* The Shard `writePointsFail` stat has been renamed to `writePointsErr` for consistency with other stats.

With this release the systemd configuration files for InfluxDB will use the system configured default for logging and will no longer write files to `/var/log/influxdb` by default. On most systems, the logs will be directed to the systemd journal and can be accessed by `journalctl -u influxdb.service`. Consult the systemd journald documentation for configuring journald.

### Features

- Add mode function. 
- Support negative timestamps for the query engine.
- Write path stats.
- Add MaxSeriesPerDatabase config setting.
- Remove IF EXISTS/IF NOT EXISTS from influxql language.
- Update go package library dependencies.
- Add tsm file export to influx_inspect tool.
- Create man pages for commands.
- Return 403 Forbidden when authentication succeeds but authorization fails.
- Added favicon.
- Run continuous query for multiple buckets rather than one per bucket.
- Log the CQ execution time when continuous query logging is enabled.
- Trim BOM from Windows Notepad-saved config files.
- Update help and remove unused config options from the configuration file.
- Add NodeID to execution options.
- Make httpd logger closer to Common (& combined) Log Format.
- Allow any variant of the help option to trigger the help.
- Reduce allocations during query parsing.
- Optimize timestamp run-length decoding.
- Adds monitoring statistic for on-disk shard size.
- Add HTTP(s) based subscriptions.
- Add new HTTP statistics to monitoring.
- Speed up drop database.
- Add Holt-Winter forecasting function.
- Add support for JWT token authentication.
- Add ability to create snapshots of shards.
- Parallelize iterators.
- Teach the http service how to enforce connection limits.
- Support cast syntax for selecting a specific type.
- Refactor monitor service to avoid expvar and write monitor statistics on a truncated time interval.
- Dynamically update the documentation link in the admin UI.
- Support wildcards in aggregate functions.
- Support specifying a retention policy for the graphite service.
- Add extra trace logging to tsm engine.
- Add stats and diagnostics to the TSM engine.
- Support regex selection in SHOW TAG VALUES for the key.
- Modify the default retention policy name and make it configurable.
- Update SHOW FIELD KEYS to return the field type with the field key.
- Support bound parameters in the parser.
- Add https-private-key option to httpd config.
- Support loading a folder for collectd typesdb files.

### Bugfixes

- Optimize queries that compare a tag value to an empty string.
- Allow blank lines in the line protocol input.
- Runtime: goroutine stack exceeds 1000000000-byte limit.
- Fix alter retention policy when all options are used.
- Concurrent series limit.
- Ensure gzip writer is closed in influx_inspect export.
- Fix CREATE DATABASE when dealing with default values.
- Fix UDP pointsRx being incremented twice.
- Tombstone memory improvements.
- Hardcode auto generated RP names to autogen.
- Ensure IDs can't clash when managing Continuous Queries.
- Continuous full compactions.
- Remove limiter from walkShards.
- Copy tags in influx_stress to avoid a concurrent write panic on a map.
- Do not run continuous queries that have no time span.
- Move the CQ interval by the group by offset.
- Fix panic parsing empty key.
- Update connection settings when changing hosts in CLI.
- Always use the demo config when outputting a new config.
- Minor improvements to init script. Removes sysvinit-utils as package dependency.
- Fix compaction planning with large TSM files.
- Duplicate data for the same timestamp.
- Fix panic: truncate the slice when merging the caches.
- Fix regex binary encoding for a measurement.
- Fix fill(previous) when used with math operators.
- Rename dumptsmdev to dumptsm in influx_inspect.
- Remove a double lock in the tsm1 index writer.
- Remove FieldCodec from TSDB package.
- Allow a non-admin to call "use" for the influx CLI.
- Set the condition cursor instead of aux iterator when creating a nil condition cursor.
- Update `stress/v2` to work with clusters, ssl, and username/password auth. Code cleanup.
- Modify the max nanosecond time to be one nanosecond less.
- Include sysvinit-tools as an rpm dependency.
- Add port to all graphite log output to help with debugging multiple endpoints.
- Fix panic: runtime error: index out of range.
- Remove systemd output redirection.
- Database unresponsive after DROP MEASUREMENT.
- Address Out of Memory Error when Dropping Measurement.
- Fix the point validation parser to identify and sort tags correctly.
- Prevent panic in concurrent auth cache write.
- Set X-Influxdb-Version header on every request (even 404 requests).
- Prevent panic if there are no values.
- Time sorting broken with overwritten points.
- queries with strings that look like dates end up with date types, not string types.
- Concurrent map read write panic. 
- Drop writes from before the retention policy time window.
- Fix SELECT statement required privileges.
- Filter out sources that do not match the shard database/retention policy.
- Truncate the shard group end time if it exceeds MaxNanoTime.
- Batch SELECT INTO / CQ writes.
- Fix compaction planning re-compacting large TSM files.
- Ensure client sends correct precision when inserting points.
- Accept points with trailing whitespace.
- Fix panic in SHOW FIELD KEYS.
- Disable limit optimization when using an aggregate.
- Fix panic: interface conversion: tsm1.Value is \*tsm1.StringValue, not \*tsm1.FloatValue.
- Data race when dropping a database immediately after writing to it.
- Make sure admin exists before authenticating query.
- Print the query executor's stack trace on a panic to the log.
- Fix read tombstones: EOF.
- Query-log-enabled in config not ignored anymore.
- Ensure clients requesting gzip encoded bodies don't receive empty body.
- Optimize shard loading.
- Queries slow down hundreds times after overwriting points.
- SHOW TAG VALUES accepts != and !~ in WHERE clause.
- Remove old cluster code.
- Ensure that future points considered in SHOW queries.
- Fix full compactions conflicting with level compactions.
- Overwriting points on large series can cause memory spikes during compactions.
- Fix parseFill to check for fill ident before attempting to parse an expression.
- Max index entries exceeded.
- Address slow startup time.
- Fix measurement field panic in tsm1 engine.
- Queries against files that have just been compacted need to point to new files.
- Check that retention policies exist before creating CQ.
