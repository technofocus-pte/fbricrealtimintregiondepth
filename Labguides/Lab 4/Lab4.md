# Lab 04: Getting Started with Building a ML Model in Fabric

**Introduction**

As a refresher on the scenario, AbboCost Financial has been modernizing
their stock market reporting for their financial analysts. In previous
modules, we've developed the solution by creating real-time dashboards,
a data warehouse, and more.

In this module, AbboCost would like to explore predictive analytics to
help inform their advisors. The goal is to analyze historical data for
patterns that can be used to create forecasts of future values.
Microsoft Fabric's Data Science capability is the ideal place to do this
kind of exploration.

**Objectives**

- To import and configure notebooks for building and storing machine
  learning models.

- To explore and execute the DS 1-Build Model notebook for building and
  validating a stock prediction model.

- To examine model metadata and performance metrics in the
  RealTimeWorkspace.

- To open and explore the DS 2-Predict Stock Prices notebook for
  predicting stock prices.

- To run the notebook for generating predictions and storing them in the
  Lakehouse.

- To import and explore the DS 3-Forecast All notebook for generating
  predictions and storing them in the Lakehouse.

- To execute the notebook and verify the predictions in the Lakehouse
  schema.

- To build a semantic model in Power BI using the
  StockLakehousePredictions data and create visualizations in Power BI
  to analyze stock predictions and market trends.

- To configure relationships between tables and publish the prediction
  report.

# Exercise 1: Building and storing an ML model

## Task -1: Import the notebook

1.  In the **StockDimensionalModel** page, click on
    **RealTimeWorkspace** on the left-sided navigation menu.

     ![](./media/image1.png)

2.  In the **Synapse Data Engineering** **RealTimeWorkspace** page,
    navigate and click on **Import** button, then select **Notebook**
    and select the **From this computer as shown in the below image.**

     ![](./media/image2.png)

3.  In the **Import status** pane that appear on the right side, click
    on **Upload** .

      ![](./media/image3.png)

4.  Navigate to **C:\LabFiles\Lab 05** and select **DS 1-Build Model, DS
    2-Predict Stock Prices and DS 3-Forecast All** notebooks, then click
    on the **Open** button.

      ![](./media/image4.png)
5.  You will see a notification stating - **Imported successfully.**

      ![](./media/image5.png)

      ![](./media/image6.png)

6.  In the **RealTimeWorkspace**, click on **DS 1-Build Model**
    notebook.

     ![](./media/image7.png)

7.  Under the Explorer, select **Lakehouse** and click on the
    **Add* *button.**

> ***Important*:** You will need to add the Lakehouse to every imported
> notebook -- do this each time you open a notebook for the first time.
     ![](./media/image8.png)
     ![](./media/image9.png)

8.  In the **Add Lakehouse** dialog box, select **Existing lakehouse**
    radio button, then click on the **Add** button.
     ![](./media/image10.png)

9.  On the OneLake data hub window, select the ***StockLakehouse*** and
    click on the **Add** button.
     ![](../media/image11.png)
     ![](./media/image12.png)

## Task 2: Explore and run the notebook

The *DS 1* notebook is documented throughout the notebook, but in short,
the notebook carries out the following tasks:

- Allows us to configure a stock to analyze (such as WHO or IDGD)

- Downloads historical data to analyze in CSV format

- Reads the data into a dataframe

- Completes some basic data cleansing

