# Laboratorio 02: Uso de KQL y creación de informes

**Introducción**

Ahora que nuestros datos están fluyendo en nuestra base de datos KQL,
podemos empezar a consultar y explorar los datos, aprovechando KQL para
obtener información sobre los datos. Un conjunto de consultas KQL se
utiliza para ejecutar consultas, ver y transformar datos de una base de
datos KQL. Al igual que otros artefactos, un conjunto de consultas KQL
existe en el contexto de un espacio de trabajo. Un queryset puede
contener múltiples consultas, cada una almacenada en una pestaña. En
este ejercicio, crearemos varias consultas KQL de complejidad creciente
para soportar diferentes usos de negocio.

**Objetivos**

- Explorar datos de precios de acciones utilizando KQL, desarrollando
  progresivamente consultas para analizar tendencias, calcular
  diferenciales de precios y visualizar datos para obtener perspectivas
  procesables.

- Aprovechar Power BI para crear informes dinámicos en tiempo real
  basados en datos de existencias analizados, configurando ajustes de
  actualización automática para actualizaciones puntuales y mejorando la
  visualización para una toma de decisiones informada.

# Ejercicio 1: Exploración de los datos

En este ejercicio, creará varias consultas KQL de complejidad creciente
para dar soporte a diferentes usos empresariales.

## Tarea 1: Crear queryset KQL: StockQueryset

1.  Haga clic en **RealTimeWorkspace** en el panel de navegación de la
    izquierda.

      ![](./media/image1.png)
2.  Desde su área de trabajo, haga clic en ***+* New *\>* KQL Queryset**
    como se muestra en la imagen inferior. En el cuadro de diálogo New
    KQL Queryset, introduzca *+++StockQueryset+++* y, a continuación,
    haga clic en el botón **Create**.

     ![](./media/image2.png)
     ![](./media/image3.png)

3.  Seleccione el ***StockDB*** y haga clic en el botón **Connectar**.
      ![](./media/image4.png)

4.  Se abrirá la ventana de consulta KQL, que le permitirá consultar los
    datos.

       ![](./media/image5.png)

5.  El código de consulta por defecto se parecerá al código mostrado en
    la siguiente imagen; contiene 3 consultas KQL distintas. Es posible
    que vea *YOUR_TABLE_HERE* en lugar de la tabla ***StockPrice***.
    Selecciónelas y elimínelas.

     ![](./media/image5.png)

6.  En el editor de consultas, copie y pegue el siguiente código.
    Seleccione todo el texto y haga clic en el botón ***Run*** para
    ejecutar la consulta. Una vez ejecutada la consulta, verá los
    resultados.
```
// Use "take" to view a sample number of records in the table and check the data.
StockPrice
| take 100;

// See how many records are in the table.
StockPrice
| count;

// This query returns the number of ingestions per hour in the given table.
StockPrice
| summarize IngestionCount = count() by bin(ingestion_time(), 1h);

```

**Nota:** Para ejecutar una sola consulta cuando hay varias en el
editor, puede resaltar el texto de la consulta o colocar el cursor de
modo que se encuentre en el contexto de la consulta (por ejemplo, al
principio o al final de la consulta) -- la consulta actual debería
resaltarse en azul. Para ejecutar la consulta, haga clic en* Run *en la
barra de herramientas. Si desea ejecutar las 3 para mostrar los
resultados en 3 tablas diferentes, cada consulta deberá tener un punto y
coma (;) después de la sentencia, como se muestra a continuación.*
    ![](./media/image6.png)

7.  Los resultados se mostrarán en 3 tablas diferentes como se muestra
    en la imagen inferior. Haga clic en cada pestaña de la tabla para
    revisar los datos.

     ![](./media/image7.png)
     ![](./media/image8.png)
     ![](./media/image9.png)
## Tarea 2: Nueva consulta de StockByTime

1.  Cree una nueva pestaña dentro del conjunto de consultas haciendo
    clic en el **icono +**como se muestra en la siguiente imagen.
    Renombre esta pestaña como +++StockByTime+++.

      ![](./media/image10.png)
      ![](./media/image11.png)
      ![](./media/image12.png)

2.  Podemos empezar a añadir nuestros propios cálculos, como calcular el
    cambio a lo largo del tiempo. Por ejemplo, la función
    [prev()](https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/prevfunction),
    un tipo de windowing function, nos permite consultar los valores de
    las filas anteriores; podemos utilizarla para calcular la variación
    del precio. Además, como los valores de precios anteriores son
    específicos de cada símbolo bursátil, podemos
    [dividir](https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/partition-operator)
    los datos al hacer los cálculos.

