# Overview

Hyperscale (Citus) has the ability to parallelize your queries across multiple workers/resources to give immense performance improvements. One workload that can benefit from this ability of parallelism is powering real-time dashboards of event data.
For example, you could be a cloud services provider helping other businesses monitor their HTTP traffic. Every time one of your clients receives an HTTP request your service receives a log record. You want to ingest all those records and create an HTTP operational analytics dashboard which gives your clients insights such as the number of HTTP errors their sites served. It’s important that this data shows up with as little latency as possible so your clients can fix problems with their sites. It’s also important for the dashboard to show graphs of historical trends.

Alternatively, maybe you’re building an advertising network and want to show clients click rates on their campaigns. In this example latency is also critical, raw data volume is also high, and both historical and live data are important.
For this experience we will instruct you on how to deal with a number of real-time and scaling issues using Hyperscale (Citus) on Azure Database for PostgreSQL. 