- Loads [Prophet](https://facebook.github.io/prophet/), a module for
  conducting time series analysis and prediction

- Builds a model based on historical data

- Validates the model

- Stores the model using MLflow

- Completes a future prediction

The routine that generates the stock data is largely random, but there
are some trends that should emerge. Because we don't know when you might
be doing this lab, we've generated several years’ worth of data. The
notebook will load the data and will truncate future data when building
the model. As part of an overall solution, we'd then supplement the
historical data with new real-time data in our Lakehouse, re-training
the model as necessary (daily/weekly/monthly).

1.  In 1<sup>st</sup> cell uncomment the STOCK_SYMBOL=”IDGD” and
    STOCK_SYMBOL=”BCUZ”, then select and **Run** the cell.

     ![](./media/image13.png)

2.  Click ***Run all*** in the top toolbar and follow along as the work
    progresses.

3.  The notebook will take roughly **15-20** minutes to execute -- some
    steps, like **training the model** and **cross validation**, will
    take some time.

    ![](./media/image14.png)
    ![](./media/image15.png)
    ![](./media/image16.png)
    ![](./media/image17.png)
    ![](./media/image18.png)
    ![](./media/image19.png)
    ![](./media/image20.png)
    ![](./media/image21.png)
    ![](./media/image22.png)
    ![](./media/image23.png)
    ![](./media/image24.png)
    ![](./media/image25.png)
    ![](./media/image26.png)
    ![](./media/image27.png)
    ![](./media/image28.png)
    ![](./media/image29.png)
    ![](./media/image30.png)
    ![](./media/image31.png)
    ![](./media/image32.png)
    ![](./media/image33.png)
    ![](./media/image34.png)
    ![](./media/image35.png)
    ![](./media/image36.png)
    ![](./media/image37.png)
    ![](./media/image38.png)
    ![](./media/image39.png)
    ![](./media/image40.png)
    ![](./media/image41.png)
    ![](./media/image42.png)
    ![](./media/image43.png)
    ![](./media/image44.png)
    ![](./media/image45.png)
    ![](./media/image46.png)
    ![](./media/image47.png)

##  Task 3: Examine the model and runs

1.  Now, click on **RealTimeWorkspace** on the left-sided navigation
    menu.

<img src="./media/image48.png" style="width:6.5in;height:4.75833in" />

2.  Experiments and runs can be viewed in the workspace resource list.

<img src="./media/image49.png"
style="width:6.49167in;height:4.61667in" />

3.  In the **RealTimeWorkspace** page, select
    **WHO-stock-prediction-model** of ML model type.

<img src="./media/image50.png" style="width:6.5in;height:5.23333in" />

<img src="./media/image51.png" style="width:6.20214in;height:3.1501in"
alt="A screenshot of a computer Description automatically generated" />

4.  Metadata, in our case, includes input parameters we may tune for our
    model, as well as metrics on the model's accuracy, such as root mean
    square error (RMSE). RMSE represents the average error -- a zero
    would be a perfect fit between model and actual data, while higher
    numbers show an increase error. While lower numbers are better, a
    "good" number is subjective based on the scenario.

<img src="./media/image52.png"
style="width:6.49167in;height:4.39167in" />

# Exercise 2-Using models, saving to Lakehouse, building a report

## Task 1: Open and explore the Predict Stock Prices notebook

The notebook has been broken out into function definitions, such as *def
write_predictions*, which help encapsulate logic into smaller steps.
Notebooks can include other libraries (as you've seen already at the top
of most notebooks), and can also execute other notebooks. This notebook
completes these tasks at a high level:

- Creates the stock predictions table in the Lakehouse, if it doesn't
  exist

- Gets a list of all stock symbols

- Creates a prediction list by examining available ML models in MLflow

- Loops through the available ML models:

  - Generates predictions

  - Stores predictions in the lakehouse

1.  Now,click on **RealTimeWorkspace** on the left-sided navigation
    pane.

<img src="./media/image48.png"
style="width:4.12917in;height:2.8375in" />

2.  In the **RealTimeWorkspace**, click on the **DS 2-Predict Stock
    Prices** notebook.

<img src="./media/image53.png" style="width:6.5in;height:4.88333in" />

3.  Under the Explorer, select the **Lakehouse**, then click on the
    **Add* ***button*.*

<img src="./media/image54.png"
style="width:5.61891in;height:4.4375in" />

<img src="./media/image55.png"
style="width:4.7625in;height:4.21227in" />

4.  In the **Add Lakehouse** dialog box, select **Existing lakehouse**
    radio button, then click on the **Add** button.

<img src="./media/image10.png" style="width:3.00833in;height:1.83333in"
alt="A screenshot of a computer Description automatically generated" />

5.  On the OneLake data hub window, select the ***StockLakehouse*** and
    click on the **Add** button.

<img src="./media/image11.png" style="width:6.49167in;height:3.91667in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image56.png" style="width:6.5in;height:4.65347in"
alt="A screenshot of a computer Description automatically generated" />

## Task 2: Run the notebook

1.  Creates the stock predictions table in the Lakehouse, select and
    **Run** the 1<sup>st</sup> and 2<sup>nd</sup> cells.

<img src="./media/image57.png"
style="width:6.49167in;height:3.99167in" />

<img src="./media/image58.png" style="width:6.5in;height:4.08333in" />

2.  Gets a list of all stock symbols, select and **Run** the
    3<sup>rd</sup> and 4<sup>th</sup> cells.

<img src="./media/image59.png"
style="width:6.49167in;height:2.80833in" />

<img src="./media/image60.png"
style="width:6.49167in;height:3.06667in" />

3.  Creates a prediction list by examining available ML models in
    MLflow, select and **Run** the 7<sup>th</sup>, 8<sup>th</sup> ,
    9<sup>th</sup> , and 10<sup>th</sup> cells.

<img src="./media/image61.png" style="width:6.5in;height:3.93333in" />

<img src="./media/image62.png" style="width:6.5in;height:4.78333in" />

<img src="./media/image63.png" style="width:6.5in;height:2.91667in" />

<img src="./media/image64.png" style="width:6.5in;height:3.65833in" />

4.  To build predictions for each model store in Lakehouse , select and
    **Run** the 11<sup>th</sup> and 12<sup>th</sup> cells.

<img src="./media/image65.png" style="width:6.5in;height:3.125in" />

<img src="./media/image66.png"
style="width:6.49167in;height:4.74167in" />

5.  When all cells have been run, refresh the schema by clicking on the
    three dots **(...)** beside *Tables,* then navigate and click on
    ***Refresh*.**

<img src="./media/image67.png" style="width:5.44167in;height:5.4in" />

<img src="./media/image68.png"
style="width:6.49167in;height:6.81667in" />

# Exercise 3: Solution in practice

The first two sections in this module are common approaches to
developing a data science solution. The first section covered the
development of the model (exploration, feature engineering, tuning,
etc.), building, and then deploying the model. The second section
covered the consumption of the model, which is typically a separate
process and may even be done by different teams.

However, in this specific scenario, there is little benefit to creating
the model and generating predictions separately because the model we
developed is time-based univariate - the predictions that the model
generates will not change without retraining the model with new data.

Most ML models are multivariate, for example, consider a travel time
estimator that calculates travel time between two locations such model
could have dozens of input variables, but two major variables would
certainly include the time of day and weather conditions. Because the
weather is changing frequently, we'd pass this data into the model to
generate new travel time predictions (inputs: time of day and weather,
output: travel time).

Therefore, we should generate our predictions immediately after creating
the model. For practical purposes, this section shows how we could
implement the ML model building and forecasting in a single step.
Instead of using the downloaded historical data, this process will use
the aggregation tables built in module 06. Our data flow can be
visualizad like so:

## Task 1: Import the notebook

Take some time exploring the *DS 3 - Forecast All* notebook, and notice
a few key things:

- Stock data is read from the stock_minute_agg table.

- All symbols are loaded, and predictions are made on each.

- The routine will check MLflow for matching models, and load their
  parameters. This allows data science teams to build optimal
  parameters.

- Predictions for each stock are logged to the stock_predicitions table.
  There is no persistence of the model.

- There is no cross validation or other steps performed in 07a. While
  these steps are useful for the data scientist, it's not needed here.

1.  Now, click on **RealTimeWorkspace** on the left-sided navigation
    menu.

<img src="./media/image48.png"
style="width:4.12917in;height:2.8375in" />

2.  *In the **RealTimeWorkspace***, click on the **DS 3-Forecast All**
    notebook.

<img src="./media/image69.png"
style="width:5.79583in;height:3.9375in" />

3.  Under the Explorer select the **Lakehouse** and click on the
    ***Add .***

<img src="./media/image54.png" style="width:5.61891in;height:4.4375in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image55.png" style="width:4.7625in;height:4.21227in"
alt="A screenshot of a computer Description automatically generated" />

4.  In the **Add Lakehouse** dialog box, select **Existing lakehouse**
    radio button, then click on the **Add** button.

<img src="./media/image10.png" style="width:3.00833in;height:1.83333in"
alt="A screenshot of a computer Description automatically generated" />

5.  On the OneLake data hub window, select the ***StockLakehouse*** and
    click on the **Add** button.

<img src="./media/image11.png" style="width:6.49167in;height:3.91667in"
alt="A screenshot of a computer Description automatically generated" />

6.  Select and **Run** the 1<sup>st</sup> cell.

<img src="./media/image70.png"
style="width:6.49167in;height:3.49167in" />

7.  Click ***Run all*** in the command and follow along as the work
    progresses.

8.  Running the notebook for all symbols could take 10 minutes.
    <img src="./media/image71.png" style="width:6.5in;height:3.89167in" />

<img src="./media/image72.png" style="width:6.5in;height:4.32014in" />

<img src="./media/image73.png" style="width:6.49167in;height:3.35in" />

<img src="./media/image74.png" style="width:6.5in;height:5.11667in" />

<img src="./media/image75.png" style="width:6.5in;height:2.86667in" />

<img src="./media/image76.png" style="width:6.5in;height:5.43333in" />

<img src="./media/image77.png" style="width:6.5in;height:4.19583in" />

<img src="./media/image78.png" style="width:7.39029in;height:3.53881in"
alt="A screenshot of a computer program Description automatically generated" />

<img src="./media/image79.png" style="width:6.5in;height:3.02639in"
alt="A graph showing the growth of the stock market Description automatically generated" />

<img src="./media/image80.png" style="width:6.5in;height:2.58403in"
alt="A graph showing different colored lines Description automatically generated" />

<img src="./media/image81.png" style="width:6.5in;height:2.72917in"
alt="A graph showing different colored lines Description automatically generated" />

<img src="./media/image82.png" style="width:6.5in;height:2.70208in"
alt="A graph showing different colored lines Description automatically generated" />

<img src="./media/image83.png" style="width:6.5in;height:2.57569in"
alt="A graph of different colored lines Description automatically generated" />

<img src="./media/image84.png" style="width:6.5in;height:2.71458in"
alt="A graph showing the growth of a company Description automatically generated" />

<img src="./media/image85.png" style="width:6.5in;height:2.88333in"
alt="A graph showing different colored lines Description automatically generated" />

<img src="./media/image86.png" style="width:6.5in;height:2.59583in"
alt="A graph showing different colored lines Description automatically generated" />

9.  When all cells have been run, refresh the schema by clicking the
    three dots **(...)** dots to the right of the *Tables*, then
    navigate and click on ***Refresh*.**

<img src="./media/image87.png"
style="width:6.49167in;height:6.00833in" />

# Exercise 4: Building a Prediction Report

## Task 1: Build a semantic model

In this step, we'll build a semantic model (formerly called Power BI
datasets) to use in our Power BI report. A semantic model represents
data that is ready for reporting and acts as an abstraction on top of a
data source. Typically, a semantic model will be purpose built (serving
a specific reporting need) and may have transformations, relationships,
and enrichments like measures to make developing reports easier

