---
title: Incorporación de conjuntos de dispositivos a una aplicación de Azure IoT Central | Microsoft Docs
description: Como operador, cómo usar conjuntos de dispositivos en la aplicación de Azure IoT Central.
author: ellenfosborne
ms.author: elfarber
ms.date: 01/21/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: d0b802842d60d68bab36e87913a84c5e40b8e431
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2018
ms.locfileid: "39206078"
---
# <a name="use-device-sets-in-your-azure-iot-central-application"></a>Uso de conjuntos de dispositivos en una aplicación de Azure IoT Central

Este artículo se describe cómo usar conjuntos de dispositivos en su aplicación de Microsoft Azure IoT Central como operador.

Un conjunto de dispositivos es una lista de dispositivos que se agrupan juntos porque todos ellas cumplen algunos criterios especificados. Los conjuntos de dispositivos ayudan a administrar, visualizar y analizar los dispositivos a escala agrupando dichos dispositivos en grupos lógicos más pequeños. Por ejemplo, puede crear una lista de todos los dispositivos de aire acondicionado de Seattle para que el técnico de Seattle pueda buscar todos los dispositivos de los que es responsable. En este artículo se muestra cómo crear y configurar conjuntos de dispositivos.

## <a name="create-a-device-set"></a>Creación de un conjunto de dispositivos

Para crear un conjunto de dispositivos:

1. Elija **Device Sets** (Conjuntos de dispositivos) en el menú de navegación de la izquierda.

1. Haga clic en **+ Nuevo**.

    ![Nuevo conjunto de dispositivos](media/howto-use-device-sets/image1.png)

1. Asigne un nombre que sea único en toda la aplicación a su conjunto de dispositivos. También puede agregar una descripción. Un conjunto de dispositivos solo puede contener los dispositivos de una sola plantilla de dispositivo. Elija la plantilla de dispositivo que se usará para este conjunto.

1. Cree la consulta para identificar los dispositivos para el conjunto de dispositivos seleccionando una propiedad, un operador de comparación y un valor. Puede agregar varias consultas y dispositivos que cumplan **todos** los criterios que se colocan en el conjunto de dispositivos. El conjunto de dispositivos que crea es accesible para cualquier persona que tenga acceso a la aplicación, por lo que cualquier usuario puede ver, modificar o eliminar el conjunto de dispositivos.

    ![Consulta de conjunto de dispositivos](media/howto-use-device-sets/image2.png)

    > [!NOTE]
    > El conjunto de dispositivos es una consulta dinámica. Cada vez que vea la lista de dispositivos, puede haber diferentes dispositivos en ella. La lista depende de qué dispositivos cumplen actualmente los criterios de la consulta.

1. Elija **Guardar**.

## <a name="configure-the-dashboard-for-your-device-set"></a>Configuración del panel para el conjunto de dispositivos

Después de crear el conjunto de dispositivos, puede configurar su **panel**. El **panel** es la página principal donde puedes colocar imágenes y vínculos. También puede agregar cuadrículas que enumeran los dispositivos del conjunto de dispositivos.

1. Elija **Device Sets** (Conjuntos de dispositivos) en el menú de navegación de la izquierda.

1. Seleccione la pestaña **Panel**.

1. Active **Modo de diseño**.

    ![Activación del modo de diseño](media/howto-use-device-sets/image3.png)

1. Para información sobre cómo agregar una imagen, vea [Prepare and upload images to your Azure IoT Central application](howto-prepare-images.md) (Preparación y carga de imágenes a la aplicación de Azure IoT Central).

1. Agregue un título de vínculo:
    1. Elija **Vínculo** en el panel derecho.

        ![Elección de Vínculo](media/howto-use-device-sets/image6.png)

    1. En **Título**, asigne un título al vínculo.
    1. Elija una dirección URL que se abrirá cuando se haga clic en el vínculo.
    1. Proporcione una descripción para el vínculo que se muestra debajo de **Título**.
    1. Elija **Guardar**.

        ![Guardar el vínculo](media/howto-use-device-sets/image7.png)

    1. Puede mover y cambiar el tamaño del icono del vínculo en el **panel**.

