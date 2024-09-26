# Laboratorio 01: Ingesta de datos mediante inteligencia en tiempo real

## Introducción

En este laboratorio, comenzará generando rápidamente datos en tiempo
real y comprendiendo cómo se pueden procesar y visualizar esos datos en
Microsoft Fabric. Con los informes iniciales en su lugar, varios módulos
están disponibles que exploran el data warehousing, la arquitectura de
Data Lakehouse, Data Activator, Data Science y, por supuesto, Real-time
Analytics. Los módulos están diseñados para ser cohesivos pero
flexibles: todos implican el mismo escenario central pero tienen
dependencias limitadas para que pueda consumir los módulos que tengan
más sentido para usted.

La arquitectura básica de la solución se ilustra a continuación. La
aplicación desplegada al principio de este laboratorio (ya sea como un
contenedor Docker o ejecutándose en Jupyter notebook) publicará eventos
en nuestro entorno Fabric. Los datos se ingieren en una base de datos
KQL para la generación de informes en tiempo real en Power BI.

En este laboratorio, te pondrás manos a la obra con una empresa
financiera ficticia "AbboCost". A AbboCost le gustaría configurar una
plataforma de monitorización de acciones para monitorizar las
fluctuaciones de precios e informar sobre datos históricos. A lo largo
del taller, veremos cómo cada aspecto de Microsoft Fabric puede ser
incorporado como parte de una solución más amplia -- al tener todo en
una solución integrada, usted será capaz de integrar datos de forma
rápida y segura, construir informes, crear data warehouses y lakehouses,
pronosticar usando modelos ML, y mucho más.

<img src="./media/image1.png" style="width:6.5in;height:3.65625in"
alt="Data Lakehouse with Azure Synapse Analytics" />

# Objetivos

- Inscribirse en la prueba gratuita de Microsoft Fabric, canjee Azure
  Pass y configure los permisos necesarios en el portal de Azure.

- Crear capacidad de Fabric y espacio de trabajo, cuenta de
  almacenamiento y espacio de trabajo de Fabric.

- Implementar la aplicación generadora de acciones a través de Azure
  Container Instance utilizando una plantilla ARM.

- Configurar Eventstream en Microsoft Fabric para la ingesta de datos en
  tiempo real desde Azure Event Hubs, garantizando una integración
  perfecta y una vista previa de los datos para su posterior análisis.

- Crear una base de datos KQL dentro de Microsoft Fabric y enviar datos
  desde Eventstream a la base de datos KQL.

# Ejercicio 1: Configuración del entorno

Para seguir los ejercicios de laboratorio, es necesario aprovisionar un
conjunto de recursos. En el centro del escenario se encuentra el script
generador de cotizaciones bursátiles en tiempo real que genera un flujo
continuo de cotizaciones bursátiles que se utilizan a lo largo del
taller.

Recomendamos desplegar el generador de precios de acciones a través de
Azure Container Instance porque el clúster Spark por defecto consumirá
un gran número de recursos.

## Tarea 1: Inicie sesión en su cuenta de Power BI y suscríbase a la [versión de prueba gratuita de Microsoft Fabric.](https://learn.microsoft.com/en-us/fabric/get-started/fabric-trial)

1.  Abra su navegador, vaya a la barra de direcciones y escriba o pegue
    la siguiente URL: https://app.fabric.microsoft.com/ y, a
    continuación, pulse el botón **Enter**.

> <img src="./media/image2.png" style="width:6.5in;height:2.89653in"
> alt="A search engine window with a red box Description automatically generated with medium confidence" />

2.  En la ventana **Microsoft Fabric**, introduzca sus credenciales de
    Microsoft 365 y haga clic en el botón **Submit**.

> <img src="./media/image3.png" style="width:6.5in;height:2.775in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image4.png" style="width:6.49167in;height:3.11667in"
> alt="A close up of a white and green object Description automatically generated" />

3.  Introduzca la **contraseña administrativa** de la pestaña
    **Resources** y haga clic en el botón **Sign In.**

> <img src="./media/image5.png" style="width:4.50833in;height:3.83333in"
> alt="A login screen with a red box and blue text Description automatically generated" />

4.  En la ventana **Stay signed in?**, haga clic en el botón **Yes**.

> <img src="./media/image6.png" style="width:4.58333in;height:3.66667in"
> alt="A screenshot of a computer error Description automatically generated" />