1.  Click on **RealTimeWorkspace** on the left-sided navigation menu.

<img src="./media/image88.png" style="width:6.5in;height:5.6in" />

2.  To create a semantic model, navigate and click on the Lakehouse
    i.e., **StackLakehouse.**

<img src="./media/image89.png"
style="width:6.49167in;height:4.63333in" />

<img src="./media/image90.png"
style="width:7.34187in;height:3.56504in" />

3.  In the ***StocksLakehouse*** page*,* click on ***New semantic
    model*** in the command bar.

<img src="./media/image91.png" style="width:6.5in;height:4.73333in" />

4.  In the **New semantic model** pane **Name** field, enter the name of
    the model as ***StocksLakehousePredictions***, select the
    **stock_prediction**, and **dim_symbol** tables. Then, click on the
    **Confirm** button as shown in the below image.

<img src="./media/image92.png" style="width:4.61667in;height:5.75in" />

<img src="./media/image93.png" style="width:7.39637in;height:3.58281in"
alt="A screenshot of a computer Description automatically generated" />

5.  When the semantic model opens, we need to define relationships
    between the stock_prediction and dim_symbol tables.

6.  From the **stock_prediction** table, drag the ***Symbol***  field
    and drop it on the ***Symbol***  field in the **dim_Symbol** table
    to create a relationship. The **New relationship** dialog box
    appears.

