# Lab 06 - Data Activator (Optional)

**Introduction**

Data Activator is an observability tool for automatically monitoring and
taking actions when certain conditions (or patterns) are detected in a
datastream. Common use cases include monitoring IoT devices, sales data,
performance counters, and system health. Actions are typically
notifications (such as e-mails or Teams messages) but can also be
customized to launch Power Automate workflows, for example.

Currently, Data Activator can consume data from Power BI and Fabric
Eventstreams (such as Azure Event Hubs, Kafka, etc.). In the future,
additional sources (like KQL querysets) and more advanced triggering
will be added. Read the [Data Activator
roadmap](https://learn.microsoft.com/en-us/fabric/release-plan/data-activator) for
more information.

In this lab, we'll use Data Activator to monitor both Eventstreams and
Power BI reports. When we configure Data Activator, we set up one or
more *Reflexes*. A Data Activator Reflex is the container that holds all
of the information needed about the data connection, the events, and
triggers.

**Objectives**

- Configure Data Activator to detect significant price increases in
  stock symbols.

- Set up a Reflex in Data Activator to trigger notifications based on
  price fluctuations.

- Utilize Eventstream data to monitor and analyze stock price changes in
  real-time.

- Create a Data Activator Reflex triggered by conditions in a Power BI
  report.

- Customize Power BI report visuals for optimal readability and
  compatibility with Data Activator.

- Set thresholds and configure alerts in Data Activator to notify users
  of significant changes in data.

# Exercise 1: Using Data Activator with an Eventstream

In this exercise, we'll configure Data Activator to detect a large
increase in price of a stock symbol and send a notification.

## Task 1: Prepare the report

1.  On the **RealTimeStocks** page, click on **RealTimeWorkspace**
    Workspace on the left-sided navigation menu.

<img src="./media/image1.png" style="width:6.34167in;height:7.1in" />

2.  In **RealTimeWorkspace** window, navigate and click on
    **StockEventStream.**

<img src="./media/image2.png" style="width:6.49167in;height:4.5in" />

3.  On the **Eventstream**, click on the **Edit** button

<img src="./media/image3.png" style="width:6.49167in;height:3.975in" />

4.  On the ***StockEventstream*** page, add a new output by dropdown the
    **Add destination**, and select ***Reflex*** as shown in the below
    image.

> <img src="./media/image4.png" style="width:6.5in;height:3.90833in" />

5.  Configure the Reflex as follows and then click on the ***Save***
    button**:**

- Destination name: +++**Reflex+++**

- Workspace: **RealTimeWorkspace** (or the name of your workspace)

- Create a new Reflex named +++**EventstreamReflex+++** and click on
  **Done** button.

> <img src="./media/image5.png" style="width:3in;height:4.575in" />

6.  You will get a notification that the destination “Reflex” was
    **Successfully added**.

> <img src="./media/image6.png"
> style="width:3.13493in;height:1.22563in" />

6.  Connect **StockEventStream** and **Reflex.** Click on **Publish**
    button.

> <img src="./media/image7.png" style="width:6.5in;height:3.68333in" />

7.  You will get a notification that the destination “Reflex” was
    **Successfully published**.

> <img src="./media/image8.png" style="width:2.97526in;height:1.87516in"
> alt="A screenshot of a phone Description automatically generated" />

8.  After the Reflex is added, open the Reflex by clicking the ***Open
    item* link** at the bottom of the page as shown in the below image.

> <img src="./media/image9.png" style="width:6.49167in;height:4.2in" />
>
> **Note**: In case, you see Error in the Status of Reflex, then wait
> for few minutes and refresh the page.
>
> <img src="./media/image10.png"
> style="width:7.08805in;height:3.58946in" />

## Task 2: Configure the object

1.  In **StockEventStram-Reflex** window, enter the following details in
    the **Assign your data*** *pane*.* Then, click on ***Save*** and
    select ***Save and go to design mode***.

- Object name - **Symbol**

- Assign key column **- symbol**

- Assign properties - select **price,timestamp**

<img src="./media/image11.png"
style="width:6.49167in;height:3.41667in" />

<img src="./media/image12.png" style="width:6.49167in;height:3.65in" />

2.  Once saved, the Reflex will load. Select ***price* **under property.

> <img src="./media/image13.png" style="width:6.5in;height:5.45in" />

<img src="./media/image14.png" style="width:6.94502in;height:3.23359in"
alt="A screenshot of a computer Description automatically generated" />

3.  This will load a view of the price property for each symbol as the
    events are coming in. On the right side of the page, click on the
    dropdown beside ***Add***, then navigate and
    select ***Summarize* \> *Average over time ***as shown in the below
    image.

<img src="./media/image15.png"
style="width:7.08561in;height:3.2125in" />

4.  Configure the ***Average over time*** to **10 minutes**. In the
    upper right corner of the page, set the time window to the ***Last
    Hour***, as shown in the below image. This step averages the data
    over 10 minute blocks - this will help in finding larger swings in
    price.

<img src="./media/image16.png"
style="width:7.17749in;height:3.04583in" />

<img src="./media/image17.png" style="width:6.5in;height:2.90417in"
alt="A graph with different colored lines Description automatically generated" />

5.  To add a new trigger, in the top navigation bar, click on the ***New
    Trigger*** button. In the **Unsaved change** dialog box, click on
    the **Save** button.

<img src="./media/image18.png" style="width:6.5in;height:3.65833in" />

<img src="./media/image19.png"
style="width:6.05833in;height:1.53333in" />

<img src="./media/image20.png" style="width:6.987in;height:3.95791in"
alt="A screenshot of a computer Description automatically generated" />

6.  When the new trigger page loads, change the name of the trigger
    to ***Price Increase*** as shown in the below image.

<img src="./media/image21.png"
style="width:6.49167in;height:3.98333in" />

7.  In the Price Increase page, click on the dropdown beside **Select a
    property or event column**, then select **Existing property** \>
    **price.**

<img src="./media/image22.png"
style="width:7.06607in;height:3.22917in" />

<img src="./media/image23.png"
style="width:7.08967in;height:3.44712in" />

8.  Verify (and change if needed) the time window in the upper right is
    set to *Last Hour*.

<img src="./media/image24.png" style="width:6.5in;height:3.125in" />

9.  Notice that the ***price* chart** should retain the summarized view,
    averaging data in 10 minute intervals. In the ***Detect*** section,
    configure the type of detection to ***Numeric*** \> ***Increases
    by***.

<img src="./media/image25.png"
style="width:7.05629in;height:3.9375in" />

10. Set the type of increase to ***Percentage***. Start with a value of
    about **6**, but you will need to modify this depending on the
    volatility of your data. Set this value to ***From last
    measurement*** and ***Each time***, as shown below:

<img src="./media/image26.png"
style="width:7.11649in;height:3.65417in" />

11. Scroll down, click on the dropdown beside **Act** and select
    **Email**. Then, click on the dropdown in the **Additional
    information** field and select the checkboxes of **price** and
    **timestamp**. Then, click on the **Save** button in the command
    bar.

<img src="./media/image27.png"
style="width:7.06125in;height:3.77083in" />

12. You will receive a notification as **Trigger saved**.

<img src="./media/image28.png"
style="width:4.54206in;height:2.3502in" />

13. Then, click on **Send me a test alert**.

<img src="./media/image29.png"
style="width:7.08352in;height:3.99583in" />

**Important Note:**  Users having a trial account won't receive
notifications.

# Exercise 2: Using Data Activator in Power BI

In this exercise, we'll create a Data Activator Reflex based off a Power
BI report. The advantage in this approach is the ability to trigger off
of more conditions. Naturally, this might include data from the
Eventstream, loaded from other data sources, augmented with DAX
expressions, and so forth. One current limitation (which may change as
Data Activator matures): Data Activator monitors Power BI reports for
data every hour. This may introduce an unacceptable delay, depending on
the nature of the data.

## Task 1: Prepare the report

For each report, modify the labels for each visual by renaming them.
While renaming them obviously makes the report more readable in Power
BI, this will also make them more readable in Data Activator.

1.  Now, click on **RealTimeWorkspace** on the left-sided navigation
    menu.

<img src="./media/image30.png" style="width:5.725in;height:7.36667in" />

2.  In the **RealTimeWorkspace** pane, navigate and click on
    **RealTimeStocks** as shown in the below image.

<img src="./media/image31.png" style="width:6.5in;height:4.675in" />

<img src="./media/image32.png" style="width:6.5in;height:3.66389in" />

3.  On the **RealTimeStock** page, click on the **Edit** button in the
    command bar to open the report editor.

<img src="./media/image33.png"
style="width:6.49167in;height:3.09167in" />

4.  While modifying the report, it's best to disable auto-refresh
    temporarily. Select Formal page under Visualization and select the
    **Page refresh** as **Off.**

<img src="./media/image34.png" style="width:6.49167in;height:2.725in" />

5.  On the **RealTimeStock** page ,select the **Sum of price by
    timestamp and symbol**.

6.  Now rename them by selecting the drop-down on each field,
    selecting ***Rename for this visual*.** Rename them similar to:

- *timestamp* to ***Timestamp* **

> <img src="./media/image35.png" style="width:6.5in;height:3.03288in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image36.png" style="width:6.5in;height:3.21691in"
> alt="A screenshot of a computer Description automatically generated" />

- *sum of price* to *Price*

> <img src="./media/image37.png" style="width:5.66667in;height:7.11667in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image38.png" style="width:6.5in;height:3.30452in"
> alt="A screenshot of a computer screen Description automatically generated" />

- *symbol* to ***Symbol* **

> <img src="./media/image39.png" style="width:3.04583in;height:5.0305in"
> alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image40.png" style="width:7.07618in;height:3.8375in"
alt="A screenshot of a graph Description automatically generated" />

7.  On the **RealTimeStock** page, select the **Sum of
    percentdifference_10min by timestamp and symbol**.

> <img src="./media/image41.png" style="width:6.49167in;height:3.90833in"
> alt="A screenshot of a computer screen Description automatically generated" />

8.  Now rename them by selecting the drop-down on each field,
    selecting ***Rename for this visual*.** Rename them similar to:

- timestamp to **Timestamp** 

- symbol to **Symbol** 

- avg of percentdifference_10min to **Percent Change**

<img src="./media/image42.png" style="width:6.5in;height:5.06667in" />

9.  Now, temporarily remove the **Timestamp** filter (set to display
    only the most recent 5 minutes) by clicking on the ***Clear
    filter*** button under **Filters** section.

<img src="./media/image43.png"
style="width:6.89583in;height:4.93951in" />

10. Data Activator will pull report data once every hour; when the
    Reflex is configured, filters are also applied to the configuration.
    We want to make sure there's at least an hour of data for the
    Reflex; the filter can be added back after the Reflex is configured.

<img src="./media/image44.png"
style="width:7.25417in;height:4.0659in" />

## Task 2: Create the trigger

We'll configure Data Activator to trigger an alert when the Percent
Change value moves above a certain threshold (likely around 0.05).

1.  To create a **new Reflex** and **trigger**, click on the
    **horizontal** **ellipsis**, navigate and click on ***Set alert***
    as shown in the below image.

<img src="./media/image45.png"
style="width:7.14854in;height:4.0875in" />

2.  In the *Set an alert* pane, most settings will be pre-selected. Use
    the following settings as shown in the below image:

- Visual: **Precent Change by Timestamp and Symbol**

- Measure: **Percent Change**

- Condition: **Becomes greater than**

- Threshold**: 0.05** (this will be changed later)

- Filters: **verify there are no filters affecting the visual**

- Notification type**: Email**

- Uncheck ***Start my alert*** and click on ***Create alert*** button.

> <img src="./media/image46.png"
> style="width:3.65833in;height:6.59167in" />
>
> <img src="./media/image47.png" style="width:3.525in;height:6.25833in" />

3.  After the Reflex is saved, the notification should include a link to
    edit the Reflex -- click on the link to open the Reflex. The Reflex
    can also be opened from the workspace items list.

<img src="./media/image48.png" style="width:5.11667in;height:2.475in" />

<img src="./media/image49.png"
style="width:7.07391in;height:3.46516in" />

## Task 3: Configure the Reflex

1.  When the new trigger page loads, click on the pencil icon on the
    title and change the title to ***Percent Change High***.

> <img src="./media/image50.png" style="width:6.5in;height:4.69167in" />
>
> <img src="./media/image51.png" style="width:6.5in;height:5.23333in" />

2.  Select the Last 24 Hours.

> <img src="./media/image52.png"
> style="width:7.04664in;height:2.95417in" />

3.  Next, add two properties for Symbol and Timestamp.

4.  Click on ***New Property*** in the upper left corner of the page,
    click on the dropdown beside Select a property or even column \>
    Column from an event stream or record \> Percent Change \> Symbol.

> <img src="./media/image53.png"
> style="width:6.93246in;height:3.60417in" />
>
> <img src="./media/image54.png" style="width:6.92in;height:3.02917in" />

5.  Similarly, click on the dropdown beside Select a property or even
    column \> Column from an event stream or record \> Percent Change \>
    **timestamp** as shown in the below images. Click on the pencil icon
    beside timestamp and change the name to Timestamp.

> <img src="./media/image55.png" style="width:6.5in;height:3.69167in" />
>
> <img src="./media/image56.png" style="width:6.5in;height:3.825in" />

6.  Click on the Percent Change High trigger under the Objects \>
    Triggers list. The top window will show data for the past 4 hours,
    and will be updated every hour. The second window defines the
    detection threshold. You may need to modify this value to make it
    either more or less restrictive. Increasing the value will reduce
    the number of detections -- change this value so there are a few
    detections, similar to the image below. The specific values will
    change slightly with your data volatility.

> <img src="./media/image57.png"
> style="width:7.06343in;height:3.34583in" />
>
> <img src="./media/image58.png"
> style="width:6.49167in;height:4.00833in" />
>
> <img src="./media/image59.png"
> style="width:6.49167in;height:3.79167in" />
>
> <img src="./media/image60.png"
> style="width:6.15625in;height:3.28735in" />

## Task 4: Configure the notification

1.  Finally, configure the *Act* to send an message, as done in the
    previous Reflex. Click in the **Additional information** field and
    select **Percent Change**, **Symbol**, **Timestamp**, then click on
    the **Save** button as shown in the below image.

> <img src="./media/image61.png"
> style="width:6.90176in;height:3.29583in" />

2.  Click on **Send me a test alert**.

> <img src="./media/image62.png"
> style="width:6.85627in;height:3.2125in" />

**Important Note:**  Users having a trial account won't receive
notifications.

## **Summary**

In this lab, you’ve configured Data Activator to monitor stock price
changes and triggered notifications based on predefined criteria. You’ve
set up the necessary workspace and navigated to the RealTimeStocks page.
Then, you’ve added a Reflex output to the StockEventStream and
configured it to detect large price increases. The Reflex was configured
to analyze price data over 10-minute intervals and sent email
notifications when significant price changes are detected. You’ve also
learned how to test the Reflex to ensure proper functionality.

Then, you’ve created a Data Activator Reflex triggered by conditions
within a Power BI report. You’ve modified the Power BI report visuals to
ensure compatibility with Data Activator and set appropriate labels for
each visual element. Then, you’ve configured alerts in Data Activator to
trigger notifications when specific thresholds are exceeded, such as a
significant percentage change in stock prices. Additionally, you’ve
learned how to test the Reflex and customize the notification content to
include relevant information such as timestamp and symbol.
