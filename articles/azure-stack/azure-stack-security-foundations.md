---
title: Información sobre los controles de seguridad de Azure Stack | Microsoft Docs
description: Como administrador de servicios, conozca los controles de seguridad aplicados a Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: mabrigg
ms.openlocfilehash: a3bd314a1df3c45c76b2e3a5acb31c1474d0fdf5
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011255"
---
# <a name="azure-stack-infrastructure-security-posture"></a>Posición de seguridad de la infraestructura de Azure Stack

*Se aplica a: sistemas integrados de Azure Stack*

Entre los principales factores que determinan el uso de nubes híbridas están las consideraciones de seguridad y las regulaciones de conformidad. Azure Stack se diseñó para estos escenarios, así que es importante comprender los controles ya existentes al adoptarlo.

En Azure Stack coexisten dos capas de posición de seguridad. La primera capa es la infraestructura de Azure Stack, que incluye los componentes de hardware hasta Azure Resource Manager. La primera capa incluye los portales Administrador e Inquilino. La segunda capa consta de las cargas de trabajo creadas, implementadas y administradas por los inquilinos. La segunda capa incluye elementos como las máquinas virtuales y sitios web de App Services.

## <a name="security-approach"></a>Enfoque de seguridad

La posición de seguridad para Azure Stack se diseñó para defenderse contra las amenazas modernas, y se creó para satisfacer los requisitos de los principales estándares de cumplimiento. Como resultado, la posición de seguridad de Azure Stack se cimienta sobre dos pilares:

 - **Supuesto de infracción**  
A partir de la suposición de que el sistema ya se ha infringido, céntrese en *detectar y limitar el impacto de las infracciones*, en lugar de intentar únicamente impedir los ataques. 
 - **Protección predeterminada**  
Dado que la infraestructura se ejecuta en hardware y software bien definidos, Azure Stack *habilita, configura y valida todas las características de seguridad* de forma predeterminada.

Como Azure Stack se proporciona como un sistema integrado, la posición de seguridad de la infraestructura de Azure Stack la define Microsoft. Al igual que en Azure, los inquilinos son responsables de definir la posición de seguridad de sus cargas de trabajo de inquilino. En este documento se proporciona conocimiento básico sobre la posición de seguridad de la infraestructura de Azure Stack.

## <a name="data-at-rest-encryption"></a>Cifrado de datos en reposo
Todos los datos de la infraestructura de Azure Stack y de los inquilinos se cifran en reposo mediante Bitlocker. Este cifrado protege contra la pérdida física o el robo de componentes de almacenamiento de Azure Stack. 

## <a name="data-in-transit-encryption"></a>Cifrado de los datos en tránsito
Los componentes de la infraestructura de Azure Stack se comunican mediante canales cifrados con TLS 1.2. Los certificados de cifrado se administran automáticamente en la infraestructura. 

Todos los puntos de conexión externos de la infraestructura, como los puntos de conexión REST o el portal de Azure Stack, admiten TLS 1.2 para proteger las comunicaciones. Para estos puntos de conexión, se deben proporcionar certificados de cifrado, bien de un tercero o de la entidad de certificación de la empresa. 

Aunque se pueden usar certificados autofirmados para estos puntos de conexión externos, Microsoft aconseja encarecidamente no hacerlo. 

## <a name="secret-management"></a>Administración de secretos
La infraestructura de Azure Stack emplea multitud de secretos, como las contraseñas, para funcionar. La mayoría de ellos se rotan automáticamente con frecuencia, ya que son cuentas de servicio administradas de grupo, que lo hacen cada 24 horas.

El resto de secretos no son cuentas de servicio administradas de grupo y se pueden rotar manualmente con un script en el punto de conexión con privilegios.

## <a name="code-integrity"></a>Integridad del código
Azure Stack emplea las características de seguridad de Windows Server 2016 más recientes. Una de ellas es Device Guard de Windows Defender, que proporciona la inclusión en lista blanca de aplicaciones y que garantiza que solo se ejecuta en la infraestructura de Azure Stack código autorizado. 

El código autorizado lleva la firma de Microsoft o del asociado OEM y se incluye en la lista de software permitido que se especifica en una directiva que define Microsoft. En otras palabras, solo se puede ejecutar software que esté aprobado para ejecutarse en la infraestructura de Azure Stack. Cualquier intento de ejecutar código no autorizado se bloqueará y se generará una auditoría.

La directiva de Device Guard también impide que agentes o software de terceros se ejecuten en la infraestructura de Azure Stack.

## <a name="credential-guard"></a>Protección de credenciales
Otra característica de seguridad de Windows Server 2016 en Azure Stack es Credential Guard de Windows Defender, que se usa para proteger las credenciales de infraestructura de Azure Stack de los ataques pass-the-hash y pass-the-ticket.

## <a name="antimalware"></a>Antimalware
Todos los componentes de Azure Stack (tanto los hosts de Hyper-V como Virtual Machines) están protegidos con el antivirus de Windows Defender.

En escenarios conectados, las actualizaciones de definiciones y motores de antivirus se aplican varias veces al día. En escenarios desconectados, se aplican las actualizaciones de antimalware como parte de las actualizaciones mensuales de Azure Stack. Consulte [actualizar Antivirus de Windows Defender en Azure Stack](azure-stack-security-av.md) para obtener más información.

## <a name="constrained-administration-model"></a>Modelo de administración restringida
La administración de Azure Stack está controlada mediante el uso de tres puntos de entrada, cada uno con un fin específico: 
1. El [portal de administrador](azure-stack-manage-portals.md) proporciona una experiencia "apuntar y hacer clic" para las operaciones de administración diarias.
2. Azure Resource Manager expone todas las operaciones de administración del portal de administrador mediante una API de REST, que usan PowerShell y la CLI de Azure. 
3. Para operaciones específicas de bajo nivel, por ejemplo, integración del centro de datos o escenarios de soporte técnico, Azure Stack expone un punto de conexión de PowerShell denominado [punto de conexión con privilegios](azure-stack-privileged-endpoint.md). Este punto de conexión solo expone un conjunto de cmdlets incluidos en lista blanca y está sometido a una auditoría exhaustiva.

## <a name="network-controls"></a>Controles de red
La infraestructura de Azure Stack incluye varias capas de listas de control de acceso a redes (ACL). Las ACL impiden el acceso no autorizado a los componentes de la infraestructura y limitan las comunicaciones de esta con solo las rutas de acceso que son necesarias para su funcionamiento. 

Las ACL de red se exigen en tres capas:
1.  Parte superior del conmutador del rack
2.  Red definida por el software
3.  Firewalls de sistema operativo host y de máquina virtual

## <a name="next-steps"></a>Pasos siguientes

- [Aprenda a cambiar los secretos en Azure Stack](azure-stack-rotate-secrets.md)