<img src="./media/image94.png"
style="width:7.0993in;height:3.84583in" />

7.  In the **New relationship** dialog box:

- **From table** is populated with **stock_prediction** and the column
  of **Symbol.**

- **To table** is populated with **dim_symbol**  and the column of
  **Symbol.**

- Cardinality: **Many to one (\*:1)**

- Cross filter direction: **Single**

- Leave the box next to **Make this relationship active** selected.

- Select **Save**

<img src="./media/image95.png"
style="width:6.60417in;height:7.72544in" />

<img src="./media/image96.png"
style="width:7.37104in;height:3.27917in" />

## Task 2: Build the report in Power BI Desktop

1.  Open your browser, navigate to the address bar, and type or paste
    the following URL: <https://powerbi.microsoft.com/en-us/desktop/> ,
    then press the **Enter** button.

2.  Click on the **Download now** button.

> <img src="./media/image97.png"
> style="width:6.49167in;height:3.76667in" />

3.  In case, **This site is trying to open Microsoft Store** dialog box
    appears, then click on **Open** button.

<img src="./media/image98.png"
style="width:6.49167in;height:1.40833in" />

4.  Under **Power BI Desktop**, click on the **Get** button.

<img src="./media/image99.png" style="width:4.95in;height:5.23333in" />

