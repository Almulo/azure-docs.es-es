---
title: Escalar SP.NET Core SignalR con Azure SignalR | Microsoft Docs
description: Información general del uso del servicio Azure SignalR para escalar aplicaciones de ASP.NET Core SignalR.
services: signalr
documentationcenter: ''
author: sffamily
manager: cfowler
editor: ''
ms.service: signalr
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: 283566558584960dc2f3361c539093a4c5ee78aa
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2018
ms.locfileid: "46958244"
---
# <a name="scale-aspnet-core-signalr-applications-with-azure-signalr-service"></a>Escalar aplicaciones de ASP.NET Core SignalR con el servicio Azure SignalR

## <a name="developing-signalr-apps"></a>Desarrollo de aplicaciones SignalR

Actualmente, hay [dos versiones](https://docs.microsoft.com/en-us/aspnet/core/signalr/version-differences) de SignalR que se puede usar con las aplicaciones web: SignalR para ASP.NET y ASP.NET Core SignalR, que es la versión más reciente. Azure SignalR Service es un servicio administrado de Azure basado en ASP.NET Core SignalR. 

ASP.NET Core SignalR es una reescritura de la versión anterior. Como resultado, ASP.NET Core SignalR no es compatible con la versión anterior de SignalR. Las API y los comportamientos son diferentes. El SDK de ASP.NET Core SignalR va dirigido a .NET Standard, por lo que también puede usarse con .NET Framework. Sin embargo, debe utilizar las nuevas API en lugar de las antiguas. Si usa SignalR y desea pasarse a ASP.NET Core SignalR, o Azure SignalR Service, debe cambiar el código para gestionar las diferencias en las API.

Con Azure SignalR Service, el componente de servidor de ASP.NET Core SignalR se hospeda en Azure. Sin embargo, dado que la tecnología se basa en ASP.NET Core, tiene la capacidad de ejecutar la aplicación web real en varias plataformas (Windows, Linux y MacOS), a la vez que la hospeda en [Azure App Service](../app-service/app-service-web-overview.md), [IIS](https://docs.microsoft.com/aspnet/core/host-and-deploy/iis/index), [Nginx](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-nginx), [Apache](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-apache), [Docker](https://docs.microsoft.com/aspnet/core/host-and-deploy/docker/index). También puede utilizar un hospedaje autosuficiente en su propio proceso.

Si los objetivos de la aplicación incluyen: compatibilidad con la funcionalidad más reciente para actualizar los clientes web con las actualizaciones de contenido en tiempo real, ejecución en varias plataformas (Azure, Windows, Linux y MacOS), y hospedaje en entornos diferentes, la mejor opción sería aprovechar el servicio Azure SignalR.


## <a name="why-not-deploy-signalr-myself"></a>Por qué no implementar SignalR usted mismo

Sigue siendo un enfoque válido implementar su propia aplicación web de Azure que admita ASP.NET Core SignalR como componente de back-end en la aplicación web general.

Una de las principales razones para usar Azure SignalR Service es su sencillez. Con Azure SignalR Service, no es necesario encargarse de problemas como el rendimiento, la escalabilidad y la disponibilidad. Estos problemas quedarán bajo su control con un contrato de nivel de servicio del 99,9 %.

Además, WebSockets suele ser la técnica de preferencia para admitir las actualizaciones de contenido en tiempo real. Sin embargo, el equilibrio de carga de un gran número de conexiones persistentes de WebSocket se convierte en un problema complicado de resolver cuando se escala. Las soluciones comunes hacen uso del equilibrio de carga de DNS, equilibradores de carga de hardware y equilibrio de carga de software. Azure SignalR Service controla este problema automáticamente.

Otra razón puede ser que en realidad no deba cumplir con ningún requisito para hospedar una aplicación web. La lógica de la aplicación web puede aprovechar la [informática sin servidor](https://azure.microsoft.com/overview/serverless-computing/). Por ejemplo, quizás el código solo se hospede y ejecuta a petición con desencadenadores de [Azure Functions](https://docs.microsoft.com/azure/azure-functions/). Este escenario puede ser engañoso, porque el código solo se ejecuta a petición y no mantiene conexiones prolongadas con los clientes. Azure SignalR Service puede encargarse de esta situación, ya que el servicio ya administra las conexiones por usted. Consulte la [información general sobre cómo usar el servicio SignalR con Azure Functions](signalr-overview-azure-functions.md) para obtener más detalles. 

## <a name="how-does-it-scale"></a>Cómo se escala

Es común escalar SignalR con SQL Server, Azure Service Bus o Redis Cache. Azure SignalR Service controla el enfoque de escalado por usted. El rendimiento y el costo es comparable a estos métodos, sin la complejidad de trabajar con estos otros servicios. Lo único que debe hacer es actualizar el número de unidades del servicio. Cada unidad admite hasta 1000 conexiones de cliente.

## <a name="next-steps"></a>Pasos siguientes
* [Quickstart: Create a chat room with Azure SignalR](signalr-quickstart-dotnet-core.md) (Guía de inicio rápido: Creación de un salón de chat con Azure SignalR)  
  

