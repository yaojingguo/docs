---
title: Explore the Admin UI
toc: false
feedback: false
---

CockroachDB's Admin UI enables you to monitor and optimize the performance of your cluster. The Admin UI provides details about your cluster and database configuration. It also enables you to monitor: 
- SQL metrics
- System metrics 
- Storage metrics
- Replication metrics
- Events


<div id="toc"></div>

## Access the Admin UI

You can access the UI from any node in the cluster, each of which will show nearly identical data.

By default, you can access it via HTTP on port `8080` of whatever value you used for the node's `--host` value. For example, `http://<any node host>:8080`.

However, you can also set the CockroachDB Admin UI to a custom port using `--http-port` or a custom hostname using `--http-host` when [starting each node](start-a-node.html) (i.e., each node's values are dependent on how it's started; there is no cluster-level configuration for non-default values). For example, if you set both a custom port and hostname, `http://<http-host value>:<http-port value>`.

For additional guidance on accessing the Admin UI, see [Start a Local Cluster](start-a-local-cluster.html) and [Manual Deployment](manual-deployment.html).

## Cluster Overview

Cluster Overview is the first view you see when you access the Admin UI. 
Cluster Overview is divided into three panels:


- 	[Time series graphs](http://localhost:4000/docs/dev/explore-the-admin-ui.html#time-series-graphs)
- 	Summary panel
- 	Events list

#### Time Series Graphs

The Admin UI displays time-series graphs of key metrics. Time series graphs are useful to visualize and monitor short-term and long-term data trends. 
You can hover over each graph to see actual point-in-time values. You can also change the time range by clicking the left and right arrows on the time window.

<Insert screenshot here>

{{site.data.alerts.callout_info}}The Admin UI shows UTC time, even if you set a different time zone for your cluster. {{site.data.alerts.end}}

The time series panel displays the graphs for the following key metrics:

#### SQL Queries
<Insert screenshot here>

The graph displays the average number of SELECT, INSERT, UPDATE, and DELETE statements per second across all nodes in the cluster.
<Need to talk about how they can benefit from monitoring this graph>

#### Service Latency: SQL, 99th percentile
<Insert screenshot here>
The graph displays the time required for each node to execute 99% of the queries over the last minute. The service latency is calculated as the time between receiving a query and returning a response. This time does not include network latency between the node and client. 
<Need to talk about how they can monitor this graph to configure their cluster/network>

#### Replicas per Node
<Insert screenshot here>
The graph displays the number of range replicas on each node. 
Ranges are subsets of your data, which are replicated to ensure survivability. Ranges are replicated to a configurable number of additional CockroachDB nodes. 
For more information, read about how CockroachDB ensures [high availability](https://www.cockroachlabs.com/docs/stable/high-availability.html#replication), [configuring replication zones](https://www.cockroachlabs.com/docs/stable/configure-replication-zones.html) or read the [CockroachDB design document](https://github.com/cockroachdb/cockroach/blob/master/docs/design.md#architecture).
<Need to talk about how they can monitor this graph to check and configure replication>

#### Capacity
<Insert screenshot here>
The graph displays the maximum allocated storage capacity as well as the available storage capacity for CockroachDB across all nodes.
On hovering over the graph, you can see the Capacity and Available values across all nodes:
Capacity refers to the maximum storage capacity allocated to CockroachDB across all nodes.
Available refers to the free storage capacity available to CockroachDB across all nodes.
 
You can configure the maximum allocated storage capacity for CockroachDB using the --store flag. For more information, see [Start a Node](https://www.cockroachlabs.com/docs/stable/start-a-node.html#store).
 
By default, the time series panel displays the metrics for the entire cluster. To view the metrics for an individual node, select the node from the Graph drop-down list.
<Insert screenshot here>

In addition to the Overview dashboard, you can also monitor the following dashboards to gain insight into your cluster’s health and performance:


-	Runtime dashboard
-	SQL dashboard
-	Storage dashboard
-	Replication dashboard

{{site.data.alerts.callout_info}}The CockroachDB Admin UI has three additional dashboards: Distributed, Queues, and Slow Requests. These dashboards are important to CockroachDB developers, but for monitoring and administering purposes, it is sufficient to monitor the Overview, Runtime, SQL, Storage, and Replication dashboards.{{site.data.alerts.end}}

## Summary panel
<Insert screenshot here>
The Summary panel provides information about:

Metric | Description
--------|----
Total Nodes | Total number of nodes in the cluster.<br><br>You can further drill down into the nodes details by clicking on **View nodes list**.
Capacity Used | Storage capacity used as a percentage of total storage capacity allocated across all nodes.
Unavailable Ranges | 
Queries per second | 
P50 Latency | 
P99 Latency | 


## Live Nodes
To see the list of live nodes in your cluster, click **View nodes list** on the Summary panel.
<Insert screenshot here>
You can click on any of the nodes to view details about the node.
<Insert screenshot here>
Once you click on the node and see the details, you can click on **View Logs** to see the logs for that node.
<Insert screenshot here>

## Events list
The Events panel displays the 10 most recent events logged for the cluster.
To see the list of all events, click **View all events** on the Events panel.











