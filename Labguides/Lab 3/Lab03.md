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

<img src="./media/image1.png" style="width:6.5in;height:2.6625in"
alt="Medallion Architecture" />

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

***Nota**: Si está completando este laboratorio después del módulo de
Data Science u otro módulo que utilice un Lakehouse, puede reutilizar
ese Lakehouse o crear uno nuevo, pero asumiremos que el mismo Lakehouse
es compartido por todos los módulos.*

1.  Dentro de su espacio de trabajo Fabric, cambie a la persona **Data
    Engineering** (abajo a la izquierda) como se muestra en la imagen
    inferior.

> <img src="./media/image2.png" style="width:4.1in;height:6.475in" />

2.  En la página de inicio de Synapse Data Engineering, navegue y haga
    clic en el mosaico ***Lakehouse***.

<img src="./media/image3.png" style="width:6.5in;height:5.48472in"
alt="A screenshot of a computer Description automatically generated" />

3.  En el cuadro de diálogo **New Lakehouse**, introduzca +++
    ***StocksLakehouse+++*** en el campo **Name** y, a continuación,
    haga clic en el botón **Create**. Aparecerá la página
    StocksLakehouse.

<img src="./media/image4.png" style="width:3.01528in;height:1.69722in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image5.png" style="width:6.5in;height:5.38056in"
alt="A screenshot of a computer Description automatically generated" />

4.  Verá una notificación que dice - **Successfully created SQL
    endpoint**.

> **Nota**: Si no ve las notificaciones, espere unos minutos.

<img src="./media/image6.png" style="width:3.37529in;height:2.55022in"
alt="A screenshot of a computer Description automatically generated" />

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

> <img src="./media/image7.png" style="width:2.85319in;height:5.87917in"
> alt="A screenshot of a graph Description automatically generated" />

2.  Ahora, haga clic en **RealTimeWorkspace** en el panel de navegación
    de la izquierda y seleccione **StockEventStream** como se muestra en
    la siguiente imagen.

<img src="./media/image8.png" style="width:5.27917in;height:4.7191in"
alt="A screenshot of a computer Description automatically generated" />

3.  Además de añadir Lakehouse al Eventstream, vamos a hacer un poco de
    limpieza de los datos utilizando algunas de las funciones
    disponibles en el Eventstream.

4.  En la página **StockEventStream**, seleccione **Edit**

<img src="./media/image9.png" style="width:7.1875in;height:4.15196in"
alt="A screenshot of a computer Description automatically generated" />

5.  En la página **StockEventStream**, haga clic en **Add destination**
    en la salida del Eventstream para añadir un nuevo destino.
    Seleccione *Lakehouse* en el menú contextual.

<img src="./media/image10.png" style="width:6.9892in;height:3.6875in"
alt="A screenshot of a computer Description automatically generated" />

6.  En el panel Lakehouse que aparece a la derecha, introduzca los
    siguientes datos y haga clic en **Save.**

| **Destination name** | +++Lakehouse+++ |
|----|----|
| **Workspace** | RealTimeWorkspace |
| **Lakehouse** | StockLakehouse |
| **Delta table** | Haga clic en **Create new**\> entre +++*raw_stock_data+++* |
| **Input data format** | Json |

> <img src="./media/image11.png" style="width:6.4375in;height:5.61525in"
> alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image12.png" style="width:3.6in;height:5.63333in"
alt="A screenshot of a computer Description automatically generated" />

6.  Conectar **StockEventStream** y **Lakehouse**

<img src="./media/image13.png" style="width:7.1375in;height:3.90318in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image14.png" style="width:7.12931in;height:3.92112in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image15.png" style="width:7.3473in;height:3.65088in"
alt="A screenshot of a computer Description automatically generated" />

7.  Seleccione el Lakehouse y pulse el botón **Refresh**

<img src="./media/image16.png" style="width:7.31512in;height:3.8125in"
alt="A screenshot of a computer Description automatically generated" />

8.  Tras hacer clic en *Open event processor*, se pueden añadir varios
    procesamientos que realizan agregaciones, filtrados y cambios de
    tipos de datos.

<img src="./media/image17.png" style="width:6.5in;height:2.5375in"
alt="A screenshot of a computer Description automatically generated" />

9.  En la página **StockEventStream**, seleccione **stockEventStream** y
    haga clic en el icono **más (+)** para añadir un **Mange field**. A
    continuación, seleccione **Mange field.**

<img src="./media/image18.png" style="width:6.5in;height:4.03862in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image19.png" style="width:6.5in;height:4.425in"
alt="A screenshot of a computer Description automatically generated" />

10. En el panel de eventos, seleccione el icono del lápiz
    **Managefields1**.

<img src="./media/image20.png" style="width:7.03788in;height:3.87083in"
alt="A screenshot of a computer Description automatically generated" />

11. En el panel *Gestionar campos* que se abre, haga clic en ***Add all
    fields*** para añadir todas las columnas. A continuación, elimine
    los campos **EventProcessedUtcTime**, **PartitionId** y
    **EventEnqueuedUtcTime** haciendo clic en la **elipsis (...)**
    situada a la derecha del nombre del campo y haga clic en *Remove.*

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

