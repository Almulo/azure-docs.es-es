---
title: Uso de Azure Portal para crear un centro de IoT | Microsoft Docs
description: Describe sobre cómo crear, administrar y eliminar los centros de IoT Hub de Azure a través de Azure Portal. Incluye información sobre los niveles de precios, el escalado, la seguridad y la configuración de la mensajería.
author: dominicbetts
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: dobett
ms.openlocfilehash: 0b03ae434e93dbab45235fe67c499497e1257064
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2018
ms.locfileid: "42142715"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Creación de una instancia de IoT Hub mediante Azure Portal

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

En este artículo se describe:

* Cómo buscar el servicio IoT Hub en Azure Portal.
* Cómo crear y administrar instancias de IoT Hub.

## <a name="where-to-find-the-iot-hub-service"></a>Dónde buscar el servicio IoT Hub

Puede buscar el servicio IoT Hub en las siguientes ubicaciones del portal:

* Elija **+ Nuevo** y después **Internet de las cosas**.
* En Marketplace, elija **Internet de las cosas**.

## <a name="create-an-iot-hub"></a>Crear un centro de IoT

Puede crear un IoT Hub con los métodos siguientes:

* La opción **+ Nuevo** abre la hoja que se muestra en la siguiente captura de pantalla. Los pasos para crear el Centro de IoT con este método y con Marketplace son idénticos.

* En Marketplace, elija **Crear** para abrir la hoja que se muestra en la siguiente captura de pantalla.

En las secciones siguientes se describen los distintos pasos para crear una instancia de IoT Hub.

### <a name="choose-the-name-of-the-iot-hub"></a>Elección del nombre del Centro de IoT

Para crear un centro de IoT, debe asignar un nombre al centro. Este nombre debe ser exclusivo para distinguirlo en todas las instancias de IoT Hub.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

### <a name="choose-the-pricing-tier"></a>Elección del plan de tarifa

Puede elegir entre varios niveles, en función del número de características que desee y del número de mensajes que envíe a través de su solución al día. El nivel gratis está pensado para la prueba y evaluación. Permite la conexión de 500 dispositivos con el centro de IoT y hasta 8000 mensajes al día. Cada suscripción a Azure puede crear una instancia de IoT Hub en el nivel gratis. 

Para más información sobre las demás opciones del nivel, consulte la sección [Elección del nivel correcto de IoT Hub](iot-hub-scaling.md).

### <a name="iot-hub-units"></a>Unidades del Centro de IoT

El número de mensajes que se permiten por unidad al día depende del plan de tarifa del centro. Por ejemplo, si desea que el Centro de IoT admita la entrada de 700 000 mensajes, elija dos unidades del nivel de S1.

### <a name="device-to-cloud-partitions-and-resource-group"></a>Dispositivo para particiones en la nube y grupo de recursos

Puede cambiar el número de particiones para un centro de IoT. El número predeterminado de particiones es 4, pero puede elegir un número diferente de la lista desplegable.

No es necesario crear explícitamente un grupo de recursos vacío. Al crear un recurso, puede elegir entre crear un grupo de recursos o usar uno existente.

![Captura de pantalla donde se muestra cómo crear un hub en Azure Portal](./media/iot-hub-create-through-portal/location1.png)

### <a name="choose-subscription"></a>Elección de la suscripción

Azure IoT Hub muestra automáticamente la lista de suscripciones de Azure a la que está vinculada la cuenta de usuario. Puede elegir la suscripción de Azure a la que desea asociar la instancia de IoT Hub.

### <a name="choose-the-location"></a>Selección de la ubicación

La opción de ubicación proporciona una lista de las regiones en las que se ofrece IoT Hub.

### <a name="create-the-iot-hub"></a>Creación del Centro de IoT

Cuando se completen todos los pasos anteriores, ya se puede crear la instancia de IoT Hub. Haga clic en **Crear** para iniciar el proceso de back-end para crear e implementar la instancia de IoT Hub con las opciones elegidas.

La creación de IoT Hub puede tardar unos minutos, ya que la implementación back-end necesita tiempo para ejecutarse en los servidores de ubicación adecuados.

## <a name="change-the-settings-of-the-iot-hub"></a>Cambio de la configuración del Centro de IoT
<!--robinsh these screenshots are out of date -->

Puede cambiar la configuración de un centro de IoT después de crearlo desde la hoja IoT Hub.

![Captura de pantalla que muestra la configuración de IoT Hub](./media/iot-hub-create-through-portal/portal-settings.png)

**Directivas de acceso compartido**: estas directivas definen los permisos para que los dispositivos y servicios se conecten a IoT Hub. Para acceder a estas directivas, haga clic en **Directivas de acceso compartido** en **General**. En esta hoja puede modificar las directivas existentes o agregar una nueva.

### <a name="create-a-policy"></a>Para crear una directiva

* Haga clic en **Agregar** para abrir una hoja. Aquí puede escribir el nombre de la nueva directiva y los permisos que quiere asociar a esta directiva, tal como se muestra en la siguiente ilustración:

    Hay varios permisos que se pueden asociar a estas directivas compartidas. Las directivas **Lectura del Registro** y **Escritura del Registro** conceden derechos de acceso de lectura y escritura para el registro de identidades. Si selecciona la opción de escritura, se elegirá automáticamente la opción de lectura.

    La directiva **Conexión del servicio** concede permiso de acceso a los puntos de conexión de servicio como **Recepción del dispositivo a la nube**. La directiva **Conexión del dispositivo** concede permisos para enviar y recibir mensajes con los puntos de conexión del dispositivo de IoT Hub.

