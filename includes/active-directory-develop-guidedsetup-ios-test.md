---
title: archivo de inclusión
description: archivo de inclusión
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: eb56712610266d974d2a646412700da5806e9c30
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/19/2018
ms.locfileid: "46466188"
---
## <a name="test-querying-the-microsoft-graph-api-from-your-ios-application"></a>Prueba de consultas de Microsoft Graph API desde la aplicación iOS

Para ejecutar el código en el simulador, presione **Comando** + **R**.

![Probar la aplicación en el simulador](media/active-directory-develop-guidedsetup-ios-test/iostestscreenshot.png)

Cuando esté listo para realizar la prueba, seleccione **Llamar a Microsoft Graph API**. Cuando se le pida, escriba su nombre de usuario y la contraseña.

### <a name="provide-consent-for-application-access"></a>Consentimiento para el acceso a la aplicación
La primera vez que inicie sesión en la aplicación, también se le pedirá que dé su consentimiento para permitir que la aplicación obtenga acceso al perfil e inicie sesión automáticamente:

![Consentimiento para el acceso a la aplicación](media/active-directory-develop-guidedsetup-ios-test/iosconsentscreen.png)

### <a name="view-application-results"></a>Visualización de los resultados de la aplicación
Una vez iniciada la sesión, podrá ver la información del perfil de usuario que devolvió la llamada a Microsoft Graph API en la sección **Registro**. 

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Más información sobre los ámbitos y permisos delegados

Microsoft Graph API requiere el ámbito **user.read** para leer el perfil del usuario. Este ámbito se agrega automáticamente de forma predeterminada en todas las aplicaciones que se registran en el portal de registro. Otras API de Microsoft Graph, así como las API personalizadas para el servidor back-end, pueden requerir ámbitos adicionales. Microsoft Graph API requiere el ámbito **Calendars.read** para mostrar los calendarios del usuario.

Para tener acceso a los calendarios del usuario en el contexto de una aplicación, agregue el permiso delegado **Calendars.Read** a la información del registro de la aplicación. A continuación, agregue el ámbito **Calendars.Read** a la llamada **acquireTokenSilent**. 

>[!NOTE]
>Es posible que se pida al usuario algún consentimiento adicional a medida que aumente el número de ámbitos.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
