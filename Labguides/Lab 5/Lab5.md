# 實驗室 05：使用管道構建資料倉庫

**導言**

在本實驗室中，您將在 Microsoft Fabric 內構建一個 Synapse
資料倉庫，以聚合來自 KQL 資料庫的資料。在 Microsoft Fabric
中，構建資料倉庫有兩種主要方式：使用 Synapse 資料倉庫（本模組的重點）和
Lakehouse。

Synapse 資料倉庫以類似於 Lakehouse 表的 Delta/Parquet 格式在 OneLake
中存儲資料。不過，只有 Synapse 資料倉庫在 T-SQL
端點上提供讀/寫功能。如果您正在遷移資料倉庫或更熟悉 T-SQL 開發，使用
Synapse 資料倉庫是一個合理的選擇。

無論是選擇 Lakehouse 資料倉庫還是 Synapse
資料倉庫，最終目標都是相似的：擁有經過高度整理的資料，以支援業務分析需求。通常情況下，資料倉庫採用星型模式，包含維度表和事實表。這些表是業務的單一真實來源。

目前，我們示例應用程式的資料流程速度為每個股票代碼每秒 1
次請求，因此每檔股票每天的資料值為 86,400
個。為便於我們的倉庫使用，我們將把這些資料整理為每日值，包括每檔股票的每日最高價、每日最低價和收盤價。這樣可以減少行數。

在
ETL（提取、轉換和載入）流程中，我們將提取所有尚未導入的資料，根據當前浮水印確定導入到暫存表中。然後對這些資料進行匯總，並將其放入維度/事實表中。請注意，雖然我們只導入一個表（股票價格），但我們正在構建的框架支持多個表的導入。

**目標**

- 在 Fabric 工作區內創建 Synapse 資料倉庫，並創建必要的暫存和 ETL
  物件，以方便資料處理和轉換。

- 建立資料 Pipeline，從源系統高效提取、轉換和載入（ETL）資料到 Synapse
  資料倉庫，確保資料的準確性和一致性。

- 在資料倉庫內創建維度表和事實表，以便有效地組織和存儲結構化資料，用於分析目的。

- 實施將資料增量載入到資料倉庫的程式，確保高效處理大型資料集，同時保持資料的完整性。

- 創建視圖以支援 ETL 過程中的資料聚合，優化資料處理並提高 Pipeline
  性能。

- 在 Synapse Data Warehouse 中創建語義模型，定義表格關係，並生成用於資料
  Visualization 的 Power BI 報告。

# 練習 1：設置倉庫和管道

## 任務 1：在 Fabric 工作區創建 Synapse 資料倉庫

要開始使用，我們首先要在工作區中創建 Synapse Data Warehouse。

1.  點擊頁面左側底部的**即時分析圖示**，導航並點擊**資料倉庫**，如下圖所示。

    ![](./media/image1.png)

2.  選擇 **"Warehouse** "瓦片，創建新的 Synapse Data Warehouse。

    ![](./media/image2.png)

3.  在**新建倉庫**對話方塊中，輸入 **+++StocksDW+++**
    作為名稱，然後按一下**創建**按鈕。

      ![](./media/image3.png)

4.  倉庫基本上是空的。

    ![](./media/image4.png)

5.  點擊命令列中的***新建 SQL
    查詢***下拉式功能表，然後選擇**空白**部分下的**新建 SQL
    查詢**。我們將在下一個任務中開始構建模式。

    ![](./media/image5.png)

## 任務 2：創建暫存和 ETL 對象

1.  運行以下查詢，創建在
    ETL（提取、轉換和載入）過程中保存資料的暫存表。這還將創建所用的兩個模式--*stg*
    和 *ETL*；模式有助於按類型或功能對工作負載進行分組。*stg*
    模式用於暫存，包含用於 ETL 流程的中間表。*ETL*
    模式包含用於資料移動的查詢，以及用於跟蹤狀態的單個表。

2.  請注意，浮水印的開始日期被任意選擇為某個以前的日期（1/1/2022），以確保捕獲所有資料--該日期將在每次成功運行時更新。

3.  在查詢編輯器中，複製並粘貼以下代碼。按一下**運行**按鈕執行查詢。執行查詢後，您將看到結果。
      ```
      /* 1 - Create Staging and ETL.sql */
      
      -- STAGING TABLES
      CREATE SCHEMA stg
      GO
      
      CREATE TABLE stg.StocksPrices
      (
         symbol VARCHAR(5) NOT NULL
         ,timestamp VARCHAR(30) NOT NULL
         ,price FLOAT NOT NULL
         ,datestamp VARCHAR(12) NOT NULL
      )
      GO
      
      -- ETL TABLES
      CREATE SCHEMA ETL
      GO
      CREATE TABLE ETL.IngestSourceInfo
      (
          ObjectName VARCHAR(50) NOT NULL
          ,WaterMark DATETIME2(6)
          ,IsActiveFlag VARCHAR(1)
      )
      
      INSERT [ETL].[IngestSourceInfo]
      SELECT 'StocksPrices', '1/1/2022 23:59:59', 'Y'
      
      ```
      
      ![](./media/image6.png)
      
      ![](./media/image7.png)

