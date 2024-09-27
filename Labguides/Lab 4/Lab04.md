# Laboratorio 04: Introducción a la creación de un ML Model en Fabric

**Introducción**

Para refrescar el escenario, AbboCost Financial ha estado modernizando
sus informes bursátiles para sus analistas financieros. En módulos
anteriores, hemos desarrollado la solución mediante la creación de
cuadros de mando en tiempo real, un almacén de datos y mucho más.

En este módulo, a AbboCost le gustaría explorar el análisis predictivo
para ayudar a informar a sus asesores. El objetivo es analizar los datos
históricos en busca de patrones que puedan utilizarse para crear
previsiones de valores futuros. La capacidad de Data Science en
Microsoft Fabric es el lugar ideal para hacer este tipo de exploración.

**Objetivos**

- Importar y configurar cuadernos para construir y almacenar modelos de
  machine learning.

- Explorar y ejecutar el cuaderno DS 1-Build Model para construir y
  validar un modelo de predicción de existencias.

- Examinar los metadatos del modelo y las métricas de rendimiento en el
  RealTimeWorkspace.

- Abrir y explorar el cuaderno DS 2-Predecir precios de acciones para
  predecir precios de acciones.

- Ejecutar el cuaderno para la generación de predicciones y almacenarlas
  en el Lakehouse.

- Importar y explorar el cuaderno DS 3-Forecast All para generar
  predicciones y almacenarlas en el Lakehouse.

- Ejecutar el cuaderno y verificar las predicciones en el esquema de
  Lakehouse.

- Construir un modelo semántico en Power BI utilizando los datos de
  StockLakehousePredictions y crear visualizaciones en Power BI para
  analizar las predicciones bursátiles y las tendencias del mercado.

- Configurar las relaciones entre tablas y publicar el informe de
  predicción.

# Ejercicio 1: Creación y almacenamiento de un modelo ML

## Tarea -1: Importar el cuaderno

1.  En la página **StockDimensionalModel**, haga clic en
    **RealTimeWorkspace** en el menú de navegación de la izquierda.

<img src="./media/image1.png"
style="width:4.75417in;height:4.19583in" />

2.  En la página **RealTimeWorkspace de Synapse Data Engineering**,
    navegue y haga clic en el botón **Import**, luego seleccione
    **Notebook** y seleccione **From this computer.**

<img src="./media/image2.png" style="width:6.5in;height:2.26667in"
alt="A screenshot of a computer Description automatically generated" />

3.  En el panel de **Import status** que aparece a la derecha, haga clic
    en **Upload** .

<img src="./media/image3.png"
style="width:5.55833in;height:4.40833in" />

4.  Navegue a **C:\LabFiles\Lab 05** y seleccione **DS 1-Build Model, DS
    2-Predict Stock Prices y DS 3-Forecast All** notebooks, luego haga
    click en el botón **Open**.

<img src="./media/image4.png" style="width:6.5in;height:2.93681in" />

5.  Verá una notificación que dice – **Imported successfully.**

<img src="./media/image5.png" style="width:3.9625in;height:1.72083in" />

<img src="./media/image6.png" style="width:6.5in;height:4.78333in" />

6.  En el **RealTimeWorkspace**, haga clic en el cuaderno **DS 1-Build
    Model**.

<img src="./media/image7.png" style="width:6.49167in;height:4.2in" />

7.  En el Explorer, seleccione **Lakehouse** y haga clic en el **botón
    Add.**

> ***Importante*:** Tendrá que añadir el Lakehouse a cada cuaderno
> importado -- hágalo cada vez que abra un cuaderno por primera vez.

<img src="./media/image8.png"
style="width:5.80417in;height:3.60113in" />

<img src="./media/image9.png" style="width:6.5in;height:3.70833in" />

8.  En el cuadro de diálogo **Add Lakehouse**, seleccione el botón de
    opción **Existing** **Lakehouse** y, a continuación, haga clic en el
    botón **Add**.

<img src="./media/image10.png"
style="width:3.00833in;height:1.83333in" />

9.  En la ventana del concentrador de datos OneLake, seleccione
    ***StockLakehouse*** y haga clic en el botón **Add**.

