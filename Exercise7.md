## **Lab 7: Dashboarding on Hyperscale (Citus) using Power BI**

Using Power BI, you get the ability to centralize your data, simplify it with queries you create, and share it in highly visual reports. Power BI has both direct query and import support for PostgreSQL i.e you can both generate reports that directly run queries/aggregates on the database or by pulling and caching data in Power BI. These features work with both Single Server and Hyperscale (Citus) deployment options for Azure Database for PostgreSQL.

In real-time analytics workloads, you can use Power BI to query both rollup tables and raw tables in Hyperscale (Citus). With larger datasets you could use the direct query functionality for better scalability. Because of sharding and parallelism across multiple nodes dashboard queries would be super snappy. For smaller datasets you could use the import functionality to create reports. Along with performance, Hyperscale (Citus) also lets you have large number of users concurrently running reports in Power BI.
 
In this exercise, you will connect to your PostgreSQL database cluster in Power BI Desktop and view/create advanced visualizations:

1.Open **PowerBI** given in left side on the desktop. A welcome page will open where you have to select **Already have PowerBI account?Sign in**.

![](images/citus1.png)

2.Now provide your **Username** as shown below:

 ![](images/powerbi10.png)

Then enter **Password** to successfully sign in.

 ![](images/powerbi8.png)

3.Now select **Get Data** as shown below:

 ![](images/citus4.png)

4.A **Get Data** block will appear. Select **Database** > **PostgreSQL database** and then **Connect**.

 ![](images/getdata1.png)

5.Now connect to the database by providing your **Server name** and **Database name** (copy both from environment details tab). For **Data Connectivity Mode** select **Direct query**. Then select **Ok**.

 ![](images/getdata2.png)

6.Navigate to the **Environment Details** tab, copy and paste your **PostgreSQL Database Username** and **PostgreSQL Database Password** in their respective blocks as shown below:

 ![](images/dbconnect.png)

Then select **Connect**.

7.Now in the **Navigator** select **public.http_request_1min** table given under the server and select **Load**.

 ![](images/getdata3.png)

8.Then in the right hand side in PowerBI, review the table you selected in the previous step under **Fields**.

 ![](images/getdata4.png)

9.Now select **Clustered Column Chart** as shown below. Then drag **site_id** under **Axis**.

 ![](images/graph1.png)

10.Now drag **error_count**, **request_count** and **success_count** under **Value** as shown below:

 ![](images/graph2.png)

11.Now add filter by dragging **ingest_time** under **Filters on this page**.

 ![](images/graph3.png)

12.It will look like as shown below. Then select **Filter Type** as **Basic Filtering** from the dropdown.

 ![](images/graph4.png)

13.Now select a randome ingest time as shown below.

 ![](images/graph5.png)

14.The final graph will look similar to the one shown below.

 ![](images/graph6.png)

You can explore more on the PowerBI Dashboard to visualize the data. As a reference, we've another PowerBI dashboard draft file saved on desktop which includes a more comprehensive pre-built PowerBi dashboard for the same data. Let's take a look at that:

15.Now Open file **Real-Time-Data-Analytics-Draft** given on the VM desktop.

16.This will give a pop-up asking for the username and password, select **Cancel** for it and then **Close** the **Cannot load model** note.

![](images/cancel.png)

![](images/cancel1.png)

17.Select **Got it** for the popup- **Introducing the new filter blade** and for **Welcome to ArcGIS Maps for PowerBI** select **Ok**. 

18.Now go to **Edit Queries** and from the dropdown arrow select **Data Source Settings**.

![](images/datasource.png)

19.The **Data Source Settings** tab will open, select the given server hostname and then click on **Change Source**.

![](images/sourcechange.png)

20.Enter your **server hostname** and **database name** from the Environment Details tab. Then select **Ok** and then **Close**.

![](images/changesource1.png)

21.You will get a warning saying **There are pending changes in your queries that haven't been applied.**, here select **Apply Changes**.

![](images/applychanges.png)

22.This will direct you to the PowerBI Report.

![](images/pbixfile.png)

23.You can explore the dashboard and report configuration here. 

24.Click **Next** on the bottom right of this page.