12. Ahora cambie la columna *timestamp* a *DateTime*, ya que es probable
    que esté clasificada como cadena. Haga clic en **los tres puntos
    (...)** a la derecha de la **columna** *de fecha y hora* y
    seleccione *Yes cambiar tipo*. Esto nos permitirá cambiar el tipo de
    datos: seleccione *DateTime***,** como se muestra en la siguiente
    imagen. Haga clic en Done

> <img src="./media/image26.png" style="width:5.225in;height:4.63333in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image27.png" style="width:3.50833in;height:5.975in"
> alt="A screenshot of a computer Description automatically generated" />

13. Ahora, haga clic en el botón **Publish** para cerrar el procesador
    de eventos

<img src="./media/image28.png" style="width:7.20548in;height:3.69583in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image29.png" style="width:7.35082in;height:3.68169in"
alt="A screenshot of a computer Description automatically generated" />

14. Una vez completado, el Lakehouse recibirá el símbolo, el precio y la
    marca de tiempo.

<img src="./media/image30.png" style="width:7.0683in;height:4.38892in"
alt="A screenshot of a computer Description automatically generated" />

Nuestro KQL (hot path) y Lakehouse (cold path) ya están configurados.
Los datos pueden tardar uno o dos minutos en ser visibles en Lakehouse.

## Tarea 3. Importar cuadernos

**Nota**: Si tiene problemas para importar estas libretas, asegúrese de
que está descargando el archivo de la libreta en bruto y no la página
HTML de GitHub que muestra la libreta.

1.  Ahora, haga clic en **RealTimeWorkspace** en el menú de navegación
    de la izquierda.

<img src="./media/image31.png" style="width:6.5in;height:7.25764in"
alt="A screenshot of a computer Description automatically generated" />

2.  En la página **RealTimeWorkspace de Synapse Data Engineering**,
    navegue y haga clic en el botón **Importar**, luego seleccione
    **Notebook** y seleccione **From this computer** como se muestra en
    la siguiente imagen.

<img src="./media/image32.png" style="width:6.5in;height:2.26667in" />

3.  Seleccione **Upload** en el panel de **Import status** que aparece
    en la parte derecha de la pantalla.

<img src="./media/image33.png" style="width:3.34861in;height:2.93958in"
alt="A screenshot of a computer Description automatically generated" />

4.  Navegue y seleccione **Lakehouse 1-Import Data, Lakehouse 2-Build
    Aggregation, Lakehouse 3-Create Star Schema** y **Lakehouse 4-Load
    Star Schema** notebooks desde **C:\LabFiles\Lab 04** y haga clic en
    el botón **Open**.

<img src="./media/image34.png" style="width:6.49236in;height:4.07569in"
alt="A screenshot of a computer Description automatically generated" />

5.  Verá una notificación indicando **Imported successfully.**

<img src="./media/image35.png" style="width:7.22809in;height:2.15341in"
alt="A screenshot of a computer Description automatically generated" />

## Tarea 4. Importar datos adicionales

Para que los informes sean más interesantes, necesitamos un poco más de
datos con los que trabajar. Tanto para el módulo Lakehouse como para el
módulo Data Science, se pueden importar datos históricos adicionales
para complementar los datos ya ingestados. El cuaderno funciona mirando
los datos más antiguos de la tabla, añadiendo los datos históricos.

1.  En la página **RealTimeWorkspace**, para ver sólo los cuadernos,
    haga clic en el **Filter** situado en la esquina superior derecha de
    la página y, a continuación, seleccione **Notebook.**

<img src="./media/image36.png" style="width:7.31575in;height:2.94886in"
alt="A screenshot of a computer Description automatically generated" />

2.  A continuación, seleccione la libreta ***Lakehouse 1 - Import
    data***.

<img src="./media/image37.png" style="width:6.5in;height:3.56042in"
alt="A screenshot of a computer Description automatically generated" />

3.  En **Explorer**, navegue y seleccione el **Lakehouse**, luego haga
    clic en el botón **Add** como se muestra en las imágenes de abajo*.*

> **Nota importante**: tendrá que añadir el Lakehouse a cada cuaderno
> importado; hágalo cada vez que abra un cuaderno por primera vez.

<img src="./media/image38.png" style="width:6.49236in;height:4.90139in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image39.png" style="width:6.5in;height:6.49236in"
alt="A screenshot of a computer Description automatically generated" />

4.  En el cuadro de diálogo **Add Lakehouse**, seleccione el botón de
    opción **Existing** **Lakehouse** y, a continuación, haga clic en el
    botón **Add**.

<img src="./media/image40.png" style="width:3.03056in;height:1.80278in"
alt="A screenshot of a computer Description automatically generated" />

5.  En la ventana **OneLake data hub**, seleccione **StockLakehouse** y
    haga clic en el botón **Add**.
    <img src="./media/image41.png" style="width:6.48472in;height:3.90139in"
    alt="A screenshot of a computer Description automatically generated" />

6.  La tabla **raw_stock_data** se creó cuando se configuró el
    Eventstream, y es el lugar de destino de los datos que se ingieren
    desde el Event Hub.

<img src="./media/image42.png" style="width:6.49236in;height:4.28819in"
alt="A screenshot of a computer Description automatically generated" />

**Nota**: Verá el botón **Run** cuando pase el ratón por encima de la
celda en el bloc de notas.