4.  重命名查詢以供參考。按右鍵**資源管理器中**的 **SQL 查詢
    1**，然後選擇**重命名**。

    ![](./media/image8.png)

5.  在**重命名**對話方塊中，在**名稱**欄位下輸入 +++Create stocks and metadata+++，然後按一下**重命名**按鈕。
      ![](./media/image9.png)

6.  點擊命令列中的***新建 SQL
    查詢***下拉式功能表，然後選擇**空白**部分下的**新建 SQL
    查詢**。下一步我們將開始構建模式：

      ![](./media/image10.png)

7.  存儲過程 *sp_IngestSourceInfo_Update*
    會更新浮水印；這可確保我們跟蹤哪些記錄已被導入

8.  在查詢編輯器中，複製並粘貼以下代碼。按一下**運行**按鈕執行查詢。執行查詢後，您將看到結果。

      **複製**
      ```
      /* 1 - Create Staging and ETL.sql */
      
      CREATE PROC [ETL].[sp_IngestSourceInfo_Update]
      @ObjectName VARCHAR(50)
      ,@WaterMark DATETIME2(6)
      AS
      BEGIN
      
      UPDATE [ETL].[IngestSourceInfo]
          SET WaterMark = @WaterMark
      WHERE 
          ObjectName  = @ObjectName
      
      END
      
      GO
      ```
      
      ![](./media/image11.png)
      
     ![](./media/image12.png)

6.  重命名查詢，以便以後參考。按右鍵**資源管理器中**的 **SQL 查詢
    1**，然後選擇**重命名**。

    ![](./media/image13.png)

7.  在 "**重命名** "對話方塊中，在 "**名稱** "欄位下輸入
    +++ETL.sql_IngestSource+++，然後按一下 "**重命名** "按鈕。

     ![](./media/image14.png)

    這應該類似於

     ![](./media/image15.png)

## 任務 3：創建資料管道

1.  在 **StockDW** 頁面上，按一下左側導航菜單上的 **RealTimeWorkspace**
    工作區。

    ![](./media/image16.png)

2.  在 **Synapse Data Warehouse RealTimeWorkhouse** 主頁上，在
    **RealTimeWorkhouse** 下按一下 **+New**，然後選擇 **Data
    Pipeline。**

    ![](./media/image17.png)

3.  **新管道**對話方塊將出現，在**名稱**欄位中輸入 +++PL_Refresh_DWH+++，然後按一下 "**創建 "**按鈕**。**

      ![](./media/image18.png)

4.  在 ***PL_Refresh_DWH*** 頁面中，導航到
    "**構建資料管道以組織和移動資料**部分"，然後按一下 "管道**活動**"。

    ![](./media/image19.png)

5.  然後，導航並選擇***查找***活動，如下圖所示。

    ![](./media/image20.png)

6.  在**常規**選項卡上**，**在**名稱欄位**中輸入 +++Get WaterMark+++

      ![](./media/image21.png)

7.  按一下 "**設置** "選項卡，輸入下圖所示的詳細資訊。

      |    |  |
      |---|---|
      |Connection|	Click on the dropdown and select StocksDW from the list.|
      |Use query|	Query|
      |Query| 	+++SELECT * FROM [ETL].[IngestSourceInfo] WHERE IsActiveFlag = 'Y'+++|
      |First row only| 	unchecked.|
      
      ![](./media/image22.png)

## 任務 4：構建 ForEach 活動

本任務的重點是在單個 ForEach 活動中構建多個活動。ForEach
活動是一個容器，可以將子活動作為一個組來執行：在本例中，如果我們有多個資料來源要從中提取資料，我們將對每個資料來源重複這些步驟。

1.  在 "**查找 - 獲取浮水印
    "**框中，導航並按一下右箭頭**添加活動**。然後，導航並選擇
    **ForEach** 活動，如下圖所示。

      ![](./media/image23.png)

2.  按一下 "**設置** "選項卡，輸入專案 +++@activity('Get WaterMark').output.value+++

      看起來應該與下圖類似：

    ![](./media/image24.png)

3.  在 **ForEach** 框中，按一下加號 (+) 添加新活動。

      ![](./media/image25.png)

4.  在 *ForEach* 中選擇並添加***複製資料***活動。

     ![](./media/image26.png)

5.  選擇**複製資料1**
    活動圖示，在**常規**選項卡上**，**在**名稱欄位**中輸入 +++Copy KQL+++

     ![](./media/image27.png)