3.  En el editor de consultas, copie y pegue el siguiente código. Haga
    clic en el botón **Ejecutar** para ejecutar la consulta. Una vez
    ejecutada la consulta, verá los resultados.
```
StockPrice
| where timestamp > ago(75m)
| project symbol, price, timestamp
| partition by symbol
(
    order by timestamp asc
    | extend prev_price = prev(price, 1)
    | extend prev_price_10min = prev(price, 600)
)
| where timestamp > ago(60m)
| order by timestamp asc, symbol asc
| extend pricedifference_10min = round(price - prev_price_10min, 2)
| extend percentdifference_10min = round(round(price - prev_price_10min, 2) / prev_price_10min, 4)
| order by timestamp asc, symbol asc
```
   ![](./media/image13.png)
4.  En esta consulta KQL, los resultados se limitan primero a los 75
    minutos más recientes. Aunque en última instancia limitamos las
    filas a los últimos 60 minutos, nuestro conjunto de datos inicial
    necesita datos suficientes para buscar valores anteriores. A
    continuación, los datos se dividen para agruparlos por símbolo, y
    consultamos el precio anterior (de hace 1 segundo) y el precio
    anterior de hace 10 minutos. Tenga en cuenta que esta consulta
    supone que los datos se generan a intervalos de 1 segundo. Para los
    fines de nuestros datos, las fluctuaciones sutiles son aceptables.
    Sin embargo, si necesita precisión en estos cálculos (por ejemplo,
    hace exactamente 10 minutos y no las 9:59 o las 10:01), tendría que
    enfocar esto de otra manera.

## Tarea 3: StockAggregate

1.  Cree otra pestaña nueva dentro del conjunto de consultas haciendo
    clic en el **icono +**como se muestra en la imagen inferior.
    Renombre esta pestaña como **+++StockAggregate+++.**

      ![](./media/image14.png)
      ![](./media/image15.png)

2.  Esta consulta buscará las mayores ganancias de precio en un periodo
    de 10 minutos para cada acción, y la hora en que se produjo. Esta
    consulta utiliza el operador
    [summarize](https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/summarizeoperator),
    que produce una tabla que agrega la tabla de entrada en grupos
    basados en los parámetros especificados (en este caso, *símbolo*),
    mientras que
    [arg_max](https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/arg-max-aggregation-function)
    devuelve el mayor valor.

3.  En el editor de consultas, copie y pegue el siguiente código. Haga
    clic en el botón **Ejecutar** para ejecutar la consulta. Una vez
    ejecutada la consulta, verá los resultados.
```
StockPrice
| project symbol, price, timestamp
| partition by symbol
(
    order by timestamp asc
    | extend prev_price = prev(price, 1)
    | extend prev_price_10min = prev(price, 600)
)
| order by timestamp asc, symbol asc
| extend pricedifference_10min = round(price - prev_price_10min, 2)
| extend percentdifference_10min = round(round(price - prev_price_10min, 2) / prev_price_10min, 4)
| order by timestamp asc, symbol asc
| summarize arg_max(pricedifference_10min, *) by symbol
```
![](./media/image16.png)
![](./media/image17.png)

## Tarea 4: StockBinned

1.  Cree otra pestaña nueva dentro del conjunto de consultas haciendo
    clic en el **icono +**como se muestra en la imagen inferior.
    Cambie el nombre de esta pestaña a **+++StockBinned+++**.

     ![](./media/image18.png)
     ![](./media/image19.png)

2.  KQL también dispone de una [función
    bin()](https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/bin-function),
    que puede utilizarse para agrupar los resultados en función del
    parámetro bin. En este caso, al especificar una marca de tiempo de 1
    hora, el resultado se agrega para cada hora. El periodo de tiempo
    puede establecerse en minutos, horas, días, etc.

3.  En el editor de consultas, copie y pegue el siguiente código. Haga
    clic en el botón **Run** para ejecutar la consulta. Una vez
    ejecutada la consulta, verá los resultados.
```
StockPrice
| summarize avg(price), min(price), max(price) by bin(timestamp, 1h), symbol
| sort by timestamp asc, symbol asc
```

     ![](./media/image20.png)

4.  Esto resulta especialmente útil cuando se crean informes que agregan
    datos en tiempo real a lo largo de un periodo de tiempo más largo.

## Tarea 5: Visualizaciones