7.  Para iniciar el bloc de notas y ejecutar la celda, seleccione el
    icono **Run** que aparece a la izquierda de la celda.

<img src="./media/image43.png" style="width:6.49236in;height:3.37847in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image44.png" style="width:6.5in;height:3.2in"
alt="A screenshot of a computer Description automatically generated" />

8.  Del mismo modo, ejecute las células 2<sup>nd</sup> y 3<sup>rd</sup>
    .

<img src="./media/image45.png" style="width:6.49236in;height:2.79514in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image46.png" style="width:6.80272in;height:3.7822in"
alt="A screenshot of a computer Description automatically generated" />

9.  Para descargar y descomprimir los datos históricos en los archivos
    no gestionados de Lakehouse, ejecute las celdas 4<sup>th</sup> y 5
    <sup>thd</sup> como se muestra en las siguientes imágenes.

<img src="./media/image47.png" style="width:7.01705in;height:4.94792in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image48.png" style="width:7.3826in;height:4.95644in"
alt="A screenshot of a computer program Description automatically generated" />

<img src="./media/image49.png" style="width:6.72454in;height:4.47297in"
alt="A screenshot of a computer Description automatically generated" />

10. Para comprobar que los archivos csv están disponibles, seleccione y
    ejecute la celda 6<sup>th</sup> .

<img src="./media/image50.png" style="width:6.49236in;height:3.28056in"
alt="A screenshot of a computer Description automatically generated" />

11. Ejecute la célula 7<sup>th</sup> , la célula 8<sup>th</sup> y la
    célula 9<sup>th</sup> .

<img src="./media/image51.png" style="width:6.49236in;height:4.12847in"
alt="A screenshot of a computer program Description automatically generated" />

<img src="./media/image52.png" style="width:6.5in;height:2.73472in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image53.png" style="width:6.49236in;height:2.94722in"
alt="A screenshot of a computer Description automatically generated" />

12. Aunque es similar a "comentar" secciones de código, congelar celdas
    tiene la ventaja de que también se conserva cualquier salida de las
    celdas.

<img src="./media/image54.png"
style="width:6.49167in;height:4.48333in" />

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

<img src="./media/image31.png" style="width:5.22948in;height:4.2875in"
alt="A screenshot of a computer Description automatically generated" />

2.  En la página **RealTimeWorkspace**, haga clic en el cuaderno
    **Lakehouse 2 - Build Aggregation Tables**.

<img src="./media/image55.png" style="width:6.49236in;height:3.4125in"
alt="A screenshot of a computer Description automatically generated" />

3.  En Explorador, navegue y seleccione el **Lakehouse**, luego haga
    clic en el botón **Add**.

<img src="./media/image56.png" style="width:6.57917in;height:3.63125in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image57.png" style="width:5.43674in;height:6.2125in"
alt="A screenshot of a computer Description automatically generated" />

4.  En el cuadro de diálogo **Add Lakehouse**, seleccione el cuadro de
    diálogo **Existing** **Lakehouse** y, a continuación, haga clic en
    el botón **Add**.

<img src="./media/image40.png" style="width:2.52083in;height:1.49956in"
alt="A screenshot of a computer Description automatically generated" />

5.  En la ventana **OneLake datahub**, seleccione **StockLakehouse** y
    haga clic en el botón **Add**.

<img src="./media/image41.png" style="width:6.48472in;height:3.90139in"
alt="A screenshot of a computer Description automatically generated" />

6.  Para construir Aggregate Tables, seleccione y ejecute las celdas
    1<sup>st</sup> , 2<sup>nd</sup> , 3<sup>rd</sup> , y 4<sup>th</sup>
    .

<img src="./media/image58.png" style="width:6.5in;height:3.38611in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image59.png" style="width:7.22159in;height:4.35147in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image60.png" style="width:7.13503in;height:3.55492in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image61.png" style="width:6.5in;height:3.34097in"
alt="A screenshot of a computer Description automatically generated" />

7.  A continuación, seleccione y ejecute las celdas 5<sup>th</sup> ,
    6<sup>th</sup> , 7<sup>th</sup> y 8<sup>th</sup> .

<img src="./media/image62.png" style="width:7.22721in;height:3.55492in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image62.png" style="width:7.0625in;height:3.47391in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image63.png" style="width:6.49236in;height:4.58333in"
alt="A screenshot of a computer program Description automatically generated" />

<img src="./media/image64.png" style="width:6.96401in;height:4.79669in"
alt="A screenshot of a computer Description automatically generated" />

8.  Añada Data Wrangler, seleccione la celda **9<sup>th</sup>** ,
    navegue por el desplegable **Data Wrangler**. Navegue y haga clic en
    **anomaly_df** para cargar el dataframe en Data Wrangler**.**

9.  Utilizaremos *anomaly_df* porque se creó intencionadamente con
    algunas filas no válidas que se pueden probar.

<img src="./media/image65.png" style="width:6.49167in;height:3.575in"
alt="A screenshot of a computer Description automatically generated" />

10. En Data Wrangler, registraremos una serie de pasos para procesar los
    datos. Observa que los datos se visualizan en la columna central.
    Las operaciones están en la parte superior izquierda, mientras que
    un resumen de cada paso está en la parte inferior izquierda.