5.  Now, click on the **Open** button.

<img src="./media/image100.png"
style="width:4.90833in;height:5.31667in" />

6.  Enter your **Microsoft Office 365** **tenant** credentials and click
    on the **Next** button.

<img src="./media/image101.png"
style="width:6.25833in;height:6.05833in" />

7.  Enter the **Administrative password** from the **Resources** tab and
    click on the **Sign in** button**.**

<img src="./media/image102.png" style="width:5.95in;height:5.175in" />

8.  In Power BI Desktop, select **Blank report.**

<img src="./media/image103.png"
style="width:7.27361in;height:4.40712in" />

9.  , On the *Home* ribbon, click the ***OneLake data hub*** and select
    **KQL Database.**

<img src="./media/image104.png"
style="width:7.27414in;height:3.1375in" />

10. On the **OneLake data hub** window, select the **StockDB**  and
    click on the **Connect** button.

<img src="./media/image105.png"
style="width:6.49167in;height:3.40833in" />

11. Enter your **Microsoft Office 365** tenant credentials and click on
    the **Next** button.

<img src="./media/image106.png" style="width:6.5in;height:6.975in" />

12. Enter the **Administrative password** from the **Resources** tab and
    click on the **Sign in** button**.**

<img src="./media/image107.png"
style="width:4.59167in;height:5.26667in" />