1.  Cree una nueva pestaña dentro del conjunto de consultas haciendo
    clic en el **icono +**como se muestra en la siguiente imagen.
    Renombra esta pestaña como **+++Visualizations+++**. Utilizaremos
    esta pestaña para explorar la visualización de datos.

     ![](./media/image21.png)
     ![](./media/image22.png)

2.  KQL soporta un gran número de
    [visualizaciones](https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/render-operator?pivots=fabric)
    utilizando el operador render. Ejecute la siguiente consulta, que es
    la misma que la consulta StockByTime, pero con una operación de
    *renderización* adicional añadida:

3.  En el editor de consultas, copie y pegue el siguiente código. Haga
    clic en el botón **Run** para ejecutar la consulta. Una vez
    ejecutada la consulta, verá los resultados.
```
StockPrice
| where timestamp > ago(75m)
| project symbol, price, timestamp
| partition by symbol
(
    order by timestamp asc
    | extend prev_price = prev(price, 1)
    | extend prev_price_10min = prev(price, 600)
)
| where timestamp > ago(60m)
| order by timestamp asc, symbol asc
| extend pricedifference_10min = round(price - prev_price_10min, 2)
| extend percentdifference_10min = round(round(price - prev_price_10min, 2) / prev_price_10min, 4)
| order by timestamp asc, symbol asc
| render linechart with (series=symbol, xcolumn=timestamp, ycolumns=price)
```
   ![](./media/image23.png)

4.  Esto generará un gráfico de líneas como se muestra en la siguiente
    imagen.

     ![](./media/image24.png)

# Ejercicio 2: Optimización de la eficacia de los informes de Power BI

Con los datos cargados en la base de datos y nuestro conjunto de
consultas KQL inicial completo, podemos empezar a elaborar
visualizaciones para cuadros de mando en tiempo real.

## Tarea 1: Configuración de la frecuencia de actualización

Nuestro tenant de Power BI necesita ser configurado para permitir una
actualización más frecuente.

1.  Para configurar este ajuste, navegue hasta el portal de
    administración de Power BI haciendo clic en el icono **Settings**
    situado en la esquina superior derecha del **portal Fabric**. Vaya a
    la sección Governance and insights y, a continuación, haga clic en
    **Admin portal**.

     ![](./media/image25.png)

2.  En la página **del portal Admin**, navegue y haga clic en **Capacity
    settings** y, a continuación, en la pestaña **trial**. Haga clic en
    el nombre de su capacidad.

      ![](./media/image26.png)>

3.  Desplácese hacia abajo y haga clic en ***Power BI workloads***, y en
    ***Semantic Models*** (recientemente rebautizados como Conjuntos de
    datos), configure ***Automatic page refresh*** a **On**, con un
    **minimum refresh interval** de **1 segundo**. A continuación, haga
    clic en el botón **Apply**.

**Nota**: Dependiendo de sus permisos administrativos, es posible que
esta configuración no esté disponible. Tenga en cuenta que este cambio
puede tardar varios minutos en completarse.
     ![](./media/image27.png)
     ![](./media/image28.png)

4.  En el cuadro de diálogo **Update your capacity workloads**, haga
    clic en el botón **Yes**.

      ![](./media/image29.png)

## Tarea 2: Creación de un informe básico de Power BI

1.  En la barra de menús de la página **Microsoft Fabric**, a la
    izquierda, seleccione **StockQueryset**.

     ![](./media/image30.png)

2.  Desde el queryset ***StockQueryset*** utilizado en el módulo
    anterior, seleccione la pestaña de consulta ***StockByTime***.

      ![](./media/image31.png)

3.  Seleccione la consulta y ejecútela para ver los resultados. Haga
    clic en el botón ***Build Power BI report*** de la barra de comandos
    para llevar esta consulta a Power BI.

     ![](./media/image32.png)
     ![](./media/image33.png)

5.  En la página de vista previa del informe, podemos configurar nuestro
    gráfico inicial, seleccionar un **gráfico de líneas** para la
    superficie de diseño y configurar el informe como se indica a
    continuación. Véase la imagen siguiente como referencia.

Legend: symbol

X-axis: timestamp

Y-axis: price
    ![](./media/image34.png)

5.  En la página Power BI (vista previa), en la cinta de opciones, haga
    clic en **file** y seleccione **Save**.

     ![](./media/image35.png)

6.  En el **primer** cuadro de diálogo, en el campo **Name your file in
    Power BI**, introduzca +++RealTimeStocks+++. En el campo **Save it
    to a workspace**, haga clic en el desplegable y seleccione
    **RealTimeWorkspace**. A continuación, haga clic en el botón
    **Continue.**

     ![](./media/image36.png)