5.  Será dirigido a la página de inicio de Power BI.

> <img src="./media/image7.png" style="width:7.17276in;height:3.31357in"
> alt="A screenshot of a computer Description automatically generated" />

## Tarea 2: Iniciar la prueba de Microsoft Fabric

1.  En la página de **inicio de Power BI**, haga clic en el icono
    **Account manager for MOD Administrator** en la esquina superior
    derecha de la página. En la hoja del Account manager, navegue y
    seleccione **Start trial** como se muestra en la siguiente
    imagen**.**

> <img src="./media/image8.png" style="width:6.61597in;height:1.75803in"
> alt="A screenshot of a computer Description automatically generated" />

2.  En el cuadro de diálogo **Upgrade to a free Microsoft Fabric**
    trial, haga clic en el botón **Activate.**

> <img src="./media/image9.png" style="width:6.5in;height:2.6in" />

3.  Aparecerá un cuadro de diálogo de notificación de **Successfully
    upgraded to a free Microsoft Fabic trial**. En el cuadro de diálogo,
    haga clic en el botón **Fabric Home Page**.

> <img src="./media/image10.png" style="width:5.41667in;height:1.85in" />
>
> <img src="./media/image11.png" style="width:6.5in;height:2.3875in"
> alt="A screenshot of a computer Description automatically generated" />

## **Tarea 3: Crear un espacio de trabajo Fabric**

En esta tarea, se crea un espacio de trabajo de Fabric. El espacio de
trabajo contiene todos los elementos necesarios para este tutorial de
Lakehouse, que incluye Lakehouse, Dataflows, Data Factory pipelines, los
cuadernos, los conjuntos de datos de Power BI y los informes.

1.  Abra su navegador, vaya a la barra de direcciones y escriba o pegue
    la siguiente URL: https://app.fabric.microsoft.com/ y, a
    continuación, pulse el botón **Enter**. En la página **Microsoft
    Fabric Home**, navegue y haga clic en el mosaico **Power BI**.

> <img src="./media/image12.png" style="width:6.49167in;height:6.30833in"
> alt="A screenshot of a computer Description automatically generated" />

2.  En el menú de navegación del lado izquierdo de la página **Power BI
    Home**, navegue y haga clic en **Workspaces**, como se muestra en la
    imagen siguiente.

> <img src="./media/image13.png" style="width:6.5in;height:6.85833in" />

3.  En el panel Workspaces, haga clic en el **botón + New workspace**

> <img src="./media/image14.png" style="width:4.41667in;height:7.7in"
> alt="A screenshot of a computer Description automatically generated" />

4.  En el panel **Create a workspace** que aparece a la derecha,
    introduzca los siguientes datos y haga clic en el botón **Apply**.

| **Name** | **+++RealTimeWorkspaceXXX+++** (XXX puede ser un número único, puede añadir más números) |
|----|----|
| **Advanced** | Seleccionar Trail |
| **Default storage format** | **Small dataset storage format** |

> <img src="./media/image15.png"
> style="width:6.14375in;height:5.77292in" />
>
> <img src="./media/image16.png"
> style="width:5.48333in;height:6.87917in" />

## **Tarea 4: Implementar la app a través de Azure Container Instances**

Esta tarea despliega la app generadora de stock en una Azure Container
Instance utilizando una plantilla ARM. La aplicación generará datos de
existencias que publicará en un Azure Event Hub, que también se
configura durante el despliegue de la plantilla ARM.

Para autoimplementar los recursos, siga estos pasos.

1.  Abra una nueva barra de direcciones e introduzca la siguiente URL.
    Si se le pide que inicie sesión, utilice sus credenciales de tenant
    de O365.

> +++https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Ffabricrealtimelab%2Fmain%2Fresources%2Fmodule00%2Ffabricworkshop_arm_managedid.json+++

2.  En la ventana **Deployment personalizado**, en la pestaña
    **Conceptos básicos**, introduzca los siguientes datos y haga clic
    en el botón **Revisar+crear**.

<table>
<colgroup>
<col style="width: 25%" />
<col style="width: 74%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>Suscription</strong></th>
<th><blockquote>
<p>Seleccione la suscripción asignada</p>
</blockquote></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><strong>Resource group</strong></td>
<td>Haga clic en <strong>Create new&gt;</strong> introduzca
<strong>+++realtimeworkshop+++</strong> y seleccione
<strong>Ok</strong></td>
</tr>
<tr class="even">
<td><strong>Region</strong></td>
<td>Seleccione <strong>West US 3</strong></td>
</tr>
</tbody>
</table>