<img src="./media/image66.png" style="width:7.31131in;height:3.66856in"
alt="A screenshot of a computer Description automatically generated" />

11. Para eliminar valores nulos/vacíos, en *Operaciones*, haga clic en
    el desplegable junto a **Find and replace**, luego navegue y haga
    clic en **Drop missing values**.

<img src="./media/image67.png" style="width:7.30724in;height:3.68371in"
alt="A screenshot of a computer Description automatically generated" />

12. En el desplegable **Target columns**, seleccione las columnas de
    ***símbolo*** y ***precio*** y, a continuación, haga clic en el
    **botón Apply** situado debajo, como se muestra en la imagen.

<img src="./media/image68.png" style="width:7.29469in;height:4.49432in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image69.png" style="width:6.71401in;height:4.10189in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image70.png" style="width:7.12291in;height:4.21401in"
alt="A screenshot of a computer Description automatically generated" />

13. En el menú desplegable **Operations**, desplácese y haga clic en
    **Sort and filter** y, a continuación, en **Filter**, como se
    muestra en la imagen siguiente.

> <img src="./media/image71.png" style="width:5.89759in;height:4.3375in"
> alt="A screenshot of a computer Description automatically generated" />

14. **Desmarque** *Conservar filas coincidentes*, seleccione **price**
    como columna de destino y establezca la condición ***Equal to*
    *0*.** Haga clic en ***Apply*** en el panel *Operations* situado
    debajo de Filter

> Nota: Las filas con cero están marcadas en rojo, ya que se eliminarán
> (si las demás filas están marcadas en rojo, asegúrese de desmarcar la
> casilla *Keep matching rows*).

<img src="./media/image72.png"
style="width:6.49167in;height:4.35833in" />

<img src="./media/image73.png" style="width:6.5in;height:3.66667in"
alt="A screenshot of a computer Description automatically generated" />

15. Haga clic en **+ Add code to notebook** en la parte superior
    izquierda de la página. En la ventana **Add code to notebook** ,
    asegúrese de que la opción *Incluir código pandas* no está marcada y
    haga clic en el botón **Add**.

<img src="./media/image74.png" style="width:6.5in;height:3.86389in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image75.png" style="width:5.25in;height:4.05278in"
alt="A screenshot of a computer code Description automatically generated" />

16. El código insertado será similar al siguiente.

<img src="./media/image76.png"
style="width:6.49167in;height:3.39167in" />

17. Ejecute la celda y observe la salida. Observará que se han eliminado
    las filas no válidas.

> <img src="./media/image77.png" style="width:6.5in;height:3.40833in" />

<img src="./media/image78.png" style="width:6.5in;height:3.47639in"
alt="A screenshot of a computer Description automatically generated" />

La función creada, *clean_data*, contiene todos los pasos en secuencia y
puede modificarse según sea necesario. Observe que cada paso realizado
en Data Wrangler está comentado. Debido a que Data Wrangler fue cargado
con la *anomalía_df*, el método está escrito para tomar ese marco de
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

20. Esta función eliminará ahora las filas inválidas de nuestro marco de
    datos *df_stocks* y devolverá un nuevo marco de datos llamado
    *df_stocks_clean*. Es común utilizar un nombre diferente para el
    marco de datos de salida (como *df_stocks_clean*) para hacer la
    celda idempotente -- de esta manera, podemos volver atrás y volver a
    ejecutar la celda, hacer modificaciones, etc., sin tener que volver
    a cargar nuestros datos originales.

<img src="./media/image80.png" style="width:5.82917in;height:4.55185in"
alt="A screenshot of a computer Description automatically generated" />

## Tarea 2: Crear una rutina de agregación

En esta tarea, estarás más involucrado porque construiremos una serie de
pasos en Data Wrangler, añadiendo columnas derivadas y agregando los
datos. Si te quedas atascado, continúa lo mejor que puedas y utiliza el
código de ejemplo en el cuaderno para ayudar a solucionar cualquier
problema después.

1.  Añada una nueva columna datestamp en la *Sección de
    **Symbol/Date/Hour/Minute Aggregation***, coloque el cursor en la
    celda *Add Data Wrangler here* y seleccione la celda. Despliegue el
    **Data Wrangler**. Navegue y haga clic en **df_stocks_clean** como
    se muestra en la siguiente imagen.

> <img src="./media/image81.png"
> style="width:6.49167in;height:4.31667in" />
>
> <img src="./media/image82.png" style="width:6.5in;height:2.98472in"
> alt="A screenshot of a computer Description automatically generated" />

2.  En el panel **Data Wrangler:df_stocks_clean**, seleccione
    **Operations** y, a continuación, **New column by example.**

> <img src="./media/image83.png" style="width:6.5in;height:3.59167in" />

3.  En el campo ***Target columns***, haga clic en el desplegable y
    seleccione ***timestamp***. A continuación, en el campo ***Derived
    columna name***, introduce ***+++datestamp+++***.

<img src="./media/image84.png" style="width:6.5in;height:4.01667in" />

4.  En la nueva columna ***timestamp***, introduzca un valor de ejemplo
    para cualquier fila. Por ejemplo, si la *fecha* es *2024-02-07
    09:54:00* introduzca ***2024-02-07***. Esto permite a Data Wrangler
    deducir que estamos buscando la fecha sin un componente de tiempo;
    una vez que las columnas se autocompleten, haga clic en el botón
    ***Apply***.

