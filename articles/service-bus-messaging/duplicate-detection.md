---
title: Detección de mensajes duplicados de Azure Service Bus | Microsoft Docs
description: Detección de mensajes duplicados de Service Bus
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: spelluru
ms.openlocfilehash: 7402fcf01078ea3934d1b6794a9190947fe339c2
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696638"
---
# <a name="duplicate-detection"></a>Detección de duplicados

Si se produce un error irrecuperable en una aplicación inmediatamente después de enviar un mensaje, y la instancia de la aplicación reiniciada cree falsamente que el mensaje anterior no se produjo, un posterior envío provoca que el mismo mensaje aparezca en el sistema dos veces.

También puede producirse un error en el nivel de cliente o de red un momento antes y que un mensaje enviado se confirme en la cola, sin que la confirmación se devuelva al cliente correctamente. Este escenario deja al cliente en duda sobre el resultado de la operación de envío.

La detección de duplicados saca de duda en estas situaciones al permitir que el remitente reenvíe el mismo mensaje, y la cola o el tema descartan cualquier copia duplicada.

La habilitación de la detección de duplicados ayuda a mantener el seguimiento del valor *MessageId* controlado por la aplicación de todos los mensajes enviados a una cola o un tema durante una ventana de tiempo específica. Si se envía un nuevo mensaje que lleva un valor *MessageId* que ya se ha registrado durante la ventana de tiempo, el mensaje se notifica como aceptado (la operación de envío se realiza correctamente), pero el mensaje recién enviado se omite y descarta al instante. No se tiene en cuenta ninguna otra parte del mensaje que no sea *MessageId*.

El control de aplicación del identificador es esencial, ya que es lo único que permite que la aplicación enlace el valor *MessageId* a un contexto de proceso empresarial desde el que se pueda reconstruir de manera predecible en caso de error.

En un proceso empresarial en el que se envían varios mensajes en el transcurso de la administración de algún contexto de aplicación, el valor *MessageId* puede constar del identificador de contexto de nivel de aplicación, como un número de orden de compra, y el asunto del mensaje, por ejemplo, **12345.2017/payment**.

El valor *MessageId* siempre puede ser algún GUID, pero el anclaje del identificador al proceso empresarial produce una capacidad de repetición predecible, lo que resulta conveniente para aprovechar con efectividad la característica de detección de duplicados.

## <a name="enable-duplicate-detection"></a>Habilitación de la detección de duplicados

En el portal, la característica se activa durante la creación de la entidad con la casilla **Habilitar detección de duplicados**, que está desactivada de forma predeterminada. La configuración para la creación de nuevos temas es equivalente.

![][1]

Mediante programación, establezca el indicador con la propiedad [QueueDescription.requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) en la API de .NET Framework completa. Con la API de Azure Resource Manager, el valor se establece con la propiedad [queueProperties.requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values).

El historial de tiempo de detección de duplicados predeterminado es de 30 segundos para colas y temas, con un valor máximo de 7 días. Esta configuración se puede cambiar en la ventana de propiedades de colas y temas de Azure Portal.

![][2]

Mediante programación, puede configurar el tamaño de la ventana de detección de duplicados durante el cual se conservan los identificadores de mensaje, mediante la propiedad [QueueDescription.DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) con la API de .NET Framework completa. Con la API de Azure Resource Manager, el valor se establece con la propiedad [queueProperties.duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values).

Tenga en cuenta que el hecho de habilitar la detección de duplicados y el tamaño de la ventana afecta directamente al rendimiento de la cola (y del tema), dado que todos los identificadores de mensaje registrados deben coincidir con el identificador de mensaje recién enviado.

Mantener la ventana pequeña significa que menos identificadores de mensaje se deben conservar y coincidir, y el rendimiento resulta afectado en un menor grado. En las entidades de alto rendimiento que requieren la detección de duplicados, debe mantener la ventana lo más pequeña posible.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la mensajería de Service Bus, consulte los siguientes temas:

* [Elementos fundamentales de Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Colas, temas y suscripciones de Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introducción a las colas de Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Uso de temas y suscripciones de Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
