---
title: Log Analytics para proveedores de servicios | Microsoft Docs
description: Log Analytics puede ayudar a proveedores de servicios administrados (MSP), grandes empresas, proveedores de software independientes (ISV) y proveedores de servicios de hospedaje a administrar y supervisar servidores en infraestructuras locales en la nube del cliente.
services: log-analytics
documentationcenter: ''
author: MeirMen
manager: jochan
editor: ''
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: meirm
ms.component: na
ms.openlocfilehash: ad0a3b8e0ee5f1114ea1db95cfe2f4176b8e2ddb
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2018
ms.locfileid: "37931997"
---
# <a name="log-analytics-for-service-providers"></a>Log Analytics para proveedores de servicios
Log Analytics puede ayudar a proveedores de servicios administrados (MSP), grandes empresas, proveedores de software independientes (ISV) y proveedores de servicios de hospedaje a administrar y supervisar servidores en infraestructuras locales en la nube del cliente. 

Las grandes empresas comparten muchas similitudes con los proveedores de servicios, especialmente cuando hay un equipo de TI centralizado que es responsable de la administración de TI de muchas unidades de negocio diferentes. Por motivos de simplicidad, este documento utiliza el término "*proveedor de servicios*", pero la misma funcionalidad también está disponible para empresas y otros clientes.

Para los asociados y proveedores de servicios que forman parte del programa de [proveedores de soluciones en la nube (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview), Log Analytics es uno de los servicios de Azure que hay disponible en una [suscripción de CSP de Azure](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview). 

## <a name="architectures-for-service-providers"></a>Arquitecturas de proveedores de servicios

Las áreas de trabajo de Log Analytics proporcionan un método para que los administradores controlen el flujo y el aislamiento de los registros y creen una arquitectura de registro que aborde sus necesidades empresariales específicas. [En este artículo](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-manage-access) se explican las consideraciones generales sobre la administración del área de trabajo. Los proveedores de servicios tienen consideraciones adicionales.

Hay tres arquitecturas posibles para proveedores de servicios con respecto a las áreas de trabajo de Log Analytics:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. Distribuida: los registros se almacenan en áreas de trabajo que se encuentran en el inquilino del cliente 

En esta arquitectura, el área de trabajo se implementa en el inquilino del cliente que se usa para todos los registros de ese cliente. A los administradores de proveedores de servicios se les concede acceso a esta área de trabajo mediante [usuarios invitados de Azure Active Directory (B2B)](https://docs.microsoft.com/en-us/azure/active-directory/b2b/what-is-b2b). El administrador del proveedor de servicios tendrá que cambiar en Azure Portal al directorio de su cliente para tener acceso a estas áreas de trabajo.

Las ventajas de dicha arquitectura son las siguientes:
* El cliente puede administrar el acceso a los registros mediante su propio [acceso basado en roles](https://docs.microsoft.com/en-us/azure/role-based-access-control/overview).
* Cada cliente puede tener diferentes valores para su área de trabajo, como la retención y límite de datos.
* El aislamiento entre los clientes con respecto a las disposiciones legales y el cumplimiento.
* El cargo por cada área de trabajo se asignará a la suscripción del cliente.
* Los registros se pueden recopilar en todos los tipos de recursos, no solo según el agente. Por ejemplo, Auditoría de Azure.

Las desventajas de dicha arquitectura son las siguientes:
* Es más difícil para el proveedor de servicios administrar un gran número de inquilinos de cliente a la vez.
* Los administradores de proveedores de servicios tienen que aprovisionarse en el directorio de cliente.
* El proveedor de servicios no puede analizar los datos en sus clientes.

### <a name="2-central---logs-are-stored-in-workspace-located-in-the-service-provider-tenant"></a>2. Central: los registros se almacenan en el área de trabajo que se encuentra en el inquilino del proveedor de servicio

En esta arquitectura, los registros no se almacenan en los inquilinos del cliente, sino solo en una ubicación central dentro de una de las suscripciones del proveedor de servicios. Los agentes instalados en máquinas virtuales del cliente se configuran para enviar sus registros a esta área de trabajo con el identificador de área de trabajo y la clave secreta.

Las ventajas de dicha arquitectura son las siguientes:
* Es fácil administrar un gran número de clientes e integrarlos en distintos sistemas back-end.
* El proveedor de servicios tiene una propiedad completa a través de los registros y los distintos artefactos, como son las funciones y las consultas guardadas.
* El proveedor de servicios puede realizar el análisis en todos los clientes.

Las desventajas de dicha arquitectura son las siguientes:
* Esta arquitectura es aplicable únicamente para los datos de máquina virtual basados en un agente, no abarcará orígenes de datos del tejido de Azure, PaaS ni SaaS.
* Puede resultar dificultoso separar los datos entre los clientes cuando se mezclan en una sola área de trabajo. El único método adecuado para ello consiste en usar el nombre del equipo de dominio completo (FQDN) o a través del identificador de la suscripción de Azure. 
* Todos los datos de todos los clientes se almacenarán en la misma región con una sola factura y las mismas opciones de configuración y de retención.
* Los servicios de PaaS y del tejido de Azure, como los Diagnósticos de Azure y la Auditoría de Azure, requieren que el área de trabajo esté en el mismo inquilino que el recurso; por tanto, no pueden enviar los registros al área de trabajo central.
* Todos los agentes de máquina virtual desde todos los clientes se autenticarán en el área de trabajo central con el mismo identificador de área de trabajo y clave. No hay ningún método para bloquear los registros desde un cliente específico sin interrumpir a otros clientes.


### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Híbrido: los registros se almacenan en el área de trabajo que se encuentra en el inquilino del cliente y algunos se extraen a una ubicación central.

La tercera arquitectura es una mezcla de las dos opciones. Se basa en la primera arquitectura distribuida donde los registros son locales para cada cliente, pero con mecanismos para crear un repositorio central de registros. Una parte de los registros se extrae en una ubicación central para los informes y el análisis. Esta parte podría ser un número pequeño de tipos de datos o un resumen de la actividad como la estadística diaria.

Hay dos opciones para implementar la ubicación central en Log Analytics:

1. Área de trabajo central: el proveedor de servicios puede crear un área de trabajo en su inquilino y usar un script que use la [API de consulta](https://dev.loganalytics.io/) con la [API de recopilación de datos](log-analytics-data-collector-api.md) para traer los datos de las diversas áreas de trabajo a esta ubicación central. Otra opción diferente del script es usar [Azure Logic Apps](https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-overview).

2. Power BI como ubicación central: Power BI puede actuar como ubicación central, cuando las diversas áreas de trabajo exportan los datos en ella mediante la integración entre Log Analytics y [Power BI](log-analytics-powerbi.md). 


## <a name="next-steps"></a>Pasos siguientes
* Automatice la creación y configuración de áreas de trabajo con [plantillas de Resource Manager](log-analytics-template-workspace-configuration.md).
* Automatice la creación de áreas de trabajo con [PowerShell](log-analytics-powershell-workspace-configuration.md). 
* Use [alertas](log-analytics-alerts.md) para integrarse con sistemas existentes.
* Genere informes de resumen con [Power BI](log-analytics-powerbi.md).