<img src="./media/image85.png"
style="width:7.16408in;height:3.74583in" />

<img src="./media/image86.png"
style="width:7.37696in;height:3.86502in" />

5.  De forma similar a la adición de la columna **datestamp** como se
    mencionó en los pasos anteriores, haga clic de nuevo en **New
    columna by example** como se muestra en la siguiente imagen.

<img src="./media/image87.png" style="width:6.5in;height:3.65833in" />

6.  En *Columnas de destino*, seleccione ***Timestamp***. Introduzca un
    *nombre de **Derived column ***de ***+++hour+++*.**

> <img src="./media/image88.png"
> style="width:6.49167in;height:3.81667in" />

7.  En la nueva columna de **hour** que aparece en la vista previa de
    datos, introduzca una hora para cualquier fila, pero intente elegir
    una fila que tenga un valor de hora único. Por ejemplo, si la *hora*
    es *2024-02-07 09:54:00* introduzca ***9***. Puede que necesite
    introducir valores de ejemplo para varias filas, como se muestra
    aquí. Pulse el botón **Apply**.

> <img src="./media/image89.png"
> style="width:6.48333in;height:3.68333in" />

8.  Data Wrangler debería inferir que estamos buscando el componente
    hora, y construir código similar a:

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

 

9.  Al igual que con la columna de horas, cree una nueva columna de
    ***minute***. En la nueva columna de *minutos*, introduzca un minuto
    para cualquier fila. Por ejemplo, si la *timestamp* es *2024-02-07
    09:54:00* introduzca *54*. Puede que necesite introducir valores de
    ejemplo para varias filas.

<img src="./media/image91.png"
style="width:6.49167in;height:3.30833in" />

10. El código generado debería ser similar a:

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

11. A continuación, convierta la columna de horas en un número entero.
    Haga clic en la **elipsis (...)** en la esquina de la columna de
    *horas* y seleccione ***Change columna type***. Haga clic en el menú
    desplegable junto a ***New type***, navegue y seleccione
    ***int32**,* a continuación, haga clic en el **botón *Apply ***como
    se muestra en la siguiente imagen***.***
    <img src="./media/image93.png"
    style="width:7.07552in;height:2.97917in" />

<img src="./media/image94.png"
style="width:7.25584in;height:3.99583in" />

12. Convierta la columna de los minutos en un número entero siguiendo
    los mismos pasos que acaba de realizar para la hora. Haga clic en la
    **elipsis (...)** en la esquina de la **columna** de ***minutos*** y
    seleccione ***Change columna type***. Haga clic en el menú
    desplegable junto a ***New type***, navegue y seleccione
    ***int32**,* luego haga clic en el **botón *Apply ***como se muestra
    en la siguiente imagen***.***

<img src="./media/image95.png"
style="width:7.32227in;height:3.5125in" />

<img src="./media/image96.png"
style="width:7.31189in;height:4.34583in" />

<img src="./media/image97.png" style="width:7.28383in;height:4.14151in"
alt="A screenshot of a computer Description automatically generated" />

13. Ahora, en la sección **Operations**, navegue y haga clic en ***Group
    by and aggregate*** como se muestra en la siguiente imagen.

<img src="./media/image98.png"
style="width:6.87083in;height:4.95181in" />

14. Haga clic en el desplegable de ***Columns to group by*** campos y
    seleccione ***symbol*, *datestamp*, *hour*, *minute***.

<img src="./media/image99.png"
style="width:6.49167in;height:5.59167in" />

15. Haga clic en +*Add aggregation*, cree un total de tres agregaciones
    como se muestra en las siguientes imágenes y haga clic en el botón
    ***Apply***.

- price: Maximum

- price: Minimum

- price: Last value

<img src="./media/image100.png"
style="width:6.79583in;height:5.6552in" />

<img src="./media/image101.png"
style="width:7.41997in;height:3.59583in" />

16. Haga clic en **Add code to notebook**  en la esquina superior
    izquierda de la página. En la **ventana Add code to notebook**,
    asegúrese de que la opción *include pandas code* no está marcada y,
    a continuación, haga clic en el botón **Add**.

<img src="./media/image102.png"
style="width:6.49167in;height:3.54167in" />

<img src="./media/image103.png"
style="width:5.23333in;height:4.175in" />

<img src="./media/image104.png" style="width:7.16746in;height:5.2278in"
alt="A screenshot of a computer Description automatically generated" />

17. Revise el código, en la celda que se agrega, en las dos últimas
    líneas de la celda, observe que el marco de datos devuelto se llama
    ***df_stocks_clean_1***. Cambie el nombre a
    ***df_stocks_agg_minute***, y cambie el nombre de la función a
    ***aggregate_data_minute*,** como se muestra a continuación.

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

18. Código generado por Data Wrangler para la celda DataFrame de
    PySpark, seleccione el icono **Run** que aparece a la izquierda de
    la celda al pasar el ratón por encima.

<img src="./media/image106.png"
style="width:7.28644in;height:5.00417in" />

<img src="./media/image107.png"
style="width:6.49167in;height:3.05833in" />

<img src="./media/image108.png"
style="width:7.28579in;height:5.14255in" />

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

