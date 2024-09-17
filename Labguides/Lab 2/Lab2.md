# Lab 02: Using KQL and building reports

**Introduction**

Now that our data is streaming into our KQL database, we can begin to
query and explore the data, leveraging KQL to gain insights into the
data. A KQL queryset is used to run queries, view, and transform data
from a KQL database. Like other artifacts, a KQL queryset exists within
the context of a workspace. A queryset can contain multiple queries each
stored in a tab. In this exercise, we'll create several KQL queries of
increasing complexity to support different business uses.

**Objectives**

- To explore stock price data using KQL, progressively developing
  queries to analyze trends, calculate price differentials, and
  visualize data for actionable insights.

- To leverage Power BI to create dynamic, real-time reports based on
  analyzed stock data, configuring auto-refresh settings for timely
  updates and enhancing visualization for informed decision-making.

# Exercise 1: Exploring the Data

In this exercise, you'll create several KQL queries of increasing
complexity to support different business uses.

## Task 1: Create KQL queryset: StockQueryset

1.  Click on **RealTimeWorkspace** on the left-sided navigation pane.

<img src="./media/image1.png" style="width:6.5in;height:6.40139in"
alt="A screenshot of a computer Description automatically generated" />

2.  From your workspace, click on ***+*** **New* \> *KQL Queryset** as
    shown in the below image. In the **New KQL Queryset** dialog box,
    enter ***StockQueryset***, then click on the **Create**
    button.

<img src="./media/image2.png" style="width:6.5in;height:6.17431in" />

<img src="./media/image3.png" style="width:3.69722in;height:2.21944in"
alt="A screenshot of a computer Description automatically generated" />

3.  Select the ***StockDB*** and click on the **Connect** button.
    <img src="./media/image4.png" style="width:6.49236in;height:3.63611in"
    alt="A screenshot of a computer Description automatically generated" />

4.  The KQL query window will open, allowing you to query the data.

> <img src="./media/image5.png" style="width:6.5in;height:3.94167in"
> alt="A screenshot of a computer Description automatically generated" />

5.  The default query code will look like the code shown in the below
    image; it contains 3 distinct KQL queries. You may
    see *YOUR_TABLE_HERE* instead of the ***StockPrice*** table. Select
    and delete them.

6.  <img src="./media/image5.png" style="width:6.86286in;height:2.86776in"
    alt="A screenshot of a computer Description automatically generated" />

7.  In the query editor, copy and paste the following code. Select the
    entire text and click on the ***Run*** button to execute the query.
    After the query is executed, you will see the results.