6.  按一下 "**源** "選項卡，輸入以下設置。

    Connection :**Select StocksDB from the dropdown**
    Use query :**Query**
    Query     :
    +++@concat('StockPrice  
        | where todatetime(timestamp) >= todatetime(''', item().WaterMark,''') 
        | order by timestamp asc
        | extend datestamp = substring(timestamp,0,10) 
        | project symbol, timestamp, price, datestamp 
        | take 500000 
        | where not(isnull(price))
        ' ) +++


    活動的 "*源* "選項卡應與之相似：
    
    ![](./media/image28.png)

7.  按一下 "**目的地** "選項卡，輸入以下設置
    |  |  |
    |---|---|
    |Connection|	drop down, select StocksDW from the list|
    |Table option|	Use existing|
    |Table| 	stg.StocksPrices|


  - 在*高級*部分下，輸入以下***預複製腳本***，以便在載入暫存表之前截斷表：

    +++刪除 stg.StocksPrices+++

    這一步首先刪除暫存表中的舊資料，然後複製 KQL
    表中的資料，從最後一個浮水印中選擇資料並插入到暫存表中。使用浮水印對於避免處理整個表非常重要；此外，KQL
    查詢的最大行數為 500,000 行。考慮到當前的資料攝取速度，這相當於一天的
    3/4。
    
    活動的 "*目的地* "選項卡應如下所示：
    
    ![](./media/image29.png)

8.  在 *ForEach* 框中，按一下加號 **(+)**，導航並選擇**查找**活動。

    ![](./media/image30.png)

9.  點擊 **Lookup1 圖示**，在 "**常規** "選項卡的 **"名稱 "欄位**中輸入
    +++Get New WaterMark+++

    ![](./media/image31.png)

10. 按一下 "**設置** "選項卡，輸入以下設置

    |  |  |
    |----|----|
    |Connection	|drop down, select StocksDW from the list|
    |Use query|	Query|
    |Query|	+++@concat('Select Max(timestamp) as WaterMark from stg.', item().ObjectName)+++|

    ![](./media/image32.png)

11. 在 *ForEach* 框中，按一下加號
    **(+)**，導航並選擇***存儲過程***活動。

    ![](./media/image33.png)
12. 按一下**存儲過程圖示**。在 "**常規** "選項卡的 **"名稱 "欄位**中輸入
    +++Update WaterMark+++

      ![](./media/image34.png)

13. 按一下 "**設置** "選項卡，輸入以下設置。
    |  |   |
    |---|---|
    |Workspace 	|StocksDW|
    |Stored procedure name| 	ETL.sp_IngestSourceInfo_Update|


    - 參數（按一下*導入*可自動添加參數名稱）：

    |Name|	Type|	Value|
    |----|----|
    |ObjectName	|String|	@item().ObjectName|
    |WaterMark	|DateTime	|@activity('Get New WaterMark').output.firstRow.WaterMark|

    ![](./media/image35.png)

## 任務 5：測試管道

1.  從管道中的 "***主頁*** "選項卡選擇 "***運行***"。

      ![](./media/image36.png)

2.  在 "**保存並運行？**"對話方塊中，按一下 "**保存並運行**"按鈕

      ![](./media/image37.png)

3.  這將提示首先保存管道，然後驗證以查找任何配置錯誤。初始運行將耗時片刻，並將資料複製到暫存表中。

      ![](./media/image38.png)

4.  在 **PL_Refresh_DWH** 頁面上，按一下左側導航菜單上的
    **RealTimeWorkspace** 工作區。

     ![](./media/image39.png)

5.  點擊**刷新**按鈕。

      ![](./media/image40.png)

6.  在資料倉庫中，數據應在暫存表中可見。在資料倉庫內，選擇一個表將顯示表中資料的預覽。按一下左側導航功能表中的
    StocksDW，然後按一下資源管理器中的 o
    **模式**。在模式下，導航並按一下 **stg**，然後按一下
    **StocksPrices**，如下圖所示。

    ![](./media/image41.png)

9.  點擊命令列中的***新建 SQL
    查詢***下拉式功能表，然後選擇**空白**部分下的**新建 SQL
    查詢**。下一步我們將開始構建模式：

    ![](./media/image42.png)

8.  當我們進入 Data Warehouse 時，在新的 SQL
    查詢視窗中運行下面的腳本來重置攝取過程。在開發過程中，重置腳本通常可以方便地進行增量測試。這將重置日期並刪除暫存表中的資料。

     ***注意：**我們還沒有創建事實表或維度表，但腳本應該仍然有效。*

9.  在查詢編輯器中，複製並粘貼以下代碼。按一下**運行**按鈕執行查詢。執行查詢後，您將看到結果。
      ```
      -- Run this to 'RESET' the ingestion tables
      
      exec ETL.sp_IngestSourceInfo_Update 'StocksPrices', '2022-01-01 23:59:59.000000'
      GO
      
      IF (EXISTS (SELECT * FROM INFORMATION_SCHEMA.TABLES 
          WHERE TABLE_SCHEMA = 'stg' AND TABLE_NAME = 'StocksPrices'))
      BEGIN
          delete stg.StocksPrices
      END
      GO
      
      IF (EXISTS (SELECT * FROM INFORMATION_SCHEMA.TABLES 
          WHERE TABLE_SCHEMA = 'dbo' AND TABLE_NAME = 'fact_Stocks_Daily_Prices'))
      BEGIN
          delete dbo.fact_Stocks_Daily_Prices
      END
      GO
      
      IF (EXISTS (SELECT * FROM INFORMATION_SCHEMA.TABLES 
          WHERE TABLE_SCHEMA = 'dbo' AND TABLE_NAME = 'dim_Symbol'))
      BEGIN
          delete dbo.dim_Symbol
      END
      GO
      ```
      ![](./media/image43.png)
      
      ![](./media/image44.png)

# 練習 2：構建星型模式

對於日期維度，我們將為可預見的未來載入足夠的值。日期維度在所有實現中都相當相似，通常保存特定日期的詳細資訊：星期、月份、季度等。

對於符號維度，我們將在流水線期間增量載入--這樣，如果在某個時間點添加了新股票，它們就會在流水線執行期間被添加到符號維度表中。符號維度保存每個符號的其他詳細資訊，如公司名稱、交易的股票市場等。

我們還將創建視圖，通過聚合股票的最低價、最高價和收盤價，更方便地從暫存表載入資料，從而支援
Data Pipeline。

## 任務 1：創建維度表和事實表

1.  點擊命令列中的***新建 SQL
    查詢***下拉式功能表，然後選擇**空白**部分下的**新建 SQL
    查詢**。下一步我們將開始構建模式。

    ![](./media/image42.png)

2.  在我們的 Data Warehouse 中，運行以下 SQL
    來創建事實表和維度表。和上一步一樣，你可以臨時運行這個查詢，也可以創建一個
    SQL 查詢來保存查詢，以備將來使用。

3.  在查詢編輯器中，複製並粘貼以下代碼。按一下**運行**按鈕執行查詢。執行查詢後，您將看到結果。
    
     **複製**
    ```
    /* 2 - Create Dimension and Fact tables.sql */
    
    -- Dimensions and Facts (dbo)
    CREATE TABLE dbo.fact_Stocks_Daily_Prices
    (
       Symbol_SK INT NOT NULL
       ,PriceDateKey DATE NOT NULL
       ,MinPrice FLOAT NOT NULL
       ,MaxPrice FLOAT NOT NULL
       ,ClosePrice FLOAT NOT NULL
    )
    GO
    
    CREATE TABLE dbo.dim_Symbol
    (
        Symbol_SK INT NOT NULL
        ,Symbol VARCHAR(5) NOT NULL
        ,Name VARCHAR(25)
        ,Market VARCHAR(15)
    )
    GO
    
    CREATE TABLE dbo.dim_Date 
    (
        [DateKey] DATE NOT NULL
        ,[DayOfMonth] int
        ,[DayOfWeeK] int
        ,[DayOfWeekName] varchar(25)
        ,[Year] int
        ,[Month] int
        ,[MonthName] varchar(25)
        ,[Quarter] int
        ,[QuarterName] varchar(2)
    )
    GO
    ```
      ![](./media/image45.png)
      
      ![](./media/image46.png)

4.  重命名查詢以供參考。按右鍵資源管理器中的 **SQL
    查詢**，然後選擇**重命名**。

    ![](./media/image47.png)

5.  在 "**重命名** "對話方塊中，在 "**名稱 "欄位下輸入 +++Create Dimension and Fact tables+++，然後按一下 "**重命名** "按鈕。

     ![](./media/image48.png)
## 任務 2：載入日期維度

1.  點擊窗口頂部的***新建 SQL 查詢***。點擊命令列中的***新建 SQL
    查詢***下拉式功能表，然後選擇**空白**部分下的**新建 SQL
    查詢**。下一步我們將開始構建模式：

       ![](./media/image49.png)

2.  日期維度是有區別的；它可以一次性載入我們需要的所有值。運行以下腳本，創建一個存儲過程，在日期維度表中填入大量值。

3.  在查詢編輯器中，複製並粘貼以下代碼。按一下**運行**按鈕執行查詢。執行查詢後，您將看到結果。

      **複製**
      ```  
          /* 3 - Load Dimension tables.sql */
          
          CREATE PROC [ETL].[sp_Dim_Date_Load]
          @BeginDate DATE = NULL
          ,@EndDate DATE = NULL
          AS
          BEGIN
          
          SET @BeginDate = ISNULL(@BeginDate, '2022-01-01')
          SET @EndDate = ISNULL(@EndDate, DATEADD(year, 2, GETDATE()))
          
          DECLARE @N AS INT = 0
          DECLARE @NumberOfDates INT = DATEDIFF(day,@BeginDate, @EndDate)
          DECLARE @SQL AS NVARCHAR(MAX)
          DECLARE @STR AS VARCHAR(MAX) = ''
          
          WHILE @N <= @NumberOfDates
              BEGIN
              SET @STR = @STR + CAST(DATEADD(day,@N,@BeginDate) AS VARCHAR(10)) 
              
              IF @N < @NumberOfDates
                  BEGIN
                      SET @STR = @STR + ','
                  END
          
              SET @N = @N + 1;
              END
          
          SET @SQL = 'INSERT INTO dbo.dim_Date ([DateKey]) SELECT CAST([value] AS DATE) FROM STRING_SPLIT(@STR, '','')';
          
          EXEC sys.sp_executesql @SQL, N'@STR NVARCHAR(MAX)', @STR;
          
          UPDATE dbo.dim_Date
          SET 
              [DayOfMonth] = DATEPART(day,DateKey)
              ,[DayOfWeeK] = DATEPART(dw,DateKey)
              ,[DayOfWeekName] = DATENAME(weekday, DateKey)
              ,[Year] = DATEPART(yyyy,DateKey)
              ,[Month] = DATEPART(month,DateKey)
              ,[MonthName] = DATENAME(month, DateKey)
              ,[Quarter] = DATEPART(quarter,DateKey)
              ,[QuarterName] = CONCAT('Q',DATEPART(quarter,DateKey))
          
          END
          GO
      ```        
       ![](./media/image50.png)
          
       ![](./media/image51.png)

4.  在同一查詢視窗中，通過運行以下腳本執行上述程式。

     **複製**
    ```
    /* 3 - Load Dimension tables.sql */
    Exec ETL.sp_Dim_Date_Load
    ```
    ![](./media/image52.png)
    ![](./media/image53.png)

5.  重命名查詢以供參考。按右鍵資源管理器中的 **SQL
    查詢**，然後選擇**重命名**。

    ![](./media/image54.png)

6.  在 "**重命名** "對話方塊中，在 "**名稱 "**欄位下輸入 +++Load Dimension tables+++，然後按一下 "**重命名** "按鈕。

    ![](./media/image55.png)

## 任務 3：創建載入符號標注的存儲過程

1.  點擊命令列中的**新建 SQL
    查詢**下拉式功能表，然後選擇**空白**部分下的**新建 SQL
    查詢**。下一步我們將開始構建模式。

      ![](./media/image49.png)

2.  與日期維度類似，每個股票代碼對應符號維度表中的一行。該表包含股票的詳細資訊，如公司名稱和股票上市市場。

3.  在查詢編輯器中，複製並粘貼以下代碼。按一下**運行**按鈕執行查詢。這將創建載入股票代碼維度的存儲過程。我們將在管道中執行該過程，以處理可能進入源的任何新股票。

      **複製**
      ```
      /* 3 - Load Dimension tables.sql */
      
      CREATE PROC [ETL].[sp_Dim_Symbol_Load]
      AS
      BEGIN
      
      DECLARE @MaxSK INT = (SELECT ISNULL(MAX(Symbol_SK),0) FROM [dbo].[dim_Symbol])
      
      INSERT [dbo].[dim_Symbol]
      SELECT  
          Symbol_SK = @MaxSK + ROW_NUMBER() OVER(ORDER BY Symbol)  
          , Symbol
          , Name
          ,Market
      FROM 
          (SELECT DISTINCT
          sdp.Symbol 
          , Name  = 'Stock ' + sdp.Symbol 
          , Market = CASE SUBSTRING(Symbol,1,1)
                          WHEN 'B' THEN 'NASDAQ'
                          WHEN 'W' THEN 'NASDAQ'
                          WHEN 'I' THEN 'NYSE'
                          WHEN 'T' THEN 'NYSE'
                          ELSE 'No Market'
                      END
          FROM 
              [stg].[vw_StocksDailyPrices] sdp
          WHERE 
              sdp.Symbol NOT IN (SELECT Symbol FROM [dbo].[dim_Symbol])
          ) stg
      
      END
      GO
      ```
      
      ![](./media/image56.png)
      
      ![](./media/image57.png)

7.  重命名查詢以供參考。按右鍵資源管理器中的 **SQL
    查詢**，然後選擇**重命名**。

      ![](./media/image58.png)

8.  在**重命名**對話方塊中，在**名稱**欄位下輸入 +++Load the stock symbol dimension+++，然後按一下**重命名**按鈕。

      ![](./media/image59.png)

## **任務 4：創建視圖**

1.  點擊命令列中的***新建 SQL
    查詢***下拉式功能表，然後選擇**空白**部分下的**新建 SQL
    查詢**。下一步我們將開始構建模式。

![A screenshot of a computer Description automatically
generated](./media/image49.png)

2.  創建視圖，支援載入過程中的資料聚合。當 Data Pipeline
    運行時，資料會從 KQL
    資料庫複製到我們的暫存表中，我們將在暫存表中把每檔股票的所有資料聚合成每天的最低價、最高價和收盤價。

3.  在查詢編輯器中，複製並粘貼以下代碼。按一下**運行**按鈕執行查詢。

/\* 4 - Create Staging Views.sql \*/

CREATE VIEW \[stg\].\[vw_StocksDailyPrices\]

AS

SELECT

Symbol = symbol

,PriceDate = datestamp

,MIN(price) as MinPrice

,MAX(price) as MaxPrice

,(SELECT TOP 1 price FROM \[stg\].\[StocksPrices\] sub

WHERE sub.symbol = prices.symbol and sub.datestamp = prices.datestamp

ORDER BY sub.timestamp DESC

) as ClosePrice

FROM

\[stg\].\[StocksPrices\] prices

GROUP BY

symbol, datestamp

GO

/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/

CREATE VIEW stg.vw_StocksDailyPricesEX

AS

SELECT

ds.\[Symbol_SK\]

,dd.DateKey as PriceDateKey

,MinPrice

,MaxPrice

,ClosePrice

FROM

\[stg\].\[vw_StocksDailyPrices\] sdp

INNER JOIN \[dbo\].\[dim_Date\] dd

ON dd.DateKey = sdp.PriceDate

INNER JOIN \[dbo\].\[dim_Symbol\] ds

ON ds.Symbol = sdp.Symbol

GO

![A screenshot of a computer Description automatically
generated](./media/image60.png)

![A screenshot of a computer Description automatically
generated](./media/image61.png)

4.  重命名查詢以供參考。按右鍵資源管理器中的 **SQL
    查詢**，然後選擇**重命名**。

![](./media/image62.png)

5.  在**重命名**對話方塊中，在**名稱**欄位下輸入 +++ **創建暫存視圖
    +++**，然後按一下**重命名**按鈕。

![A screenshot of a computer screen Description automatically
generated](./media/image63.png)

## 任務 5：添加載入符號的活動

1.  在 **StockDW** 頁面，點擊左側導航菜單上的 **PL_Refresh_DWH**。

![](./media/image64.png)

2.  在管道中，添加名為 "***填充符號維度
    "***的新***存儲過程***活動，該活動將執行載入股票 符號的存儲過程。

3.  這應該連接到 ForEach 活動的成功輸出（而不是 ForEach 活動內部）。

> ![A screenshot of a computer Description automatically
> generated](./media/image65.png)

4.  在**常規**選項卡上**，**在**名稱欄位**中輸入 +++ 填充**符號尺寸**
    +++

> ![A screenshot of a computer Description automatically
> generated](./media/image66.png)

5.  按一下 "**設置** "選項卡，輸入以下設置。

[TABLE]

![](./media/image67.png)

## 任務 6：創建載入每日價格的程式

1.  在 **PL_Refresh_DWH** 頁面，點擊左側導航菜單上的 **StockDW**。

![A screenshot of a computer Description automatically
generated](./media/image68.png)

2.  點擊命令列中的***新建 SQL
    查詢***下拉式功能表，然後選擇**空白**部分下的**新建 SQL
    查詢**。下一步我們將開始構建模式。

![A screenshot of a computer Description automatically
generated](./media/image49.png)

3.  接下來，運行下面的腳本，創建用於構建事實表的存儲過程。該過程將暫存資料合併到事實表中。如果管道全天運行，則會更新值，以反映最低價、最高價和收盤價的任何變化。

> **注意**：目前，Fabric 資料 Warehouse 不支援 T-SQL
> 合併語句；因此，將根據需要更新資料，然後插入。

4.  在查詢編輯器中，複製並粘貼以下代碼。按一下**運行**按鈕執行查詢。

/\* 5 - ETL.sp_Fact_Stocks_Daily_Prices_Load.sql \*/

CREATE PROCEDURE \[ETL\].\[sp_Fact_Stocks_Daily_Prices_Load\]

AS

BEGIN

BEGIN TRANSACTION

UPDATE fact

SET

fact.MinPrice = CASE

WHEN fact.MinPrice IS NULL THEN stage.MinPrice

ELSE CASE WHEN fact.MinPrice \< stage.MinPrice THEN fact.MinPrice ELSE
stage.MinPrice END

END

,fact.MaxPrice = CASE

WHEN fact.MaxPrice IS NULL THEN stage.MaxPrice

ELSE CASE WHEN fact.MaxPrice \> stage.MaxPrice THEN fact.MaxPrice ELSE
stage.MaxPrice END

END

,fact.ClosePrice = CASE

WHEN fact.ClosePrice IS NULL THEN stage.ClosePrice

WHEN stage.ClosePrice IS NULL THEN fact.ClosePrice

ELSE stage.ClosePrice

END

FROM \[dbo\].\[fact_Stocks_Daily_Prices\] fact

INNER JOIN \[stg\].\[vw_StocksDailyPricesEX\] stage

ON fact.PriceDateKey = stage.PriceDateKey

AND fact.Symbol_SK = stage.Symbol_SK

INSERT INTO \[dbo\].\[fact_Stocks_Daily_Prices\]

(Symbol_SK, PriceDateKey, MinPrice, MaxPrice, ClosePrice)

SELECT

Symbol_SK, PriceDateKey, MinPrice, MaxPrice, ClosePrice

FROM

\[stg\].\[vw_StocksDailyPricesEX\] stage

WHERE NOT EXISTS (

SELECT \* FROM \[dbo\].\[fact_Stocks_Daily_Prices\] fact

WHERE fact.PriceDateKey = stage.PriceDateKey

AND fact.Symbol_SK = stage.Symbol_SK

)

COMMIT

END

GO

![A screenshot of a computer Description automatically
generated](./media/image69.png)

![A screenshot of a computer Description automatically
generated](./media/image70.png)

6.  重命名查詢以供參考。按右鍵資源管理器中的 **SQL
    查詢**，然後選擇**重命名**。

![A screenshot of a computer Description automatically
generated](./media/image71.png)

7.  在 "**重命名** "對話方塊中，在 "**名稱** "欄位下輸入 +++
    ETL.sp\_**Fact**\_Stocks_Daily_Prices_Load++++，然後按一下
    "**重命名** "按鈕。

![A screenshot of a computer Description automatically
generated](./media/image72.png)

## 任務 7：為管道添加活動以載入每日股票價格

1.  在 **StockDW** 頁面，點擊左側導航菜單上的 **PL_Refresh_DWH**。

![A screenshot of a computer Description automatically
generated](./media/image73.png)

2.  在管道中添加另一個名為 "***填充事實股票每日價格***
    "的***存儲過程***活動，將股票價格從暫存載入到事實表中。將 *Populate
    Symbols Dimension* 的成功輸出連接到新的 *Populate Fact Stocks Daily
    Prices* 活動。

> ![A screenshot of a computer Description automatically
> generated](./media/image74.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image75.png)

3.  按一下 "**設置** "選項卡，輸入以下設置。

[TABLE]

![](./media/image76.png)

## 任務 8.運行管道

1.  按一下 "運***行***
    "按鈕運行管道，並驗證管道是否運行以及是否載入了事實表和維度表。

![A screenshot of a computer Description automatically
generated](./media/image77.png)

2.  在 "**保存並運行？**"對話方塊中，按一下 "**保存並運行 "**按鈕

> ![A screenshot of a computer Description automatically
> generated](./media/image37.png)

![A screenshot of a computer Description automatically
generated](./media/image78.png)

## 任務 9：安排管道

1.  接下來，安排管道定期運行。這將因業務情況而異，但可以頻繁運行（每幾分鐘一次）或全天運行。

> **注意**：在這種特定情況下，由於每天大約有 700k 條記錄，而 KQL
> 將查詢結果限制在 500k，因此管道必須每天至少運行兩次才能保持最新狀態。

2.  要對管道進行計畫，請按一下計畫按鈕（*運行*按鈕旁邊）並設置一個週期性計畫，如每小時或每幾分鐘一次。

![A screenshot of a computer Description automatically
generated](./media/image79.png)

![A screenshot of a computer Description automatically
generated](./media/image80.png)

# 練習 3：語義建模

最後一步是通過創建語義模型和在 Power BI 中查看資料來實現資料的可操作性。

## 任務 1：創建語義模型

從概念上講，語義模型為我們在業務分析中使用的資料提供了一個抽象。通常，我們通過
Power BI 中使用的語義模型來 Expose
資料倉庫中的資料。在基本層面上，它們將包括表之間的關係。

*注： Power BI 資料集最近已更名為語義模型（Semantic
Models）。在某些情況下，標籤可能尚未更新。這兩個術語可以互換使用。請[在
Power BI
博客上](https://powerbi.microsoft.com/en-us/blog/datasets-renamed-to-semantic-models/)閱讀有關這一變更的更多資訊。*

當我們創建資料倉庫時，會自動創建一個預設語義模型。我們可以在 Power BI
中利用它，但它也包含了許多我們可能不需要的表的工件。因此，我們將只創建事實表和二維表的新語義模型。

1.  在 **PL_Refresh_DWH** 頁面，點擊左側導航菜單上的 **StockDW**。

![A screenshot of a computer Description automatically
generated](./media/image81.png)

2.  點擊**刷新**圖示，如下圖所示。

![A screenshot of a computer Description automatically
generated](./media/image82.png)

![A screenshot of a computer Description automatically
generated](./media/image83.png)

3.  在 StockDW 頁面，選擇 "***報告*** "選項卡，然後選擇
    "***新建語義模型***"。

> ![A screenshot of a computer Description automatically
> generated](./media/image84.png)

4.  在 "新建語義模型 "選項卡中，輸入名稱
    ***StocksModel*，**並只選擇事實表和維度表**，**因為我們關注的是
    fact***\_Stocks_Daily_Prices*、*dim_Date* 和**
    dim***\_Symbol***。按一下 "**確認** "按鈕。

![A screenshot of a computer Description automatically
generated](./media/image85.png)

## 任務 2.添加關係

1.  在 **StockDW** 頁面上，點擊左側導航功能表上的
    **RealTimeWorkspace**，然後選擇 **StockModel**。

![A screenshot of a computer Description automatically
generated](./media/image86.png)

2.  創建上述語義模型後，模型設計器應該會自動打開。如果沒有打開，或者您想稍後再返回設計器，可以從工作區的資源清單中打開模型，然後從語義模型項中選擇打開資料模型。

![A screenshot of a computer Description automatically
generated](./media/image87.png)

![A screenshot of a computer Description automatically
generated](./media/image88.png)

3.  要在事實表和維度表之間創建關係，可將事實表中的鍵拖到維度表中的相應鍵上。

4.  對於此 Data
    Model，您需要定義不同表之間的關係，以便根據來自不同表的資料創建報表和
    Visualization。從 **fact_Stocks_Daily_Prices** 表中，拖動
    **PriceDateKey** 欄位並將其放置在 **dim_Date** 表中的 **DateKey**
    欄位上，以創建關係。此時會出現**新建關係**對話方塊。

> ![](./media/image89.png)

5.  在**新建關係**對話方塊中：

- **From** 表中填充了 **fact_Stocks_Daily_Prices** 和 **PriceDateKey**
  列**。**

- 表中已填充 **dim_Date** 和 DateKey 列

- 卡性：**多對一 (\*:1)**

- 交叉過濾方向：**單**

- 選中 "**啟動此關係 "**旁邊的方框。

- 選擇 **"確定"。**

![A screenshot of a computer Description automatically
generated](./media/image90.png)

![A screenshot of a computer Description automatically
generated](./media/image91.png)

6.  從 **fact_Stocks_Daily_Prices** 表中拖動 **Symbol_SK**
    欄位，並將其拖放到 **dim_Symbol** 表中的 **Symbol_SK**
    欄位上，以創建關係。此時將出現**新建關係**對話方塊。

![A screenshot of a computer Description automatically
generated](./media/image92.png)

7.  在**新建關係**對話方塊中：

- 從表中填入 **fact_Stocks_Daily_Prices** 和 **Symbol_Sk** 列**。**

- 表中有 **dim_Symabol** 和 Symbol_Sk 列

- 卡性：**多對一 (\*:1)**

- 交叉過濾方向：**單**

- 選中 "**啟動此關係 "**旁邊的方框。

- 選擇 **"確定"。**

![A screenshot of a computer Description automatically
generated](./media/image93.png)

![A screenshot of a computer Description automatically
generated](./media/image94.png)

## 任務 3.創建一份簡單的報告

1.  點擊 "***新建報告***"，在 Power BI 中載入語義模型。

> ![A screenshot of a computer Description automatically
> generated](./media/image95.png)

2.  雖然我們還不會有太多的資料，無法做出太多的報告，但從概念上講，我們可以建立一個類似下圖的報告，顯示實驗室運行一周左右後的報告（Data
    Lakehouse
    模組會導入更多的歷史資料，以便做出更有趣的報告）。上圖顯示了每檔股票每天的收盤價，下圖顯示了世衛組織股票的最高價/最低價/收盤價。

3.  在 **Power BI** 頁面的 "**視覺化** "下，按一下 "**折線圖**
    "圖示，為報告添加**柱狀圖**。

- 在**資料**窗格中，展開 **fact_Stocks_Daily_Prices**，然後選中
  **PriceDateKey** 旁邊的核取方塊。這將創建一個柱狀圖，並將欄位添加到
  **X 軸**。

- 在**資料**窗格中，展開 **fact_Stocks_Daily_Prices**，然後選中
  **ClosePrice** 旁邊的核取方塊。這將把該欄位添加到 **Y 軸。**

- 在**資料**窗格中，展開
  **dim_Symbol**，然後選中**符號**旁邊的核取方塊。這將把欄位添加到**圖例**中。

![A screenshot of a computer Description automatically
generated](./media/image96.png)

![A screenshot of a computer Description automatically
generated](./media/image97.png)

4.  從功能區選擇**檔** \> **保存。**

![A screenshot of a computer Description automatically
generated](./media/image98.png)

5.  在 "保存報告 "對話方塊中，輸入 +++ **語義報告** +++
    作為報告名稱，並選擇**工作區**。按一下**保存按鈕**。

![](./media/image99.png)

![A screenshot of a computer Description automatically
generated](./media/image100.png)

## **摘要**

在本實驗室中，您在 Fabric 工作區中配置了 Synapse
資料倉庫，並為資料處理建立了強大的資料管道。本實驗室首先創建 Synapse
資料倉庫，然後創建資料轉換所需的暫存和 ETL
物件。您已經創建了模式、表、存儲過程和管道，以便有效管理資料流程。

然後，您將深入研究建立維度表和事實表，這對於有效組織資料以進行分析至關重要。您已經創建了用於存儲每日股票價格、符號詳細資訊和日期資訊的表格。此外，還開發了程式，用於用相關資料載入維度表，並用每日股票價格填充事實表。

您在 Synapse Data Warehouse
中創建了一個語義模型，重點關注基本事實表和維度表。建立名為 "StocksModel
"的語義模型後，您在 fact_Stocks_Daily_Prices 表、dim_Date 表和
dim_Symbol
表之間建立了關係，從而實現了連貫的資料分析。總之，通過本實驗室可以全面瞭解如何設置資料倉庫環境和構建用於分析的可靠資料
Pipeline。