<img src="./media/image11.png"
style="width:6.49167in;height:3.91667in" />

<img src="./media/image12.png" style="width:6.5in;height:3.66667in" />

## Tarea 2: Explorar y ejecutar el bloc de notas

El cuaderno *DS 1* está documentado a lo largo de todo el cuaderno, pero
en resumen, el cuaderno lleva a cabo las siguientes tareas:

- Nos permite configurar una acción para analizar (como OMS o IDGD)

- Descarga de datos históricos para analizar en formato CSV

- Lee los datos en un marco de datos

- Realiza algunas tareas básicas de limpieza de datos

- Carga [Prophet](https://facebook.github.io/prophet/), un módulo de
  análisis y predicción de series temporales

- Construye un modelo basado en datos históricos

- Valida el modelo

- Almacena el modelo mediante MLflow

- Completa una predicción del futuro

La rutina que genera los datos de existencias es en gran medida
aleatoria, pero hay algunas tendencias que deberían surgir. Como no
sabemos cuándo vas a hacer este laboratorio, hemos generado datos de
varios años. El cuaderno cargará los datos y truncará los datos futuros
cuando construya el modelo. Como parte de una solución global,
complementaremos los datos históricos con nuevos datos en tiempo real en
nuestro Lakehouse, reentrenando el modelo según sea necesario
(diariamente/semanalmente/mensualmente).

1.  En 1 celda de<sup>st</sup> descomente el STOCK_SYMBOL="IDGD" y
    STOCK_SYMBOL="BCUZ", luego seleccione y **Ejecute** la celda.

<img src="./media/image13.png"
style="width:6.97485in;height:3.77917in" />

2.  Haga clic en ***Run all*** en la barra de herramientas superior y
    sigue el progreso del trabajo.

3.  El cuaderno tardará unos **15-20** minutos en ejecutarse -- algunos
    pasos, como el **training the model** y **cross validation**,
    llevarán tiempo.

<img src="./media/image14.png"
style="width:7.24624in;height:4.32917in" />

<img src="./media/image15.png" style="width:6.5in;height:3.90694in" />

<img src="./media/image16.png" style="width:7.23529in;height:4.5638in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image17.png"
style="width:6.2625in;height:4.02245in" />

<img src="./media/image18.png" style="width:6.5in;height:3.35in" />

<img src="./media/image19.png"
style="width:6.49167in;height:3.79167in" />

<img src="./media/image20.png"
style="width:6.49167in;height:5.55833in" />

<img src="./media/image21.png"
style="width:6.49167in;height:3.85833in" />

<img src="./media/image22.png"
style="width:6.49167in;height:3.35833in" />

<img src="./media/image23.png"
style="width:6.49167in;height:2.69167in" />

<img src="./media/image24.png" style="width:6.5in;height:4.88819in"
alt="A screen shot of a computer Description automatically generated" />

<img src="./media/image25.png" style="width:6.5in;height:4.16111in"
alt="A screen shot of a graph Description automatically generated" />

<img src="./media/image26.png" style="width:6.5in;height:4.02222in" />

<img src="./media/image27.png" style="width:6.5in;height:2.03125in"
alt="A graph of a graph Description automatically generated with medium confidence" />

<img src="./media/image28.png" style="width:7.28625in;height:2.73935in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image29.png" style="width:7.1626in;height:3.63946in"
alt="A screen shot of a graph Description automatically generated" />

<img src="./media/image30.png"
style="width:6.83274in;height:3.67917in" />

<img src="./media/image31.png"
style="width:7.30775in;height:3.20417in" />

<img src="./media/image32.png" style="width:6.49167in;height:4.8in" />

<img src="./media/image33.png"
style="width:6.49167in;height:4.86667in" />

<img src="./media/image34.png" style="width:6.5in;height:4.15833in"
alt="A graph with a blue line Description automatically generated" />

<img src="./media/image35.png" style="width:6.5in;height:4.21111in"
alt="A graph showing a graph Description automatically generated with medium confidence" />

<img src="./media/image36.png" style="width:6.5in;height:4.45556in"
alt="A screenshot of a computer program Description automatically generated" />

<img src="./media/image37.png"
style="width:7.24755in;height:2.0375in" />

<img src="./media/image38.png"
style="width:7.36209in;height:4.02083in" />

<img src="./media/image39.png"
style="width:7.15286in;height:4.4625in" />

<img src="./media/image40.png" style="width:6.49167in;height:2.65in" />

<img src="./media/image41.png" style="width:6.5in;height:4.99167in" />

<img src="./media/image42.png"
style="width:7.2625in;height:3.31893in" />

<img src="./media/image43.png"
style="width:7.28305in;height:2.64583in" />

<img src="./media/image44.png"
style="width:6.49167in;height:2.61667in" />

<img src="./media/image45.png" style="width:6.49167in;height:3.55in" />

<img src="./media/image46.png" style="width:6.5in;height:2.63333in" />

<img src="./media/image47.png"
style="width:6.49167in;height:2.18333in" />

##  Tarea 3: Examinar el modelo y las ejecuciones

1.  Ahora, haga clic en **RealTimeWorkspace** en el menú de navegación
    de la izquierda.

<img src="./media/image48.png" style="width:6.5in;height:4.75833in" />

2.  Los experiments y las runs pueden verse en la lista de recursos del
    espacio de trabajo.

<img src="./media/image49.png"
style="width:6.49167in;height:4.61667in" />

3.  En la página **RealTimeWorkspace**, seleccione
    **WHO-stock-prediction-model** de tipo ML model.

<img src="./media/image50.png" style="width:6.5in;height:5.23333in" />

<img src="./media/image51.png" style="width:6.20214in;height:3.1501in"
alt="A screenshot of a computer Description automatically generated" />

4.  En nuestro caso, los metadatos incluyen parámetros de entrada que
    podemos ajustar a nuestro modelo, así como métricas sobre la
    precisión del modelo, como el error cuadrático medio (RMSE). El RMSE
    representa el error medio: un cero sería un ajuste perfecto entre el
    modelo y los datos reales, mientras que los números más altos
    muestran un aumento del error. Aunque los números más bajos son
    mejores, un número "bueno" es subjetivo en función del escenario.

<img src="./media/image52.png"
style="width:6.49167in;height:4.39167in" />

# Ejercicio 2-Utilizar modelos, guardar en Lakehouse, crear un informe

## Tarea 1: Abre y explora el cuaderno Predecir cotizaciones bursátiles

El cuaderno se ha dividido en definiciones de funciones, como *def
write_predictions*, que ayudan a encapsular la lógica en pasos más
pequeños. Los cuadernos pueden incluir otras bibliotecas (como ya has
visto en la parte superior de la mayoría de los cuadernos), y también
pueden ejecutar otros cuadernos. Este cuaderno completa estas tareas a
alto nivel:

- Crea la tabla de previsiones de existencias en el Lakehouse, si no
  existe.

- Obtiene una lista de todos los símbolos bursátiles

- Crea una lista de predicciones examinando los modelos ML disponibles
  en MLflow

- Recorre los modelos ML disponibles:

  - Genera predicciones

  - Almacena las predicciones en la Lakehouse

1.  Ahora, haga clic en **RealTimeWorkspace** en el panel de navegación
    de la izquierda.

<img src="./media/image48.png"
style="width:4.12917in;height:2.8375in" />

2.  En el **RealTimeWorkspace**, haga clic en el cuaderno **DS 2-Predict
    Stock Prices**.

<img src="./media/image53.png" style="width:6.5in;height:4.88333in" />

3.  En el Explorer, seleccione el **Lakehouse** y haga clic en el botón
    **Add***.*

<img src="./media/image54.png"
style="width:5.61891in;height:4.4375in" />

<img src="./media/image55.png"
style="width:4.7625in;height:4.21227in" />

4.  En el cuadro de diálogo **Add Lakehouse**, seleccione el botón de
    opción **Existing** **Lakehouse** y, a continuación, haga clic en el
    botón **Add**.

<img src="./media/image10.png" style="width:3.00833in;height:1.83333in"
alt="A screenshot of a computer Description automatically generated" />

5.  En la ventana del OneLake data hub, seleccione ***StockLakehouse***
    y haga clic en el botón **Add**.

<img src="./media/image11.png" style="width:6.49167in;height:3.91667in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image56.png" style="width:6.5in;height:4.65347in"
alt="A screenshot of a computer Description automatically generated" />

## Tarea 2: Ejecutar el bloc de notas

1.  Crea la tabla de predicciones de stock en el Lakehouse, seleccione y
    **ejecute** las celdas 1<sup>st</sup> y 2<sup>nd</sup> .

<img src="./media/image57.png"
style="width:6.49167in;height:3.99167in" />

<img src="./media/image58.png" style="width:6.5in;height:4.08333in" />

2.  Obtiene una lista de todos los símbolos bursátiles, seleccione y
    **ejecute** las celdas 3<sup>rd</sup> y 4<sup>th</sup> .

<img src="./media/image59.png"
style="width:6.49167in;height:2.80833in" />

<img src="./media/image60.png"
style="width:6.49167in;height:3.06667in" />

3.  Crea una lista de predicción examinando los modelos ML disponibles
    en MLflow, seleccione y **Ejecute** las celdas 7<sup>th</sup> ,
    8<sup>th</sup> , 9<sup>th</sup> , y 10<sup>th</sup> .

<img src="./media/image61.png" style="width:6.5in;height:3.93333in" />

<img src="./media/image62.png" style="width:6.5in;height:4.78333in" />

<img src="./media/image63.png" style="width:6.5in;height:2.91667in" />

<img src="./media/image64.png" style="width:6.5in;height:3.65833in" />

4.  Para construir predicciones para cada tienda modelo en Lakehouse ,
    seleccione y **Ejecute** las 11<sup>th</sup> y 12<sup>th</sup>
    celdas.

<img src="./media/image65.png" style="width:6.5in;height:3.125in" />

<img src="./media/image66.png"
style="width:6.49167in;height:4.74167in" />

5.  Cuando se hayan ejecutado todas las celdas, actualice el esquema
    haciendo clic en los tres puntos **(...)** junto a *Tables,* luego
    navegue y haga clic en ***Refresh*.**

<img src="./media/image67.png" style="width:5.44167in;height:5.4in" />

<img src="./media/image68.png"
style="width:6.49167in;height:6.81667in" />

# Ejercicio 3: Solución práctica

Las dos primeras secciones de este módulo son enfoques comunes para
desarrollar una solución de Data Science. La primera sección abarca el
desarrollo del modelo (exploración, ingeniería de características,
ajuste, etc.), la construcción y el despliegue del modelo. La segunda
sección trata del consumo del modelo, que suele ser un proceso
independiente y que incluso puede ser realizado por diferentes equipos.

Sin embargo, en este caso concreto, la creación del modelo y la
generación de predicciones por separado no resultan muy beneficiosas, ya
que el modelo que hemos desarrollado es univariante basado en el tiempo:
las predicciones que genera el modelo no cambiarán si no se vuelve a
entrenar el modelo con nuevos datos.

La mayoría de los modelos ML son multivariantes, por ejemplo,
consideremos un estimador del tiempo de viaje que calcule el tiempo de
viaje entre dos ubicaciones dicho modelo podría tener docenas de
variables de entrada, pero dos variables principales incluirían sin duda
la hora del día y las condiciones meteorológicas. Dado que las
condiciones meteorológicas cambian con frecuencia, introduciríamos estos
datos en el modelo para generar nuevas predicciones del tiempo de viaje
(entradas: hora del día y condiciones meteorológicas, salida: tiempo de
viaje).

Por lo tanto, deberíamos generar nuestras predicciones inmediatamente
después de crear el modelo. A efectos prácticos, esta sección muestra
cómo podríamos implementar la construcción del modelo ML y la predicción
en un solo paso. En lugar de utilizar los datos históricos descargados,
este proceso utilizará las tablas de agregación construidas en el módulo
06. Nuestro flujo de datos puede visualizarse así:

## Tarea 1: Importar el cuaderno

Dedique algún tiempo a explorar el cuaderno *DS 3 - Prever todo*, y
fíjese en algunas cosas clave:

- Los datos de stock se leen de la tabla stock_minute_agg.

- Se cargan todos los símbolos y se hacen predicciones sobre cada uno de
  ellos.

- La rutina buscará en MLflow modelos que coincidan y cargará sus
  parámetros. Esto permite a los equipos de Data Science construir
  parámetros óptimos.

- Las predicciones para cada población se registran en la tabla
  stock_predicitions. No hay persistencia del modelo.

- No hay validación cruzada u otros pasos realizados en 07a. Aunque
  estos pasos son útiles para el científico de datos, no son necesarios
  aquí.

1.  Ahora, haga clic en **RealTimeWorkspace** en el menú de navegación
    de la izquierda.

<img src="./media/image48.png"
style="width:4.12917in;height:2.8375in" />

2.  *En el **RealTimeWorkspace***, haga clic en el cuaderno **DS
    3-Forecast All**.

<img src="./media/image69.png"
style="width:5.79583in;height:3.9375in" />

3.  En el Explorer seleccione el **Lakehouse** y haga clic en el botón
    ***Add .***

<img src="./media/image54.png" style="width:5.61891in;height:4.4375in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image55.png" style="width:4.7625in;height:4.21227in"
alt="A screenshot of a computer Description automatically generated" />

4.  En el cuadro de diálogo **Add Lakehouse**, seleccione el botón de
    opción **Existing Lakehouse** y, a continuación, haga clic en el
    botón **Add**.

<img src="./media/image10.png" style="width:3.00833in;height:1.83333in"
alt="A screenshot of a computer Description automatically generated" />

5.  En la ventana del concentrador de datos OneLake, seleccione
    ***StockLakehouse*** y haga clic en el botón **Add**.

<img src="./media/image11.png" style="width:6.49167in;height:3.91667in"
alt="A screenshot of a computer Description automatically generated" />

6.  Seleccione y **ejecute** la celda 1<sup>st</sup> .

<img src="./media/image70.png"
style="width:6.49167in;height:3.49167in" />

7.  Haga clic en ***Run all*** en el comando y siga el progreso del
    trabajo.

8.  Ejecutar el cuaderno para todos los símbolos podría llevar 10
    minutos.
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

9.  Cuando se hayan ejecutado todas las celdas, actualice el esquema
    haciendo clic en los tres puntos **(...)** a la derecha de las
    *Tables*, luego navegue y haga clic en ***Refresh*.**

<img src="./media/image87.png"
style="width:6.49167in;height:6.00833in" />

# Ejercicio 4: Elaboración de un informe de predicción

## Tarea 1: Construir un modelo semántico

En este paso, crearemos un modelo semántico (anteriormente denominado
conjunto de datos de Power BI) para utilizarlo en nuestro informe de
Power BI. Un modelo semántico representa datos que están listos para la
generación de informes y actúa como una abstracción sobre una fuente de
datos. Típicamente, un modelo semántico será construido con un propósito
(sirviendo a una necesidad específica de reporte) y puede tener
transformaciones, relaciones y enriquecimientos como medidas para hacer
más fácil el desarrollo de reportes.

1.  Haga clic en **RealTimeWorkspace** en el menú de navegación de la
    izquierda.

<img src="./media/image88.png" style="width:6.5in;height:5.6in" />

2.  Para crear un modelo semántico, navegue y haga clic en Lakehouse, es
    decir, **StackLakehouse.**

<img src="./media/image89.png"
style="width:6.49167in;height:4.63333in" />

<img src="./media/image90.png"
style="width:7.34187in;height:3.56504in" />

3.  En la página ***StocksLakehouse**,* haga clic en ***New semantic
    model*** en la barra de comandos.

<img src="./media/image91.png" style="width:6.5in;height:4.73333in" />

4.  En el campo **Nombre** en el panel ***New semantic model***,
    introduzca el nombre del modelo como
    ***StocksLakehousePredictions***, seleccione las tablas
    **stock_prediction** y **dim_symbol**. A continuación, haga clic en
    el botón **Confirm** como se muestra en la siguiente imagen.

<img src="./media/image92.png" style="width:4.61667in;height:5.75in" />

<img src="./media/image93.png" style="width:7.39637in;height:3.58281in"
alt="A screenshot of a computer Description automatically generated" />

5.  Cuando se abre el modelo semántico, tenemos que definir las
    relaciones entre las tablas stock_prediction y dim_symbol.

6.  Desde la tabla **stock_prediction**, arrastre el campo
    ***Symbol*** y suéltelo sobre el campo ***Symbol*** de la tabla
    **dim_Symbol** para crear una relación. Aparecerá el cuadro de
    diálogo **New relationship**.

<img src="./media/image94.png"
style="width:7.0993in;height:3.84583in" />

7.  En el cuadro de diálogo **New relationship**:

- La **tabla From** se rellena con **stock_prediction** y la columna
  **Symbol.**

- **La tabla To** se rellena con **dim_symbol** y la columna de
  **Symbol.**

- Cardinalidad: **Many to one (\*:1)**

- Dirección del filtro transversal: **Single**

- Deje seleccionada la casilla junto a **Make this relationship
  active** .

- Seleccione **Save**

<img src="./media/image95.png"
style="width:6.60417in;height:7.72544in" />

<img src="./media/image96.png"
style="width:7.37104in;height:3.27917in" />

## Tarea 2: Crear el informe en Power BI Desktop

1.  Abra su navegador, vaya a la barra de direcciones y escriba o pegue
    la siguiente URL: <https://powerbi.microsoft.com/en-us/desktop/> ,
    después pulse el botón **Enter**.

2.  Haga clic en el botón **Download now**.

> <img src="./media/image97.png"
> style="width:6.49167in;height:3.76667in" />

3.  En caso de que aparezca el cuadro de diálogo **This site is trying
    to open Microsoft Store**, haga clic en el botón **Open**.

<img src="./media/image98.png"
style="width:6.49167in;height:1.40833in" />

4.  En **Power BI Desktop**, haga clic en el botón **Get**.

<img src="./media/image99.png" style="width:4.95in;height:5.23333in" />

5.  Ahora, haga clic en el botón **Open**.

<img src="./media/image100.png"
style="width:4.90833in;height:5.31667in" />

6.  Introduzca sus credenciales de **Microsoft Office 365 tenant** y
    haga clic en el botón **Next**.

<img src="./media/image101.png"
style="width:6.25833in;height:6.05833in" />

7.  Introduzca la **contraseña administrativa** de la pestaña
    **Resources** y haga clic en el botón **Sign in.**

<img src="./media/image102.png" style="width:5.95in;height:5.175in" />

8.  En Power BI Desktop, seleccione **Blank report.**

<img src="./media/image103.png"
style="width:7.27361in;height:4.40712in" />

9.  En la cinta *Home*, haga clic en el ***OneLake data hub*** y
    seleccione **KQL database.**

<img src="./media/image104.png"
style="width:7.27414in;height:3.1375in" />

10. En la ventana **OneLake data hub**, seleccione **StockDB** y haga
    clic en el botón **Connect**.

<img src="./media/image105.png"
style="width:6.49167in;height:3.40833in" />

11. Introduzca sus credenciales de tenant de **Microsoft Office 365** y
    haga clic en el botón **Next**.

<img src="./media/image106.png" style="width:6.5in;height:6.975in" />

12. Introduzca la **contraseña administrativa** de la pestaña
    **Resources** y haga clic en el botón **Sign in.**

<img src="./media/image107.png"
style="width:4.59167in;height:5.26667in" />

13. En la página Navegador, en **Display options**, seleccione Tabla de
    **StockPrice** y, a continuación, haga clic en el botón **Load**.

<img src="./media/image108.png" style="width:6.5in;height:5.275in" />

14. En el cuadro de diálogo ***Connection settings***, seleccione el
    botón de opción ***DirectQuery*** y haga clic en el botón **Ok**.

<img src="./media/image109.png"
style="width:4.22083in;height:2.56858in" />

15. En la cinta de ***Home***, haga clic en el ***OneLake data hub***y
    seleccione **Power BI semantic models** como se muestra en la
    siguiente imagen.

<img src="./media/image110.png" style="width:6.5in;height:4.19167in" />

16. En la ventana **OneLake data hub**, seleccione
    **StockLakehousePredictions** de la lista y haga clic en el botón
    **Connect**.

<img src="./media/image111.png"
style="width:6.49167in;height:3.38333in" />

17. En la página **Connect to your data**, seleccione **dim_symbol,
    stock_prediction** y haga clic en el botón **Submit**.

<img src="./media/image112.png"
style="width:5.55833in;height:5.88333in" />

18. En este caso, podemos descartar la advertencia de **Potential
    security risk** haciendo clic en el botón **OK**.

<img src="./media/image113.png" style="width:6.5in;height:1.40833in" />

<img src="./media/image114.png" style="width:7.20263in;height:3.5236in"
alt="A screenshot of a computer Description automatically generated" />

19. Haga clic en ***Modeling*** en la barra de comandos y, a
    continuación, en ***Manage relationships.***

<img src="./media/image115.png" style="width:6.5in;height:3.40833in" />

20. En el panel **Manage relationshpis**, seleccione **+New
    relationship** como se muestra en la siguiente imagen.

<img src="./media/image116.png"
style="width:6.49167in;height:4.99167in" />

21. Cree una **New relationship** entre la **tabla *StockPrice ***-From
    y la ***tabla stocks_prediction*** - ***To*** (después de
    seleccionar la tabla, asegúrese de seleccionar las columnas de
    símbolo en cada tabla). Establezca la dirección del filtro cruzado
    en ***Both***, y asegúrese de que la cardinalidad está establecida
    en ***Many-to-many*.** A continuación, haga clic en el botón
    **Save**.

> <img src="./media/image117.png" style="width:6.5in;height:6.38333in" />

22. En la página **Mange relationships**, seleccione ***StockPrice***,
    ***stocks_prediction*** tables y haga clic en el botón **Close**.

<img src="./media/image118.png" style="width:6.5in;height:4.85833in" />

23. En la página **de Power BI**, en **Visualizations**, haga clic en el
    icono **Gráfico de líneas** para añadir un **Gráfico de columnas** a
    su informe.

- En el panel de **Data**, expanda **StockPrice** y marque la casilla
  junto a **timestamp**. Esto crea un gráfico de columnas y añade el
  campo al **eje X**.

- En el panel de **Data**, expanda **StockPrice** y marque la casilla
  junto a **Price**. Esto añade el campo al **eje Y**.

- En el panel **Data**, expanda **StockPrice** y marque la casilla junto
  a **Símbolo**. Esto añade el campo a la **Legend**.

- **Filtro**: **timestamp** to ***Relative time** is in the last* **15
  minutes**.

<img src="./media/image119.png"
style="width:5.675in;height:6.80833in" />

<img src="./media/image120.png"
style="width:7.1554in;height:4.06467in" />

24. En la página de **Power BI**, en **Visualizations**, haga clic en el
    icono **Gráfico de líneas** para añadir un **Gráfico de columnas** a
    su informe.

- En el panel de **Data**, expanda **StockPrice** y marque la casilla
  junto a **timestamp**. Esto crea un gráfico de columnas y añade el
  campo al **eje X**.

- En el panel de **Data**, expanda **StockPrice** y marque la casilla
  junto a **Price**. Esto añade el campo al **eje Y**.

- En el panel **Datos**, expanda **dim_symbol** y marque la casilla
  junto a **Market**. Esto añade el campo a la **Legend**.

- **Filtro**: **timestamp** to ***Relative time** is in the last* **1
  hour**.

<img src="./media/image121.png"
style="width:7.18155in;height:2.64583in" />

<img src="./media/image122.png"
style="width:5.25417in;height:5.53899in" />

<img src="./media/image123.png" style="width:5.8in;height:6.025in" />

<img src="./media/image124.png"
style="width:7.32714in;height:4.17083in" />

25. En la página **de Power BI**, en **Visualizaciones**, haga clic en
    el icono **Gráfico de líneas** para añadir un **Gráfico de
    columnas** a su informe.

- En el panel **Data**, expanda **Stock_prediction** y marque la casilla
  junto a **predict_time**. Esto crea un gráfico de columnas y añade el
  campo al **eje X**.

- En el panel **Data**, expanda **Stock_prediction** y marque la casilla
  junto a **yhat**. Esto añade el campo al **eje Y**.

- En el panel **Data**, expanda **Stock_prediction** y marque la casilla
  junto a **Symbol**. Esto añade el campo a la **Legend**.

- **Filtro**: **predict_time** a ***Relative date** in the last* **3
  days**.

<img src="./media/image125.png"
style="width:5.60833in;height:6.90833in" />

<img src="./media/image126.png"
style="width:6.49167in;height:3.54167in" />

<img src="./media/image127.png"
style="width:4.44583in;height:5.09322in" />

<img src="./media/image128.png" style="width:6.5in;height:4.03333in" />

26. En la página **de Power BI**, en **Data,** haga clic con el botón
    derecho en la tabla ***stocks_prediction*** y seleccione ***New
    measure.***

<img src="./media/image129.png" style="width:6.5in;height:5.35in" />

27. Las medidas son fórmulas escritas en el lenguaje Data Analysis
    Expressions (DAX); para esta fórmula DAX, introduzca ***+++currdate
    = NOW()*+++**

<img src="./media/image130.png" style="width:6.49167in;height:4in" />

28. Con el gráfico de predicción seleccionado, vaya a las opciones de
    visualización adicionales, es decir, al icono de la lupa/gráfico y
    añada una **new *X-Axis Constant Line***.

<img src="./media/image131.png" style="width:5.75in;height:5.9in" />

29. En *Value*, utilice el botón de fórmula **(fx)** para elegir un
    campo.

<img src="./media/image132.png"
style="width:5.89583in;height:3.65557in" />

30. En **Value -Apply settings to** page, haga clic en el desplegable
    bajo **what field should we base this on?**, luego haga clic en el
    desplegable de **stocks_prediction**, seleccione la medida
    ***currdate***. A continuación, haga clic en el botón **OK**.

<img src="./media/image133.png"
style="width:6.41495in;height:4.52917in" />

<img src="./media/image134.png"
style="width:7.38149in;height:4.0125in" />

31. Navegue hasta las opciones adicionales de visualización, es decir,
    el icono de la lupa/gráfico, active la **Shade
    area**.<img src="./media/image135.png"
    style="width:6.91875in;height:3.7125in" />

32. Configuradas las relaciones entre tablas, todos los visuales deben
    cruzar filtros; al seleccionar un símbolo o un mercado en un
    gráfico, todos los visuales deben reaccionar en consecuencia. Como
    se muestra en la siguiente imagen, el mercado **NASDAQ** está
    seleccionado en el gráfico de mercado superior derecho:

<img src="./media/image136.png" style="width:6.5in;height:4.01667in" />

<img src="./media/image137.png"
style="width:7.31785in;height:4.51424in" />

33. Haga clic en **Publish** en la barra de comandos.

<img src="./media/image138.png" style="width:6.5in;height:3.69167in" />

34. En el cuadro de diálogo **Microsoft Power BI Desktop**, haga clic en
    el botón **Save**.

<img src="./media/image139.png"
style="width:5.09167in;height:1.80833in" />

35. En el cuadro de diálogo **Save this file**, introduzca el Nombre
    como **Prediction Report** y seleccione la ubicación. A
    continuación, pulse el botón **Save**.

<img src="./media/image140.png" style="width:4.95in;height:4.73333in" />

## **Resumen**

En este laboratorio, ha aprendido cómo abordar una solución de Data
Science en Fabric importando y ejecutando un bloc de notas que procesa
datos, crea un modelo ML de los datos utilizando Prophet y almacena el
modelo en MLflow. Además, ha evaluado el modelo mediante validación
cruzada y lo ha probado con datos adicionales.

Ha seguido la creación del modelo ML consumiendo el modelo ML, generando
predicciones y almacenando esas predicciones en Lakehouse. Ha refinado
el proceso para construir un modelo y generar predicciones en un solo
paso, agilizando el proceso de creación de predicciones y
operacionalizando la generación del modelo. A continuación, ha creado un
informe (o informes) que muestra los precios actuales de las acciones
con los valores predichos, poniendo los datos a disposición de los
usuarios empresariales.