<img src="./media/image109.png" style="width:6.49167in;height:3.6in" />

## Tarea 3: Agregación horaria

Repasemos el progreso actual: nuestros datos por segundo se han limpiado
y, a continuación, se han resumido al nivel por minuto. Esto reduce
nuestro recuento de filas de 86.400 filas/día a 1.440 filas/día por
símbolo bursátil. Para los informes que podrían mostrar datos mensuales,
podemos agregar aún más los datos a la frecuencia por hora, reduciendo
los datos a 24 filas/día por símbolo bursátil.

1.  En el marcador de posición final de la sección *Símbolo/Fecha/Hora*,
    cargue el marco de datos ***df_stocks_agg_minute*** existente en
    Data Wrangler.

2.  En el marcador de posición final de la sección
    ***Symbol/Date/Hour***, coloque el cursor en la celda *Añadir Data
    Wrangler aquí* y seleccione la celda. Despliegue el **Data
    Wrangler,** navegue y haga clic en ***df_stocks_agg_minute*** como
    se muestra en la siguiente imagen.

<img src="./media/image110.png"
style="width:7.15417in;height:4.66536in" />

> <img src="./media/image111.png" style="width:6.5in;height:4.05625in"
> alt="A screenshot of a computer Description automatically generated" />

3.  En ***Operations*,** seleccione ***Group by and aggregate***. Haga
    clic en el menú desplegable debajo de ***Group by and aggregate***
    campo y seleccione ***symbol*, *datestamp*, and *hour***, y a
    continuación, haga clic en **+ Add aggregations**. Cree las tres
    agregaciones siguientes y haga clic en el botón Apply situado
    debajo, como se muestra en la imagen inferior.

- price_min: Minimum

- price_max: Maximum

- price_last: Last value

<img src="./media/image112.png"
style="width:7.2125in;height:4.82683in" />

<img src="./media/image113.png" style="width:7.23649in;height:3.51619in"
alt="A screenshot of a computer Description automatically generated" />

4.  A continuación se muestra un ejemplo de código. Además de cambiar el
    nombre de la función a *aggregate_data_hour*, también se ha cambiado
    el alias de cada columna de precio para mantener los mismos nombres
    de columna. Debido a que estamos agregando datos que ya han sido
    agregados, Data Wrangler está nombrando las columnas como
    precio_max_max, precio_min_min; modificaremos los alias para
    mantener los mismos nombres para mayor claridad.

<img src="./media/image114.png"
style="width:7.27027in;height:3.3625in" />

5.  Haga clic en **Add code to notebook** en la esquina superior
    izquierda de la página. En la ventana **Add code to notebook**,
    asegúrate de que la opción *Incluir código pandas* no está marcada y
    haz clic en el botón **Add**.

<img src="./media/image115.png"
style="width:6.49167in;height:3.06667in" />

<img src="./media/image116.png"
style="width:5.76667in;height:4.54167in" />

<img src="./media/image117.png" style="width:6.5in;height:2.44236in" />

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

2.  Seleccione y **ejecute** la celda.

<img src="./media/image119.png" style="width:6.5in;height:3.16667in"
alt="A screenshot of a computer Description automatically generated" />

3.  El código para combinar los datos agregados por horas se encuentra
    en la siguiente celda: **merge_hour_agg(df_stocks_agg_hour)**

4.  Ejecute la celda para completar la fusión. Hay algunas celdas de
    utilidad en la parte inferior para comprobar los datos de las
    tablas: explora un poco los datos y experimenta libremente.

<img src="./media/image120.png"
style="width:6.49167in;height:2.11667in" />

21. Utilice la sección **Handy SQL Commands for testing**, limpiar
    tablas para volver a ejecutarlas, etc. Seleccione y **Ejecute** las
    celdas en esta sección.

<img src="./media/image121.png" style="width:6.5in;height:3.875in" />

<img src="./media/image122.png" style="width:6.5in;height:4.08819in"
alt="A screenshot of a computer program Description automatically generated" />

<img src="./media/image123.png"
style="width:6.30833in;height:3.525in" />

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

<img src="./media/image124.png"
style="width:4.4375in;height:3.50417in" />

2.  En el espacio de trabajo RealTimeWorkshop, seleccione el cuaderno
    ***Lakehouse 3 - Create Star Schema***.

<img src="./media/image125.png"
style="width:6.42917in;height:3.82917in" />

3.  En el Explorador, navegue y haga clic en los **Lakehouses**, luego
    haga clic en el botón **Add***.*

<img src="./media/image126.png"
style="width:4.9625in;height:4.07407in" />

<img src="./media/image127.png" style="width:6.5in;height:6.125in" />

4.  En el cuadro de diálogo **Add Lakehouse**, seleccione el botón de
    opción **Existing** **Lakehouse** y, a continuación, haga clic en el
    botón **Add**.

<img src="./media/image40.png" style="width:2.52083in;height:1.49956in"
alt="A screenshot of a computer Description automatically generated" />

5.  En la ventana del concentrador de datos OneLake, seleccione
    **StockLakehouse** y haga clic en el botón **Add**.

<img src="./media/image41.png" style="width:6.48472in;height:3.90139in"
alt="A screenshot of a computer Description automatically generated" />