>>```**Copy**
>>// Use "take" to view a sample number of records in the table and check the data.
>>StockPrice
>>| take 100;
>>
>>// See how many records are in the table.
>>StockPrice
>>| count;
>>
>>// This query returns the number of ingestions per hour in the given table.
>>StockPrice
>>| summarize IngestionCount = count() by bin(ingestion_time(), 1h);


***Note:** To run a single query when there are multiple queries in the
editor, you can highlight the query text or place your cursor so the
cursor is in the context of the query (for example, at the beginning or
end of the query) -- the current query should highlight in blue. To run
the query, click *Run* in the toolbar. If you'd like to run all 3 to
display the results in 3 different tables, each query will need to have
a semicolon (;) after the statement, as shown below.*

<img src="./media/image6.png" style="width:6.5in;height:2.73472in" />

8.  The results will be displayed in 3 different tables as shown in the
    below image. Click on each table tab to review the data.

<img src="./media/image7.png" style="width:6.49236in;height:4.34861in"
alt="A screenshot of a computer Description automatically generated" />

> <img src="./media/image8.png" style="width:6.5in;height:5.39375in" />
>
> <img src="./media/image9.png"
> style="width:6.49236in;height:4.56806in" />

## Task 2: New Query of StockByTime

1.  Create a new tab within the queryset by clicking on the ***+* icon**
    as shown in the below image. Rename this tab as
    +++***StockByTime***+++

> <img src="./media/image10.png"
> style="width:6.49236in;height:5.06806in" />
>
> <img src="./media/image11.png"
> style="width:6.49236in;height:4.95486in" />
>
> <img src="./media/image12.png"
> style="width:6.22083in;height:3.9272in" />

2.  We can begin to add our own calculations, such as calculating the
    change over time. For example,
    the [prev()](https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/prevfunction) function,
    a type of windowing function, allows us to look at values from
    previous rows; we can use this to calculate the change in price. In
    addition, as the previous price values are stock symbol specific, we
    can [partition](https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/partition-operator) the
    data when making calculations.

3.  In the query editor, copy and paste the following code. Click on
    the **Run** button to execute the query. After the query is
    executed, you will see the results.

>>````Copy
>>
>>StockPrice
>>| where timestamp > ago(75m)
>>| project symbol, price, timestamp
>>| partition by symbol
>>(
>>    order by timestamp asc
>>    | extend prev_price = prev(price, 1)
>>    | extend prev_price_10min = prev(price, 600)
>>)
>>| where timestamp > ago(60m)
>>| order by timestamp asc, symbol asc
>>| extend pricedifference_10min = round(price - prev_price_10min, 2)
>>| extend percentdifference_10min = round(round(price - prev_price_10min, 2) / prev_price_10min, 4)
>>| order by timestamp asc, symbol asc


<img src="./media/image13.png"
style="width:6.49236in;height:4.32569in" />

4.  In this KQL query, the results are first limited to the most recent
    75 minutes. While we ultimately limit the rows to the last 60
    minutes, our initial dataset needs enough data to lookup previous
    values. The data is then partitioned to group the data by symbol,
    and we look at the previous price (from 1 second ago) as well as the
    previous price from 10 minutes ago. Note that this query assumes
    data is generated at 1 second intervals. For the purposes of our
    data, subtle fluctuations are acceptable. However, if you need
    precision in these calculations (such as exactly 10 minutes ago and
    not 9:59 or 10:01), you'd need to approach this differently.

## Task 3: StockAggregate

1.  Create another new tab within the queryset by clicking on
    the ***+* icon** as shown in the below image. Rename this tab as
    **+++*StockAggregate*+++**

> <img src="./media/image14.png"
> style="width:6.49236in;height:3.79514in" />
>
> <img src="./media/image15.png" style="width:6.5in;height:3.90903in"
> alt="A screenshot of a computer Description automatically generated" />

2.  This query will find the biggest price gains over a 10-minute period
    for each stock, and the time it occurred. This query uses
    the [summarize](https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/summarizeoperator) operator,
    which produces a table that aggregates the input table into groups
    based on the specified parameters (in this case, *symbol*),
    while [arg_max](https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/arg-max-aggregation-function) returns
    the greatest value.

3.  In the query editor, copy and paste the following code. Click on
    the **Run** button to execute the query. After the query is
    executed, you will see the results.

> ````**Copy**
>
> StockPrice
>| project symbol, price, timestamp
>| partition by symbol
>(
>    order by timestamp asc
>    | extend prev_price = prev(price, 1)
>    | extend prev_price_10min = prev(price, 600)
>)
>| order by timestamp asc, symbol asc
>| extend pricedifference_10min = round(price - prev_price_10min, 2)
>| extend percentdifference_10min = round(round(price - prev_price_10min, 2) / prev_price_10min, 4)
>| order by timestamp asc, symbol asc
>| summarize arg_max(pricedifference_10min, *) by symbol

<img src="./media/image16.png" style="width:6.49236in;height:3.46944in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image17.png" style="width:6.5in;height:3.86389in"
alt="A screenshot of a computer Description automatically generated" />

## Task 4: StockBinned

1.  Create another new tab within the queryset by clicking on
    the ***+* icon** as shown in the below image. Rename this tab as
    ***StockBinned***

<img src="./media/image18.png" style="width:6.5in;height:3.90903in" />

<img src="./media/image19.png"
style="width:6.49236in;height:3.85625in" />

2.  KQL also has a [bin()
    function](https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/bin-function),
    which can be used to bucket results based on the bin parameter. In
    this case, by specifying a timestamp of 1 hour, the result is
    aggregated for each hour. The time period can be set to minute,
    hour, day, and so on.

3.  In the query editor, copy and paste the following code. Click on
    the **Run** button to execute the query. After the query is
    executed, you will see the results.

> ````**Copy**
>StockPrice
>
>| summarize avg(price), min(price), max(price) by bin(timestamp, 1h),symbol
>| sort by timestamp asc, symbol asc

<img src="./media/image20.png" style="width:7.20076in;height:5.10038in"
alt="A screenshot of a computer Description automatically generated" />

4.  This is particularly useful when creating reports that aggregate
    real-time data over a longer time period.

## Task 5: Visualizations

1.  Create a final new tab within the queryset by clicking on
    the ***+* icon** as shown in the below image. Rename this tab as
    ***Visualizations*.** We'll use this tab to explore
    visualizing data.

> <img src="./media/image21.png"
> style="width:6.49236in;height:2.79514in" />
>
> <img src="./media/image22.png" style="width:6.5in;height:2.41667in" />

2.  KQL supports a large number
    of [visualizations](https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/render-operator?pivots=fabric) by
    using the *render* operator. Run the below query, which is the same
    as the StockByTime query, but with an additional *render* operation
    added:

3.  In the query editor, copy and paste the following code. Click on
    the **Run** button to execute the query. After the query is
    executed, you will see the results.

>```` Copy
>StockPrice
>| where timestamp > ago(75m)
>| project symbol, price, timestamp
>| partition by symbol
>(
>    order by timestamp asc
>    | extend prev_price = prev(price, 1)
>    | extend prev_price_10min = prev(price, 600)
>)
>| where timestamp > ago(60m)
>| order by timestamp asc, symbol asc
>| extend pricedifference_10min = round(price - prev_price_10min, 2)
>| extend percentdifference_10min = round(round(price - prev_price_10min, 2) / prev_price_10min, 4)
>| order by timestamp asc, symbol asc
>| render linechart with (series=symbol, xcolumn=timestamp, ycolumns=price)


<img src="./media/image23.png" style="width:7.2814in;height:3.54735in"
alt="A screenshot of a computer Description automatically generated" />

4.  This will render a line chart as shown in the below image.

<img src="./media/image24.png" style="width:7.35905in;height:3.641in"
alt="A screenshot of a graph Description automatically generated" />

# Exercise 2: Optimizing Power BI Reporting Efficiency

With the data loaded in the database and our initial KQL queryset
complete, we can begin to craft visualizations for real-time dashboards.

## Task 1: Configuring Refresh Rate

Our Power BI tenant needs to be configured to allow for more frequent
updating.

1.  To configure this setting, navigate to the Power BI admin portal by
    clicking on the ***Settings* **icon in the upper right corner of the
    **Fabric portal**. Navigate to Governance and insights section, then
    click on **Admin portal**.

<img src="./media/image25.png"
style="width:3.9125in;height:6.35976in" />

2.  In the **Admin portal** page, navigate and click on **Capacity
    settings***,* then click on **Trial** tab. Click on your capacity
    name.

> <img src="./media/image26.png"
> style="width:7.17657in;height:3.0125in" />

3.  Scroll down and click on ***Power BI workloads***, and
    under ***Semantic Models*** (recently renamed from *Datasets*),
    configure ***Automatic page refresh*** to ***On***, with a **minimum
    refresh interval** of **1 Seconds**. Then, click on the **Apply**
    button.

**Note**: Depending on your administrative permissions, this setting may
not be available. Note that this change may take several minutes to
complete.

<img src="./media/image27.png"
style="width:6.49236in;height:5.40139in" />

<img src="./media/image28.png" style="width:6.5in;height:5.93958in" />

4.  On **Update your capacity workloads** dialog box, click on the
    **Yes** button.

<img src="./media/image29.png"
style="width:5.74236in;height:1.44722in" />

## Task 2: Creating a basic Power BI report

1.  In the **Microsoft Fabric** page menu bar on the left side, select
    **StockQueryset**.

<img src="./media/image30.png" style="width:6.06042in;height:7.32008in"
alt="A screenshot of a computer Description automatically generated" />

2.  From the ***StockQueryset*** queryset used in the previous module,
    select the ***StockByTime*** query tab.

<img src="./media/image31.png" style="width:6.5in;height:3.38333in" />

3.  Select the query and run to view the results. Click** **on the
    ***Build Power BI report*** button in the command bar to bring this
    query into Power BI.

<img src="./media/image32.png" style="width:6.49167in;height:4.05in" />

<img src="./media/image33.png" style="width:6.5in;height:4.46319in"
alt="A screenshot of a computer Description automatically generated" />

4.  On the report preview page, we can configure our initial chart,
    select a **line chart** to the design surface, and configure the
    report as follows. See the image below as a reference.

- Legend: **symbol**

- X-axis: **timestamp**

- Y-axis**: price**

<img src="./media/image34.png"
style="width:6.49236in;height:4.32569in" />

5.  In the Power BI (preview) page, from the ribbon, click on
    **File** and select **Save**.

> <img src="./media/image35.png" style="width:5.9425in;height:3.7375in"
> alt="A screenshot of a graph Description automatically generated" />

6.  On **Just a few details first** dialog box, in **Name your file in
    Power BI** field, enter +++***RealTimeStocks**+++*. In **Save it to
    a workspace** field, click on the dropdown and select
    ***RealTimeWorkspace***. Then, click on the **Continue** button**.**

<img src="./media/image36.png" style="width:3.17917in;height:2.63638in"
alt="A screenshot of a computer Description automatically generated" />

7.  In the Power BI (preview) page, click on **Open the file in Power BI
    to view, edit and get a shareable link.**

<img src="./media/image37.png"
style="width:6.49236in;height:4.70486in" />

8.  On the **RealTimeStock** page, click on the **Edit** button in the
    command bar to open the report editor.

> <img src="./media/image38.png" style="width:6.49236in;height:3.81806in"
> alt="A graph of different colored lines Description automatically generated" />

9.  Select the line chart on the report. Configure a **Filter**
    for ***timestamp*** to display data for the last 5 minutes using
    these settings:

- Filter type: Relative time

- Show items when the value: is in the last 5 minutes

Click on ***Apply filter*** to enable the filter. You will see a similar
type of output as shown in the below image.

<img src="./media/image39.png" style="width:7.4018in;height:3.10038in"
alt="A screenshot of a computer Description automatically generated" />

## Task 3: Creating a second visual for percent change

1.  Create a second line chart, under **Visualizations**, select **Line
    chart**.

2.  Instead of plotting the current stock price, select
    the ***percentdifference_10min*** value, which is a positive or
    negative value based off the difference between the current price
    and the value of the price from 10 minutes ago. Use these values for
    the chart:

- Legend: **symbol**

- X-axis: **timestamp**

- Y-axis: **average of percentdifference_10min**

<img src="./media/image40.png"
style="width:6.05833in;height:6.78333in" />

<img src="./media/image41.png" style="width:7.40867in;height:3.39801in"
alt="A screenshot of a computer Description automatically generated" />

3.  Under the **Visualization,** select the **Analytics** represented by
    a magnifier-like icon as shown in the below image, then click on
    **Y-Axis Constant Line(1).** In the **Apply settings to**
    section**,** click on **+Add line,** then enter **Value 0.**

<img src="./media/image42.png"
style="width:7.07083in;height:5.00132in" />

4.  Select the line chart on the report. Configure a **Filter**
    for ***timestamp*** to display data for the last 5 minutes using
    these settings:

- Filter type: Relative time

- Show items when the value: is in the last 5 minutes

<img src="./media/image43.png"
style="width:7.16929in;height:4.95417in" />

## Task 4: Configuring the report to auto-refresh

1.  Deselect the chart. On the ***Visualizations* settings**,
    enable ***Page refresh*** to automatically refresh every second or
    two, based on your preference. Of course, realistically we need to
    balance the performance implications of refresh frequency, user
    demand, and system resources.

2.  Click on **Format your report** **page** icon, navigate and click on
    **Page refresh**. Turn on the toggle. Set the Auto page refresh
    value as **2 Seconds** as shown in the below image.

<img src="./media/image44.png"
style="width:7.3928in;height:3.52083in" />

3.  In the Power BI (preview) page, from the ribbon, click on
    **File** and select **Save**.

<img src="./media/image45.png"
style="width:6.49167in;height:5.55833in" />

**Summary**

In this lab, you embarked on a comprehensive exploration of stock price
data using Kusto Query Language (KQL). Starting with the creation of a
KQL queryset named "StockQueryset," you executed a series of
increasingly complex queries to analyze various facets of the data. From
viewing sample records to calculating price differentials over time and
identifying significant price gains, each query unveils valuable
insights into the dynamics of stock prices. By leveraging windowing
functions, aggregation techniques, and data partitioning, you gained a
deeper understanding of stock price trends and fluctuations.

Then, you’ve shifted the focus to optimizing Power BI reporting
efficiency, where you configured refresh rates and crafted dynamic
visualizations for real-time dashboards. By configuring refresh rates in
the Power BI admin portal and creating Power BI reports based on the
previously defined KQL queries, you ensured timely updates and enabled
insightful visualization of stock price data. Through tasks like
creating visualizations for percent change and configuring auto-refresh
settings, you harnessed the full potential of Power BI to drive informed
decision-making and enhanced business intelligence capabilities.
