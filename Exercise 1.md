## **Lab 1: Getting started with Hyperscale (Citus)**

For a successful connection into Hyperscale (Citus) on Azure Database for PostgreSQL, we need to enable firewall services.

### Task 1: Configure server-level firewall service

Follow the instructions given below to allow yourself access to the Hyperscale (Citus) server group.
 
1. In the **Azure Portal** go to **Home**, then locate **Azure services** and select **Azure Database for PostgreSQL servers** on homepage.

![](images/azpostgresql.png)


3. Click on your PostgreSQL Database **postgreXXXXX**.

![](images/azpostgresql1.png)


4. On the overview pane under **Security** click **Networking** and put **Allow Azure Service** to **YES**.

![](images/2postgresqlfw.png)

5. Now add **Firewall Rule**. Select **Add current client IP address**, this will add the client IP by creating a new rule. Then **save** the changes.

![](images/firewallip1.png)

6. Click **Next** on the bottom right of this page.