> <img src="./media/image17.png" style="width:5.6875in;height:5.81892in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image18.png" style="width:6.5in;height:6.04167in"
> alt="A screenshot of a computer Description automatically generated" />

3.  En la pestaña **Review + create**, navegue y haga clic en el botón
    **Create.**

> <img src="./media/image19.png" style="width:6.5in;height:5.91667in" />

5.  Espere a que se complete el Deployment. La implementación tardará
    unos 10-15 minutos.

6.  Una vez completado el Deployment, haga clic en el botón **Go to
    resource**.

> <img src="./media/image20.png" style="width:6.49167in;height:3.04167in"
> alt="A screenshot of a computer Description automatically generated" />

4.  En **el grupo de recursos realtimeworkshop**, compruebe que el
    **espacio de nombres Event Hub** y **Azure Container Instance
    (ACI)** se han desplegado correctamente.

> <img src="./media/image21.png"
> style="width:6.68767in;height:3.55417in" />

5.  Abra el **espacio de nombres Event Hub**, que tendrá un nombre
    similar a ***ehns-XXXXXX-fabricworkshop***.

> <img src="./media/image22.png" style="width:6.5in;height:3.35833in" />

6.  En el menú de navegación izquierdo de la página **del espacio de
    nombres Event Hub**, vaya a la sección **Settings** y haga clic en
    **Shared access policies**.

> <img src="./media/image23.png" style="width:6.01946in;height:5.04583in"
> alt="A screenshot of a computer Description automatically generated" />

7.  En la página ***Shared access policies***, haga clic en
    ***stockeventhub_sas*** .**Política SAS:** el panel
    **stockeventhub_sas** aparece en el lado derecho, copie la **clave
    primaria** y el **espacio de nombres Event Hub** (como
    *ehns-XXXXXX-fabricworkshop*) y péguelos en un bloc de notas, ya que
    los necesita en la próxima tarea. En resumen, necesitará lo
    siguiente:

> <img src="./media/image24.png" style="width:6.5in;height:3.27083in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image25.png" style="width:5.40417in;height:5.10485in"
> alt="A screenshot of a computer Description automatically generated" />

## **Tarea 5: Obtener datos con Eventstreams**

1.  Vuelva a Microsoft Fabric, navegue y haga clic en **Power BI** en la
    parte inferior de la página y, a continuación, seleccione
    **Real-Time Intelligence**.

<img src="./media/image26.png" style="width:5.71667in;height:7.59167in"
alt="A screenshot of a computer Description automatically generated" />