6.  Con el cuaderno cargado y el Lakehouse conectado, fíjese en el
    esquema de la izquierda. Además de la tabla **raw_stock_data**, debe
    haber las tablas **stocks_minute_agg** y **stocks_hour_agg**.

<img src="./media/image128.png"
style="width:4.20417in;height:5.125in" />

7.  Ejecute cada celda individualmente haciendo clic en el botón de
    **play** situado a la izquierda de cada celda para seguir el
    proceso.

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

8.  Cuando todas las celdas se hayan ejecutado correctamente, navegue
    hasta la sección **StocksLakehouse**, haga clic en la elipsis
    horizontal junto a **Tables (...)**, luego navegue y haga clic en
    ***Refresh*** como se muestra en la siguiente imagen.

<img src="./media/image138.png" style="width:6.49167in;height:6.3in" />

9.  Ahora, puede ver todas las tablas adicionales ***dim_symbol*,
    *dim_date* y *fact_stocks_daily_prices ***para nuestro modelo
    dimensional.

<img src="./media/image139.png"
style="width:5.775in;height:7.51667in" />

## Tarea 2: Cargar la tabla de hechos

Nuestra tabla de hechos contiene los precios diarios de las acciones (el
precio máximo, mínimo y de cierre), mientras que nuestras dimensiones
son para la fecha y los símbolos de las acciones (que pueden contener
detalles de la empresa y otra información). Aunque simple,
conceptualmente este modelo representa un esquema en estrella que puede
aplicarse a conjuntos de datos mayores.

1.  Ahora, haga clic en **RealTimeWorkspace** en el menú de navegación
    de la izquierda.

<img src="./media/image140.png"
style="width:5.74583in;height:4.87917in" />

2.  En el espacio de trabajo RealTimeWorkshop, seleccione el cuaderno
    ***Lakehouse 4 – Load fact table***.

<img src="./media/image141.png"
style="width:5.65417in;height:4.02917in" />

3.  En el Explorer, seleccione **Lakehouse** y haga clic en el botón
    **Add***.*

<img src="./media/image142.png"
style="width:5.2125in;height:3.74711in" />

<img src="./media/image143.png" style="width:6.49167in;height:5.55in" />

4.  En el cuadro de diálogo **Add Lakehouse**, seleccione el botón de
    opción **Existing** **Lakehouse** y, a continuación, haga clic en el
    botón **Add**.

<img src="./media/image40.png" style="width:2.52083in;height:1.49956in"
alt="A screenshot of a computer Description automatically generated" />

5.  En la pestaña del concentrador de datos OneLake, seleccione
    **StockLakehouse** y haga clic en el botón **Add**.

<img src="./media/image41.png" style="width:6.48472in;height:3.90139in"
alt="A screenshot of a computer Description automatically generated" />

6.  Seleccione y ejecute cada celda individualmente.

<img src="./media/image144.png" style="width:6.5in;height:3.3in" />

7.  La función añade símbolos a dim_symbol que pueden no existir en la
    tabla, seleccione y **ejecute** las celdas 2<sup>nd</sup> y
    3<sup>rd</sup> .

<img src="./media/image145.png" style="width:6.49167in;height:3.7in" />

<img src="./media/image146.png"
style="width:6.49167in;height:4.23333in" />

8.  Para obtener los nuevos datos de stock a ingestar, empezando por la
    marca de agua, seleccione y ejecute la celda 4<sup>th</sup> .

<img src="./media/image147.png"
style="width:6.49167in;height:4.19167in" />

9.  Cargue la dimensión fecha para posteriores uniones, seleccione y
    **Ejecute** las celdas 5<sup>th</sup> , 6<sup>th</sup> , y
    7<sup>th</sup> .

<img src="./media/image148.png"
style="width:6.49167in;height:4.26667in" />

<img src="./media/image149.png" style="width:6.5in;height:3.46389in" />

<img src="./media/image150.png" style="width:6.5in;height:4.77153in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image151.png"
style="width:7.14583in;height:3.99947in" />

10. Para unir los datos agregados a la dimensión fecha, seleccione y
    **Ejecute** las celdas 8<sup>th</sup> y 9<sup>th</sup> .

<img src="./media/image152.png"
style="width:7.24934in;height:3.22917in" />

<img src="./media/image153.png"
style="width:7.38358in;height:3.34583in" />

11. Cree una vista final con nombres limpios para facilitar el
    procesamiento, seleccione y **Ejecute** las celdas 10<sup>th</sup> ,
    11<sup>th</sup> , y 12<sup>th</sup> .

<img src="./media/image154.png"
style="width:7.24583in;height:3.34423in" />

<img src="./media/image155.png"
style="width:7.1862in;height:3.97083in" />

<img src="./media/image156.png"
style="width:7.16715in;height:3.40417in" />

12. Para obtener el resultado y trazar un gráfico, seleccione y
    **ejecute** 13<sup>th</sup> y 14<sup>th</sup> celdas.

<img src="./media/image157.png"
style="width:6.49167in;height:3.775in" />

<img src="./media/image158.png"
style="width:7.27514in;height:2.97917in" />

<img src="./media/image159.png" style="width:6.5in;height:3.12014in" />

13. Para validar las tablas creadas, haga clic con el botón derecho en
    la elipsis horizontal (...) junto a **Tables,** luego navegue y haga
    clic en **Refresh.** Aparecerán las tablas.