7.  En la página de Power BI (preview), haga clic en **Open the file in
    Power BI to view, edit and get a shareable link.**

     ![](./media/image37.png)

8.  En la página **RealTimeStock**, haga clic en el botón **Edit** de la
    barra de comandos para abrir el editor de informes.

      ![](./media/image38.png)

9.  Seleccione el gráfico de líneas en el informe. Configure un
    **Filtro** por **marca de tiempo** para mostrar los datos de los
    últimos 5 minutos utilizando estos ajustes:

- Filter type: Relative time

- Show items when the value: is in the last 5 minutes

Haga clic en **Apply filter** para activar el filtro. Verá un tipo de
salida similar al que se muestra en la siguiente imagen.
     ![](./media/image39.png)

## Tarea 3: Crear un segundo visual para el cambio porcentual

1.  Cree un segundo gráfico de **líneas**, en **Visualizations**,
    seleccione **Gráfico de líneas**.

2.  En lugar de trazar el precio actual de la acción, seleccione el
    valor ***percentdifference_10min***, que es un valor positivo o
    negativo basado en la diferencia entre el precio actual y el valor
    del precio de hace 10 minutos. Utilice estos valores para el
    gráfico:

- Legend: **symbol**

- X-axis: **timestamp**

- Y-axis: **average of percentdifference_10min**
      ![](./media/image40.png)

     ![](./media/image41.png)
3.  En la **Visualization,** seleccione el **Analytics** representado
    por un icono en forma de lupa como se muestra en la imagen de abajo,
    a continuación, haga clic en **Y-Axis Constant Line(1).** En la
    sección **Apply settings a,** haga clic en **+Add line** e
    introduzca **el valor 0**
      ![](./media/image42.png)

4.  Seleccione el gráfico de líneas en el informe. Configure un
    **Filter** por ***Timestamp*** para mostrar los datos de los últimos
    5 minutos utilizando estas opciones:

- Filter type: Relative time

- Show items when the value: is in the last 5 minutes

     ![](./media/image43.png)

## Tarea 4: Configurar el informe para que se actualice automáticamente

1.  Deseleccione el gráfico. En la **Visualizations settings**, active
    ***Page refresh*** para que se actualice automáticamente cada uno o
    dos segundos, según sus preferencias. Por supuesto, siendo
    realistas, tenemos que equilibrar las implicaciones de rendimiento
    de la frecuencia de actualización, la demanda de los usuarios y los
    recursos del sistema.

2.  Haga clic en el icono **Format your report** **page**, navegue y
    haga clic en **Page refresh**. Encienda el interruptor. Establezca
    el valor de actualización automática de la página en **2 seconds**,
    como se muestra en la siguiente imagen.

      ![](./media/image44.png)

3.  En la página Power BI (vista previa), en la cinta de opciones, haga
    clic en **File** y seleccione **Save**.

      ![](./media/image45.png)

**Resumen**

En este laboratorio, usted se embarcó en una exploración exhaustiva de
datos de precios de acciones utilizando KQL (Kusto Query Language).
Comenzando con la creación de un conjunto de consultas KQL llamado
"StockQueryset", ejecutó una serie de consultas cada vez más complejas
para analizar diversas facetas de los datos. Desde la visualización de
registros de muestra hasta el cálculo de diferenciales de precios a lo
largo del tiempo y la identificación de ganancias significativas de
precios, cada consulta desvela información valiosa sobre la dinámica de
los precios de las acciones. Aprovechando las funciones de ventana, las
técnicas de agregación y la partición de datos, ha obtenido una
comprensión más profunda de las tendencias y fluctuaciones de los
precios de las acciones.

A continuación, ha cambiado el enfoque a la optimización de la
eficiencia de los informes de Power BI, donde ha configurado las tasas
de actualización y ha creado visualizaciones dinámicas para cuadros de
mando en tiempo real. Al configurar las frecuencias de actualización en
el portal de administración de Power BI y crear informes de Power BI
basados en las consultas KQL definidas previamente, ha garantizado
actualizaciones puntuales y ha permitido una visualización detallada de
los datos de precios de las acciones. A través de tareas como la
creación de visualizaciones para el cambio porcentual y la configuración
de los ajustes de actualización automática, aprovechó todo el potencial
de Power BI para impulsar la toma de decisiones informadas y mejorar las
capacidades de inteligencia empresarial.
