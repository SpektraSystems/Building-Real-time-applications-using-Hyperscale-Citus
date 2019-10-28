## Dashboarding on Hyperscale (Citus) using Power BI

You can use Power BI to analyze and visualize your PostgreSQL data. Using Power BI, you get the ability to centralize your data, simplify it with queries you create, and share it in highly visual reports. Power BI has both direct query and import support for PostgreSQL i.e either you can generate reports that directly run queries/aggregates on the database or by pulling and caching data in Power BI. These functionalities should work with both Single Server and Hyperscal (Citus) options of Azure Database for PostgreSQL.
 
In real-time analytics workloads, you can use Power BI to query both rollup tables and raw tables in Hyperscale (Citus). With larger datasets you could use the direct query functionality for better scalability. Because of sharding and parallelism across multiple nodes dashboard queries would be super snappy. For smaller datasets you could use the import functionality to create reports. Along with performance, Hyperscale (Citus) also lets you have large number of users concurrently running reports in Power BI.
 
In this exercise, you will connect to your PostgreSQL database cluster in Power BI Desktop and view/create advanced visualizations: