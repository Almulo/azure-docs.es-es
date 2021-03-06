---
title: 'Esquema de dispositivos en una solución de simulación de dispositivo: Azure | Microsoft Docs'
description: En este artículo se describe el esquema JSON que define un dispositivo simulado en la solución de simulación de dispositivo.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: b325873819caff139727ec15d6aecd2d4be89c9e
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338162"
---
# <a name="understand-the-device-model-schema"></a>Descripción del esquema de modelo del dispositivo

Puede usar el acelerador de soluciones de simulación de dispositivo para generar cargas de prueba para las soluciones que usan IoT Hub. Al implementar la solución de simulación de dispositivo, se aprovisiona automáticamente una colección de dispositivos simulados. Puede personalizar los dispositivos simulados existentes o crear los suyos propios.

En este artículo se describe el esquema de modelo del dispositivo que especifica las características y el comportamiento de un dispositivo simulado. El modelo del dispositivo se almacena en un archivo JSON.

Los siguientes artículos están relacionadas con el artículo actual:

* [Implement the device model behavior](iot-accelerators-device-simulation-device-behavior.md) (Implementar el comportamiento de modelo del dispositivo): describe los archivos de JavaScript que se usan para implementar el comportamiento de un dispositivo simulado.
* [Create a new simulated device](iot-accelerators-device-simulation-create-simulated-device.md) (Crear un nuevo dispositivo simulado): reúne todos los elementos necesarios y le muestra cómo implementar un nuevo tipo de dispositivo simulado en la solución.

En este artículo, aprenderá a:

>[!div class="checklist"]
> * Usar un archivo JSON para definir un modelo de dispositivo simulado.
> * Especificar las propiedades del dispositivo simulado.
> * Especificar la telemetría que envía el dispositivo simulado.
> * Especificar los métodos de tipo "de la nube al dispositivo" a los que el dispositivo responde.

[!INCLUDE [iot-accelerators-device-schema](../../includes/iot-accelerators-device-schema.md)]

## <a name="next-steps"></a>Pasos siguientes

En este artículo se describe la manera de crear su propio modelo de dispositivo simulado personalizado. En este artículo le mostramos cómo:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Usar un archivo JSON para definir un modelo de dispositivo simulado.
> * Especificar las propiedades del dispositivo simulado.
> * Especificar la telemetría que envía el dispositivo simulado.
> * Especificar los métodos de tipo "de la nube al dispositivo" a los que el dispositivo responde.

Ahora que tiene información sobre el esquema JSON, el siguiente paso propuesto aprender a [implementar el comportamiento del dispositivo simulado](iot-accelerators-device-simulation-device-behavior.md).

Si busca más información para desarrolladores sobre la solución de simulación de dispositivo, vea la [Guía de referencia para desarrolladores](https://github.com/Azure/device-simulation-dotnet/wiki/Simulation-Service-Developer-Reference-Guide).
