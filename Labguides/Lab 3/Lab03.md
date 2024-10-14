# 實驗室 03：構建數據 Lakehouse

**導言**

本實驗的大部分內容將在 Jupyter Notebooks
中完成，這是一種進行探索性資料分析、構建模型、Data Visualization
和處理資料的行業標準方式。筆記本本身被分隔成稱為儲存格的單獨部分，其中包含代碼或文檔。單元，甚至單元中的部分，都可以根據需要適應不同的語言（儘管
Python
是使用最多的語言）。單元的目的是將任務分解成易於管理的小塊，使協作更容易；單元可以單獨運行，也可以作為一個整體運行，這取決於筆記本的目的。

在 Lakehouse
的獎章架構中（有銅、銀、金三層），資料在原始/銅層攝取，通常是從源頭
"按原樣
"攝取。資料通過提取、載入和轉換（ELT）流程進行處理，在該流程中，資料被逐步處理，直至到達黃金層進行報告。典型的架構可能類似於

 ![](./media/image1.png)

這些圖層並非硬性規定，而是指導原則。層通常被分隔到不同的 Lakehouse
中，但在我們的實驗室中，我們將使用同一個 Lakehouse
來存儲所有層。[點擊此處](https://learn.microsoft.com/en-us/fabric/onelake/onelake-medallion-lakehouse-architecture)瞭解[在
Fabric
中](https://learn.microsoft.com/en-us/fabric/onelake/onelake-medallion-lakehouse-architecture)實施[獎章架構的](https://learn.microsoft.com/en-us/fabric/onelake/onelake-medallion-lakehouse-architecture)更多資訊。

**目標**

- 在 Synapse Data Engineering Home 的 Data Engineering 角色中創建名為
  "StocksLakehouse "的 Lakehouse，確認 SQL Engine 端點創建成功。

- 要將 Lakehouse 添加到
  StockEventStream，需要對其進行配置、執行資料清理，並確保 Lakehouse
  接收到所需的資料欄位。

- 將筆記本 "Lakehouse 1-4 "導入 RealTimeWorkspace，然後單獨執行。

- 利用事件處理器和資料處理技術，匯總和清理存儲在 Data Lakehouse
  中的原始資料。這包括執行過濾、資料類型轉換和欄位管理等功能，為下游分析準備資料。

- 建立適合構建維度模型和支援資料科學活動的策劃和匯總資料表。這包括創建匯總常式，以匯總不同細微性的資料，如每分鐘和每小時的匯總。

- 在 Lakehouse
  環境中建立維度模型，納入事實表和維度表。定義這些表之間的關係，以促進高效查詢和報告。

- 根據維度模型創建語義模型，並使用 Power BI
  等工具構建互動式報告，以便對匯總資料進行 Visualization 和分析。

# 練習 1：設置 Lakehouse

## 任務 1：創建 Lakehouse

從創建 Lakehouse 開始。

***注意**：如果你是在資料科學模組或其他使用 Lakehouse
的模組之後完成本實驗，你可以重新使用該 Lakehouse
或創建一個新的，但我們假設所有模組共用同一個 Lakehouse。*

1.  在 Fabric 工作區中，切換到 **Data Engineering**
    角色（左下角），如下圖所示。

      ![](./media/image2.png)

2.  在 Synapse Data Engineering 主頁，導航並點擊 ***Lakehouse*** 磁貼。

      ![](./media/image3.png)

3.  在**新建 Lakehouse** 對話方塊中，在**名稱**欄位中輸入 +++
    **StocksLakehouse+++**，然後點擊**創建**按鈕。將出現**股票湖舍**頁面。

      ![](./media/image4.png)

      ![](./media/image5.png)

4.  您將看到一條通知，說明 "**成功創建 SQL 端點**"。**注意**：如果沒有看到通知，請等待幾分鐘。

     ![](./media/image6.png)

## 任務 2.將 Lakehouse 加入 Eventstreams

從架構的角度來看，我們將通過拆分 Eventstreams 的熱路徑和冷路徑資料來實現
Lambda 架構。熱路徑將繼續按照已經配置的方式進入 KQL
資料庫，而冷路徑將被添加以將原始資料寫入我們的
Datahouse。我們的資料流程將如下所示：

***注意**：如果你是在資料科學模組或其他使用 Lakehouse
的模組之後完成本實驗，你可以重新使用該 Lakehouse
或創建一個新的，但我們假設所有模組共用同一個 Lakehouse。*

1.  在 Fabric 工作區中，切換到 **Data Engineering**
    角色（左下角），如下圖所示。

      ![](./media/image7.png)

2.  現在，點擊左側功能窗格中的 **RealTimeWorkspace**，然後選擇
    **StockEventStream**，如下圖所示。

     ![](./media/image8.png)

3.  除了將 Datahouse 添加到 Eventstreams 之外，我們還將使用 Eventstreams
    中的一些功能對資料進行一些清理。

4.  在 **StockEventStream** 頁面，選擇**編輯**

      ![](./media/image9.png)

5.  在 **StockEventStream** 頁面上，點擊 Eventstreams
    輸出**端的添加目的地**，添加新的目的地。從上下文功能表中選擇
    *Lakehouse*。

      ![](./media/image10.png)

6.  在右側出現的 Lakehouse 窗格中，輸入以下詳細資訊，然後點擊**保存**

      |    |   |
      |-----|----|
      |Destination name	| +++Lakehouse+++|
      |Workspace |	RealTimeWorkspace|
      ||Lakehouse	|StockLakehouse|
      |Delta table |	Click on Create new> enter +++raw_stock_data+++|
      |Input data format |	Json|

      ![](./media/image11.png)
       ![](./media/image12.png)

6.  連接 **StockEventStream** 和 **Lakehouse**

     ![](./media/image13.png)

     ![](./media/image14.png)

     ![](./media/image15.png)

7.  選擇 Lakehouse，點擊**刷新**按鈕

     ![](./media/image16.png)

8.  按一下 "*打開事件處理器
    "*後，可以添加各種處理，執行聚合、過濾和更改資料類型。

     ![](./media/image17.png)

9.  在 **StockEventStream** 頁面上，選擇
    **stockEventStream**，然後按一下**加號 (+) 圖示**添加 **Mange
    欄位**。然後，選擇 **Mange 欄位。**

     ![](./media/image18.png)

     ![](./media/image19.png)

10. 在事件窗格中選擇**管理欄位1** 鉛筆圖示。

     ![](./media/image20.png)

11. 在打開的 "*管理欄位* "窗格中，按一下 "***添加所有欄位***
    "以添加所有列。然後，按一下欄位名右側的**省略號（...）**並按一下*刪除*，從而刪除
    **EventProcessedUtcTime**、**PartitionId** 和 **EventEnqueuedUtcTime
    欄位**。

      ![](./media/image21.png)
      ![](./media/image22.png)
      ![](./media/image23.png)
      ![](./media/image24.png)
      ![](./media/image25.png)
12. 現在將*時間戳記*列更改為*日期時間*，因為它很可能被歸類為字串。按一下*時間戳記***列**右側的**三個省略號（...）**，然後選擇*是
    更改類型*。這將允許我們更改資料類型：選擇
    **DateTime**，如下圖所示。點擊完成

      ![](./media/image26.png)
      ![](./media/image27.png)

13. 現在，點擊 "**發佈 "**按鈕關閉事件處理器

      ![](./media/image28.png)
      ![](./media/image29.png)

14. 一旦完成，Lakehouse 將收到符號、價格和時間戳記。

      ![](./media/image30.png)

我們的 KQL（熱路徑）和 Lakehouse（冷路徑）現已配置完畢。資料在 Lakehouse
中顯示可能需要一兩分鐘。

## 任務 3.導入筆記本

**注**：如果在導入這些筆記本時遇到問題，請確保您下載的是原始筆記本檔，而不是
GitHub 上顯示筆記本的 HTML 頁面。

1.  現在，點擊左側導航功能表上的 **RealTimeWorkspace**。

     ![](./media/image31.png)

2.  在 **Synapse Data Engineering RealTimeWorkspace**
    頁面，導航並按一下**導入**按鈕，然後選擇**筆記本**並選擇**從這台電腦**，如下圖所示。

     ![](./media/image32.png)

3.  從螢幕右側出現的**導入狀態**窗格中選擇**上傳**。

     ![](./media/image33.png)

4.  導航並從 **C:\LabFiles\Lab 04** 中選擇 **Lakehouse 1-Import Data（導入資料）**、**Lakehouse 2-Build Aggregation（建立聚合）**、**Lakehouse 3-Create Star 
   Schema** （創建星型模式） 和 Lakehouse **4-Load Star Schema notebook**（載入星型模式筆記本），然後按一下 Open（打開）按鈕。

      ![](./media/image34.png)

5.  您將看到一條通知，說明**已成功導入**

      ![](./media/image35.png)

## 任務 4.導入其他資料

為了讓報告更有趣，我們需要更多的資料。對於 Lakehouse 和 Data Science
模組，可以導入額外的歷史資料來補充已攝取的資料。筆記本的工作原理是查看表中最舊的資料，並預置歷史資料。

1.  在 **RealTimeWorkspace**  頁面，要只查看筆記本，請按一下頁面右上角的**篩選器**，然後選擇**筆記本。**

     ![](./media/image36.png)

2.  然後，選擇 ***Lakehouse 1 - Import Data*** 筆記本。

      ![](./media/image37.png)

3. 在資源管理器下，導航並選擇 Lakehouse，然後點擊添加按鈕，如下圖所示。
   重要提示：您需要為每本導入的筆記本添加 Lakehouse -- 每次首次打開筆記本時都要這樣做。
     ![](./media/image38.png)

     ![](./media/image39.png)

4.  在 **"添加 Lakehouse** "對話方塊中，選擇 "**現有 Lakehouse**
    "選項按鈕，然後按一下 "**添加** "按鈕。

     ![](./media/image40.png)

5.  在 **OneLake 資料中心**視窗，選擇 **StockLakehouse**
    並點擊**添加**按鈕。
     ![](./media/image41.png)

6.  **raw_stock_data** 表是在配置 Eventstreams 時創建的，是從 Event Hub
    接收的資料的落腳點。

     ![](./media/image42.png)

**注意**：將滑鼠懸停在筆記本中的儲存格上時，會看到**運行**按鈕。

7.  要啟動筆記本並執行儲存格，請選擇儲存格左側的**運行圖示**。

     ![](./media/new14.png)

8.  同樣，運行 2^(nd) 和 3^(rd) 單元。

      ![](./media/new15.png)
      ![](./media/new16.png)

9.  要將歷史資料下載並解壓到 Lakehouse 非託管檔，請運行 4^(th) 和 5
    ^(thd) 單元，如下圖所示。

     ![](./media/new17.png)

     ![](./media/new18.png)

10. 要驗證 csv 檔是否可用，請選擇並運行 6^(th) 儲存格。

      ![](./media/new19.png)

11. 運行 7^(th) 單元、8^(th) 單元和 9^(th) 單元。

      ![](./media/new20.png)

      ![](./media/new21.png)

12. 與 "注釋掉
    "代碼部分類似，凍結儲存格的強大之處在於，儲存格的任何輸出也會被保留。

     ![](./media/new22.png)

# 練習 2：建立匯總表

在本練習中，您將構建適合用於構建我們的維度模型和資料科學的經過策劃和聚合的資料。由於原始資料的頻率為每秒一次，因此這種資料大小通常不適合用於報告或分析。此外，這些資料沒有經過清理，因此我們面臨著不符合要求的資料在報告或資料管道中造成問題的風險，而錯誤資料並不是我們所期望的。這些新表將以每分鐘和每小時為單位存儲資料。幸運的是，*Data
Wrangler* 可以輕鬆完成這項任務。

這裡使用的筆記本將建立兩個聚合表，它們都是銀級工件。雖然將獎章層分隔到不同的
Lakehouse
中是很常見的做法，但考慮到我們的資料規模較小，而且出於實驗室的目的，我們將使用同一個
Lakehouse 來存儲所有層。

## 任務 1：建立匯總表筆記本

花點時間流覽一下筆記本。如果尚未添加默認的 Lakehouse，請務必添加。

1.  現在，點擊左側導航功能表上的 **RealTimeWorkspace**。

    ![](./media/image31.png)

2.  在 **RealTimeWorkspace** 頁面，按一下 **Lakehouse 2 - Build
    Aggregation Tables** 筆記本。

     ![](./media/image55.png)

3.  在資源管理器下，導航並選擇 **Lakehouse**，然後點擊**添加**按鈕。

      ![](./media/image56.png)

      ![](./media/image57.png)

4.  在 "**添加湖舍** "對話方塊中，選擇 "**現有湖舍**
    "對話方塊，然後按一下 "**添加** "按鈕。

     ![](./media/image40.png)

5.  在 **OneLake 資料中心**視窗，選擇
    **StockLakehouse**，然後點擊**添加**按鈕。

     ![](./media/image41.png)

6.  要建立匯總表，請選擇並運行 1^(st) , 2^(nd) , 3^(rd) , 和 4^(th)
    儲存格。

      ![](./media/image58.png)
      ![](./media/image59.png)
      ![](./media/image60.png)
      ![](./media/image61.png)

7.  然後，選擇並運行 5^(th) 、6^(th) 、7^(th) 和 8^(th) 儲存格。

     ![](./media/image62.png)

     ![](./media/image62.png)

     ![](./media/image63.png)

     ![](./media/image64.png)

8.  添加資料 Wrangler，選擇 **9^(th)** 儲存格，導航下拉式功能表 **Data
    Wrangler**。導航並按一下 **anomaly_df**，在 Data Wrangler
    中載入數據幀**。**

9.  我們將使用
    **anomaly_df**，因為它是故意創建的，其中有一些無效行可以進行測試。

     ![](./media/image65.png)

10. 在 Data Wrangler
    中，我們將記錄一些處理資料的步驟。請注意，資料在中央欄中是
    Visualization 的。左上方是操作，左下方是每個步驟的概覽。

     ![](./media/image66.png)

11. 要刪除空值/空值，請在*操作*下點擊**查找和替換**旁邊的下拉式功能表，然後導航並點擊**刪除缺失值**。

     ![](./media/image67.png)

12. 從**目標列**下拉式功能表中，選擇***符號***列和***價格***列，然後點擊下方的**應用按鈕**，如圖所示。

      ![](./media/image68.png)

     ![](./media/image69.png)

     ![](./media/image70.png)

13. 在 **"操作** "下拉式功能表中，導航並按一下
    "**排序和篩選**"，然後按一下 "**篩選**"，如下圖所示。

     ![](./media/image71.png)

14. **取消選中***保留匹配行*，選擇**價格**作為目標列，並將條件設置為***等於
    0*。 在**篩選器下方的*操作*面板中按一下***應用*。**

    注意：0 的行標記為紅色，因為它們將被刪除（如果其他行標記為紅色，請確保取消選中保持匹配行核取方塊）。 
     ![](./media/image72.png)

     ![](./media/image73.png)

15. 點擊頁面左上方的 **+Add code to notebook（向筆記本添加代碼**）。在
    "***向筆記本添加代碼*** "視窗中，確保 "*包含 pandas 代碼*
    "未選中，然後點擊 "**添加** "按鈕。

      ![](./media/image74.png)

      ![](./media/image75.png)

16. 插入的代碼將與下面的代碼相似。

    ![](./media/image76.png)

17. 運行儲存格並觀察輸出結果。你會發現無效行已被刪除。

     ![](./media/image77.png)

     ![](./media/image78.png)

    創建的函數 **clean_data** 依次包含所有步驟，可根據需要進行修改。請注意，在
    Data Wrangler 中執行的每個步驟都有注釋。由於 Data Wrangler 已載入了
    **anomaly_df**，因此所編寫的方法以該資料幀為名，但也可以是與模式匹配的任何資料幀。

18. 將函數名稱從 **clean_data** 改為 **remove_invalid_rows**，並將
    **anomaly_df_clean = clean_data(anomaly_df)** 改為 **df_stocks_clean =
    remove_invalid_rows(df_stocks)**。此外，雖然功能上沒有必要，但您可以將函數中使用的資料幀名稱改為簡單的
    **df**，如下所示

19. 運行該單元並觀察輸出結果。
      ```
      # Code generated by Data Wrangler for PySpark DataFrame
      
      def remove_invalid_rows(df):
          # Drop rows with missing data in columns: 'symbol', 'price'
          df = df.dropna(subset=['symbol', 'price'])
          # Filter rows based on column: 'price'
          df = df.filter(~(df['price'] == 0))
          return df
      
      df_stocks_clean = remove_invalid_rows(df_stocks)
      display(df_stocks_clean)
      ```

     ![](./media/image79.png)

20. 現在，該函數將刪除 **df_stocks** 資料幀中的無效行，並返回一個名為
    **df_stocks_clean** 的新資料幀。我們通常會為輸出資料幀使用不同的名稱（如
    **df_stocks_clean**），以使儲存格具有惰性--這樣，我們就可以返回並重新運行儲存格，進行修改等，而無需重新載入原始資料。

     ![](./media/image80.png)

## 任務 2：建立匯總常式

在這個任務中，你的參與度會更高，因為我們將在 Data Wrangler
中建立一些步驟，添加派生列並聚合資料。如果遇到困難，請盡力繼續，並使用筆記本中的示例代碼幫助解決之後的問題。

1.  在 "***符號/日期/小時/分鐘聚合***
    "*部分添加*新列***日期***戳，將游標放在 "*在此處添加 Data Wrangler*
    "儲存格中並選擇該儲存格。下拉**數據
    Wrangler**。如下圖所示，導航並按一下 **df_stocks_clean**。

     ![](./media/image81.png)
 
      ![](./media/image82.png)

2.  在 **Data Wrangler:df_stocks_clean**
    窗格中，選擇**操作**，然後**按示例**選擇**新建列。**

      ![](./media/image83.png)

3.  在**目標列欄**位下，點擊下拉式功能表並選擇**時間戳記**。然後，在**派生列名稱**欄位中輸入
    ***+++datestamp++++***

     ![](./media/image84.png)

4.  在新的***日期戳***一欄中，輸入任何給定行的示例值。例如，如果*時間戳記*是
    *2024-02-07 09:54:00*，則輸入 ***2024-**02**-07***。這樣，Data
    Wrangler
    就能推斷出我們要查找的是沒有時間成分的日期；列自動填充後，按一下
    "***應用***"按鈕。

     ![](./media/image85.png)

      ![](./media/image86.png)

5.  與上述步驟中添加**日期戳**列類似，再次點擊**新建列**，如下圖所示。

      ![](./media/image87.png)

6.  在*目標列下*，選擇**時間戳記**。輸入 **+++hour+++**
    的**派生列**名稱。

     ![](./media/image88.png)

7.  在資料預覽中出現的新**小時**列中，為任意給定行輸入一個小時，但儘量選擇具有唯一小時值的行。例如，如果*時間戳記*是
    *2024-02-07 09:54:00*，則輸入
    ***9***。您可能需要為幾行輸入示例值，如圖所示。按一下 "**應用**" 按鈕。

     ![](./media/image89.png)

8.  Data Wrangler 應該會推斷出我們正在尋找小時組件，並構建類似的代碼：

      # Derive column 'hour' from column: 'timestamp'
      def hour(timestamp):
          """
          Transform based on the following examples:
             timestamp           Output
          1: 2024-02-07T09:54 => "9"
          """
          number1 = timestamp.hour
          return f"{number1:01.0f}"
      
      pandas_df_stocks_clean.insert(3, "hour", pandas_df_stocks_clean.apply(lambda row : hour(row["timestamp"]), axis=1))

      ![](./media/image90.png)

 9.  與小時欄相同，創建一個新的***分鐘***欄。在新的*分鐘*列中，為任何給定行輸入分鐘。例如，如果*時間戳記*是
    **2024-02-07 09:54:00**，則輸入 **54**。您可能需要為多行輸入示例值。

     ![](./media/image91.png)

10. 生成的代碼應類似於
      ```
      # Derive column 'minute' from column: 'timestamp'
      def minute(timestamp):
          """
          Transform based on the following examples:
             timestamp           Output
          1: 2024-02-07T09:57 => "57"
          """
          number1 = timestamp.minute
          return f"{number1:01.0f}"
      
      pandas_df_stocks_clean.insert(3, "minute", pandas_df_stocks_clean.apply(lambda row : minute(row["timestamp"]), axis=1))
      ```

     ![](./media/image92.png)

11. 接下來，將小時列轉換為整數列。點擊小時列邊角的省略號++（...）+++，然後選擇 "更改列類型"。按一下新類型旁邊的下拉式功能表，導航並選擇 **int32**，然後按一下應用按鈕，如下圖所示。 
     ![](./media/image93.png)

     ![](./media/image94.png)

13. 使用與小時相同的步驟將分鐘列轉換為整數。按一下***分鐘*列**邊角的**省略號（...）**並選擇
    "***更改列**類型*"。按一下***新類型***旁邊的下拉式功能表，導航並選擇
    **int32**，*然後按一下***應用*按鈕***，如下圖所示。

    ![](./media/image95.png)

    ![](./media/image96.png)

     ![](./media/image97.png)

13. 現在，在 "操作 "部分下，導航並按一下 "分組 "和 "匯總"，如下圖所示。 

    ![](./media/image98.png)

14. 按一下 "**列** "下的下拉式功能表**按欄位分組**，然後選擇**符號**、**日期戳**、**小時**、**分鐘**。

     ![](./media/image99.png)

15. 按一下 +Add aggregation**（添加*聚合*），共創建三個聚合，如下圖所示，然後按一下
    **Apply（應用**）按鈕。

      - 價格最高
      
      - 價格最低
      
      - 價格最後值

     ![](./media/image100.png)

     ![](./media/image101.png)

16. 按一下頁面左上角的 "**添加代碼到筆記本**"。在
    "***向筆記本添加代碼*** "**視窗**中，確保未選中 "**包含 pandas
    代碼**"，然後點擊 "**添加** "按鈕。

     ![](./media/image102.png)

     ![](./media/image103.png)

     ![](./media/image104.png)

17. 查看代碼，在添加的儲存格中，在儲存格的最後兩行，注意返回的資料幀名為
    ***df_stocks_clean_1***。將其重命名為
    ***df_stocks_agg_minute***，並將函數名稱改為
    ***aggregate_data_minute***，如下所示。

      # old：
      def clean_data(df_stocks_clean)：
        ...
      
      df_stocks_clean_1 = clean_data(df_stocks_clean)
      display(df_stocks_clean_1)
      
      # new：
      def aggregate_data_minute(df_stocks_clean)：
        ...
      
      df_stocks_agg_minute = aggregate_data_minute(df_stocks_clean)
      display(df_stocks_agg_minute)


     ![](./media/image105.png)

18. Data Wrangler 為 PySpark DataFrame
    單元生成的代碼，選擇儲存格左側懸停時出現的**運行圖示**。

     ![](./media/image106.png)

     ![](./media/image107.png)

     ![](./media/image108.png)

**注**：如果遇到困難，請參考注釋代碼。如果任何資料處理步驟似乎不太正確（例如沒有得到正確的小時或分鐘），請參考注釋示例。下面的步驟
7 有一些額外的注意事項，可能會有所幫助。

**注：**如果想注釋掉（或取消注釋）較大的代碼塊，可以高亮顯示代碼部分（或
CTRL-A 選擇目前的儲存格中的所有內容），然後使用
CTRL-/（控制*斜線*）切換注釋。

19. 在合併儲存格中，選擇儲存格左側懸停時出現的**運行圖示**。合併功能會將資料寫入表格：

      > \# 將資料寫入 stocks_minute_agg 表
      >
      > merge_minute_agg(df_stocks_agg_minute)

    ![](./media/image109.png)

## 任務 3：每小時匯總

讓我們回顧一下目前的進展情況--我們的每秒資料已經過清理，然後匯總到每分鐘級別。這樣，每個股票代碼的行數就從每天
86,400 行減少到每天 1,440
行。對於可能顯示月度資料的報告，我們可以進一步將資料匯總到每小時頻率，將資料減少到每個股票代碼每天
24 行。

1.  在*符號/日期/小時*部分下的最後一個預留位置中，將現有的
    **df_stocks_agg_minute**資料幀載入到 Data Wrangler 中。

2.  在 **"符號/日期/小時"**部分下的最後一個預留位置中，將游標放在
    "*在此處添加數據 Wrangler*
    "儲存格中並選中該儲存格。如下圖所示**，**下拉** Data
    Wrangler，**導航並點擊** **df_stocks_agg_minute**。

     ![](./media/image110.png)

     ![](./media/image111.png)

3. 在操作下，選擇分組和匯總。按一下列下面的下拉式功能表，按欄位分組並選擇符號、日期戳和小時，然後按一下 +添加聚合。創建以下三個聚合，然後按一下下面的應用按鈕，如下圖所示。

    - price_min: 最小值
    
    - price_max：最高
    
    - price_last：最後值

      ![](./media/image112.png)

      ![](./media/image113.png)

4.  示例代碼如下所示。除了將函數重命名為
    *aggregate_data_hour*，還修改了每個價格列的別名，以保持列名不變。因為我們聚合的是已經聚合過的資料，所以
    Data Wrangler 將列命名為
    price_max_max、price_min_min；為了清晰起見，我們將修改別名以保持列名不變。

     ![](./media/image114.png)

5.  按一下頁面左上角的 "**添加代碼到筆記本**"。在
    "***向筆記本添加代碼*** "**視窗**中，確保未選中 "**包含 pandas
    代碼**"，然後按一下 "**添加** "按鈕。

     ![](./media/image115.png)

     ![](./media/image116.png)

     ![](./media/image117.png)

6.  在添加的儲存格中，注意儲存格的最後兩行，返回的資料幀名為 def
    clean_data(df_stocks_agg_minute)：，將其重命名為

> **def aggregate_data_hour(df_stocks_agg_minute)：**

7. 在添加的儲存格中，注意儲存格的最後兩行，返回的資料幀名為 df_stocks_agg_minute_clean = clean_data(df_stocks_agg_minute)。將其重命名為 df_stocks_agg_hour = 
   aggregate_data_hour(df_stocks_agg_minute)，並將函數 display(df_stocks_agg_minute_clean) 的名稱改為 aggregate_data_minute，如下所示。 

      參考編碼：
      ```
      # Code generated by Data Wrangler for PySpark DataFrame
      
      from pyspark.sql import functions as F
      
      def aggregate_data_hour(df_stocks_agg_minute):
          # Performed 3 aggregations grouped on columns: 'symbol', 'datestamp', 'hour'
          df_stocks_agg_minute = df_stocks_agg_minute.groupBy('symbol', 'datestamp', 'hour').agg(
              F.max('price_max').alias('price_max'), 
              F.min('price_min').alias('price_min'), 
              F.last('price_last').alias('price_last'))
          df_stocks_agg_minute = df_stocks_agg_minute.dropna()
          df_stocks_agg_minute = df_stocks_agg_minute.sort(df_stocks_agg_minute['symbol'].asc(), df_stocks_agg_minute['datestamp'].asc(), 
          df_stocks_agg_minute['hour'].asc())
          return df_stocks_agg_minute
      
      df_stocks_agg_hour = aggregate_data_hour(df_stocks_agg_minute)
      display(df_stocks_agg_hour)
      ```
    ![](./media/image118.png)

2.  選擇並**運行**儲存格。

     ![](./media/image119.png)

3.  合併小時匯總資料的代碼位於下一個儲存格：**merge_hour_agg(df_stocks_agg_hour)**

4.  運行儲存格完成合併。底部有幾個實用儲存格，用於檢查表格中的資料 --
    可以自由探索資料並進行試驗。

     ![](./media/image120.png)

21. 使用**測試**部分**的便捷 SQL
    命令進行**測試、清理表格以重新運行等。選擇並**運行**本節中的儲存格。

     ![](./media/image121.png)

    ![](./media/image122.png)

     ![](./media/image123.png)

# 練習 3：建立維度模型

在本練習中，我們將進一步完善聚合表，並使用事實表和維度表創建傳統的星形模式。如果你已經完成了
Data Warehouse 模組，本模組也會產生類似的結果，但通過在 Lakehouse
中使用筆記本，方法有所不同。

**注**：可以使用管道來 Orchestration
活動，但本解決方案將完全在筆記本中完成。

## 任務 1：創建模式

此一次性運行筆記本將為建立事實表和維度表設置模式。配置第一個儲存格中的
sourceTableName
變數（如需要），使其與小時聚合表相匹配。開始/結束日期用於日期維度表。本筆記本將重新創建所有表，重建模式：現有的事實表和維度表將被覆蓋。

1.  按一下左側導航菜單上的 **RealTimeWorkspace**。

     ![](./media/image124.png)

2.  在 RealTimeWorkshop 工作區，選擇 **Lakehouse 3 - Create Star
    Schema** 筆記本。

      ![](./media/image125.png)

3.  在資源管理器下，導航並點擊 **Lakehouse**，然後點擊**添加**按鈕*。*

     ![](./media/image126.png)

     ![](./media/image127.png)

4.  在 **"添加湖舍** "對話方塊中，選擇 "**現有湖舍**
    "選項按鈕，然後按一下 "**添加** "按鈕。

     ![](./media/image40.png)

5.  在 OneLake 資料中心視窗，選擇 **StockLakehouse**
    並點擊**添加**按鈕。

      ![](./media/image41.png)

6.  載入筆記本並連接 Lakehouse 後，請注意左側的模式。除了
    **raw_stock_data** 表，還應該有 **stocks_minute_agg** 表和
    **stocks_hour_agg** 表。

      ![](./media/image128.png)

7.  按一下每個儲存格左側的播放按鈕，逐個運行每個儲存格，跟進整個過程。

      ![](./media/image129.png)
      
      ![](./media/image130.png)
      
      ![](./media/image131.png)
      
      ![](./media/image132.png)
      
      ![](./media/image133.png)
      
      ![](./media/image134.png)

      ![](./media/image135.png)

      ![](./media/image136.png)

      ![](./media/image137.png)

8.  當所有儲存格都成功運行後，導航到 **StocksLakehouse**
    部分，點擊**表格**旁邊的水準省略號 **（...）**，然後導航並點擊***刷新***，如下圖所示。

      ![](./media/image138.png)

9.  現在，你可以看到維度模型的所有附加表 ***dim_symbol**、**dim_date** 和
    **fact_stocks_daily_prices**。

     ![](./media/image139.png)

## 任務 2：載入事實表

我們的事實表包含每日股票價格（最高價、最低價和收盤價），而我們的維度是日期和股票代碼（可能包含公司詳情和其他資訊）。這個模型雖然簡單，但從概念上講，它代表了一種可應用於更大資料集的星形模式。

1.  現在，點擊左側導航功能表上的 **RealTimeWorkspace**。

     ![](./media/image140.png)

2.  在 RealTimeWorkshop 工作區，選擇 ***Lakehouse 4 -
    載入事實表***筆記本。

     ![](./media/image141.png)

3.  在資源管理器下，選擇 **Lakehouse**，然後點擊**添加**按鈕。

     ![](./media/image142.png)

      ![](./media/image143.png)

4.  在 **"添加湖舍** "對話方塊中，選擇 "**現有湖舍**
    "選項按鈕，然後按一下 "**添加** "按鈕。

     ![](./media/image40.png)

5.  在 OneLake 資料中心選項卡上，選擇 **StockLakehouse**
    並點擊**添加**按鈕。

     ![](./media/image41.png)

6.  選擇並單獨運行每個儲存格。

     ![](./media/image144.png)

7.  函數在 dim_symbol 中添加表格中可能不存在的符號，選擇並**運行**
    2^(nd) 和 3^(rd) 儲存格。

     ![](./media/image145.png)

     ![](./media/image146.png)

8.  從浮水印開始，選擇並運行 4^(th) 單元，以獲取新的庫存資料。

      ![](./media/image147.png)

9.  為以後的連接載入日期維度，選擇並**運行** 5^(th) 、6^(th) 和 7^(th)
    儲存格。

     ![](./media/image148.png)

     ![](./media/image149.png)

     ![](./media/image150.png)

      ![](./media/image151.png)

10. 要將匯總資料連接到日期維度，請選擇並**運行** 8^(th) 和 9^(th)
    儲存格。

    ![](./media/image152.png)
    
    ![](./media/image153.png)

11. 創建一個最終視圖，並清理名稱以方便處理，選擇並**運行** 10^(th)
    、11^(th) 和 12^(th) 儲存格。

      ![](./media/image154.png)
      
      ![](./media/image155.png)
      
      ![](./media/image156.png)

12. 要獲得結果並繪製圖表，請選擇並**運行** 13^(th) 和 14^(th) 儲存格。

      ![](./media/image157.png)
      
      ![](./media/image158.png)
      
      ![](./media/image159.png)

13. 要驗證創建的表格，請按右鍵**表格**旁邊的水準省略號（...），**然後導航並按一下**刷新。表格就會出現。

      ![](./media/image160.png)

14. 要安排筆記本定期運行，請按一下 "***運行*** "選項卡，然後按一下
    "計***畫***"，如下圖所示*。*

      ![](./media/image161.png)

15. 在 Lackhouse 4-Load Star Schema 選項卡中，選擇以下詳細資訊並按一下
    "**應用** "按鈕。

    - 計畫運行：**開啟**
    
    - 重複**：每小時**
    
    - 每**4 小時**
    
    - 選擇今天的日期

     ![](./media/image162.png)

## 任務 3：建立語義模型和簡單報告

在本任務中，我們將創建一個可用於報告的新語義模型，並創建一個簡單的 Power
BI 報告。

1.  現在，點擊左側導航功能表上的 **StocksLakehouse**。

     ![](./media/image163.png)

2.  在 ***StocksLakehouse***
    視窗中*，*導航並點擊命令列中的***新建語義模型***。

     ![](./media/image164.png)

3.  將模型命名為 ***StocksDimensionalModel***，並選擇
    **fact_stocks_daily_prices**、**dim_date** 和 **dim_symbol**
    表。然後，按一下 "**確認** "按鈕。

     ![](./media/image165.png)

      ![](./media/image166.png)

4.  語義模型打開後，我們需要定義事實表和維度表之間的關係。

5.  從 **fact_Stocks_Daily_Prices** 表中，拖動 ***Symbol_SK***
    欄位並將其拖放到 **dim_Symbol** 表中的 ***Symbol_SK***
    欄位上，以創建關係。此時將出現**新建關係**對話方塊。

      ![](./media/image167.png)

6.  在**新建關係**對話方塊中：

      - 從表中填入 **fact_Stocks_Daily_Prices** 和 **Symbol_SK** 列**。**
      
      - 表中填充了 **dim_symbol** 和 **Symbol_SK** 列
      
      - 卡性：**多對一 (\*:1)**
      
      - 交叉過濾方向：**單**
      
      - 選中 "**啟動此關係**" 旁邊的方框。
      
      - 選擇**保存**

     ![](./media/image168.png)

       ![](./media/image169.png)

7.  從 **fact_Stocks_Daily_Prices** 表中，拖動 **PrinceDateKey**
    欄位並將其放到 **dim_date** 表中的 ***DateKey***
    欄位上，以創建關係。此時會出現**新建關係**對話方塊。

     ![](./media/image170.png)

8.  在**新建關係**對話方塊中：

    - **From** 表中填充了 **fact_Stocks_Daily_Prices** 和 **PrinceDateKey**
      列。
    
    - 表中的資料由 **dim_date** 和 **DateKey** 列填充
    
    - 卡性：**多對一 (\*:1)**
    
    - 交叉過濾方向：**單**
    
    - 選中 "**啟動此關係 "**旁邊的方框。
    
    - 選擇**保存。**

     ![](./media/image171.png)

     ![](./media/image172.png)

9.  按一下 "***新建報告*** "在 Power BI 中載入語義模型。

     ![](./media/image173.png)

10. 在 **Power BI** 頁面的 "**視覺化** "下，按一下 "**折線圖**
    "圖示，為報告添加**柱狀圖**。

    - 在**資料**窗格中，展開 **fact_Stocks_Daily_Prices**，然後選中
      **PriceDateKey** 旁邊的核取方塊。這將創建一個柱狀圖，並將該欄位添加到
      **X 軸**。
    
    - 在**資料**窗格中，展開 **fact_Stocks_Daily_Prices**，然後選中
      **ClosePrice** 旁邊的核取方塊。這將把該欄位添加到 **Y 軸**。
    
    - 在**資料**窗格中，展開
      **dim_Symbol**，然後選中**符號**旁邊的核取方塊。這將把欄位添加到**圖例**中。

     ![](./media/image174.png)

11. 在**篩檢程式**下**，**選擇 **PriceDateKey**
    並輸入以下詳細資訊。點擊**應用**篩檢程式

    - 篩選類型**相對日期**
    
    - 當值：**在最近 45 天內時**顯示專案

    ![](./media/image175.png)

    ![](./media/image176.png)

12. 從功能區選擇**檔** \> **另存為**

      ![](./media/image177.png)

13. 在 "保存報告 "對話方塊中，輸入 +++StocksDimensional+++
    作為報告名稱，並選擇**工作區**。按一下**保存**按鈕。

      ![](./media/image178.png)

      ![](./media/image179.png)

**摘要**

在本實驗室中，您已經配置了全面的 Lakehouse
基礎架構，並實施了資料處理管道，以有效處理即時和批量資料流程。從創建
Data Lakehouse 環境開始，本實驗室將進一步配置用於處理冷熱資料路徑的
Lambda 架構。

您已經應用了資料聚合和清理技術，為下游分析準備好了存儲在 Lakehouse
中的原始資料。您建立了聚合表來匯總不同細微性的資料，從而提高了查詢和分析的效率。隨後，您在
Lakehouse
中建立了一個維度模型，其中包含事實表和維度表。您定義了這些表之間的關係，以支援複雜的查詢和報表要求。

最後，生成語義模型，提供統一的資料視圖，從而能夠使用 Power BI 等
Visualization 工具創建互動式報告。這種整體方法可在 Data Lakehouse
環境中實現高效的資料管理、分析和報告。
