---
title: Diagnostics Reporting
summary: Learn about the diagnostic details that get shared with CockroachDB and how to opt out of sharing.
toc: false
---

By default, each node of a CockroachDB cluster shares diagnostic details with Cockroach Labs on a daily basis. These details, which are completely scrubbed of identifiable information, greatly help us understand and improve how the system behaves in real-world scenarios.

This page explains the details that get shared and how to opt out of sharing.

{{site.data.alerts.callout_success}}For insights into your cluster's performance and health, use the built-in <a href="explore-the-admin-ui.html">Admin UI</a> or a third-party monitoring tool like <a href="monitor-cockroachdb-with-prometheus.html">Prometheus</a>.{{site.data.alerts.end}}

<div id="toc"></div>

## What Gets Shared

When diagnostics reporting is on, each node of a CockroachDB cluster shares anonymized storage and table structure details with Cockroach Labs on a daily basis. Please note that the details that get shared may change over time, but as that happens, we will update this page and announce the changes in release notes.

### Storage Details

Each node of a CockroachDB cluster shares the following storage details:

Detail | Description
-------|------------
Node ID | The internal ID of the node.
Store ID | The internal ID of each store on the node.
Bytes | The amount of live data used by applications and the CockroachDB system on the node and per store. This excludes historical and deleted data.
Range Count | The number of ranges on the node and per store.
Key Count | The number of keys stored on the node and per store.

#### Example

This JSON example shows what storage details look like when sent to Cockroach Labs, in this case for a node with two stores.

~~~ json
{
   "node":{
      "node_id":1,
      "bytes":64828,
      "key_count":138,
      "range_count":12
   },
   "stores":[
      {
         "node_id":1,
         "store_id":1,
         "bytes":64828,
         "key_count":138,
         "range_count":12
      },
      {
         "node_id":1,
         "store_id":2,
         "bytes":0,
         "key_count":0,
         "range_count":0
      }
   ]
}
~~~

### SQL Table Structure

Each node of a CockroachDB cluster shares the following details about the structure of each table stored on the node:

{{site.data.alerts.callout_info}}No actual table data or table/column names are shared, just metadata about the structure of tables. All names and other string values are scrubbed and replaced with underscores.{{site.data.alerts.end}}

Detail | Description
-------|------------
Table | Metadata about each table, such as its internal ID, when it was last modified, and how many times it has been renamed. Table names are replaced with underscores.
Column | Metadata about each column in a table, such as its internal ID and type. Column names are replaced with undescores.
Column Families | Metadata about [column families](column-families.html) in a table, such as its internal ID and the columns included in the family. Family and column names are replaced with underscores.
Indexes | Metadata about the primary index and any secondary indexes on the table, such as the internal ID of an index and the columns covered by an index. All index, column, and other strings are replaced with underscores.
Privileges | Metadata about user [privileges](privileges.html) on the table, such as the number of privileges granted to each user. Usernames are replaced with underscores.
Checks | Metadata about any [check constraints](check.html) on the table. Check constraint names and expressions are replaced with underscores.

#### Example

This JSON example shows an excerpt of what table structure details look like when sent to Cockroach Labs, in this case for a node with just one table. Note that all names and other strings have been scrubbed and replaced with underscores.

~~~ json
{
   "schema":[
      {
         "name":"_",
         "id":51,
         "parent_id":50,
         "version":1,
         "up_version":false,
         "modification_time":{
            "wall_time":0,
            "logical":0
         },
         "columns":[
            {
               "name":"_",
               "id":1,
               "type":{
                  "kind":1,
                  "width":0,
                  "precision":0
               },
               "nullable":true,
               "default_expr":"_",
               "hidden":false
            },
            ...
         ],
         ...
      }
   ]
}
~~~

## Opt Out of Diagnostics Reporting

To stop sending diagnostic details to Cockroach Labs once a cluster is running, [use the built-in SQL client](use-the-built-in-sql-client.html) to execute the following `SET CLUSTER SETTING` statement, which switches the `diagnostics.reporting.enabled` [cluster setting](cluster-settings.html) to `false`:

~~~ sql
> SET CLUSTER SETTING diagnostics.reporting.enabled = false;
~~~

This change will not be instantaneous, as it must be propagated to other nodes in the cluster. If you want to make sure that no diagnostic details are shared, you can set the environment variable `COCKROACH_SKIP_ENABLING_DIAGNOSTIC_REPORTING=true` before starting the first node of the cluster. Note that this works only when set before starting the first node of the cluster. Once the cluster is running, you need to use the `SET` method described above.

## See Also

- [Cluster Settings](cluster-settings.html)
- [Start a Node](start-a-node.html)