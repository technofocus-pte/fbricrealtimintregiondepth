# Lab 03: Building a Data Lakehouse

**Introduction**

Most of this lab will be done within Jupyter notebooks, an industry
standard way of doing exploratory data analysis, building models,
visualizing datasets, and processing data. A notebook itself is
separated into individual sections called cells, which contain code or
documentation. Cells, and even sections within cells, can adapt to
different languages as needed (though Python is the most used language).
The purpose of the cells are to break tasks down into manageable chunks
and make collaboration easier; cells may be run individually or as a
whole depending on the purpose of the notebook.

In a Lakehouse medallion architecture (with bronze, silver, gold layers)
data is ingested in the raw/bronze layer, typically "as-is" from the
source. Data is processed through an Extract, Load, and Transform (ELT)
process where the data is incrementally processed, until it reaches the
curated gold layer for reporting. A typical architecture may look
similar to:

<img src="./media/image1.png" style="width:6.5in;height:2.6625in"
alt="Medallion Architecture" />

These layers are not intended to be a hard rule, but rather a guiding
principle. Layers are often separated into different Lakehouses, but for
the purposes of our lab, we'll be using the same Lakehouse to store all
layers. Read more on implementing a [medallion architecture in Fabric
here](https://learn.microsoft.com/en-us/fabric/onelake/onelake-medallion-lakehouse-architecture).

**Objectives**

- To create a Lakehouse named "StocksLakehouse" in the Data Engineering
  persona of Synapse Data Engineering Home, confirming successful SQL
  endpoint creation.

- To add the Lakehouse to the StockEventStream, configure it, perform
  data cleanup, and ensure the Lakehouse receives required data fields.

- To import notebooks "Lakehouse 1-4" into RealTimeWorkspace, then
  execute them individually.

- To utilize event processors and data wrangling techniques for
  aggregating and cleaning the raw data stored in the Lakehouse. This
  includes performing functions such as filtering, data type conversion,
  and field management to prepare the data for downstream analytics.

- To build curated and aggregated data tables suitable for building a
  dimensional model and supporting data science activities. This
  involves creating aggregation routines to summarize data at different
  levels of granularity, such as per-minute and per-hour aggregations.

- To build a dimensional model within the Lakehouse environment,
  incorporating fact and dimension tables. Define relationships between
  these tables to facilitate efficient querying and reporting.

- To create a semantic model based on the dimensional model and build
  interactive reports using tools like Power BI to visualize and analyze
  the aggregated data.

# Exercise 1: Setting up the Lakehouse

## Task 1: Create the Lakehouse

Start by creating a Lakehouse.

***Note**: If you are completing this lab after the data science module
or another module that uses a Lakehouse, you may re-use that Lakehouse
or create a new one, but we'll assume the same Lakehouse is shared
across all modules.*

1.  Within your Fabric workspace, switch to the **Data engineering**
    persona (bottom left) as shown in the below image.

> <img src="./media/image2.png" style="width:4.1in;height:6.475in" />

2.  In Synapse Data Engineering Home page, navigate and click on
    ***Lakehouse* **tile.

<img src="./media/image3.png" style="width:6.5in;height:5.48472in"
alt="A screenshot of a computer Description automatically generated" />

3.  In the **New lakehouse** dialog box, enter ***StocksLakehouse*** in
    the **Name** field, then click on the **Create** button. A
    **StocksLakehouse** page will appear.

<img src="./media/image4.png" style="width:3.01528in;height:1.69722in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image5.png" style="width:6.5in;height:5.38056in"
alt="A screenshot of a computer Description automatically generated" />

4.  You will see a notification stating - **Successfully created SQL
    endpoint**.

> **Note**: In case, you did not see the Notifications, then wait for
> few minutes.

<img src="./media/image6.png" style="width:3.37529in;height:2.55022in"
alt="A screenshot of a computer Description automatically generated" />

## Task 2. Add Lakehouse to the Eventstream

From an architecture perspective, we'll implement a Lambda architecture
by splitting hot path and cold path data from the Eventstream. Hot path
will continue to the KQL database as already configured, and cold path
will be added to write the raw data to our lakehouse. Our data flow will
resemble the following:

***Note**: If you are completing this lab after the data science module
or another module that uses a Lakehouse, you may re-use that Lakehouse
or create a new one, but we'll assume the same Lakehouse is shared
across all modules.*

1.  Within your Fabric workspace, switch to the **Data engineering**
    persona (bottom left) as shown in the below image.

> <img src="./media/image7.png" style="width:2.85319in;height:5.87917in"
> alt="A screenshot of a graph Description automatically generated" />

2.  Now, click on **RealTimeWorkspace** on the left-sided navigation
    pane and select **StockEventStream** as shown in the below image.

<img src="./media/image8.png" style="width:5.27917in;height:4.7191in"
alt="A screenshot of a computer Description automatically generated" />

3.  In addition to adding Lakehouse to the Eventstream, we'll do some
    cleanup of the data using some of the functions available in the
    Eventstream.

4.  On the **StockEventStream** page, select **Edit**

<img src="./media/image9.png" style="width:7.1875in;height:4.15196in"
alt="A screenshot of a computer Description automatically generated" />

5.  On the **StockEventStream** page, click on the **Add destination**
    on the output of the Eventstream to add a new destination.
    Select *Lakehouse*** **from the context menu.

<img src="./media/image10.png" style="width:6.9892in;height:3.6875in"
alt="A screenshot of a computer Description automatically generated" />

6.  In the Lakehouse pane that appears on the right side, enter the
    following details and click on **Save.**

| **Destination name** | **Lakehouse** |
|----|----|
| **Workspace** | RealTimeWorkspace |
| **Lakehouse** | StockLakehouse |
| **Delta table** | Click on **Create new**\> enter ***raw_stock_data*** |
| **Input data format** | Json |

> <img src="./media/image11.png" style="width:6.4375in;height:5.61525in"
> alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image12.png" style="width:3.6in;height:5.63333in"
alt="A screenshot of a computer Description automatically generated" />

6.  Connect **StockEventStream** and **Lakehouse**

<img src="./media/image13.png" style="width:7.1375in;height:3.90318in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image14.png" style="width:7.12931in;height:3.92112in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image15.png" style="width:7.3473in;height:3.65088in"
alt="A screenshot of a computer Description automatically generated" />

7.  Select the Lakehouse and click on **Refresh** button

<img src="./media/image16.png" style="width:7.31512in;height:3.8125in"
alt="A screenshot of a computer Description automatically generated" />

8.  After clicking *Open event processor*, various processing can be
    added that perform aggregations, filtering, and changing datatypes.

<img src="./media/image17.png" style="width:6.5in;height:2.5375in"
alt="A screenshot of a computer Description automatically generated" />

9.  On the **StockEventStream** page, select **stockEventStream**, and
    click the **plus (+)** icon to add a **Mange field**. Then, select
    **Mange field.**

<img src="./media/image18.png" style="width:6.5in;height:4.03862in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image19.png" style="width:6.5in;height:4.425in"
alt="A screenshot of a computer Description automatically generated" />

10. In the eventstreem pane select **Managefields1** pencil icon.

<img src="./media/image20.png" style="width:7.03788in;height:3.87083in"
alt="A screenshot of a computer Description automatically generated" />

11. In the *Manage fields* pane that opens, click ***Add all
    fields*** to add all columns. Then, remove the fields
    **EventProcessedUtcTime**, **PartitionId**, and
    **EventEnqueuedUtcTime** by clicking the **ellipsis (...)** to the
    right of the field name, and click *Remove*

<img src="./media/image21.png" style="width:3.95417in;height:4.27144in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image22.png" style="width:7.14896in;height:4.39583in"
alt="A screenshot of a computer Description automatically generated" />

> <img src="./media/image23.png" style="width:6.49167in;height:3.63333in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image24.png" style="width:6.49167in;height:4.61667in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image25.png" style="width:4.9625in;height:4.02568in"
> alt="A screenshot of a computer Description automatically generated" />

12. Now change the *timestamp* column to a *DateTime*** **as it is
    likely classified as a string. Click the **three ellipsis (...)** to
    the right of the *timestamp*** column** and select *Yes change
    type*. This will allow us to change the datatype:
    select *DateTime***,** as shown in the below image. Click on
    **Done**

> <img src="./media/image26.png" style="width:5.225in;height:4.63333in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image27.png" style="width:3.50833in;height:5.975in"
> alt="A screenshot of a computer Description automatically generated" />

12. Now, click on the **Publish** button to close the event processor

<img src="./media/image28.png" style="width:7.20548in;height:3.69583in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image29.png" style="width:7.35082in;height:3.68169in"
alt="A screenshot of a computer Description automatically generated" />

13. Once it is completed, the Lakehouse will receive the symbol, price,
    and timestamp.

<img src="./media/image30.png" style="width:7.0683in;height:4.38892in"
alt="A screenshot of a computer Description automatically generated" />

Our KQL (hot path) and Lakehouse (cold path) is now configured. It may
take a minute or two for data to be visible in the Lakehouse.

## Task 3. Import notebooks

**Note**: If you have issues importing these notebooks, be sure you are
downloading the raw notebook file and not the HTML page from GitHub that
is displaying the notebook.

1.  Now, click on **RealTimeWorkspace** on the left-sided navigation
    menu.

<img src="./media/image31.png" style="width:6.5in;height:7.25764in"
alt="A screenshot of a computer Description automatically generated" />

2.  In the **Synapse Data Engineering** **RealTimeWorkspace** page,
    navigate and click on **Import** button, then select **Notebook**
    and select the **From this computer** as shown in the below image.

<img src="./media/image32.png" style="width:6.5in;height:2.26667in" />

3.  Select **Upload** from the **Import status** pane that appears on
    the right side of the screen.

<img src="./media/image33.png" style="width:3.34861in;height:2.93958in"
alt="A screenshot of a computer Description automatically generated" />

4.  Navigate and select **Lakehouse 1-Import Data, Lakehouse 2-Build
    Aggregation, Lakehouse 3-Create Star Schema** and **Lakehouse 4-Load
    Star Schema** notebooks from **C:\LabFiles\Lab 04** and click on the
    **Open** button.

<img src="./media/image34.png" style="width:6.49236in;height:4.07569in"
alt="A screenshot of a computer Description automatically generated" />

5.  You will see a notification stating **Imported successfully.**

<img src="./media/image35.png" style="width:7.22809in;height:2.15341in"
alt="A screenshot of a computer Description automatically generated" />

## Task 4. Import additional data

In order the make the reports more interesting, we need a bit more data
to work with. For both the Lakehouse and Data Science modules,
additional historical data can be imported to supplement the data that
is already ingested. The notebook works by looking at the oldest data in
the table, prepending the historical data.

1.  In the **RealTimeWorkspace** page, to view only the notebooks, click
    on the **Filter** at the top right corner of the page, then select
    **Notebook.**

<img src="./media/image36.png" style="width:7.31575in;height:2.94886in"
alt="A screenshot of a computer Description automatically generated" />

2.  Then, select the ***Lakehouse 1 - Import Data* **notebook.

<img src="./media/image37.png" style="width:6.5in;height:3.56042in"
alt="A screenshot of a computer Description automatically generated" />

3.  Under **Explorer**, navigate and select the **Lakehouse**, then
    click on the **Add* ***button as shown in the below images*.*

> **Important Note**: You’ll need to add the Lakehouse to every imported
> notebook -- do this each time you open a notebook for the first time.

<img src="./media/image38.png" style="width:6.49236in;height:4.90139in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image39.png" style="width:6.5in;height:6.49236in"
alt="A screenshot of a computer Description automatically generated" />

4.  In the **Add Lakehouse** dialog box, select the **Existing
    lakehouse** radio button, then click on the **Add** button.

<img src="./media/image40.png" style="width:3.03056in;height:1.80278in"
alt="A screenshot of a computer Description automatically generated" />

5.  On the **OneLake data hub** window, select **StockLakehouse** and
    click on the **Add** button.
    <img src="./media/image41.png" style="width:6.48472in;height:3.90139in"
    alt="A screenshot of a computer Description automatically generated" />

6.  The **raw_stock_data** table was created when the Eventstream was
    configured, and is the landing place for the data that is ingested
    from the Event Hub.

<img src="./media/image42.png" style="width:6.49236in;height:4.28819in"
alt="A screenshot of a computer Description automatically generated" />

**Note**: You will see the **Run** button when you hover your mouse over
the cell in the notebook.

7.  To start the notebook and execute the cell, select the **Run** icon
    that appears on the left side of the cell.

<img src="./media/image43.png" style="width:6.49236in;height:3.37847in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image44.png" style="width:6.5in;height:3.2in"
alt="A screenshot of a computer Description automatically generated" />

8.  Similarly, run the 2<sup>nd</sup> and 3<sup>rd</sup> cells.

<img src="./media/image45.png" style="width:6.49236in;height:2.79514in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image46.png" style="width:6.80272in;height:3.7822in"
alt="A screenshot of a computer Description automatically generated" />

9.  To download and unzip historical data to the Lakehouse unmanaged
    files, run 4<sup>th</sup> and 5<sup>th</sup> <sup>d</sup> cells as
    shown in the below images.

<img src="./media/image47.png" style="width:7.01705in;height:4.94792in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image48.png" style="width:7.3826in;height:4.95644in"
alt="A screenshot of a computer program Description automatically generated" />

<img src="./media/image49.png" style="width:6.72454in;height:4.47297in"
alt="A screenshot of a computer Description automatically generated" />

10. To verify csv files are available, select and run the 6<sup>th</sup>
    cell.

<img src="./media/image50.png" style="width:6.49236in;height:3.28056in"
alt="A screenshot of a computer Description automatically generated" />

11. Run the 7<sup>th</sup> cell, 8<sup>th</sup> cell , and
    9<sup>th</sup> cell.

<img src="./media/image51.png" style="width:6.49236in;height:4.12847in"
alt="A screenshot of a computer program Description automatically generated" />

<img src="./media/image52.png" style="width:6.5in;height:2.73472in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image53.png" style="width:6.49236in;height:2.94722in"
alt="A screenshot of a computer Description automatically generated" />

12. While similar to 'commenting out' sections of code, freezing cells
    is powerful in that any output of the cells are also preserved.

<img src="./media/image54.png"
style="width:6.49167in;height:4.48333in" />

# Exercise 2: Building the Aggregation Tables

In this exercise, you’ll build curated and aggregated data suitable for
use in building our dimensional model and in data science. With the raw
data having a per-second frequency, this data size is often not ideal
for reporting or analysis. Further, the data isn't cleansed, so we're at
risk of non-conforming data causing issues in reports or pipelines where
erroneous data isn't expected. These new tables will store the data at
the per-minute and per-hour level. Fortunately, *data wrangler* makes
this an easy task.

The notebook used here will build both aggregation tables, which are
silver-level artifacts. While it is common to separate medallion layers
into different Lakehouses, given the small size of our data and for the
purposes of our lab, we'll be using the same Lakehouse to store all
layers.

## Task 1: Build Aggregation Tables notebook

Take a moment to scroll through the notebook. Be sure to add the default
Lakehouse if it is not already added.

1.  Now, click on **RealTimeWorkspace** on the left-sided navigation
    menu.

<img src="./media/image31.png" style="width:5.22948in;height:4.2875in"
alt="A screenshot of a computer Description automatically generated" />

2.  In the **RealTimeWorkspace** page, click on **Lakehouse 2 – Build
    Aggregation Tables** notebook.

<img src="./media/image55.png" style="width:6.49236in;height:3.4125in"
alt="A screenshot of a computer Description automatically generated" />

3.  Under Explorer, navigate and select the **Lakehouse**, then click on
    the **Add* ***button.

<img src="./media/image56.png" style="width:6.57917in;height:3.63125in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image57.png" style="width:5.43674in;height:6.2125in"
alt="A screenshot of a computer Description automatically generated" />

4.  In the **Add Lakehouse** dialog box, select the **Existing
    lakehouse** dialog box, then click on the **Add** button.

<img src="./media/image40.png" style="width:2.52083in;height:1.49956in"
alt="A screenshot of a computer Description automatically generated" />

5.  On the **OneLake data hub** window, select the **StockLakehouse**,
    and click on the **Add** button.

<img src="./media/image41.png" style="width:6.48472in;height:3.90139in"
alt="A screenshot of a computer Description automatically generated" />

6.  To build Aggregate Tables, select and run the 1<sup>st</sup> ,
    2<sup>nd</sup> , 3<sup>rd</sup> , and 4<sup>th</sup> cells.

<img src="./media/image58.png" style="width:6.5in;height:3.38611in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image59.png" style="width:7.22159in;height:4.35147in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image60.png" style="width:7.13503in;height:3.55492in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image61.png" style="width:6.5in;height:3.34097in"
alt="A screenshot of a computer Description automatically generated" />

7.  Then, select and run the 5<sup>th</sup> , 6<sup>th</sup>,
    7<sup>th</sup> , and 8<sup>th</sup> cells.

<img src="./media/image62.png" style="width:7.22721in;height:3.55492in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image62.png" style="width:7.0625in;height:3.47391in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image63.png" style="width:6.49236in;height:4.58333in"
alt="A screenshot of a computer program Description automatically generated" />

<img src="./media/image64.png" style="width:6.96401in;height:4.79669in"
alt="A screenshot of a computer Description automatically generated" />

8.  Add data wrangler, select **9<sup>th</sup>** cell, navigate dropdown
    **Data Wrangler**. Navigate and click on **anomaly_df** to load the
    dataframe in data wrangler**.**

9.  We'll use the *anomaly_df* because it was intentionally created with
    a few invalid rows that can be tested. 

<img src="./media/image65.png" style="width:6.49167in;height:3.575in"
alt="A screenshot of a computer Description automatically generated" />

10. In data wrangler, we'll record a number of steps to process data.
    Notice the data is visualized in the central column. Operations are
    in the top left, while an overview of each step is in the
    bottom left.

<img src="./media/image66.png" style="width:7.31131in;height:3.66856in"
alt="A screenshot of a computer Description automatically generated" />

11. To remove null/empty values, under *Operations*, click on the
    dropdown beside **Find and replace**, then navigate and click on
    **Drop missing values**.

<img src="./media/image67.png" style="width:7.30724in;height:3.68371in"
alt="A screenshot of a computer Description automatically generated" />

12. From the **Target columns** dropdown, select
    the ***symbol*** and ***price* **columns and then click on **Apply
    button** below it as shown in the image***.***

<img src="./media/image68.png" style="width:7.29469in;height:4.49432in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image69.png" style="width:6.71401in;height:4.10189in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image70.png" style="width:7.12291in;height:4.21401in"
alt="A screenshot of a computer Description automatically generated" />

13. Under **Operations** dropdown, navigate and click on **Sort and
    filter**, then click on **Filter** as shown in the below image. 

> <img src="./media/image71.png" style="width:5.89759in;height:4.3375in"
> alt="A screenshot of a computer Description automatically generated" />

14. **Uncheck** *Keep matching rows*, select **price** as the target
    column, and set the condition to ***Equal* to *0*.**
    Click ***Apply* **in the *Operations* panel beneath the Filter

> Note: The rows with zero are marked red as they will be dropped (if
> the other rows are marked red, ensure to uncheck the *Keep matching
> rows* checkbox).

<img src="./media/image72.png"
style="width:6.49167in;height:4.35833in" />

<img src="./media/image73.png" style="width:6.5in;height:3.66667in"
alt="A screenshot of a computer Description automatically generated" />

15. Click on **+Add code to notebook** in the upper left side of the
    page. On the ***Add code to notebook* **window, ensure that *Include
    pandas code* is unchecked and click on the **Add** button.

<img src="./media/image74.png" style="width:6.5in;height:3.86389in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image75.png" style="width:5.25in;height:4.05278in"
alt="A screenshot of a computer code Description automatically generated" />

16. The code inserted will look similar to the below.

<img src="./media/image76.png"
style="width:6.49167in;height:3.39167in" />

17. Run the cell and observe the output. You’ll observe that the invalid
    rows were removed.

> <img src="./media/image77.png" style="width:6.5in;height:3.40833in" />

<img src="./media/image78.png" style="width:6.5in;height:3.47639in"
alt="A screenshot of a computer Description automatically generated" />

The function created, *clean_data*, contains all of the steps in
sequence and can be modified as needed. Notice that each step performed
in data wrangler is commented. Because data wrangler was loaded with
the *anomaly_df*, the method is written to take that dataframe by name,
but this can be any dataframe that matches the schema.

18. Modify the function name
    from **clean_data** to *remove_invalid_rows*, and change the
    line *anomaly_df_clean = clean_data(anomaly_df)* to *df_stocks_clean
    = remove_invalid_rows(df_stocks)* . Also, while not necessary for
    functionality, you can change the name of the dataframe used in the
    function to simply *df* as shown below

19. Run this cell and observe the output.

> <span class="mark">\# Code generated by Data Wrangler for PySpark
> DataFrame</span>
>
> <span class="mark">def remove_invalid_rows(df):</span>
>
> <span class="mark">\# Drop rows with missing data in columns:
> 'symbol', 'price'</span>
>
> <span class="mark">df = df.dropna(subset=\['symbol', 'price'\])</span>
>
> <span class="mark">\# Filter rows based on column: 'price'</span>
>
> <span class="mark">df = df.filter(~(df\['price'\] == 0))</span>
>
> <span class="mark">return df</span>
>
> <span class="mark">df_stocks_clean =
> remove_invalid_rows(df_stocks)</span>
>
> <span class="mark">display(df_stocks_clean)</span>

<img src="./media/image79.png" style="width:6.5in;height:3.15903in"
alt="A screenshot of a computer Description automatically generated" />

20. This function will now remove the invalid rows from
    our *df_stocks* dataframe and return a new dataframe
    called *df_stocks_clean*. It is common to use a different name for
    the output dataframe (such as *df_stocks_clean*) to make the cell
    idempotent -- this way, we can go back and re-run the cell, make
    modifications, etc., without having to reload our original data.

<img src="./media/image80.png" style="width:5.82917in;height:4.55185in"
alt="A screenshot of a computer Description automatically generated" />

## Task 2: Build aggregation routine

In this task, you’ll be more involved because we'll build a number of
steps in data wrangler, adding derived columns and aggregating the data.
If you get stuck, continue as best you can and use the sample code in
the notebook to help fix any issues after.

1.  Add new column datestamp in the ***Symbol/Date/Hour/Minute
    Aggregation** Section*, place your cursor in the *Add data wrangler
    here* cell and select the cell. Dropdown the **Data Wrangler**.
    Navigate and click on **df_stocks_clean** as shown in the below
    image.

> <img src="./media/image81.png"
> style="width:6.49167in;height:4.31667in" />
>
> <img src="./media/image82.png" style="width:6.5in;height:2.98472in"
> alt="A screenshot of a computer Description automatically generated" />

2.  In **Data Wrangler:df_stocks_clean** pane, select **Operations**,
    then select **New column by example.**

> <img src="./media/image83.png" style="width:6.5in;height:3.59167in" />

3.  Under ***Target columns*** field, click on the dropdown and select
    ***timestamp***. Then, in the ***Derived column** **name*** field,
    enter +++***datestamp+++***

<img src="./media/image84.png" style="width:6.5in;height:4.01667in" />

4.  In the new ***datestamp*** column, enter an example value for any
    given row. For example, if the *timestamp* is *2024-02-07
    09:54:00* enter ***2024-02-07***. This allows data wrangler to infer
    we are looking for the date without a time component; once the
    columns autofill, click on the ***Apply*** button.

<img src="./media/image85.png"
style="width:7.16408in;height:3.74583in" />

<img src="./media/image86.png"
style="width:7.37696in;height:3.86502in" />

5.  Similar to adding the **datestamp** column as mentioned in the above
    steps, click again on **New column by example** as shown in the
    below image.

<img src="./media/image87.png" style="width:6.5in;height:3.65833in" />

6.  Under *Target columns*, choose ***timestamp***. Enter a ***Derived
    column** name* of +++***hour+++*.**

> <img src="./media/image88.png"
> style="width:6.49167in;height:3.81667in" />

7.  In the new **hour* ***column that appear in the data preview, enter
    an hour for any given row -- but try to pick a row that has a unique
    hour value. For example, if the *timestamp* is *2024-02-07
    09:54:00* enter ***9***. You may need to enter example values for
    several rows, as shown here. Click on **Apply** button.

> <img src="./media/image89.png"
> style="width:6.48333in;height:3.68333in" />

8.  Data wrangler should infer we are looking for the hour component,
    and build code similar to:

\# Derive column 'hour' from column: 'timestamp'

def hour(timestamp):

    """

    Transform based on the following examples:

       timestamp           Output

    1: 2024-02-07T09:54 =\> "9"

    """

    number1 = timestamp.hour

    return f"{number1:01.0f}"

pandas_df_stocks_clean.insert(3, "hour",
pandas_df_stocks_clean.apply(lambda row : hour(row\["timestamp"\]),
axis=1))

<img src="./media/image90.png"
style="width:7.24178in;height:4.30417in" />

 

9.  Same as with the hour column, create a new ***minute* **column. In
    the new *minute* column, enter a minute for any given row. For
    example, if the *timestamp* is *2024-02-07 09:54:00* enter *54*. You
    may need to enter example values for several rows.

<img src="./media/image91.png"
style="width:6.49167in;height:3.30833in" />

10. The code generated should look similar to:

\# Derive column 'minute' from column: 'timestamp'

def minute(timestamp):

    """

    Transform based on the following examples:

       timestamp           Output

    1: 2024-02-07T09:57 =\> "57"

    """

    number1 = timestamp.minute

    return f"{number1:01.0f}"

pandas_df_stocks_clean.insert(3, "minute",
pandas_df_stocks_clean.apply(lambda row : minute(row\["timestamp"\]),
axis=1))

<img src="./media/image92.png"
style="width:7.05417in;height:4.41338in" />

11. Next, convert the hour column to an integer. Click on the **ellipsis
    (...)** in the corner of the *hour* column and select ***Change
    column** type*. Click on the dropdown beside ***New type***,
    navigate and select ***int32**,* then click on the ***Apply*
    button** as shown in the below image. <img src="./media/image93.png"
    style="width:7.07552in;height:2.97917in" />

<img src="./media/image94.png"
style="width:7.25584in;height:3.99583in" />

12. Convert the minute column to an integer using the same steps as you
    just performed for the hour. Click on the **ellipsis (...)** in the
    corner of the ***minute* column** and select ***Change column**
    type*. Click on the dropdown beside ***New type***, navigate and
    select ***int32**,* then click on the ***Apply* button** as shown in
    the below image.

<img src="./media/image95.png"
style="width:7.32227in;height:3.5125in" />

<img src="./media/image96.png"
style="width:7.31189in;height:4.34583in" />

<img src="./media/image97.png" style="width:7.28383in;height:4.14151in"
alt="A screenshot of a computer Description automatically generated" />

13. Now, under the **Operations** section, navigate and click on
    ***Group by and aggregate*** as shown in the below image.

<img src="./media/image98.png"
style="width:6.87083in;height:4.95181in" />

14. Click on the dropdown under ***Columns to group by*** field and
    select ***symbol*, *datestamp*, *hour*, *minute***.

<img src="./media/image99.png"
style="width:6.49167in;height:5.59167in" />

15. Click on +*Add aggregation*, create a total of three aggregations as
    shown in the below images and click on the ***Apply*** button.

- price: Maximum

- price: Minimum

- price: Last value

<img src="./media/image100.png"
style="width:6.79583in;height:5.6552in" />

<img src="./media/image101.png"
style="width:7.41997in;height:3.59583in" />

16. Click **Add code to notebook** in the upper left corner of the
    page. On the ***Add code to notebook* window**, ensure *Include
    pandas code* is unchecked, then click on the **Add** button.

<img src="./media/image102.png"
style="width:6.49167in;height:3.54167in" />

<img src="./media/image103.png"
style="width:5.23333in;height:4.175in" />

<img src="./media/image104.png" style="width:7.16746in;height:5.2278in"
alt="A screenshot of a computer Description automatically generated" />

17. Review the code, in the cell that is added, in the last two lines of
    the cell, notice the dataframe returned is
    named ***df_stocks_clean_1***. Rename
    this ***df_stocks_agg_minute***, and change the name of the function
    to ***aggregate_data_minute*,** as shown below.

**\# old:**

def clean_data(df_stocks_clean):

...

df_stocks_clean_1 = clean_data(df_stocks_clean)

display(df_stocks_clean_1)

**\# new:**

def aggregate_data_minute(df_stocks_clean):

...

df_stocks_agg_minute = aggregate_data_minute(df_stocks_clean)

display(df_stocks_agg_minute)

<img src="./media/image105.png"
style="width:6.49167in;height:3.38333in" />

18. Code generated by Data Wrangler for PySpark DataFrame cell, select
    the **Run** icon that appears to the left of the cell upon hover.

<img src="./media/image106.png"
style="width:7.28644in;height:5.00417in" />

<img src="./media/image107.png"
style="width:6.49167in;height:3.05833in" />

<img src="./media/image108.png"
style="width:7.28579in;height:5.14255in" />

**Note**: If you get stuck, refer to the commented-out code as a
reference. If any of the data wrangling steps don't seem to be quite
correct (not getting the correct hour or minute, for example), refer to
the commented-out samples. Step 7 below has a number of additional
considerations that may help.

**Note:** If you'd like to comment-out (or uncomment) large blocks, you
can highlight the section of code (or CTRL-A to select everything in the
current cell) and use CTRL-/ (Control *slash*) to toggler commenting.

19. In the merge cell, select the **Run** icon that appears to the left
    of the cell upon hover. The merge function writes the data into the
    table:

> <span class="mark">\# write the data to the stocks_minute_agg
> table</span>
>
> <span class="mark">merge_minute_agg(df_stocks_agg_minute)</span>

<img src="./media/image109.png" style="width:6.49167in;height:3.6in" />

## Task 3: Aggregate hourly

Let's review current progress - our per-second data has been cleansed,
and then summarized to the per-minute level. This reduces our rowcount
from 86,400 rows/day to 1,440 rows/day per stock symbol. For reports
that might show monthly data, we can further aggregate the data to
per-hour frequency, reducing the data to 24 rows/day per stock symbol.

1.  In the final placeholder under the *Symbol/Date/Hour* section, load
    the existing ***df_stocks_agg_minute*** dataframe into data
    wrangler.

2.  In the final placeholder under the ***Symbol/Date/Hour*** section,
    place your cursor in the *Add data wrangler here* cell and select
    the cell. Dropdown the **Data Wrangler,** navigate and click on
    ***df_stocks_agg_minute*** as shown in the below image.

<img src="./media/image110.png"
style="width:7.15417in;height:4.66536in" />

> <img src="./media/image111.png" style="width:6.5in;height:4.05625in"
> alt="A screenshot of a computer Description automatically generated" />

3.  Under ***Operations*,** select ***Group by and aggregate***. Click
    on the dropdown below **Columns to group by** field and select
    ***symbol*, *datestamp*, and *hour***, and then click on **+Add
    aggregations**. Create the following three aggregations and click on
    Apply button below it, as shown in the below image.

- price_min: Minimum

- price_max: Maximum

- price_last: Last value

<img src="./media/image112.png"
style="width:7.2125in;height:4.82683in" />

<img src="./media/image113.png" style="width:7.23649in;height:3.51619in"
alt="A screenshot of a computer Description automatically generated" />

4.  Example code is shown below. In addition to renaming the function
    to *aggregate_data_hour*, the alias of each price column has also
    been changed to keep the column names the same. Because we are
    aggregating data that has already been aggregated, data wrangler is
    naming the columns like price_max_max, price_min_min; we will modify
    the aliases to keep the names the same for clarity.

<img src="./media/image114.png"
style="width:7.27027in;height:3.3625in" />

5.  Click **Add code to notebook** in the upper left corner of the
    page. On the ***Add code to notebook* window**, ensure *Include
    pandas code* is unchecked and click on the **Add** button.

<img src="./media/image115.png"
style="width:6.49167in;height:3.06667in" />

<img src="./media/image116.png"
style="width:5.76667in;height:4.54167in" />

<img src="./media/image117.png" style="width:6.5in;height:2.44236in" />

6.  In the cell that is added, in the last two lines of the cell, notice
    the dataframe returned is named def
    <span class="mark"></span>clean_data(df_stocks_agg_minute):, rename
    this

> **def aggregate_data_hour(df_stocks_agg_minute):**

7.  In the cell that is added, in the last two lines of the cell, notice
    the dataframe returned is named **df_stocks_agg_minute_clean =
    clean_data(df_stocks_agg_minute).**Rename this **df_stocks_agg_hour
    = aggregate_data_hour(df_stocks_agg_minute),** and change the name
    of the function **display(df_stocks_agg_minute_clean)**
    to *aggregate_data_minute*, as shown below. 

Reference Code:

\# Code generated by Data Wrangler for PySpark DataFrame

from pyspark.sql import functions as F

def aggregate_data_hour(df_stocks_agg_minute):

\# Performed 3 aggregations grouped on columns: 'symbol', 'datestamp',
'hour'

df_stocks_agg_minute = df_stocks_agg_minute.groupBy('symbol',
'datestamp', 'hour').agg(

F.max('price_max').alias('price_max'),

F.min('price_min').alias('price_min'),

F.last('price_last').alias('price_last'))

df_stocks_agg_minute = df_stocks_agg_minute.dropna()

df_stocks_agg_minute =
df_stocks_agg_minute.sort(df_stocks_agg_minute\['symbol'\].asc(),
df_stocks_agg_minute\['datestamp'\].asc(),
df_stocks_agg_minute\['hour'\].asc())

return df_stocks_agg_minute

df_stocks_agg_hour = aggregate_data_hour(df_stocks_agg_minute)

display(df_stocks_agg_hour)

<img src="./media/image118.png"
style="width:7.02419in;height:1.95417in" />

2.  Select and **Run** the cell.

<img src="./media/image119.png" style="width:6.5in;height:3.16667in"
alt="A screenshot of a computer Description automatically generated" />

3.  The code to merge the hour aggregated data is in the next cell:
    **merge_hour_agg(df_stocks_agg_hour)**

4.  Run the cell to complete the merge. There are a few utility cells at
    the bottom for checking the data in the tables -- explore the data a
    bit and feel free to experiment.

<img src="./media/image120.png"
style="width:6.49167in;height:2.11667in" />

21. Use **Handy SQL Commands for testing** section for testing, cleaning
    out tables to re-run, etc. Select and **Run** the cells in this
    section.

<img src="./media/image121.png" style="width:6.5in;height:3.875in" />

<img src="./media/image122.png" style="width:6.5in;height:4.08819in"
alt="A screenshot of a computer program Description automatically generated" />

<img src="./media/image123.png"
style="width:6.30833in;height:3.525in" />

# Exercise 3: Building the Dimensional Model

In this exercise, we'll further refine our aggregation tables and create
a traditional star schema using fact and dimension tables. If you've
completed the Data Warehouse module, this module will produce a similar
result, but is different in approach by using notebooks within a
Lakehouse.

**Note**: It is possible to use pipelines to orchestrate activities, but
this solution will be done completely within notebooks.

## Task 1: Create schema

This run-once notebook will setup the schema for building the fact and
dimension tables. Configure the sourceTableName variable in the first
cell (if needed) to match the hourly aggregation table. The begin/end
dates are for the date dimension table. This notebook will recreate all
tables, rebuilding the schema: existing fact and dimension tables will
be overwritten.

1.  Click on **RealTimeWorkspace** on the left-sided navigation menu.

<img src="./media/image124.png"
style="width:4.4375in;height:3.50417in" />

2.  In the RealTimeWorkshop workspace, select the ***Lakehouse 3 –
    Create Star Schema*  **notebook.

<img src="./media/image125.png"
style="width:6.42917in;height:3.82917in" />

3.  Under the Explorer, navigate and click on the **Lakehouses**, then
    click on the **Add** button*.*

<img src="./media/image126.png"
style="width:4.9625in;height:4.07407in" />

<img src="./media/image127.png" style="width:6.5in;height:6.125in" />

4.  In the **Add Lakehouse** dialog box, select the **Existing
    lakehouse** radio button, then click on the **Add** button.

<img src="./media/image40.png" style="width:2.52083in;height:1.49956in"
alt="A screenshot of a computer Description automatically generated" />

5.  On the OneLake data hub window, select **StockLakehouse**  and click
    on the **Add** button.

<img src="./media/image41.png" style="width:6.48472in;height:3.90139in"
alt="A screenshot of a computer Description automatically generated" />

6.  With the notebook loaded and the Lakehouse attached, notice the
    schema on the left. In addition to the **raw_stock_data** table,
    there should be the **stocks_minute_agg** and **stocks_hour_agg**
    tables.

<img src="./media/image128.png"
style="width:4.20417in;height:5.125in" />

7.  Run each cell individually by clicking the **play** button on the
    left side of each cell to follow along with the process.

<img src="./media/image129.png"
style="width:6.49167in;height:3.70833in" />

<img src="./media/image130.png" style="width:6.5in;height:3.55in" />

<img src="./media/image131.png" style="width:6.5in;height:3.18333in" />

<img src="./media/image132.png"
style="width:6.49167in;height:3.84167in" />

<img src="./media/image133.png"
style="width:7.31744in;height:4.52102in" />

<img src="./media/image134.png"
style="width:7.04583in;height:5.07409in" />

<img src="./media/image135.png" style="width:7.25719in;height:5.02343in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image136.png" style="width:6.5in;height:2.93333in" />

<img src="./media/image137.png"
style="width:7.24583in;height:3.89731in" />

8.  When all cells have been run successfully, navigate to
    **StocksLakehouse** section, click on the horizontal ellipsis beside
    **Tables** **(...)**, then navigate and click on ***Refresh*** as
    shown in the below image.

<img src="./media/image138.png" style="width:6.49167in;height:6.3in" />

9.  Now, you can see all additional tables ***dim_symbol*, *dim_date*,
    and *fact_stocks_daily_prices*** for our dimensional model.

<img src="./media/image139.png"
style="width:5.775in;height:7.51667in" />

## Task 2: Load fact table

Our fact table contains the daily stock prices (the high, low, and
closing price), while our dimensions are for our date and stock symbols
(which might contain company details and other information). Although
simple, conceptually, this model represents a star schema that can be
applied to larger datasets.

1.  Now, click on **RealTimeWorkspace** on the left-sided navigation
    menu.

<img src="./media/image140.png"
style="width:5.74583in;height:4.87917in" />

2.  In the RealTimeWorkshop workspace, select the ***Lakehouse 4 – Load
    fact table*  **notebook.

<img src="./media/image141.png"
style="width:5.65417in;height:4.02917in" />

3.  Under the Explorer, select **Lakehouse**, then click on the **Add**
    button*.*

<img src="./media/image142.png"
style="width:5.2125in;height:3.74711in" />

<img src="./media/image143.png" style="width:6.49167in;height:5.55in" />

4.  In the **Add Lakehouse** dialog box, select the **Existing
    lakehouse** radio button, then click on the **Add** button.

<img src="./media/image40.png" style="width:2.52083in;height:1.49956in"
alt="A screenshot of a computer Description automatically generated" />

5.  On the OneLake data hub tab, select the **StockLakehouse**  and
    click on the **Add** button.

<img src="./media/image41.png" style="width:6.48472in;height:3.90139in"
alt="A screenshot of a computer Description automatically generated" />

6.  Select and run each cell individually.

<img src="./media/image144.png" style="width:6.5in;height:3.3in" />

7.  Function adds symbols to dim_symbol that may not exist in table,
    select and **Run** the 2<sup>nd</sup> and 3<sup>rd</sup> cells.

<img src="./media/image145.png" style="width:6.49167in;height:3.7in" />

<img src="./media/image146.png"
style="width:6.49167in;height:4.23333in" />

8.  To get new stock data to ingest, starting at watermark, select and
    run the 4<sup>th</sup> cell.

<img src="./media/image147.png"
style="width:6.49167in;height:4.19167in" />

9.  Load the date dimension for later joins, select and **Run** the
    5<sup>th</sup>, 6<sup>th</sup>, and 7<sup>th</sup> cells.

<img src="./media/image148.png"
style="width:6.49167in;height:4.26667in" />

<img src="./media/image149.png" style="width:6.5in;height:3.46389in" />

<img src="./media/image150.png" style="width:6.5in;height:4.77153in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image151.png"
style="width:7.14583in;height:3.99947in" />

10. To join the aggregated data to the date dimension, select and
    **Run** the 8<sup>th</sup> and 9<sup>th</sup> cells.

<img src="./media/image152.png"
style="width:7.24934in;height:3.22917in" />

<img src="./media/image153.png"
style="width:7.38358in;height:3.34583in" />

11. Create a final view with cleaned names for processing ease, select
    and **Run** the 10<sup>th</sup>, 11<sup>th</sup> , and
    12<sup>th</sup> cells.

<img src="./media/image154.png"
style="width:7.24583in;height:3.34423in" />

<img src="./media/image155.png"
style="width:7.1862in;height:3.97083in" />

<img src="./media/image156.png"
style="width:7.16715in;height:3.40417in" />

12. To obtain the result and plot a graph, select and
    **Run** 13<sup>th</sup> and 14<sup>th</sup> cells.

<img src="./media/image157.png"
style="width:6.49167in;height:3.775in" />

<img src="./media/image158.png"
style="width:7.27514in;height:2.97917in" />

<img src="./media/image159.png" style="width:6.5in;height:3.12014in" />

13. To validate the created tables, right click on the horizontal
    ellipsis (…) beside **Tables,** then navigate and click on
    **Refresh.** The tables will appear.

<img src="./media/image160.png"
style="width:6.49167in;height:6.49167in" />

14. To schedule the notebook to run periodically, click on
    the ***Run*** tab, and click on ***Schedule*** as shown in the below
    image*.*

<img src="./media/image161.png"
style="width:6.49167in;height:3.99167in" />

15. In Lackhouse 4-Load Star Schema tab, select the below details and
    click on the **Apply** button.

- Schedule run: **On**

- Repeat**: Hourly**

- Every: **4 hours**

- Select today date

<img src="./media/image162.png"
style="width:6.49167in;height:5.50833in" />

## Task 3: Build semantic model and simple report

In this task, we'll create a new semantic model that we can use for
reporting, and create a simple Power BI report.

1.  Now, click on **StocksLakehouse** on the left-sided navigation menu.

<img src="./media/image163.png"
style="width:6.49167in;height:5.7625in" />

2.  In the ***StocksLakehouse*** window*,* navigate and click on ***New
    semantic model*** in the command bar*.*

<img src="./media/image164.png"
style="width:7.26479in;height:4.7375in" />

3.  Name the model ***StocksDimensionalModel* **and select the
    **fact_stocks_daily_prices**, **dim_date** and **dim_symbol**
    tables. Then, click on the **Confirm** button.

<img src="./media/image165.png" style="width:3.6in;height:5.54167in" />

<img src="./media/image166.png" style="width:6.5in;height:3.00694in"
alt="A screenshot of a computer Description automatically generated" />

4.  When the semantic model opens, we need to define relationships
    between the fact and dimension tables.

5.  From the **fact_Stocks_Daily_Prices** table, drag
    the ***Symbol_SK***  field and drop it on
    the ***Symbol_SK***   field in the **dim_Symbol** table to create a
    relationship. The **New relationship** dialog box appears.

<img src="./media/image167.png"
style="width:6.49167in;height:4.91667in" />

6.  In the **New relationship** dialog box:

- **From table** is populated with **fact_Stocks_Daily_Prices** and the
  column of **Symbol_SK.**

- **To table** is populated with **dim_symbol**  and the column of
  **Symbol_SK**

- Cardinality: **Many to one (\*:1)**

- Cross filter direction: **Single**

- Leave the box next to **Make this relationship active** selected.

- Select **Save.**

<img src="./media/image168.png"
style="width:5.58333in;height:6.125in" />

<img src="./media/image169.png" style="width:6.5in;height:3.11319in"
alt="A screenshot of a computer Description automatically generated" />

7.  From the **fact_Stocks_Daily_Prices** table, drag
    the **PrinceDateKey**  field and drop it on
    the ***DateKey***   field in the **dim_date** table to create a
    relationship. The **New relationship** dialog box appears.

<img src="./media/image170.png"
style="width:6.49167in;height:3.98333in" />

8.  In the **New relationship** dialog box:

- **From table** is populated with **fact_Stocks_Daily_Prices** and the
  column of **PrinceDateKey.**

- **To table** is populated with **dim_date**  and the column of
  **DateKey**

- Cardinality: **Many to one (\*:1)**

- Cross filter direction: **Single**

- Leave the box next to **Make this relationship active** selected.

- Select **Save.**

<img src="./media/image171.png" style="width:5.6in;height:6.21667in" />

<img src="./media/image172.png" style="width:6.5in;height:4.2625in"
alt="A screenshot of a computer Description automatically generated" />

9.  Click ***New Report*** to load the semantic model in Power BI.

<img src="./media/image173.png"
style="width:6.49167in;height:4.13333in" />

10. In the **Power BI** page, under **Visualizations**, click to the
    **Line chart** icon to add a **Column chart** to your report.

- On the **Data** pane, expand **fact_Stocks_Daily_Prices**  and check
  the box next to **PriceDateKey**. This creates a column chart and adds
  the field to the **X-axis**.

- On the **Data** pane, expand **fact_Stocks_Daily_Prices** and check
  the box next to **ClosePrice**. This adds the field to the **Y-axis**.

- On the **Data** pane, expand **dim_Symbol** and check the box next
  to **Symbol**. This adds the field to the **Legend**.

<img src="./media/image174.png"
style="width:5.7375in;height:6.75241in" />

11. Under **Filters,** select **PriceDateKey** and enter the below
    details. Click on the **Apply filte**r

- Filter type: **Relative date**

- Show items when the value: **is in the last 45 days**

<img src="./media/image175.png" style="width:5.35in;height:5.66667in" />

<img src="./media/image176.png"
style="width:7.36665in;height:3.81633in" />

12. From the ribbon, select **File** \> **Save as.**

<img src="./media/image177.png"
style="width:7.09665in;height:4.2125in" />

13. In the Save your report dialog box, enter +++
    **StocksDimensional** +++ as the name of your report and select
    **your workspace**. Click on the **Save** button**.**

<img src="./media/image178.png"
style="width:7.22083in;height:3.57797in" />

<img src="./media/image179.png" style="width:7.38292in;height:3.52187in"
alt="A screenshot of a computer Description automatically generated" />

**Summary**

In this lab, you’ve configured a comprehensive Lakehouse infrastructure
and implemented data processing pipelines to handle real-time and batch
data streams effectively. You’ve started with the creation of the
Lakehouse environment, the lab progresses to configuring Lambda
architecture for processing hot and cold data paths.

You’ve applied data aggregation and cleansing techniques to prepare the
raw data stored in the Lakehouse for downstream analytics. You’ve built
aggregation tables to summarize data at different levels of granularity,
facilitating efficient querying and analysis. Subsequently, you’ve built
a dimensional model within the Lakehouse, incorporating fact and
dimension tables. You’ve defined the relationships between these tables
to support complex queries and reporting requirements.

Finally, you’ve generated a semantic model to provide a unified view of
the data, enabling the creation of interactive reports using
visualization tools like Power BI. This holistic approach enables
efficient data management, analysis, and reporting within the Lakehouse
environment.
