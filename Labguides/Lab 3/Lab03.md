# Laboratorio 03: Creación de un Data Lakehouse

**Introducción**

La mayor parte de este laboratorio se realizará en Jupyter notebooks,
una forma estándar de realizar análisis exploratorios de datos,
construir modelos, visualizar conjuntos de datos y procesar datos. Un
cuaderno se divide en secciones individuales llamadas celdas, que
contienen código o documentación. Las celdas, e incluso las secciones
dentro de las celdas, pueden adaptarse a diferentes lenguajes según sea
necesario (aunque Python es el lenguaje más utilizado). El propósito de
las celdas es dividir las tareas en trozos manejables y facilitar la
colaboración; las celdas pueden ejecutarse individualmente o en
conjunto, dependiendo del propósito del cuaderno.

En una arquitectura de medallón Lakehouse (con capas bronce, plata y
oro), los datos se ingieren en la capa bruta/bronce, normalmente "tal
cual" desde la fuente. Los datos se procesan a través de un proceso de
Extracción, Carga y Transformación (ELT) en el que los datos se procesan
de forma incremental, hasta que llegan a la capa de oro curada para la
elaboración de informes. Una arquitectura típica puede ser similar a la
siguiente:

![](./media/image1.png)

Estas capas no pretenden ser una regla rígida, sino más bien un
principio rector. Las capas a menudo se separan en diferentes
Lakehouses, pero para los propósitos de nuestro laboratorio, vamos a
utilizar el mismo Lakehouse para almacenar todas las capas. Lea más
sobre la implementación de una [arquitectura medallón en Fabric
aquí](https://learn.microsoft.com/en-us/fabric/onelake/onelake-medallion-lakehouse-architecture).

**Objetivos**

- Para crear un Lakehouse llamado "StocksLakehouse" en la persona Data
  Engineering de Synapse Data Engineering Home, confirmando la exitosa
  creación del endpoint SQL.

- Para añadir el Lakehouse al StockEventStream, configúrelo, realice la
  limpieza de datos y asegúrese de que el Lakehouse recibe los campos de
  datos requeridos.

- Para importar los cuadernos "Lakehouse 1-4" a RealTimeWorkspace,
  ejecútelos individualmente.

- Utilizar procesadores de eventos y técnicas de gestión de datos para
  agregar y limpiar los datos brutos almacenados en Lakehouse. Esto
  incluye la realización de funciones como el filtrado, la conversión de
  tipos de datos y la gestión de campos para preparar los datos para el
  análisis posterior.

- Construir tablas de datos curadas y agregadas adecuadas para construir
  un modelo dimensional y apoyar actividades de ciencia de datos. Esto
  implica crear rutinas de agregación para resumir los datos a
  diferentes niveles de granularidad, como agregaciones por minuto y por
  hora.

- Construir un modelo dimensional dentro del entorno Lakehouse,
  incorporando tablas de hechos y dimensiones. Definir las relaciones
  entre estas tablas para facilitar la eficacia de las consultas y la
  elaboración de informes.

- Crear un modelo semántico basado en el modelo dimensional y construir
  informes interactivos utilizando herramientas como Power BI para
  visualizar y analizar los datos agregados.

# Ejercicio 1: Configuración de Lakehouse

## Tarea 1: Crear el Lakehouse

Empieza por crear un Lakehouse.

**Nota**: Si está completando este laboratorio después del módulo de
Data Science u otro módulo que utilice un Lakehouse, puede reutilizar
ese Lakehouse o crear uno nuevo, pero asumiremos que el mismo Lakehouse
es compartido por todos los módulos.*

1.  Dentro de su espacio de trabajo Fabric, cambie a la persona **Data
    Engineering** (abajo a la izquierda) como se muestra en la imagen
    inferior.

     ![](./media/image2.png)

2.  En la página de inicio de Synapse Data Engineering, navegue y haga
    clic en el mosaico **Lakehouse**.

      ![](./media/image3.png)

3.  En el cuadro de diálogo **New Lakehouse**, introduzca 
    **+++StocksLakehouse+++** en el campo **Name** y, a continuación,
    haga clic en el botón **Create**. Aparecerá la página
    StocksLakehouse.

      ![](./media/image4.png)
      ![](./media/image5.png)

4.  Verá una notificación que dice - **Successfully created SQL
    endpoint**.

**Nota**: Si no ve las notificaciones, espere unos minutos.
     ![](./media/image6.png)

## Tarea 2. Añadir Lakehouse al Eventstreams

Desde el punto de vista de la arquitectura, implementaremos una
arquitectura Lambda dividiendo los datos de la ruta caliente y la ruta
fría del Eventstream. La ruta caliente continuará hacia la base de datos
KQL como ya se ha configurado, y la ruta fría se añadirá para escribir
los datos sin procesar en nuestro Lakehouse. Nuestro flujo de datos se
parecerá al siguiente:

***Nota**: Si está completando este laboratorio después del módulo de
Data Science u otro módulo que utilice un Lakehouse, puede reutilizar
ese Lakehouse o crear uno nuevo, pero asumiremos que el mismo Lakehouse
es compartido por todos los módulos.*

1.  En su espacio de trabajo de Fabric, cambie a la persona de **Data
    Engineering** (abajo a la izquierda) como se muestra en la siguiente
    imagen.

      ![](./media/image7.png)

2.  Ahora, haga clic en **RealTimeWorkspace** en el panel de navegación
    de la izquierda y seleccione **StockEventStream** como se muestra en
    la siguiente imagen.

      ![](./media/image8.png)

3.  Además de añadir Lakehouse al Eventstream, vamos a hacer un poco de
    limpieza de los datos utilizando algunas de las funciones
    disponibles en el Eventstream.

4.  En la página **StockEventStream**, seleccione **Edit**

      ![](./media/image9.png)

5.  En la página **StockEventStream**, haga clic en **Add destination**
    en la salida del Eventstream para añadir un nuevo destino.
    Seleccione *Lakehouse* en el menú contextual.

      ![](./media/image10.png)

6.  En el panel Lakehouse que aparece a la derecha, introduzca los
    siguientes datos y haga clic en **Save.**

| **Destination name** | +++Lakehouse+++ |
|----|----|
| **Workspace** | RealTimeWorkspace |
| **Lakehouse** | StockLakehouse |
| **Delta table** | Haga clic en **Create new**\> entre +++*raw_stock_data+++* |
| **Input data format** | Json |
     ![](./media/image11.png)
     ![](./media/image12.png)

6.  Conectar **StockEventStream** y **Lakehouse**

     ![](./media/image13.png)
     ![](./media/image14.png)
     ![](./media/image15.png)
7.  Seleccione el Lakehouse y pulse el botón **Refresh**

       ![](./media/image16.png)

8.  Tras hacer clic en *Open event processor*, se pueden añadir varios
    procesamientos que realizan agregaciones, filtrados y cambios de
    tipos de datos.

      ![](./media/image17.png)

9.  En la página **StockEventStream**, seleccione **stockEventStream** y
    haga clic en el icono **más (+)** para añadir un **Mange field**. A
    continuación, seleccione **Mange field.**

     ![](./media/image18.png)
     ![](./media/image19.png)

10. En el panel de eventos, seleccione el icono del lápiz
    **Managefields1**.

       ![](./media/image20.png)

11. En el panel *Gestionar campos* que se abre, haga clic en ***Add all
    fields*** para añadir todas las columnas. A continuación, elimine
    los campos **EventProcessedUtcTime**, **PartitionId** y
    **EventEnqueuedUtcTime** haciendo clic en la **elipsis (...)**
    situada a la derecha del nombre del campo y haga clic en *Remove.*

      ![](./media/image21.png)
      ![](./media/image22.png)
      ![](./media/image23.png)
      ![](./media/image24.png)
      ![](./media/image25.png)

12. Ahora cambie la columna *timestamp* a *DateTime*, ya que es probable
    que esté clasificada como cadena. Haga clic en **los tres puntos
    (...)** a la derecha de la **columna** *de fecha y hora* y
    seleccione *Yes cambiar tipo*. Esto nos permitirá cambiar el tipo de
    datos: seleccione *DateTime***,** como se muestra en la siguiente
    imagen. Haga clic en Done

     ![](./media/image26.png)
     ![](./media/image27.png)

13. Ahora, haga clic en el botón **Publish** para cerrar el procesador
    de eventos

     ![](./media/image28.png)
     ![](./media/image29.png)

14. Una vez completado, el Lakehouse recibirá el símbolo, el precio y la
    marca de tiempo.

     ![](./media/image30.png)

Nuestro KQL (hot path) y Lakehouse (cold path) ya están configurados.
Los datos pueden tardar uno o dos minutos en ser visibles en Lakehouse.

## Tarea 3. Importar cuadernos

**Nota**: Si tiene problemas para importar estas libretas, asegúrese de
que está descargando el archivo de la libreta en bruto y no la página
HTML de GitHub que muestra la libreta.

1.  Ahora, haga clic en **RealTimeWorkspace** en el menú de navegación
    de la izquierda.

      ![](./media/image31.png)

2.  En la página **RealTimeWorkspace de Synapse Data Engineering**,
    navegue y haga clic en el botón **Importar**, luego seleccione
    **Notebook** y seleccione **From this computer** como se muestra en
    la siguiente imagen.

     ![](./media/image32.png)

3.  Seleccione **Upload** en el panel de **Import status** que aparece
    en la parte derecha de la pantalla.

      ![](./media/image33.png)

4.  Navegue y seleccione **Lakehouse 1-Import Data, Lakehouse 2-Build
    Aggregation, Lakehouse 3-Create Star Schema** y **Lakehouse 4-Load
    Star Schema** notebooks desde **C:\LabFiles\Lab 04** y haga clic en
    el botón **Open**.

     ![](./media/image34.png)

5.  Verá una notificación indicando **Imported successfully.**

     ![](./media/image35.png)

## Tarea 4. Importar datos adicionales

Para que los informes sean más interesantes, necesitamos un poco más de
datos con los que trabajar. Tanto para el módulo Lakehouse como para el
módulo Data Science, se pueden importar datos históricos adicionales
para complementar los datos ya ingestados. El cuaderno funciona mirando
los datos más antiguos de la tabla, añadiendo los datos históricos.

1.  En la página **RealTimeWorkspace**, para ver sólo los cuadernos,
    haga clic en el **Filter** situado en la esquina superior derecha de
    la página y, a continuación, seleccione **Notebook.**

     ![](./media/image36.png)

2.  A continuación, seleccione la libreta **Lakehouse 1 - Import
    data**.

     ![](./media/image37.png)

3.  En **Explorer**, navegue y seleccione el **Lakehouse**, luego haga
    clic en el botón **Add** como se muestra en las imágenes de abajo*.*

> **Nota importante**: tendrá que añadir el Lakehouse a cada cuaderno
> importado; hágalo cada vez que abra un cuaderno por primera vez.
      ![](./media/image38.png)
      ![](./media/image39.png)

4.  En el cuadro de diálogo **Add Lakehouse**, seleccione el botón de
    opción **Existing** **Lakehouse** y, a continuación, haga clic en el
    botón **Add**.
      ![](./media/image40.png)
5.  En la ventana **OneLake data hub**, seleccione **StockLakehouse** y
    haga clic en el botón **Add**.
      ![](./media/image41.png)

6.  La tabla **raw_stock_data** se creó cuando se configuró el
    Eventstream, y es el lugar de destino de los datos que se ingieren
    desde el Event Hub.

     ![](./media/image42.png)

**Nota**: Verá el botón **Run** cuando pase el ratón por encima de la
celda en el bloc de notas.

7.  Para iniciar el bloc de notas y ejecutar la celda, seleccione el
    icono **Run** que aparece a la izquierda de la celda.

     ![](./media/image43.png)
     ![](./media/image44.png)

8.  Del mismo modo, ejecute las células 2<sup>nd</sup> y 3<sup>rd</sup>
    .
      ![](./media/image45.png)
      ![](./media/image46.png)

10.  Para descargar y descomprimir los datos históricos en los archivos
    no gestionados de Lakehouse, ejecute las celdas 4<sup>th</sup> y 5
    <sup>thd</sup> como se muestra en las siguientes imágenes.
       ![](./media/image47.png)
       ![](./media/image48.png)
       ![](./media/image49.png)

10. Para comprobar que los archivos csv están disponibles, seleccione y
    ejecute la celda 6<sup>th</sup> .

      ![](./media/image50.png)

11. Ejecute la célula 7<sup>th</sup> , la célula 8<sup>th</sup> y la
    célula 9<sup>th</sup> .

     ![](./media/image51.png)
     ![](./media/image52.png)
     ![](./media/image53.png)

12. Aunque es similar a "comentar" secciones de código, congelar celdas
    tiene la ventaja de que también se conserva cualquier salida de las
    celdas.

     ![](./media/image54.png)

# Ejercicio 2: Creación de tablas de agregación

En este ejercicio, construirá datos curados y agregados adecuados para
su uso en la construcción de nuestro modelo dimensional y en Data
Science. Dado que los datos en bruto tienen una frecuencia por segundo,
este tamaño de datos no suele ser ideal para la elaboración de informes
o análisis. Además, los datos no se limpian, por lo que corremos el
riesgo de que los datos no conformes causen problemas en informes o
canalizaciones en los que no se esperan datos erróneos. Estas nuevas
tablas almacenarán los datos por minuto y por hora. Afortunadamente,
*Data Wrangler* facilita esta tarea.

El cuaderno utilizado aquí construirá ambas tablas de agregación, que
son artefactos de nivel de plata. Aunque es habitual separar las capas
de medallones en diferentes Lakehouses, dado el pequeño tamaño de
nuestros datos y a efectos de nuestro laboratorio, utilizaremos el mismo
Lakehouse para almacenar todas las capas.

## Tarea 1: Construir el cuaderno de Tablas de Agregación

Tómese un momento para desplazarse por el cuaderno. Asegúrate de añadir
el Lakehouse por defecto si aún no está añadido.

1.  Ahora, haga clic en **RealTimeWorkspace** en el menú de navegación
    de la izquierda.

      ![](./media/image54.png)

2.  En la página **RealTimeWorkspace**, haga clic en el cuaderno
    **Lakehouse 2 - Build Aggregation Tables**.

      ![](./media/image55.png)

3.  En Explorador, navegue y seleccione el **Lakehouse**, luego haga
    clic en el botón **Add**.

       ![](./media/image56.png)
       ![](./media/image57.png)

4.  En el cuadro de diálogo **Add Lakehouse**, seleccione el cuadro de
    diálogo **Existing** **Lakehouse** y, a continuación, haga clic en
    el botón **Add**.

      ![](./media/image40.png)

5.  En la ventana **OneLake datahub**, seleccione **StockLakehouse** y
    haga clic en el botón **Add**.

      ![](./media/image41.png)

6.  Para construir Aggregate Tables, seleccione y ejecute las celdas
    1<sup>st</sup> , 2<sup>nd</sup> , 3<sup>rd</sup> , y 4<sup>th</sup>
    .

    ![](./media/image58.png)
    ![](./media/image59.png)
    ![](./media/image60.png)
    ![](./media/image61.png)
7.  A continuación, seleccione y ejecute las celdas 5<sup>th</sup> ,
    6<sup>th</sup> , 7<sup>th</sup> y 8<sup>th</sup> .

     ![](./media/image62.png)
     ![](./media/image62.png)
     ![](./media/image63.png)
     ![](./media/image64.png)

8.  Añada Data Wrangler, seleccione la celda **9<sup>th</sup>** ,
    navegue por el desplegable **Data Wrangler**. Navegue y haga clic en
    **anomaly_df** para cargar el dataframe en Data Wrangler.

9.  Utilizaremos **anomaly_df** porque se creó intencionadamente con
    algunas filas no válidas que se pueden probar.

      ![](./media/image65.png)

10. En Data Wrangler, registraremos una serie de pasos para procesar los
    datos. Observa que los datos se visualizan en la columna central.
    Las operaciones están en la parte superior izquierda, mientras que
    un resumen de cada paso está en la parte inferior izquierda.

      ![](./media/image66.png)

11. Para eliminar valores nulos/vacíos, en *Operaciones*, haga clic en
    el desplegable junto a **Find and replace**, luego navegue y haga
    clic en **Drop missing values**.

      ![](./media/image67.png)

12. En el desplegable **Target columns**, seleccione las columnas de
    **símbolo** y **precio** y, a continuación, haga clic en el
    **botón Apply** situado debajo, como se muestra en la imagen.

     ![](./media/image68.png)
     ![](./media/image69.png)
     ![](./media/image70.png)

13. En el menú desplegable **Operations**, desplácese y haga clic en
    **Sort and filter** y, a continuación, en **Filter**, como se
    muestra en la imagen siguiente.

      ![](./media/image71.png)

14. **Desmarque** *Conservar filas coincidentes*, seleccione **price**
    como columna de destino y establezca la condición **Equal to**
    **0**. Haga clic en **Apply** en el panel **Operations** situado
    debajo de Filter

> Nota: Las filas con cero están marcadas en rojo, ya que se eliminarán
> (si las demás filas están marcadas en rojo, asegúrese de desmarcar la
> casilla **Keep matching rows**).
      ![](./media/image72.png)
      ![](./media/image73.png)
15. Haga clic en **+ Add code to notebook** en la parte superior
    izquierda de la página. En la ventana **Add code to notebook** ,
    asegúrese de que la opción *Incluir código pandas* no está marcada y
    haga clic en el botón **Add**.
     ![](./media/image74.png)
     ![](./media/image75.png)

16. El código insertado será similar al siguiente.

      ![](./media/image76.png)

17. Ejecute la celda y observe la salida. Observará que se han eliminado
    las filas no válidas.

     ![](./media/image77.png)
     ![](./media/image78.png)

La función creada, **clean_data**, contiene todos los pasos en secuencia y
puede modificarse según sea necesario. Observe que cada paso realizado
en Data Wrangler está comentado. Debido a que Data Wrangler fue cargado
con la **anomalía_df**, el método está escrito para tomar ese marco de
datos por su nombre, pero este puede ser cualquier marco de datos que
coincida con el esquema.

18. Modifique el nombre de la función de **clean_data** a
    *remove_invalid_rows*, y cambie la línea *anomaly_df_clean =
    clean_data(anomaly_df)* a *df_stocks_clean =
    remove_invalid_rows(df_stocks)* . Además, aunque no es necesario
    para la funcionalidad, puede cambiar el nombre del marco de datos
    utilizado en la función a simplemente *df* como se muestra a
    continuación

19. Ejecute esta célula y observe el resultado.

# Code generated by Data Wrangler for PySpark DataFrame

def remove_invalid_rows(df):
    # Drop rows with missing data in columns: 'symbol', 'price'
    df = df.dropna(subset=['symbol', 'price'])
    # Filter rows based on column: 'price'
    df = df.filter(~(df['price'] == 0))
    return df

df_stocks_clean = remove_invalid_rows(df_stocks)
display(df_stocks_clean)
     ![](./media/image79.png)

20. Esta función eliminará ahora las filas inválidas de nuestro marco de
    datos *df_stocks* y devolverá un nuevo marco de datos llamado
    *df_stocks_clean*. Es común utilizar un nombre diferente para el
    marco de datos de salida (como *df_stocks_clean*) para hacer la
    celda idempotente -- de esta manera, podemos volver atrás y volver a
    ejecutar la celda, hacer modificaciones, etc., sin tener que volver
    a cargar nuestros datos originales.
     ![](./media/image80.png)

## Tarea 2: Crear una rutina de agregación

En esta tarea, estarás más involucrado porque construiremos una serie de
pasos en Data Wrangler, añadiendo columnas derivadas y agregando los
datos. Si te quedas atascado, continúa lo mejor que puedas y utiliza el
código de ejemplo en el cuaderno para ayudar a solucionar cualquier
problema después.

1.  Añada una nueva columna datestamp en la Sección de
    **Symbol/Date/Hour/Minute Aggregation**, coloque el cursor en la
    celda **Add Data Wrangler here** y seleccione la celda. Despliegue el
    **Data Wrangler**. Navegue y haga clic en **df_stocks_clean** como
    se muestra en la siguiente imagen.

      ![](./media/image81.png)
      ![](./media/image82.png)

2.  En el panel **Data Wrangler:df_stocks_clean**, seleccione
    **Operations** y, a continuación, **New column by example.**

      ![](./media/image83.png)

3.  En el campo **Target columns**, haga clic en el desplegable y
    seleccione **timestamp**. A continuación, en el campo **Derived
    columna name**, introduce **+++datestamp+++**.

       ![](./media/image84.png)

4.  En la nueva columna **timestamp**, introduzca un valor de ejemplo
    para cualquier fila. Por ejemplo, si la *fecha* es *2024-02-07
    09:54:00* introduzca **2024-02-07**. Esto permite a Data Wrangler
    deducir que estamos buscando la fecha sin un componente de tiempo;
    una vez que las columnas se autocompleten, haga clic en el botón
    **Apply**.

      ![](./media/image85.png)
      ![](./media/image86.png)

5.  De forma similar a la adición de la columna **datestamp** como se
    mencionó en los pasos anteriores, haga clic de nuevo en **New
    columna by example** como se muestra en la siguiente imagen.

      ![](./media/image87.png)

6.  En *Columnas de destino*, seleccione **Timestamp**. Introduzca un
    *nombre de **Derived column**de **+++hour+++**

      ![](./media/image88.png)

7.  En la nueva columna de **hour** que aparece en la vista previa de
    datos, introduzca una hora para cualquier fila, pero intente elegir
    una fila que tenga un valor de hora único. Por ejemplo, si la *hora*
    es *2024-02-07 09:54:00* introduzca ***9***. Puede que necesite
    introducir valores de ejemplo para varias filas, como se muestra
    aquí. Pulse el botón **Apply**.

      ![](./media/image89.png)

8.  Data Wrangler debería inferir que estamos buscando el componente
    hora, y construir código similar a:

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

 9.  Al igual que con la columna de horas, cree una nueva columna de
    **minute**. En la nueva columna de *minutos*, introduzca un minuto
    para cualquier fila. Por ejemplo, si la *timestamp* es **2024-02-07
    09:54:00** introduzca **54**. Puede que necesite introducir valores de
    ejemplo para varias filas.
      ![](./media/image91.png)

10. El código generado debería ser similar a:

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
       ![](./media/image92.png)
11. A continuación, convierta la columna de horas en un número entero.
    Haga clic en la **elipsis (...)** en la esquina de la columna de
    *horas* y seleccione **Change columna type**. Haga clic en el menú
    desplegable junto a **New type**, navegue y seleccione
    **int32**, a continuación, haga clic en el botón **Apply**como
    se muestra en la siguiente imagen.
      ![](./media/image93.png)
      ![](./media/image94.png)

12. Convierta la columna de los minutos en un número entero siguiendo
    los mismos pasos que acaba de realizar para la hora. Haga clic en la
    **elipsis (...)** en la esquina de la **columna** de **minutos** y
    seleccione ***Change columna type***. Haga clic en el menú
    desplegable junto a ***New type***, navegue y seleccione
    ***int32**,* luego haga clic en el botón **Apply**como se muestra
    en la siguiente imagen.

      ![](./media/image95.png)

     ![](./media/image96.png)

     ![](./media/image97.png)

13. Ahora, en la sección **Operations**, navegue y haga clic en **Group
    by and aggregate** como se muestra en la siguiente imagen.

      ![](./media/image98.png)

14. Haga clic en el desplegable de ***Columns to group by*** campos y
    seleccione **symbol**, **datestamp**, **hour**, **minute**.
     ![](./media/image99.png)

15. Haga clic en **+Add aggregation**, cree un total de tres agregaciones
    como se muestra en las siguientes imágenes y haga clic en el botón
    **Apply**.

- price: Maximum

- price: Minimum

- price: Last value

     ![](./media/image100.png)
     ![](./media/image101.png)

16. Haga clic en **Add code to notebook**  en la esquina superior
    izquierda de la página. En la **ventana Add code to notebook**,
    asegúrese de que la opción *include pandas code* no está marcada y,
    a continuación, haga clic en el botón **Add**.
     ![](./media/image102.png)
     ![](./media/image103.png)
     ![](./media/image104.png)

17. Revise el código, en la celda que se agrega, en las dos últimas
    líneas de la celda, observe que el marco de datos devuelto se llama
    **df_stocks_clean_1**. Cambie el nombre a
    **df_stocks_agg_minute**, y cambie el nombre de la función a
    **aggregate_data_minute** como se muestra a continuación.

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
     ![](./media/image105.png)

18. Código generado por Data Wrangler para la celda DataFrame de
    PySpark, seleccione el icono **Run** que aparece a la izquierda de
    la celda al pasar el ratón por encima.

     ![](./media/image106.png)
     ![](./media/image107.png)
     ![](./media/image108.png)

**Nota**: Si te quedas atascado, consulta el código comentado como
referencia. Si alguno de los pasos de manipulación de datos no parece
ser del todo correcto (no obtener la hora o los minutos correctos, por
ejemplo), consulte los ejemplos comentados. El paso 7 tiene una serie de
consideraciones adicionales que pueden ser útiles.

**Nota:** Si desea comentar (o descomentar) bloques grandes, puede
resaltar la sección de código (o CTRL-A para seleccionar todo en la
celda actual) y utilizar CTRL-/ (*barra de* control) para activar los
comentarios.

19. En la celda de fusión, seleccione el icono **Ejecutar** que aparece
    a la izquierda de la celda al pasar el ratón por encima. La función
    de fusión escribe los datos en la tabla:

> <span class="mark">\# write the data to the stocks_minute_agg
> table</span>
>
> <span class="mark">merge_minute_agg(df_stocks_agg_minute)</span>
    ![](./media/image109.png)

## Tarea 3: Agregación horaria

Repasemos el progreso actual: nuestros datos por segundo se han limpiado
y, a continuación, se han resumido al nivel por minuto. Esto reduce
nuestro recuento de filas de 86.400 filas/día a 1.440 filas/día por
símbolo bursátil. Para los informes que podrían mostrar datos mensuales,
podemos agregar aún más los datos a la frecuencia por hora, reduciendo
los datos a 24 filas/día por símbolo bursátil.

1.  En el marcador de posición final de la sección *Símbolo/Fecha/Hora*,
    cargue el marco de datos **df_stocks_agg_minute** existente en
    Data Wrangler.

2.  En el marcador de posición final de la sección
    **Symbol/Date/Hour**, coloque el cursor en la celda *Añadir Data
    Wrangler aquí* y seleccione la celda. Despliegue el **Data
    Wrangler,** navegue y haga clic en **df_stocks_agg_minute** como
    se muestra en la siguiente imagen.
      ![](./media/image110.png)
      ![](./media/image111.png)

3.  En ***Operations*,** seleccione ***Group by and aggregate***. Haga
    clic en el menú desplegable debajo de ***Group by and aggregate***
    campo y seleccione ***symbol*, *datestamp*, and *hour***, y a
    continuación, haga clic en **+ Add aggregations**. Cree las tres
    agregaciones siguientes y haga clic en el botón Apply situado
    debajo, como se muestra en la imagen inferior.

- price_min: Minimum

- price_max: Maximum

- price_last: Last value
      ![](./media/image112.png)
      ![](./media/image113.png)
4.  A continuación se muestra un ejemplo de código. Además de cambiar el
    nombre de la función a *aggregate_data_hour*, también se ha cambiado
    el alias de cada columna de precio para mantener los mismos nombres
    de columna. Debido a que estamos agregando datos que ya han sido
    agregados, Data Wrangler está nombrando las columnas como
    precio_max_max, precio_min_min; modificaremos los alias para
    mantener los mismos nombres para mayor claridad.
      ![](./media/image114.png)

5.  Haga clic en **Add code to notebook** en la esquina superior
    izquierda de la página. En la ventana **Add code to notebook**,
    asegúrate de que la opción **Incluir código pandas** no está marcada y
    haz clic en el botón **Add**.

     ![](./media/image115.png)
     ![](./media/image116.png)
     ![](./media/image117.png)

6.  En la celda que se añade, en las dos últimas líneas de la celda,
    observe que el marco de datos devuelto se llama def
    clean_data(df_stocks_agg_minute):, renombre esto

> **def aggregate_data_hour(df_stocks_agg_minute):**

7.  En la celda que se añade, en las dos últimas líneas de la celda,
    observe que el marco de datos devuelto se llama
    **df_stocks_agg_minute_clean = clean_data(df_stocks_agg_minute).**
    Cámbiele el nombre **df_stocks_agg_hour =
    aggregate_data_hour(df_stocks_agg_minute),** y cambie el nombre de
    la función **display(df_stocks_agg_minute_clean)** por
    *aggregate_data_minute*, como se muestra a continuación.

Código de referencia:

# Code generated by Data Wrangler for PySpark DataFrame

from pyspark.sql import functions as F

def aggregate_data_hour(df_stocks_agg_minute):
    # Performed 3 aggregations grouped on columns: 'symbol', 'datestamp', 'hour'
    df_stocks_agg_minute = df_stocks_agg_minute.groupBy('symbol', 'datestamp', 'hour').agg(
        F.max('price_max').alias('price_max'), 
        F.min('price_min').alias('price_min'), 
        F.last('price_last').alias('price_last'))
    df_stocks_agg_minute = df_stocks_agg_minute.dropna()
    df_stocks_agg_minute = df_stocks_agg_minute.sort(df_stocks_agg_minute['symbol'].asc(), df_stocks_agg_minute['datestamp'].asc(), df_stocks_agg_minute['hour'].asc())
    return df_stocks_agg_minute

df_stocks_agg_hour = aggregate_data_hour(df_stocks_agg_minute)
display(df_stocks_agg_hour)
    ![](./media/image118.png)

2.  Seleccione y **ejecute** la celda.
     ![](./media/image119.png)

3.  El código para combinar los datos agregados por horas se encuentra
    en la siguiente celda: **merge_hour_agg(df_stocks_agg_hour)**

4.  Ejecute la celda para completar la fusión. Hay algunas celdas de
    utilidad en la parte inferior para comprobar los datos de las
    tablas: explora un poco los datos y experimenta libremente.
      ![](./media/image120.png)


21. Utilice la sección **Handy SQL Commands for testing**, limpiar
    tablas para volver a ejecutarlas, etc. Seleccione y **Ejecute** las
    celdas en esta sección.
      ![](./media/image121.png)
      ![](./media/image122.png)
      ![](./media/image123.png)


# Ejercicio 3: Construcción del modelo dimensional

En este ejercicio, refinaremos aún más nuestras tablas de agregación y
crearemos un esquema en estrella tradicional utilizando tablas de hechos
y dimensiones. Si ha completado el módulo Data Warehouse, este módulo
producirá un resultado similar, pero es diferente en el enfoque mediante
el uso de cuadernos dentro de un Lakehouse.

**Nota**: Es posible utilizar pipelines para orquestar actividades, pero
esta solución se hará completamente dentro de los cuadernos.

## Tarea 1: Crear esquema

Este cuaderno de una sola ejecución configurará el esquema para
construir las tablas de hechos y dimensiones. Configure la variable
sourceTableName en la primera celda (si es necesario) para que coincida
con la tabla de agregación horaria. Las fechas de inicio y fin
corresponden a la tabla de dimensiones de fecha. Este cuaderno recreará
todas las tablas, reconstruyendo el esquema: las tablas de hechos y
dimensiones existentes se sobrescribirán.

1.  Haga clic en **RealTimeWorkspace** en el menú de navegación de la
    izquierda.
     ![](./media/image124.png)

2.  En el espacio de trabajo RealTimeWorkshop, seleccione el cuaderno
    **Lakehouse 3 - Create Star Schema**.

       ![](./media/image125.png)

3.  En el Explorador, navegue y haga clic en los **Lakehouses**, luego
    haga clic en el botón **Add**.

      ![](./media/image126.png)
      ![](./media/image127.png)


4.  En el cuadro de diálogo **Add Lakehouse**, seleccione el botón de
    opción **Existing** **Lakehouse** y, a continuación, haga clic en el
    botón **Add**.

      ![](./media/image40.png)

5.  En la ventana del concentrador de datos OneLake, seleccione
    **StockLakehouse** y haga clic en el botón **Add**.

      ![](./media/image41.png)

6.  Con el cuaderno cargado y el Lakehouse conectado, fíjese en el
    esquema de la izquierda. Además de la tabla **raw_stock_data**, debe
    haber las tablas **stocks_minute_agg** y **stocks_hour_agg**.

       ![](./media/image128.png)


7.  Ejecute cada celda individualmente haciendo clic en el botón de
    **play** situado a la izquierda de cada celda para seguir el
    proceso.

     ![](./media/image129.png)
     ![](./media/image130.png)
     ![](./media/image131.png)
     ![](./media/image132.png)
     ![](./media/image133.png)
     ![](./media/image134.png)
     ![](./media/image135.png)
     ![](./media/image136.png)
     ![](./media/image137.png)
    
8.  Cuando todas las celdas se hayan ejecutado correctamente, navegue
    hasta la sección **StocksLakehouse**, haga clic en la elipsis
    horizontal junto a **Tables (...)**, luego navegue y haga clic en
    **Refresh** como se muestra en la siguiente imagen.

     ![](./media/image138.png)

9.  Ahora, puede ver todas las tablas adicionales **dim_symbol**,
    **dim_date** y **fact_stocks_daily_prices** para nuestro modelo
    dimensional.
     ![](./media/image139.png)

## Tarea 2: Cargar la tabla de hechos

Nuestra tabla de hechos contiene los precios diarios de las acciones (el
precio máximo, mínimo y de cierre), mientras que nuestras dimensiones
son para la fecha y los símbolos de las acciones (que pueden contener
detalles de la empresa y otra información). Aunque simple,
conceptualmente este modelo representa un esquema en estrella que puede
aplicarse a conjuntos de datos mayores.

1.  Ahora, haga clic en **RealTimeWorkspace** en el menú de navegación
    de la izquierda.

     ![](./media/image140.png)

2.  En el espacio de trabajo RealTimeWorkshop, seleccione el cuaderno
    **Lakehouse 4 – Load fact table**.

      ![](./media/image141.png)

3.  En el Explorer, seleccione **Lakehouse** y haga clic en el botón
    **Add**

      ![](./media/image142.png)

      ![](./media/image143.png)

4.  En el cuadro de diálogo **Add Lakehouse**, seleccione el botón de
    opción **Existing** **Lakehouse** y, a continuación, haga clic en el
    botón **Add**.

     ![](./media/image144.png)

5.  En la pestaña del concentrador de datos OneLake, seleccione
    **StockLakehouse** y haga clic en el botón **Add**.

      ![](./media/image41.png)

6.  Seleccione y ejecute cada celda individualmente.

      ![](./media/image144.png)

7.  La función añade símbolos a dim_symbol que pueden no existir en la
    tabla, seleccione y **ejecute** las celdas 2<sup>nd</sup> y
    3<sup>rd</sup> .
      ![](./media/image145.png)
      ![](./media/image146.png)

8.  Para obtener los nuevos datos de stock a ingestar, empezando por la
    marca de agua, seleccione y ejecute la celda 4<sup>th</sup> .
      ![](./media/image147.png)

9.  Cargue la dimensión fecha para posteriores uniones, seleccione y
    **Ejecute** las celdas 5<sup>th</sup> , 6<sup>th</sup> , y
    7<sup>th</sup> .

      ![](./media/image148.png)
      ![](./media/image149.png)
      ![](./media/image150.png)
      ![](./media/image151.png)

10. Para unir los datos agregados a la dimensión fecha, seleccione y
    **Ejecute** las celdas 8<sup>th</sup> y 9<sup>th</sup> .

      ![](./media/image152.png)

      ![](./media/image153.png)

11. Cree una vista final con nombres limpios para facilitar el
    procesamiento, seleccione y **Ejecute** las celdas 10<sup>th</sup> ,
    11<sup>th</sup> , y 12<sup>th</sup> .

     ![](./media/image154.png)
     ![](./media/image155.png)
     ![](./media/image156.png)

12. Para obtener el resultado y trazar un gráfico, seleccione y
    **ejecute** 13<sup>th</sup> y 14<sup>th</sup> celdas.

     ![](./media/image157.png)
     ![](./media/image158.png)
     ![](./media/image159.png)

13. Para validar las tablas creadas, haga clic con el botón derecho en
    la elipsis horizontal (...) junto a **Tables,** luego navegue y haga
    clic en **Refresh.** Aparecerán las tablas.

      ![](./media/image160.png)

14. Para programar la libreta para que se ejecute periódicamente, haga
    clic en la pestaña **Run**, y haga clic en **Schedule** como se
    muestra en la siguiente imagen*.*

      ![](./media/image161.png)

15. En la pestaña Lackhouse 4-Load Star Schema, seleccione los
    siguientes datos y haga clic en el botón **Apply**.

- Schedule run: **On**

- Repeat**: Hourly**

- Every: **4 hours**

- Seleccione la fecha de hoy

     ![](./media/image162.png)

## Tarea 3: Construir un modelo semántico y un informe sencillo

En esta tarea, crearemos un nuevo modelo semántico que podremos utilizar
para la generación de informes, y crearemos un informe sencillo de Power
BI.

1.  Ahora, haz clic en **StocksLakehouse** en el menú de navegación de
    la izquierda.

      ![](./media/image163.png)

2.  En la ventana **StocksLakehouse**, navegue y haga clic en **New
    semantic model** en la barra de comandos.

      ![](./media/image164.png)

3.  Nombre el modelo **StocksDimensionalModel** y seleccione las
    tablas **fact_stocks_daily_prices**, **dim_date** y **dim_symbol**.
    A continuación, haga clic en el botón **Confirm**.
     ![](./media/image165.png)
     ![](./media/image166.png)

4.  Cuando se abre el modelo semántico, tenemos que definir las
    relaciones entre las tablas de hechos y dimensiones.

5.  Desde la tabla **fact_Stocks_Daily_Prices**, arrastre el campo
    **Symbol_SK** y suéltelo en el campo **Symbol_SK** de la tabla
    **dim_Symbol** para crear una relación. Aparecerá el cuadro de
    diálogo **New relationship**.

       ![](./media/image167.png)

6.  En el cuadro de diálogo **Nueva relación**:

- **La tabla From** se rellena con **fact_Stocks_Daily_Prices** y la
  columna **Symbol_SK.**

- **La tabla To** se rellena con **dim_symbol** y la columna de
  **Symbol_SK**

- Cardinalidad: **Many to one (*:1)**

- Dirección del filtro transversal: **Single**

- Deje seleccionada la casilla junto a **Make this relationship
  active**.

- Seleccione **Save.**
      ![](./media/image168.png)
      ![](./media/image169.png)

7.  Desde la tabla **fact_Stocks_Daily_Prices**, arrastre el campo
    **PrinceDateKey** y suéltelo sobre el campo **DateKey** de la
    tabla **dim_date** para crear una relación. Aparecerá el cuadro de
    diálogo **New relationship**.

      ![](./media/image170.png)

8.  En el cuadro de diálogo **New relationship:**

- **La tabla From** se rellena con **fact_Stocks_Daily_Prices** y la
  columna **PrinceDateKey.**

- **La tabla To** se rellena con **dim_date** y la columna de
  **DateKey**

- Cardinalidad: **Many to one **(:1)**

- Dirección del filtro transversal: **Single**

- Deje seleccionada la casilla junto a **Make this relationship
  active**.

- Seleccione **Save.**
      ![](./media/image171.png)
      ![](./media/image172.png)

9.  Haga clic en ***New Report*** para cargar el modelo semántico en
    Power BI.

      ![](./media/image173.png)

10. En la página **de Power BI**, en **Visualizations**, haga clic en el
    icono **Line charts** para añadir un **gráfico de columnas** a su
    informe.

- En el panel **Data**, expanda **fact_Stocks_Daily_Prices** y marque la
  casilla junto a **PriceDateKey**. Esto crea un gráfico de columnas y
  añade el campo al **eje X**.

- En el panel de **Data**, expanda **fact_Stocks_Daily_Prices** y marque
  la casilla junto a **ClosePrice**. Esto añade el campo al **eje Y**.

- En el panel **Data**, expanda **dim_Symbol** y marque la casilla junto
  a **Símbolo**. Esto añade el campo a la **Legend**.
      ![](./media/image174.png)

11. En **Filters,** seleccione **PriceDateKey** e introduzca los
    siguientes datos. Haga clic en **Apply** filtro

- Tipo de filtro: **Relative date**

- Mostrar artículos cuando el valor: **is in the last 45 days**

     ![](./media/image175.png)

     ![](./media/image176.png)

12. En la cinta de opciones, seleccione **File**  **Save as.**

      ![](./media/image177.png)

13. En el cuadro de diálogo Guardar su informe, introduzca 
    +++StocksDimensional+++ como nombre de su informe y seleccione
    **su espacio de trabajo**. Haga clic en el botón **Save.**

      ![](./media/image178.png)

      ![](./media/image179.png)

**Resumen**

En este laboratorio, ha configurado una infraestructura completa de
Lakehouse y ha implementado canalizaciones de procesamiento de datos
para gestionar eficazmente flujos de datos en tiempo real y por lotes.
Ha comenzado con la creación del entorno de Lakehouse, el laboratorio
avanza hacia la configuración de la arquitectura de Lambda para procesar
rutas de datos calientes y frías.

Ha aplicado técnicas de agregación y limpieza de datos para preparar los
datos brutos almacenados en Lakehouse para su análisis posterior. Ha
creado tablas de agregación para resumir los datos en diferentes niveles
de granularidad, lo que facilita la consulta y el análisis eficientes. A
continuación, ha creado un modelo dimensional dentro de Lakehouse,
incorporando tablas de hechos y dimensiones. Ha definido las relaciones
entre estas tablas para soportar consultas complejas y requisitos de
generación de informes.

Por último, ha generado un modelo semántico para proporcionar una visión
unificada de los datos, lo que permite la creación de informes
interactivos utilizando herramientas de visualización como Power BI.
Este enfoque holístico permite una gestión eficaz de los datos, el
análisis y la elaboración de informes en el entorno de Lakehouse.