* Haga clic en **Crear** para agregar la directiva recién creada a la lista existente.

   ![Captura de pantalla muestra la adición de una directiva de acceso compartido](./media/iot-hub-create-through-portal/shared-access-policies.png)

## <a name="endpoints"></a>Puntos de conexión

Haga clic en **Endpoints** (Puntos de conexión) para mostrar una lista de puntos de conexión para el centro de IoT que está modificando. Hay dos tipos de puntos de conexión: los integrados en el centro de IoT y los que agrega al centro de IoT después de crearlo.

![Captura de pantalla que muestra la incorporación de un punto de conexión](./media/iot-hub-create-through-portal/messaging-settings.png)

### <a name="built-in-endpoints"></a>Puntos de conexión integrados

Hay dos puntos de conexión integrados: **Cloud to device feedback** (Comentarios de la nube al dispositivo) y **Events** (Eventos).

* Configuración de **Cloud to device feedback** (Comentarios de la nube al dispositivo): esta configuración tiene dos opciones secundarias: **Cloud to Device TTL** (TTL de nube a dispositivo) (período de vida) y **Retention time** (Tiempo de retención) (en horas) para los mensajes. Al crear por primera vez un centro de IoT, ambas configuraciones tienen el valor predeterminado de una hora. Para ajustar la configuración, use los controles deslizantes o escriba los valores.

* Configuración de **Events** (Eventos): esta configuración tiene distintas opciones secundarias, algunas de solo lectura. Estas opciones secundarias se describen en la lista siguiente:

  * **Partitions** (Particiones): se establece un valor predeterminado cuando se crea el centro de IoT. Esta opción permite cambiar el número de particiones.

  * **Punto de conexión y nombre compatible del Centro de eventos**: Cuando se crea IoT Hub, se crea internamente un Event Hub al que el usuario podría necesitar acceder en determinadas circunstancias. No se pueden personalizar los valores de nombre y punto de conexión compatibles con el centro de eventos, pero los puede copiar al hacer clic en **Copy** (Copiar).

  * **Retention Time** (Tiempo de retención): se establece en un día de forma predeterminada, pero se puede cambiar gracias a la lista desplegable. Este valor está en días para la configuración del dispositivo a la nube.

  * **Grupos de consumidores**: los grupos de consumidores permiten que varios lectores lean mensajes con independencia de la instancia de IoT Hub desde la que lo hagan. Cada Centro de IoT se crea con un grupo de consumidores predeterminado. Sin embargo, con esta configuración puede agregar o eliminar grupos de consumidores en los centros de IoT.

  > [!NOTE]
  > El grupo de consumidores predeterminado no se puede modificar ni eliminar.

### <a name="custom-endpoints"></a>Puntos de conexión personalizados

Puede agregar puntos de conexión personalizados al centro de IoT a través del portal. En la hoja **Endpoints** (Puntos de conexión), haga clic en **Add** (Agregar) de la parte superior para abrir la hoja **Add endpoint** (Agregar punto de conexión). Escriba la información necesaria y haga clic en **OK** (Aceptar). El punto de conexión personalizado aparecerá ahora en la hoja principal de **Endpoints** (Puntos de conexión).

![Captura de pantalla que muestra la creación de un punto de conexión personalizado](./media/iot-hub-create-through-portal/endpoint-creation.png)

Puede leer más sobre los puntos de conexión personalizados en [Referencia: Puntos de conexión de IoT Hub]( iot-hub-devguide-endpoints.md).

## <a name="routes"></a>Rutas

Haga clic en **Routes** (Rutas) para administrar el envío de mensajes del dispositivo a la nube de IoT Hub.

![Captura de pantalla que muestra la incorporación de una nueva ruta](./media/iot-hub-create-through-portal/routes-list.png)

Puede agregar rutas adicionales a su IoT Hub; para ello, haga clic en **Agregar** en la parte superior de la hoja **Rutas**, escriba la información necesaria y haga clic en **Aceptar**. La ruta aparecerá en la lista de la hoja principal de **Rutas**. Puede editar una ruta al hacer clic en ella en la lista de rutas. Para habilitar una ruta, haga clic en ella en la lista de rutas y establezca el control de alternancia de **Habilitar** en **Desactivado**. Para guardar los cambios, haga clic en **Aceptar** en la parte inferior de la hoja.

![Captura de pantalla que muestra la edición de una nueva regla de enrutamiento](./media/iot-hub-create-through-portal/route-edit.png)

## <a name="delete-the-iot-hub"></a>Eliminación del Centro de IoT

Puede examinar el Centro de IoT que desea eliminar haciendo clic en **Examinar**y, a continuación, seleccionar el centro adecuado que desea eliminar. Para eliminar la instancia de IoT Hub, haga clic en el botón **Eliminar** debajo del nombre de dicha instancia.

## <a name="next-steps"></a>Pasos siguientes

Siga estos vínculos para más información sobre la administración de Azure IoT Hub:

* [Administración de identidades de dispositivos de Centro de IoT de forma masiva](iot-hub-bulk-identity-mgmt.md)
* [Métricas de IoT Hub](iot-hub-metrics.md)
* [Supervisión de operaciones](iot-hub-operations-monitoring.md)

Para explorar aún más las funcionalidades de IoT Hub, consulte:

* [Guía para desarrolladores de IoT Hub](iot-hub-devguide.md)
* [Implementación del primer módulo de IoT Edge en un dispositivo Linux x64](../iot-edge/tutorial-simulate-device-linux.md)
* [Seguridad de Internet de las cosas desde el principio](../iot-fundamentals/iot-security-ground-up.md)