2.  En la pagina de inicio **de Synapse Real-Time Analytics**,
    seleccione **Eventstream**. Nombre el Eventstream +++
    *StockEventStream+++, marque las **Enhanced Capabilities (preview
    ***y haga clic en el botón **Create**.

<img src="./media/image27.png" style="width:6.49167in;height:4.28333in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image28.png" style="width:3.39167in;height:2.9in"
alt="A screenshot of a computer Description automatically generated" />

3.  En el Eventstreams, seleccione **Add external source**

> <img src="./media/image29.png"
> style="width:6.49167in;height:3.79167in" />

4.  En Add source, seleccione **Azure *Event Hubs.***

> <img src="./media/image30.png" style="width:6.49167in;height:2.99167in"
> alt="A screenshot of a chat Description automatically generated" />

5.  En la página de configuración de **Azure Event Hubs**, introduzca
    los siguientes datos y haga clic en el botón **Add**.

<!-- -->

1.  Configure los ajustes de conexión: Haga clic en **New Connection** e
    introduzca los siguientes datos y, a continuación, haga clic en el
    botón **Create**.

<!-- -->

1.  En el espacio de nombres Event Hub: introduzca el nombre del Event
    Hub (los valores que ha guardado en el bloc de notas**).**

2.  Event Hub : **+++StockEventHub+++**

3.  Nombre de Shared Access Key:**+++stockeventhub_sas+++**

4.  Shared Access Key: introduzca la clave principal (el valor que ha
    guardado en el bloc de notas en la **Tarea 8).**

<!-- -->

2.  Consumer group: \$Default

3.  Formato de datos: **JSON** y pulse el botón **Next**

> <img src="./media/image31.png" style="width:6.55193in;height:3.17083in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image32.png" style="width:6.48406in;height:3.55417in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image33.png" style="width:6.60034in;height:3.79583in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image34.png" style="width:6.57222in;height:3.13847in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image35.png" style="width:6.36149in;height:3.05417in"
> alt="A screenshot of a computer Description automatically generated" />

8.  Verá una notificación indicando **Successfully added The source
    “StockEventHub,Azure Event Hubs”**.

> <img src="./media/image36.png" style="width:4.17536in;height:2.61689in"
> alt="A screenshot of a computer Description automatically generated" />

9.  Con el Event Hub configurado, haga clic en ***Test result***.
    Debería ver eventos que incluyan el símbolo de stock, el precio y la
    marca de tiempo.

> <img src="./media/image37.png" style="width:7.0667in;height:4.3125in" />

10. En el Eventstream, seleccione **Publish.**

> <img src="./media/image38.png"
> style="width:6.11111in;height:3.7106in" />
>
> <img src="./media/image39.png"
> style="width:6.58371in;height:3.97555in" />

11. En el Eventstream, seleccione **AzureEventHub** y haga clic en el
    botón **Refreash**.

> <img src="./media/image40.png" style="width:6.8155in;height:3.77083in"
> alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image41.png" style="width:7.35367in;height:3.63127in"
alt="A screenshot of a computer Description automatically generated" />

# Ejercicio 2: Configuración e ingestión de la base de datos KQL

Ahora que nuestro entorno está totalmente configurado, vamos a completar
la ingesta del Eventstreams, para que los datos sean ingeridos en una
base de datos KQL. Estos datos también se almacenarán en Fabric OneLake.

## Tarea 1: Crear base de datos KQL

KQL (Kusto Query Language) es el lenguaje de consulta utilizado por
Real-time Analytics en Microsoft Fabric junto con otras soluciones, como
Azure Data Explorer, Log Analytics, Microsoft 365 Defender, etc. Similar
a Structured Query Language (SQL), KQL está optimizado para consultas
ad-hoc sobre Big Data, series temporales de datos y transformación de
datos.

Para trabajar con los datos, crearemos una base de datos KQL y
transmitiremos los datos del Eventstream a la base de datos KQL.

1.  En el menú de navegación de la izquierda, navegue y haga clic en
    **RealTime workspaceXXX**, como se muestra en la siguiente imagen.

> <img src="./media/image42.png"
> style="width:3.12083in;height:5.12655in" />

2.  En la página **Real-Time Intelligence**, vaya a la sección **+New
    item** y haga clic en, seleccione **Eventhouse** para crear
    Eventhouse.

> <img src="./media/image43.png" style="width:6.49167in;height:4.275in" />

3.  En el cuadro de diálogo **New Eventhouse**, introduzca
    **+++StockDB+++** en el campo **Name**, haga clic en el botón
    **Create** y abra el nuevo Eventhouse.

> <img src="./media/image44.png" style="width:3.53056in;height:2.09861in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image45.png" style="width:6.5in;height:3.75417in"
> alt="A screenshot of a computer Description automatically generated" />

4.  Haga clic en el **icono del lápiz** como se muestra en la imagen de
    abajo para cambiar la configuración y seleccione el **Turno on**, a
    continuación, haga clic en el botón **Done** para habilitar el
    acceso OneLake.

> <img src="./media/image46.png" style="width:5.75947in;height:4.78341in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image47.png" style="width:3.64375in;height:6.13611in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image48.png" style="width:3in;height:1.5in"
> alt="A screenshot of a computer Description automatically generated" />

5.  Después de habilitar OneLake, es posible que tenga que actualizar la
    página para verificar que la integración de la carpeta OneLake está
    activa.

> <img src="./media/image49.png" style="width:6.5in;height:4.82083in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image50.png" style="width:6.5in;height:3.57986in"
> alt="A screenshot of a computer Description automatically generated" />

## Tarea 2: Enviar datos del Eventstreams a la base de datos KQL

1.  En el menú de navegación de la izquierda, navegue y haga clic en
    **StockEventStream** creado en la tarea anterior, como se muestra en
    la siguiente imagen.

> <img src="./media/image51.png" style="width:6.5in;height:5.975in"
> alt="A screenshot of a computer Description automatically generated" />

2.  En el Eventstream, haga clic en el botón **Edit**.

> <img src="./media/image52.png" style="width:6.49167in;height:3.78333in"
> alt="A screenshot of a computer Description automatically generated" />

3.  Nuestros datos deberían estar llegando a nuestro Eventstream, y
    ahora vamos a configurar los datos para ser ingeridos en la base de
    datos KQL que creamos en la tarea anterior. En el Eventstream, haga
    clic en *Transform events or add destination,* luego navega y haga
    clic en **Eventhouse**.

> <img src="./media/image53.png"
> style="width:6.49236in;height:3.72708in" />

4.  En la configuración de KQL, seleccione *Direct ingestion*. Si bien
    tenemos la oportunidad de procesar los datos de eventos en esta
    etapa, para nuestros propósitos, vamos a ingerir los datos
    directamente en la base de datos KQL. Establezca el nombre de
    destino en *+++KQL+++*, luego seleccione su **workspace,Eventhouse**
    y la base de datos KQL creada en la tarea anterior, luego haga clic
    en el botón **Save**.

> <img src="./media/image54.png" style="width:2.81042in;height:5.02292in"
> alt="A screenshot of a computer Description automatically generated" />

5.  Pulse el botón **Publish**

> <img src="./media/image55.png"
> style="width:6.49167in;height:4.50833in" />
>
> <img src="./media/image56.png" style="width:4.13369in;height:2.3502in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image57.png" style="width:6.5in;height:3.26319in"
> alt="A screenshot of a computer Description automatically generated" />

6.  En el panel Eventstreams, seleccione **configure** en el destino
    **KQL**.

> <img src="./media/image58.png" style="width:6.5in;height:3.46667in"
> alt="A screenshot of a computer Description automatically generated" />

7.  En la primera página de configuración, seleccione **+New table** e
    introduzca el nombre *+++StockPrice+++* para la tabla que contendrá
    los datos en StockDB. Haga clic en el botón **Next**.

> <img src="./media/image59.png" style="width:6.49167in;height:3.84167in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image60.png" style="width:6.5in;height:3.78333in"
> alt="A screenshot of a computer Description automatically generated" />

8.  La siguiente página nos permite inspeccionar y configurar el
    esquema. Asegúrese de cambiar el formato de TXT a **JSON**, si es
    necesario. Las columnas predeterminadas de *símbolo*, *precio* y
    *fecha y hora* deben tener el formato que se muestra en la imagen
    siguiente; a continuación, haga clic en el botón *Finish*.

> <img src="./media/image61.png" style="width:6.69992in;height:3.8875in"
> alt="A screenshot of a computer Description automatically generated" />

9.  En la página **Summary**, si no hay errores, verá una **marca de
    verificación verde** como se muestra en la siguiente imagen, a
    continuación, haga clic en el botón *Close* para completar la
    configuración.

> <img src="./media/image62.png" style="width:6.49167in;height:3.79167in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image63.png" style="width:6.5in;height:3.38819in"
> alt="A screenshot of a computer Description automatically generated" />

10. Pulse el botón **Refresh**

> <img src="./media/image64.png" style="width:7.1875in;height:3.73676in"
> alt="A screenshot of a computer Description automatically generated" />

11. Seleccione el destino **KQL** y pulse el botón **Refresh**.

> <img src="./media/image65.png" style="width:6.69406in;height:3.72083in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image66.png" style="width:6.70292in;height:3.25335in"
> alt="A screenshot of a computer Description automatically generated" />

**Resumen**

En este laboratorio, se ha registrado para la prueba de Microsoft Fabric
y canjeado Azure Pass, seguido de la configuración de permisos y la
creación de los recursos necesarios en el portal de Azure, como la
capacidad de Fabric, el espacio de trabajo y la cuenta de
almacenamiento. A continuación, ha desplegado la aplicación generadora
de existencias a través de Azure Container Instance utilizando una
plantilla ARM para generar datos de existencias en tiempo real. Además,
ha configurado Eventstream en Microsoft Fabric para ingerir datos de
Azure Event Hubs y ha preparado la base de datos KQL para almacenar
estos datos de forma eficiente. En este laboratorio, ha establecido un
entorno totalmente funcional para continuar con los ejercicios de
laboratorio posteriores relacionados con el análisis en tiempo real y el
procesamiento de datos.