13. In the Navigator page, under **Display Options**, select
    **StockPrice** table, then click on the **Load** button.

<img src="./media/image108.png" style="width:6.5in;height:5.275in" />

14. In the ***Connection settings*** dialog box, select
    ***DirectQuery*** radio button and click on **OK** button.

<img src="./media/image109.png"
style="width:4.22083in;height:2.56858in" />

15. On the ***Home*** ribbon, click the ***OneLake data hub*** and
    select **Power BI semantic models** as shown in the below image.

<img src="./media/image110.png" style="width:6.5in;height:4.19167in" />

16. On the **OneLake data hub** window, select
    the **StockLakehousePredictions** from the list and click on the
    **Connect** button.

<img src="./media/image111.png"
style="width:6.49167in;height:3.38333in" />

17. In the **Connect to your data** page, select **dim_symbol,
    stock_prediction**, and click on the **Submit** button.

<img src="./media/image112.png"
style="width:5.55833in;height:5.88333in" />

18. In this case, we can dismiss the **Potential security risk** warning
    by clicking on the **OK** button.

<img src="./media/image113.png" style="width:6.5in;height:1.40833in" />

<img src="./media/image114.png" style="width:7.20263in;height:3.5236in"
alt="A screenshot of a computer Description automatically generated" />

19. Click on ***Modeling*** in the command bar, then click on ***Manage
    relationships.***

<img src="./media/image115.png" style="width:6.5in;height:3.40833in" />

20. In the **Manage relationships** pane, select +**New relationship**
    as shown in the below image.

<img src="./media/image116.png"
style="width:6.49167in;height:4.99167in" />

21. Create a **New relationship** between the ***StockPrice*** -**From
    table** and the ***stocks_prediction*** – ***To table* **(after
    selecting the table, ensure to select the symbol columns in each
    table). Set the cross filter direction to ***Both***, and make sure
    the cardinality is set to ***Many-to-many*.** Then, click on the
    **Save** button.

> <img src="./media/image117.png" style="width:6.5in;height:6.38333in" />

22. In **Mange relationships** page, select ***StockPrice***,
    ***stocks_prediction*** tables and click on the **Close** button.

<img src="./media/image118.png" style="width:6.5in;height:4.85833in" />

23. In the **Power BI** page, under **Visualizations**, click on the
    **Line chart** icon to add a **Column chart** to your report.

- On the **Data** pane, expand **StockPrice**  and check the box next
  to **timestamp**. This creates a column chart and adds the field to
  the **X-axis**.

- On the **Data** pane, expand **StockPrice** and check the box next
  to **Price**. This adds the field to the **Y-axis**.

- On the **Data** pane, expand **StockPrice** and check the box next
  to **Symbol**. This adds the field to the **Legend**.

- **Filter**: **timestamp** to ***Relative time** is in the last* **15
  minutes**.

<img src="./media/image119.png"
style="width:5.675in;height:6.80833in" />

<img src="./media/image120.png"
style="width:7.1554in;height:4.06467in" />

24. In the **Power BI** page, under **Visualizations**, click on the
    **Line chart** icon to add a **Column chart** to your report.

- On the **Data** pane, expand **StockPrice**  and check the box next
  to **timestamp**. This creates a column chart and adds the field to
  the **X-axis**.

- On the **Data** pane, expand **StockPrice** and check the box next
  to **Price**. This adds the field to the **Y-axis**.

- On the **Data** pane, expand **dim_symbol** and check the box next
  to **Market**. This adds the field to the **Legend**.

- **Filter**: **timestamp** to ***Relative time** is in the last* **1
  hour**.

<img src="./media/image121.png"
style="width:7.18155in;height:2.64583in" />

<img src="./media/image122.png"
style="width:5.25417in;height:5.53899in" />

<img src="./media/image123.png" style="width:5.8in;height:6.025in" />