1. Agregue una cuadrícula. Una cuadrícula es una tabla de dispositivos en el conjunto de dispositivos con las columnas que elija.
    1. Elija **Cuadrícula** en el panel derecho.

        ![Elija Cuadrícula](media/howto-use-device-sets/image8.png)

    1. En **Título**, proporcione un título a la cuadrícula.
    1. Mediante el botón de configuración, seleccione las columnas que se mostrarán. En el panel que aparece, elija la columna que desea que se muestre y elija la flecha derecha para seleccionarla.
    1. Elija **Aceptar**.
    1. Elija **Guardar**.

        ![Guardar la cuadrícula](media/howto-use-device-sets/image9.png)

    1. Arrastre y coloque la cuadrícula para situarla en el **panel**.

    > [!NOTE]
    > Puede agregar varias imágenes, vínculos y cuadrículas.
  
    1. Desactive **Modo de diseño**.

    ![Desactivación del modo de diseño](media/howto-use-device-sets/image10.png)


### <a name="configuring-location-map-in-your-device-sets-dashboard"></a>Configuración del mapa de ubicación en el panel de conjuntos de dispositivos 
Puede agregar un mapa de ubicación para visualizar la ubicación de los conjuntos de dispositivos en un mapa. 

Para agregar un mapa de ubicación al panel de conjuntos de dispositivos, debe haber configurado la propiedad de ubicación en la plantilla de dispositivo. Consulte [Creación de una propiedad de ubicación con Azure Maps](howto-set-up-template.md).


1. En el panel de conjuntos de dispositivos, seleccione Mapa en la biblioteca. 

    ![Mapas del panel de conjuntos de dispositivos](media/howto-use-device-sets/LocationMaps1.png)


2. Asigne un título y elija la propiedad de ubicación que se ha configurado anteriormente como parte de la propiedad de dispositivo.

    ![Configuración de mapas del panel](media/howto-use-device-sets/LocationMaps2.png)

3. Después de guardar, verá el icono de mapa, que muestra la ubicación de los dispositivos del conjunto de dispositivos.

    ![Guardar mapas del panel](media/howto-use-device-sets/LocationMaps3.png)


5. Ahora, cuando un operador visualice el panel de conjuntos de dispositivos, puede ver todos los iconos configurados, incluido el mapa de ubicación para visualizar la ubicación de todos los dispositivos de un vistazo. 

    ![Vista del operador de los mapas del panel](media/howto-use-device-sets/LocationMaps4.png)

    Podrá cambiar el tamaño del mapa hasta obtener el tamaño deseado.

    Al hacer clic en una chincheta en el mapa, se mostrarán la información, el nombre y la ubicación del dispositivo. Puede hacer clic en la ventana emergente para ir a la página de propiedades del dispositivo.  


## <a name="configure-the-list-for-your-device-set"></a>Configuración de la lista del conjunto de dispositivos

Después de crear el conjunto de dispositivos, puede configurar la **lista**. La **lista** muestra todos los dispositivos del conjunto de dispositivos con las columnas que elija.

1. Elija **Device Sets** (Conjuntos de dispositivos) en el menú de navegación de la izquierda.

1. Elija la pestaña **Lista**.

1. Elija **Opciones de columna**.

    ![Opciones de columna](media/howto-use-device-sets/image11.png)

1. Elija las columnas que se mostrarán seleccionando la columna que desea mostrar y eligiendo la flecha derecha para seleccionarla.

    ![Elegir columna](media/howto-use-device-sets/image12.png)

1. Elija **Aceptar**.

## <a name="analytics"></a>Análisis

Los análisis de los conjuntos de dispositivos es lo mismo que la pestaña principal de análisis del menú de navegación izquierdo. Puede obtener más información sobre los análisis en el artículo sobre [creación de análisis](howto-create-analytics.md).

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a usar conjuntos de dispositivos en la aplicación de Azure IoT Central, este es el paso siguiente sugerido:

> [!div class="nextstepaction"]
> [Creación de reglas de telemetría](howto-create-telemetry-rules.md)
