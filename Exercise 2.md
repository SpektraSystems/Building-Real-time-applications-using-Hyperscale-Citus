## Lab 2: Connecting to Hyperscale (Citus) on Azure Database for PostgreSQL

Here we will start wroking with Azure Data Studio by connecting our database to it.

### Task 1: Connect to the database using Psql in Azure Data Studio

Now we will connect to PostgreSQL Database through Azure Data Studio.

1. Open **Azure Data Studio**. On the left click on **Extension** icon, then in the search bar enter **PostgreSQL** and select **Install**. After this you may need to restart the Azure Data Studio.

<kbd>![](images/postext.png)</kbd>


2. Now select **New Connection** button to establish connection with the postgreSQL database.


<kbd>![](images/azdatastudio.png)<kbd>


3.  Use following configurations for **Connection Details**:
* Connection type: select **PostgreSQL** from the dropdown
* Server Name: **srvxxxxx.postgres.database.azure.com** (go to Azure Database for PostgreSQL server v2 - PREVIEW **srvxxxx**, on the       top right corner locate the server name)
* Username: **citus**
* Passwword: **Password@123**
* Database Name: **citus**
* Server Group: For server group name, select **Add server group** from the dropdown and enter your server group name i.e., **postgrexxxxx** as shown below:


<kbd>![](images/newconnection2.png)<kbd>


4. **Connection details** should look similar to the below, then select **Connect**:


<kbd>![](images/newconnection1.png)<kbd>


5. After getting connected, you can find your **PostgreSQL Database** under **Server** pane as shown below:

<kbd>![](images/newconnection3.png	)<kbd>

6. Click **Next** on the bottom right of this page.
