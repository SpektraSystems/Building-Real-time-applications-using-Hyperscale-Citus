## Lab 6: Approximate Distinct Counts

A common question in HTTP operational analytics deals with approximate distinct counts: How many unique visitors visited your site over the last month? Answering this question exactly requires storing the list of all previously-seen visitors in the rollup tables, a prohibitively large amount of data. However an approximate answer is much more manageable.

A datatype called hyperloglog, or HLL, can answer the query approximately; it takes a surprisingly small amount of space to tell you approximately how many unique elements are in a set. Its accuracy can be adjusted. Without HLLs this query involves shipping lists of IP addresses from the workers to the coordinator for it to deduplicate. By using HLLs you can greatly improve query speed.

For non-Hyperscale (Citus) installs you much first you must install the HLL extension and enable it. You would run the Psql command CREATE EXTENSION hll; on all nodes in this case. This is not necessary on Azure as Hyperscale (Citus) already comes with HLL installed, along with other useful Extensions.
Now we’re ready to track IP addresses in our rollup with HLL. First add a column to the rollup table.

### Task 1: Track IP addresses in Rollup

 1. Open a **New Query** console and paste the following:

```
ALTER TABLE http_request_1min ADD COLUMN distinct_ip_addresses hll; 
```

<kbd>![](images/query7rollup.png)</kbd>

 
2. Next we will use our custom aggregation to populate the column. Open a **New Query** console and paste the following to add it to the query of our rollup function.

```
-- function to do the rollup
CREATE OR REPLACE FUNCTION rollup_http_request() RETURNS void AS $$
DECLARE
curr_rollup_time timestamptz := date_trunc('minute', now());
last_rollup_time timestamptz := minute from latest_rollup;
BEGIN
INSERT INTO http_request_1min (
    site_id, ingest_time, request_count,
    success_count, error_count, average_response_time_msec,
    distinct_ip_addresses
) SELECT
    site_id,
    date_trunc('minute', ingest_time),
    COUNT(1) as request_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
    SUM(response_time_msec) / COUNT(1) AS average_response_time_msec,
    hll_add_agg(hll_hash_text(ip_address)) AS distinct_ip_addresses
FROM http_request
-- roll up only data new since last_rollup_time
WHERE date_trunc('minute', ingest_time) <@
        tstzrange(last_rollup_time, curr_rollup_time, '(]')
GROUP BY 1, 2;

-- update the value in latest_rollup so that next time we run the
-- rollup it will operate on data newer than curr_rollup_time
UPDATE latest_rollup SET minute = curr_rollup_time;
END;
$$ LANGUAGE plpgsql;
```

<kbd>![](images/query8rollup.png)</kbd>

3.	Then open a **New Query** console and paste the following to execute the updated function.

```
SELECT rollup_http_request(); 
```

<kbd>![](images/query8rollup1.png)</kbd>

 
4. Dashboard queries are a little more complicated, you have to read out the distinct number of IP addresses by calling the hll_cardinality function.

5. For this open a **New Query** console and paste the following to create a report using the hll_cardinality function 

```
SELECT site_id, ingest_time as minute, request_count, 
    success_count, error_count, average_response_time_msec, 
    hll_cardinality(distinct_ip_addresses)::bigint AS distinct_ip_address_count 
FROM http_request_1min 
WHERE ingest_time > date_trunc('minute', now()) - interval '5 minutes' 
LIMIT 15;
```

<kbd>![](images/query8rollup2.png)</kbd>

HLLs aren’t just faster, they let you do things you couldn’t previously. Say we did our rollups, but instead of using HLLs we saved the exact unique counts. This works fine, but you can’t answer queries such as “how many distinct sessions were there during this one-week period in the past we’ve thrown away the raw data for?”.
With HLLs, this is easy. You can compute distinct IP counts over a time period with the following query
 
6. open a **New Query** console and paste the following to compute distinct IP counts over time.

```
SELECT hll_cardinality(hll_union_agg(distinct_ip_addresses))::bigint
FROM http_request_1min
WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval
LIMIT 15;
```

<kbd>![](images/query8rollup3.png)</kbd>

## Task 2: Unstructured Data with JSONB

Hyperscale (Citus) works well with Postgres’ built-in support for unstructured data types. To demonstrate this, let’s keep track of the number of visitors which came from each country. Using a semi-structure data type saves you from needing to add a column for every individual country and ending up with rows that have hundreds of sparsely filled columns. 

PostgreSQL has JSONB and JSON data types for storing JSON data. The recommended data type is JSONB because a) indexing capabilities (GIN and GIST) of JSONB compared to JSON and b) JSONB provides compression because of binary format. Here we’ll demonstrate how to incorporate JSONB columns into your data model.
 
1. Open a **New Query** console and paste the following to add a new JSONB column to our rollup table 

```
ALTER TABLE http_request_1min ADD COLUMN country_counters JSONB; 
```

2. Now open a **New Query** console and paste the following to update the rollup_http_request function with country_counters 

```
-- function to do the rollup
CREATE OR REPLACE FUNCTION rollup_http_request() RETURNS void AS $$
DECLARE
curr_rollup_time timestamptz := date_trunc('minute', now());
last_rollup_time timestamptz := minute from latest_rollup;
BEGIN
INSERT INTO http_request_1min (
    site_id, ingest_time, request_count,
    success_count, error_count, average_response_time_msec,
    distinct_ip_addresses,
    country_counters
) SELECT
    site_id,
    date_trunc('minute', ingest_time),
    COUNT(1) as request_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
    SUM(response_time_msec) / COUNT(1) AS average_response_time_msec,
    hll_add_agg(hll_hash_text(ip_address)) AS distinct_ip_addresses,
    jsonb_object_agg(request_country, country_count) AS country_counters
FROM (
    SELECT *,
        count(1) OVER (
        PARTITION BY site_id, date_trunc('minute', ingest_time), request_country
        ) AS country_count
    FROM http_request
    ) h
-- roll up only data new since last_rollup_time
WHERE date_trunc('minute', ingest_time) <@
        tstzrange(last_rollup_time, curr_rollup_time, '(]')
GROUP BY 1, 2;

-- update the value in latest_rollup so that next time we run the
-- rollup it will operate on data newer than curr_rollup_time
UPDATE latest_rollup SET minute = curr_rollup_time;
END;
$$ LANGUAGE plpgsql;
```

<kbd>![](images/query9rollup.png)</kbd>

3. In the **New Query** console enter the following to execute the updated function .

```
SELECT rollup_http_request(); 
```

<kbd>![](images/rollup10.png)</kbd>

Now, if you want to get the number of requests which came from America in your dashboard, your can modify the dashboard query to look like this.
 
4. Then replace the above with the following to see the requests from America.
.
```
SELECT
request_count, success_count, error_count, average_response_time_msec,
COALESCE(country_counters->>'USA', '0')::int AS american_visitors
FROM http_request_1min
WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval
LIMIT 15;
```

<kbd>![](images/query9rollup1.png)</kbd>

5. Click **Next** on the bottom right of this page.