<img src="./media/image124.png"
style="width:7.32714in;height:4.17083in" />

25. In the **Power BI** page, under **Visualizations**, click on the
    **Line chart** icon to add a **Column chart** to your report.

- On the **Data** pane, expand **Stock_prediction**  and check the box
  next to **predict_time**. This creates a column chart and adds the
  field to the **X-axis**.

- On the **Data** pane, expand **Stock_prediction**  and check the box
  next to **yhat**. This adds the field to the **Y-axis**.

- On the **Data** pane, expand **Stock_prediction** and check the box
  next to **Symbol**. This adds the field to the **Legend**.

- **Filter**: **predict_time** to ***Relative date** in the last* **3
  days**.

<img src="./media/image125.png"
style="width:5.60833in;height:6.90833in" />

<img src="./media/image126.png"
style="width:6.49167in;height:3.54167in" />

<img src="./media/image127.png"
style="width:4.44583in;height:5.09322in" />

<img src="./media/image128.png" style="width:6.5in;height:4.03333in" />

26. In the **Power BI** page, under **Data,** right click
    the ***stocks_prediction*** table and select ***New measure.***

<img src="./media/image129.png" style="width:6.5in;height:5.35in" />

27. Measures are formulas written in the Data Analysis Expressions (DAX)
    language; for this DAX formula, enter +++***currdate = NOW()+++* **

<img src="./media/image130.png" style="width:6.49167in;height:4in" />

28. With the prediction chart selected, navigate to the additional
    visualization options i.e., the magnifying glass/chart icon and add
    a **new *X-Axis Constant Line***.

<img src="./media/image131.png" style="width:5.75in;height:5.9in" />

29. Under *Value*, use the formula button **(fx)** to choose a field.

<img src="./media/image132.png"
style="width:5.89583in;height:3.65557in" />

30. In **Value -Apply settings to** page, click on the dropdown under
    **what field should we base this on?**, then click on the dropdown
    of **stocks_prediction**, select the ***currdate* **measure. Then,
    click on the **OK** button.

<img src="./media/image133.png"
style="width:6.41495in;height:4.52917in" />

<img src="./media/image134.png"
style="width:7.38149in;height:4.0125in" />

31. Navigate to the additional visualization options i.e., the
    magnifying glass/chart icon, turn On the **Shade
    area**.<img src="./media/image135.png"
    style="width:6.91875in;height:3.7125in" />

32. Configured the relationships between tables, all visual should cross
    filter; when selecting either a symbol or market on a chart, all
    visuals should react accordingly. As shown in the below image, the
    **NASDAQ** market is selected on the upper-right market chart:

<img src="./media/image136.png" style="width:6.5in;height:4.01667in" />

<img src="./media/image137.png"
style="width:7.31785in;height:4.51424in" />

33. Click on the **Publish** in the command bar.

<img src="./media/image138.png" style="width:6.5in;height:3.69167in" />

34. In **Microsoft Power BI Desktop** dialog box, click on the **Save**
    button.

<img src="./media/image139.png"
style="width:5.09167in;height:1.80833in" />

35. In **Save this file** dialog box, enter the Name as **Prediction
    Report** and select the location. Then, click on the **Save**
    button.

<img src="./media/image140.png" style="width:4.95in;height:4.73333in" />

## **Summary**

In this lab, you’ve learned how to approach a data science solution in
Fabric by importing and executing a notebook that processed data,
created a ML model of the data using Prophet, and stored the model in
MLflow. Additionally, you’ve evaluated the model using cross validation
and tested the model using additional data.

You’ve followed up on the creation of the ML model by consuming the ML
model, generating predictions, and storing those predictions in the
Lakehouse. Refined the process to build a model and generate predictions
in a single step, streamlining the process of creating predictions and
operationalizing the model generation. Then, you’ve created a report (or
reports) that show the current stock prices with predicted values,
making the data available for business users.