<img src="./media/image160.png"
style="width:6.49167in;height:6.49167in" />

14. Para programar la libreta para que se ejecute periódicamente, haga
    clic en la pestaña ***Run***, y haga clic en ***Schedule*** como se
    muestra en la siguiente imagen*.*

<img src="./media/image161.png"
style="width:6.49167in;height:3.99167in" />

15. En la pestaña Lackhouse 4-Load Star Schema, seleccione los
    siguientes datos y haga clic en el botón **Apply**.

- Schedule run: **On**

- Repeat**: Hourly**

- Every: **4 hours**

- Seleccione la fecha de hoy

<img src="./media/image162.png"
style="width:6.49167in;height:5.50833in" />

## Tarea 3: Construir un modelo semántico y un informe sencillo

En esta tarea, crearemos un nuevo modelo semántico que podremos utilizar
para la generación de informes, y crearemos un informe sencillo de Power
BI.

1.  Ahora, haz clic en **StocksLakehouse** en el menú de navegación de
    la izquierda.

<img src="./media/image163.png"
style="width:6.49167in;height:5.7625in" />

2.  En la ventana ***StocksLakehouse**,* navegue y haga clic en ***New
    semantic model*** en la barra de comandos.

<img src="./media/image164.png"
style="width:7.26479in;height:4.7375in" />

3.  Nombre el modelo ***StocksDimensionalModel*** y seleccione las
    tablas **fact_stocks_daily_prices**, **dim_date** y **dim_symbol**.
    A continuación, haga clic en el botón **Confirm**.

<img src="./media/image165.png" style="width:3.6in;height:5.54167in" />

<img src="./media/image166.png" style="width:6.5in;height:3.00694in"
alt="A screenshot of a computer Description automatically generated" />

4.  Cuando se abre el modelo semántico, tenemos que definir las
    relaciones entre las tablas de hechos y dimensiones.

5.  Desde la tabla **fact_Stocks_Daily_Prices**, arrastre el campo
    ***Symbol_SK*** y suéltelo en el campo ***Symbol_SK*** de la tabla
    **dim_Symbol** para crear una relación. Aparecerá el cuadro de
    diálogo **New relationship**.

<img src="./media/image167.png"
style="width:6.49167in;height:4.91667in" />

6.  En el cuadro de diálogo **Nueva relación**:

- **La tabla From** se rellena con **fact_Stocks_Daily_Prices** y la
  columna **Symbol_SK.**

- **La tabla To** se rellena con **dim_symbol** y la columna de
  **Symbol_SK**

- Cardinalidad: **Many to one (\*:1)**

- Dirección del filtro transversal: **Single**

- Deje seleccionada la casilla junto a **Make this relationship
  active**.

- Seleccione **Save.**

<img src="./media/image168.png"
style="width:5.58333in;height:6.125in" />

<img src="./media/image169.png" style="width:6.5in;height:3.11319in"
alt="A screenshot of a computer Description automatically generated" />

7.  Desde la tabla **fact_Stocks_Daily_Prices**, arrastre el campo
    **PrinceDateKey** y suéltelo sobre el campo ***DateKey*** de la
    tabla **dim_date** para crear una relación. Aparecerá el cuadro de
    diálogo **New relationship**.

<img src="./media/image170.png"
style="width:6.49167in;height:3.98333in" />

8.  En el cuadro de diálogo **New relationship:**

- **La tabla From** se rellena con **fact_Stocks_Daily_Prices** y la
  columna **PrinceDateKey.**

- **La tabla To** se rellena con **dim_date** y la columna de
  **DateKey**

- Cardinalidad: **Many to one (\*:1)**

- Dirección del filtro transversal: **Single**

- Deje seleccionada la casilla junto a **Make this relationship
  active**.

- Seleccione **Save.**

<img src="./media/image171.png" style="width:5.6in;height:6.21667in" />

<img src="./media/image172.png" style="width:6.5in;height:4.2625in"
alt="A screenshot of a computer Description automatically generated" />

9.  Haga clic en ***New Report*** para cargar el modelo semántico en
    Power BI.

<img src="./media/image173.png"
style="width:6.49167in;height:4.13333in" />

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

<img src="./media/image174.png"
style="width:5.7375in;height:6.75241in" />

11. En **Filters,** seleccione **PriceDateKey** e introduzca los
    siguientes datos. Haga clic en **Apply** filtro

- Tipo de filtro: **Relative date**

- Mostrar artículos cuando el valor: **is in the last 45 days**

<img src="./media/image175.png" style="width:5.35in;height:5.66667in" />

<img src="./media/image176.png"
style="width:7.36665in;height:3.81633in" />

12. En la cinta de opciones, seleccione **File** \> **Save as.**

<img src="./media/image177.png"
style="width:7.09665in;height:4.2125in" />

13. En el cuadro de diálogo Guardar su informe, introduzca +++
    **StocksDimensional** +++ como nombre de su informe y seleccione
    **su espacio de trabajo**. Haga clic en el botón **Save.**

<img src="./media/image178.png"
style="width:7.22083in;height:3.57797in" />

<img src="./media/image179.png" style="width:7.38292in;height:3.52187in"
alt="A screenshot of a computer Description automatically generated" />

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
