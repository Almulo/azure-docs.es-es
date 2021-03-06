---
title: Desarrollo de aplicaciones para Azure Stack | Microsoft Docs
description: Consideraciones a la hora de desarrollar prototipos de aplicaciones en Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: a6f89f9a7e5960e4749c14fc9a4adb648f6781f4
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2018
ms.locfileid: "41946490"
---
# <a name="develop-for-azure-stack"></a>Desarrollo para Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede empezar a desarrollar aplicaciones hoy mismo, incluso si no tiene acceso a un entorno de Azure Stack. Dado que Azure Stack proporciona servicios de Microsoft Azure que se ejecutan en el centro de datos, puede usar herramientas y procesos similares para desarrollar con Azure Stack tal como lo haría con Azure. Con un poco de preparación y la orientación que le proporcionarán los siguientes temas, puede usar Azure para emular un entorno de Azure Stack.

* En Azure, puede crear plantillas de Azure Resource Manager que pueden implementarse en Azure Stack. Consulte el artículo sobre [consideraciones de las plantillas](azure-stack-develop-templates.md) para obtener ayuda acerca de cómo desarrollar las plantillas para garantizar la portabilidad.
* Existen diferencias en la disponibilidad y la versión del servicio entre Azure y Azure Stack. Puede usar el [módulo de directivas de Azure Stack](azure-stack-policy-module.md) para restringir los tipos de recursos y la disponibilidad del servicio de Azure a fin de adaptarlos a la disponibilidad de Azure Stack. La restricción de los servicios permitirá que las aplicaciones se basen en los servicios disponibles de Azure Stack.
* Las [plantillas de inicio rápido de Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) son ejemplos comunes de escenarios que muestran cómo desarrollar plantillas que puedan implementarse en Azure y Azure Stack.
