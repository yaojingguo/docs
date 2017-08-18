---
title: SHOW QUERIES
summary: The SHOW QUERIES statement lists all currently active queries across the cluster or on the local node.
toc: false
---

<span class="version-tag">New in v1.1:</span> The `SHOW QUERIES` [statement](sql-statements.html) lists details about currently active SQL queries, including:

- The internal ID of the query
- The node executing the query
- The SQL query itself
- How long the query has been running
- The client address, application name, and user that issued the query

These details let you monitor the progress of active queries and, if necessary, identify those that may need to be [cancelled](cancel-query.html) to prevent unwanted resource consumption.

{{site.data.alerts.callout_info}}Schema changes and <a href="backup.html"><code>BACKUP</code></a>/<a href="restore.html"><code>RESTORE</code></a> statements are not executed as queries internally and so are not listed by <code>SHOW QUERIES</code>. To monitor such statements, use <a href="show-jobs.html"><code>SHOW JOBS</code></a> instead.{{site.data.alerts.end}}

<div id="toc"></div>

## Required Privileges

No [privileges](privileges.html) are required to execute this statement. However, note that non-`root` users see only their own currently active queries, whereas the `root` user sees all users' currently active queries.

## Synopsis

{% include sql/{{ page.version.version }}/diagrams/show_queries.html %}

- To list the active queries across all nodes of the cluster, use `SHOW QUERIES` or `SHOW CLUSTER QUERIES`.
- To list the active queries just on the local node, use `SHOW LOCAL QUERIES`.

## Response

The following fields are returned for each query:

Field | Description
------|------------
`query_id` | The ID of the query.
`node_id` | The ID of the node connected to.
`username` | The username of the connected user.
`start` | The timestamp at which the query started.
`query` | The SQL query.
`client_address` | The address and port of the client that issued the SQL query.
`application_name` | The [application name](set-vars.html#supported-variables) specified by the client, if any.
`distributed` | If `true`, the query is being executed by the Distributed SQL (DistSQL) engine. If `false`, the query is being executed by the standard "local" SQL engine. If `NULL`, the query is being prepared and it's not yet known which execution engine will be used.
`phase` | The phase of the query's execution. If `preparing`, the statement is being parsed and planned. If `executing`, the statement is being executed.

## Examples

### List Queries Across the Cluster

~~~ sql
> SHOW CLUSTER QUERIES;
~~~

~~~
+----------------------------------+---------+----------+----------------------------------+-------------------------------------------+---------------------+------------------+-------------+-----------+
|             query_id             | node_id | username |              start               |                   query                   |   client_address    | application_name | distributed |   phase   |
+----------------------------------+---------+----------+----------------------------------+-------------------------------------------+---------------------+------------------+-------------+-----------+
| 14db657443230c3e0000000000000001 |       1 | root     | 2017-08-16 18:00:50.675151+00:00 | UPSERT INTO test.kv(k, v) VALUES ($1, $2) | 192.168.12.56:54119 | test_app         | false       | executing |
| 14db657443b68c7d0000000000000001 |       1 | root     | 2017-08-16 18:00:50.684818+00:00 | UPSERT INTO test.kv(k, v) VALUES ($1, $2) | 192.168.12.56:54123 | test_app         | false       | executing |
| 14db65744382c2340000000000000001 |       1 | root     | 2017-08-16 18:00:50.681431+00:00 | UPSERT INTO test.kv(k, v) VALUES ($1, $2) | 192.168.12.56:54103 | test_app         | false       | executing |
| 14db657443c9dc660000000000000001 |       1 | root     | 2017-08-16 18:00:50.686083+00:00 | SHOW CLUSTER QUERIES                      | 192.168.12.56:54108 |                  | NULL        | preparing |
| 14db657443e30a850000000000000003 |       3 | root     | 2017-08-16 18:00:50.68774+00:00  | UPSERT INTO test.kv(k, v) VALUES ($1, $2) | 192.168.12.58:54118 | test_app         | false       | executing |
| 14db6574439f477d0000000000000003 |       3 | root     | 2017-08-16 18:00:50.6833+00:00   | UPSERT INTO test.kv(k, v) VALUES ($1, $2) | 192.168.12.58:54122 | test_app         | false       | executing |
| 14db6574435817d20000000000000002 |       2 | root     | 2017-08-16 18:00:50.678629+00:00 | UPSERT INTO test.kv(k, v) VALUES ($1, $2) | 192.168.12.57:54121 | test_app         | false       | executing |
| 14db6574433c621f0000000000000002 |       2 | root     | 2017-08-16 18:00:50.676813+00:00 | UPSERT INTO test.kv(k, v) VALUES ($1, $2) | 192.168.12.57:54124 | test_app         | false       | executing |
| 14db6574436f71d50000000000000002 |       2 | root     | 2017-08-16 18:00:50.680165+00:00 | UPSERT INTO test.kv(k, v) VALUES ($1, $2) | 192.168.12.57:54117 | test_app         | false       | executing |
+----------------------------------+---------+----------+----------------------------------+-------------------------------------------+---------------------+------------------+-------------+-----------+
(9 rows)
~~~

Alternatively, you can use `SHOW QUERIES` to receive the same response.

### List Queries on the Local Node

~~~ sql
> SHOW LOCAL QUERIES;
~~~

~~~
+----------------------------------+---------+----------+----------------------------------+-------------------------------------------+---------------------+------------------+-------------+-----------+
|             query_id             | node_id | username |              start               |                   query                   |   client_address    | application_name | distributed |   phase   |
+----------------------------------+---------+----------+----------------------------------+-------------------------------------------+---------------------+------------------+-------------+-----------+
| 14db657cd9005cb90000000000000001 |       1 | root     | 2017-08-16 18:01:27.5492+00:00   | UPSERT INTO test.kv(k, v) VALUES ($1, $2) | 192.168.12.56:54103 | test_app         | false       | executing |
| 14db657cd8d7d9a50000000000000001 |       1 | root     | 2017-08-16 18:01:27.546538+00:00 | UPSERT INTO test.kv(k, v) VALUES ($1, $2) | 192.168.12.56:54119 | test_app         | false       | executing |
| 14db657cd8e966c40000000000000001 |       1 | root     | 2017-08-16 18:01:27.547696+00:00 | UPSERT INTO test.kv(k, v) VALUES ($1, $2) | 192.168.12.56:54123 | test_app         | false       | executing |
| 14db657cd92ad8f80000000000000001 |       1 | root     | 2017-08-16 18:01:27.551986+00:00 | SHOW LOCAL QUERIES                        | 192.168.12.56:54122 |                  | NULL        | preparing |
+----------------------------------+---------+----------+----------------------------------+-------------------------------------------+---------------------+------------------+-------------+-----------+
(4 rows)
~~~

### Filter for Specific Queries

You can use a [`SELECT`](select.html) statement to filter the list of active queries by one or more of the [response fields](#response).

For example, the following query filters for queries where the client address is `192.168.0.72:56194 ` and the issuing user is `mroach`:

~~~ sql
> SELECT * FROM [SHOW CLUSTER QUERIES]
      WHERE client_address = '192.168.0.72:56194'
          AND username = 'mroach';
~~~

~~~
+----------------------------------+---------+----------+----------------------------------+----------------------------------+--------------------+------------------+-------------+-----------+
|             query_id             | node_id | username |              start               |              query               |   client_address   | application_name | distributed |   phase   |
+----------------------------------+---------+----------+----------------------------------+----------------------------------+--------------------+------------------+-------------+-----------+
| 14dacc1f9a781e3d0000000000000001 |       2 | mroach   | 2017-08-10 14:08:22.878113+00:00 | SELECT * FROM test.kv ORDER BY k | 192.168.0.72:56194 | test_app         | false       | executing |
+----------------------------------+---------+----------+----------------------------------+----------------------------------+--------------------+------------------+-------------+-----------+
~~~

### Cancel a Query

When you see a query that is incorrect or taking too long to complete, you can use the [`CANCEL QUERY`](cancel-query.html) statement to kill it.

For example, let's say you run `SHOW QUERIES` and notice that the `SELECT * FROM test.kv ORDER BY k` query has been running for too long:

~~~ sql
> SHOW CLUSTER QUERIES;
~~~

~~~
+----------------------------------+---------+----------+----------------------------------+----------------------------------+-----------------+------------------+-------------+-----------+
|             query_id             | node_id | username |              start               |              query               | client_address  | application_name | distributed |   phase   |
+----------------------------------+---------+----------+----------------------------------+----------------------------------+-----------------+------------------+-------------+-----------+
| 14dacc206c47a9690000000000000002 |       2 | root     | 2017-08-14 19:11:05.309119+00:00 | SHOW CLUSTER QUERIES             | 127.0.0.1:50921 |                  | NULL        | preparing |
| 14dacc1f9a781e3d0000000000000001 |       1 | root     | 2017-08-14 17:02:01.789073+00:00 | SELECT * FROM test.kv ORDER BY k | 127.0.0.1:51221 | test_app         | false       | executing |
+----------------------------------+---------+----------+----------------------------------+----------------------------------+-----------------+------------------+-------------+-----------+
(2 rows)
~~~

To cancel the query, and stop it from consuming resources, you note the `query_id` and use it with the `CANCEL QUERY` statement:

~~~ sql
> CANCEL QUERY '14dacc1f9a781e3d0000000000000001';
~~~

When you then re-run `SHOW QUERIES`, the long-running query is no longer listed:

~~~ sql
> SHOW QUERIES;
~~~

~~~
+----------------------------------+---------+----------+----------------------------------+----------------------+-----------------+------------------+-------------+-----------+
|             query_id             | node_id | username |              start               |        query         | client_address  | application_name | distributed |   phase   |
+----------------------------------+---------+----------+----------------------------------+----------------------+-----------------+------------------+-------------+-----------+
| 14daccf030ef49410000000000000002 |       2 | root     | 2017-08-14 19:25:57.666661+00:00 | SHOW CLUSTER QUERIES | 127.0.0.1:50921 |                  | NULL        | preparing |
+----------------------------------+---------+----------+----------------------------------+----------------------+-----------------+------------------+-------------+-----------+
(1 row)
~~~

## See Also

- [`CANCEL QUERY`](cancel-query.html)
- [`SHOW SESSIONS`](show-sessions.html)
- [`SHOW JOBS`](show-jobs.html)
- [Other SQL Statements](sql-statements.html)
