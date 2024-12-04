# Lab 01: Ingesting Data using Real-Time Intelligence

## Introduction

In this lab, you will start by rapidly generating real-time data and
understanding how that data can be processed and visualized in Microsoft
Fabric. With the initial reporting in place, multiple modules are
available that explore data warehousing, data lakehouse architecture,
data activator, data science, and of course, real-time analytics. The
modules are designed to be cohesive but flexible -- they all involve the
same core scenario but have limited dependencies so you can consume the
modules that make the most sense for you.

The basic architecture of the solution is illustrated below. The app
deployed in the beginning of this lab (either as a docker container or
running in Jupyter notebook) will publish events to our Fabric
environment. The data is ingested into a KQL database for real-time
reporting in Power BI.

In this lab, you’ll get hands-on with a fictitious financial company
"AbboCost." AbboCost would like to set up a stock monitoring platform to
monitor price fluctuations and report on historical data. Throughout the
workshop, we'll look at how every aspect of Microsoft Fabric can be
incorporated as part of a larger solution -- by having everything in an
integrated solution, you'll be able to quickly and securely integrate
data, build reports, create data warehouses and lakehouses, forecast
using ML models, and more.

 ![](./media/image1.png)

# Objectives

- To sign up for the free Microsoft Fabric trial, redeem Azure Pass, and
  configure necessary permissions within the Azure portal.

- To create fabric capacity and workspace, storage account, and fabric
  workspace.

- To deploy the stock generator app via Azure Container Instance using
  an ARM template.

- To configure Eventstream in Microsoft Fabric for ingesting real-time
  data from Azure Event Hubs, ensuring seamless integration and data
  preview for subsequent analysis.

- To create a KQL database within Microsoft Fabric and send data from
  Eventstream to the KQL database.

# Exercise 1: Environment Setup

To follow the lab exercises, a set of resources must be provisioned. At
the heart of the scenario is the real-time stock price generator script
that generates a continuous stream of stock prices that are used
throughout the workshop.

We recommend deploying the stock price generator via Azure Container
Instance because the default Spark cluster will consume a large number
of resources.

## Task 1: Sign in to Power BI account and sign up for the free [Microsoft Fabric trial](https://learn.microsoft.com/en-us/fabric/get-started/fabric-trial)

1.  Open your browser, navigate to the address bar, and type or paste
    the following URL: +++https://app.fabric.microsoft.com/+++ then
    press the **Enter** button.

    ![](./media/image2.png)

2.  In the **Microsoft Fabric** window, enter your given credentials and
    click on the **Submit** button.

     ![](./media/image3.png)

3.  Enter the **password** from the **Resources** tab and click on the
    **Sign in** button.

     ![](./media/image4.png)

4.  In **Stay signed in?** window, click on the **Yes** button.

     ![](./media/image5.png)

5.  You’ll be directed to Power BI Home page.

      ![](./media/image6.png)

## Task 2: Start the Microsoft Fabric trial

1.  On **Power BI Home** page, click on the **Account manager** **for
    MOD Administrator** icon on the top right corner of the page. In the
    Account manager blade, navigate and select **Start trial** as shown
    in the below image.

      ![](./media/image7.png)

2.  On **Upgrade to a free Microsoft Fabric** trial dialog box, click on
    **Start trial** button.

     ![](./media/image8.png)

3.  You will see a **Successfully upgraded to a free Microsoft Fabic
    trial** notification dialog box. In the dialog box, click on
    **Fabric Home Page** button.

     ![](./media/image9.png)
     ![](./media/image10.png)
 ## Task 3: Redeem Azure Pass

1.  Open a new tab on your browser and browse to the **Microsoft Azure
    Pass** website using the given link+++https://www.microsoftazurepass.com/+++.

2.  Click on **Start**.

     ![](./media/pic2.png)

3.  Enter the **Office 365 tenant credentials** from the Lab
    VM(**Resources** tab) and **Sign In**.

      ![](./media/pic3.png)
      ![](./media/pic4.png)

4.  Verify email id and then click on **Confirm Microsoft Account**.

      ![](./media/pic5.png)

5.  Paste the **promo code** from the Resources tab in the **Enter Promo
    code** box and click **Claim Promo Code**.

     ![](./media/pic6.png)

      ![](./media/pic7.png)

6.  It may take few seconds to process the redemption.

7.  Fill in the details appropriately on the **Sign up** page.

8.  On the **Agreement** window, select the check box - I agree to the
    subscription agreement, offer details, and privacy statement, and
    then click on **Sign up**.

    ![](./media/pic8.png)

9.  You may **Submit** the feedback while the account setup is in
    progress.

    ![](./media/pic9.png)
    ![](./media/pic10.png)

10. The account setup will take about 2-3 minutes to complete. It would
    automatically redirect you to the **Azure Portal** and now you are
    ready to use Azure services.

     ![](./media/pic11.png)

## **Task 4: Create a Fabric workspace**

In this task, you create a Fabric workspace. The workspace contains all
the items needed for this lakehouse tutorial, which includes lakehouse,
dataflows, Data Factory pipelines, the notebooks, Power BI datasets, and
reports.

1.  Open your browser, navigate to the address bar, and type or paste
    the following URL: +++https://app.fabric.microsoft.com/+++ then
    press the **Enter** button. In the **Microsoft Fabric Home** page,
    navigate and click on **Power BI** tile.

     ![](./media/image11.png)

2.  In the **Power BI Home** page left-sided navigation menu, navigate
    and click on **Workspaces** as shown in the below image.

     ![](./media/image12.png)

3.  In the Workspaces pane, click on **+** **New workspace button**

      ![](./media/image13.png)

4.  In the **Create a workspace** pane that appears on the right side,
    enter the following details, and click on the **Apply** button.

    | **Name** | +++RealTimeWorkspaceXXX+++(XXX can be a unique number, you can add more numbers) |
    |----|----|
    | **Advanced** | Select Trail |
    | **Default storage format** | **Small dataset storage format**|

     ![](./media/image14.png)
     ![](./media/image15.png)

## **Task 5: Deploy the app via Azure Container Instance**

This task deploys the stock generator app to an Azure Container Instance
using an ARM template. The app will generate stock data that publishes
the data to an Azure Event Hub, which is also configured during the
deployment of the ARM template.

To auto-deploy the resources, use these steps below.

1.  Open a new address bar and enter the following URL. If prompted to
    Sign in, then use your O365 tenant credentials +++https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Ffabricrealtimelab%2Fmain%2Fresources%2Fmodule00%2Ffabricworkshop_arm_managedid.json+++

2.  In the **Custom deployment** window, under the **Basics** tab, enter
    the following details and click on the **Review+create** button.

    |               |                                                                |
    |---------------|----------------------------------------------------------------|
    |Subscription   |Select the assigned subscription	                               |
    |Resource group |Click on Create new> enter +++realtimeworkshop+++ and select Ok	|
    |Region         |Select West US 3	|
    
     ![](./media/image16.png)
     ![](./media/image17.png)
4.  In the **Review + create** tab, navigate and click on the **Create**
    button.
     ![](./media/image18.png)

5.  Wait for the deployment to complete. The deployment will take around
    10-15 minutes.

6.  After the deployment is completed, click on the **Go to resource**
    button.

      ![](./media/image19.png)
7.  In **realtimeworkshop** **Resource group**, verify that the **Event
    Hub Namespace** and **Azure Container Instance (ACI)** are
    successfully deployed.
      ![](./media/image20.png)

8.  Open the **Event Hub** **namespace**, which will have a name similar
    to **ehns-XXXXXX-fabricworkshop**.

     ![](./media/image21.png)

9.  In **Event Hub** **namespace** page left-sided navigation menu,
    navigate to **Settings** section and click on **Shared access
    policies**.

      ![](./media/image22.png)

10.   In the **Shared access policies** page, click on
    ***stockeventhub_sas*** .**SAS Policy: stockeventhub_sas** pane
    appear on the right side, copy the **primary key** and **Event Hub
    namespace** (such as *ehns-XXXXXX-fabricworkshop*) and paste them on
    a notepad, as you need them in the upcoming task. In short, you'll
    need the following:

      ![](./media/image23.png)
      ![](./media/image24.png)
## **Task 6: Get data with Eventstream**

1.  Go back to the Microsoft Fabric, navigate and click on **Power BI**
    at the bottom of the page, then select **Real-Time Intelligence**.

     ![](./media/pic1.png)

2.  On the **Real-Time Intelligence** home page,
    select **Eventstream**. Name the Eventstream
    **+++StockEventStream+++**, and click on the **Create** button.

     ![](./media/pic2.png)
     ![](./media/pic3.png)
3.  On the Eventstream, select **Add external source**

     ![](./media/image28.png)

4.  On the Add source, select **Azure *Event Hubs.***

     ![](./media/image29.png)
5.  On the **Azure Event Hubs** configuration page, enter the below
    details and click on **Add** button.
      
     - Configure connection settings: Click on the **New connection** and
         enter the below details then click on **Create** button.
               
     a.  In Event Hub namespace-Enter Event Hub name (the values that you
         have saved in your notepad)
     
     b.  Event Hub : **+++StockEventHub+++**
     
     c.  Shared Access Key Name:**+++stockeventhub_sas+++**
     
     d.  Shared Access Key- Enter Primary Key (the value that you have saved
         in your notepad in the **Task 4**)
            
     e.  Consumer group: **$Default**
     
     f.  Data format: **JSON** and click on **Connect** button
          ![](./media/image30.png)
           ![](./media/image31.png)
           ![](./media/image32.png)
           ![](./media/image33.png)
           ![](./media/image34.png)

6.  You will see a notification stating **Successfully added The source
    “StockEventHub,Azure Event Hubs”** was added.

     ![](./media/image35.png)

7.  With the Event Hub configured, click on ***Test result***. You
    should see events including the stock symbol, price, and timestamp.

     ![](./media/pic4.png)

8.  On the Eventstream, select **Publish.**

     ![](./media/pic5.png)
     ![](./media/pic6.png)

9.  On the Eventstream, select **AzureEventHub** and click on
    **Refresh** button.

     ![](./media/image39.png)
     ![](./media/image40.png)

# Exercise 2: KQL Database Configuration and Ingestion

Now that our environment is fully configured, we will complete the
ingestion of the Eventstream, so that the data is ingested into a KQL
database. This data will also be stored in Fabric OneLake.

## Task 1: Create KQL Database

Kusto Query Language (KQL) is the query language used by Real-Time
analytics in Microsoft Fabric along with several other solutions, like
Azure Data Explorer, Log Analytics, Microsoft 365 Defender, etc. Similar
to Structured Query Language (SQL), KQL is optimized for ad-hoc queries
over big data, time series data, and data transformation.

To work with the data, we'll create a KQL database and stream data from
the Eventstream into the KQL DB.

1.  In the left-sided navigation menu, navigate and click on **RealTime
    workspaceXXX**, as shown in the below image.

      ![](./media/image41.png)

2.  In the **Real-Time Intelligence** page, navigate to **+New item**
    section and click on, select **Eventhouse** to create Eventhouse.

      ![](./media/image42.png)

3.  In the **New Eventhouse** dialog box, enter **+++StockDB+++** in
    the **Name** field, click on the **Create** button and open the new
    Eventhouse.

      ![](./media/image43.png)
      ![](./media/image44.png)

4.  Select StockDB, click on the **OneLake availability** as shown in
    the below image to change the setting and, then click on the **Turn
    on** button to enable OneLake access.

       ![](./media/image45.png)
       ![](./media/image46.png)
       ![](./media/image47.png)

5.  After enabling OneLake, you may need to refresh the page to verify
    the OneLake folder integration is active.

      ![](./media/image48.png)
      ![](./media/image49.png)

## Task 2: Send data from the Eventstream to the KQL database

1.  In the left-sided navigation menu, navigate and click on
    **StockEventStream** created in the previous task, as shown in the
    below image.

      ![](./media/image50.png)

2.  On the Eventstream, click on the **Edit** button.

      ![](./media/image51.png)
3.  Our data should be arriving into our Eventstream, and we'll now
    configure the data to be ingested into the KQL database we created
    in the above task. On the Eventstream, click on *Transform events or
    add destination,* then navigate and click on **Eventhouse**.

      ![](./media/image52.png)

4.  On the KQL settings, select ***Direct ingestion***. While we have
    the opportunity to process event data at this stage, for our
    purposes, we will ingest the data directly into the KQL database.
    Set the destination name to **+++KQL+++**, then select your
    **workspace**, **Eventhouse** and KQL database created in the above
    task, then click on **Save** button.

     ![](./media/image53.png)

5.  Click on the **Publish** button

      ![](./media/pic8.png)
      ![](./media/image55.png)
      ![](./media/image56.png)
6.  On the Eventstream pane, select **configure** in the **KQL**
    destination.

       ![](./media/image57.png)

7.  On the first settings page, select **+New table** and enter the
    name **+++StockPrice+++** for the table to hold the data in
    StockDB. Click on the **Next** button.

      ![](./media/image58.png)

      ![](./media/image59.png)

8.  The next page allows us to inspect and configure the schema. Be sure
    to change the format from TXT to **JSON**, if necessary. The default
    columns of *symbol*, *price*, and *timestamp* should be formatted as
    shown in the below image; then click on the *Finish* button.

      ![](./media/image60.png)

9.  On the **Summary** page, if there are no errors, you’ll see a
    **green checkmark** as shown in the below image, then click on the
    **Close** button to complete the configuration.

      ![](./media/image61.png)
      ![](./media/image62.png)
10.  Click on the **Refresh** button

     ![](./media/image63.png)

11. Select the **KQL** destination and click on the **Refresh** button.

     ![](./media/image64.png)
     ![](./media/image65.png)

**Summary**

In this lab, you’ve signed up for the Microsoft Fabric trial and
redeemed Azure Pass, followed by configuring permissions and creating
necessary resources within the Azure portal such as Fabric Capacity,
Workspace, and Storage Account. Then, you’ve deployed the stock
generator app via Azure Container Instance using an ARM template to
generate real-time stock data. Additionally, you’ve configured
Eventstream in Microsoft Fabric to ingest data from Azure Event Hubs and
prepared the KQL Database to store this data efficiently. In this lab,
you’ve established a fully functional environment to proceed with
subsequent lab exercises related to real-time analytics and data
processing.
