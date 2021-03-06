---
title: Uso compartido externo de Office 365 y colaboración B2B de Azure Active Directory | Microsoft Docs
description: Se describe el uso compartido de recursos con asociados externos mediante Office 365 y la colaboración B2B de Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/24/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 7e0a65e4a5807cb9a6b39feecfd2d5b2643ea4a9
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/27/2018
ms.locfileid: "39330744"
---
# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Uso compartido externo de Office 365 y colaboración B2B de Azure Active Directory

Técnicamente, es lo mismo utilizar el uso compartido externo de Office 365 (OneDrive, SharePoint Online, grupos unificados, etc.) y la colaboración B2B de Azure Active Directory (Azure AD). Todos los usos compartidos externos (excepto OneDrive o SharePoint Online), incluidos los invitados de grupos de Office 365, ya utilizan las API de invitación de colaboración B2B de Azure AD para el uso compartido.

## <a name="how-does-azure-ad-b2b-differ-from-external-sharing-in-sharepoint-online"></a>¿En qué se diferencia Azure AD B2B del uso compartido externo de SharePoint Online?

OneDrive y SharePoint Online tiene un administrador de invitaciones independiente. La compatibilidad con el uso compartido externo en OneDrive o SharePoint Online comenzó antes que Azure AD desarrollara su compatibilidad. Con el tiempo, el uso compartido externo de OneDrive o SharePoint Online ha acumulado varias características y muchos millones de usuarios que usan el patrón de uso compartido integrado del producto. Sin embargo, existen algunas diferencias sutiles entre cómo funciona el uso compartido externo de OneDrive y SharePoint Online y la colaboración B2B de Azure AD. Puede aprender más sobre el uso compartido externo de OneDrive o SharePoint Online en [Información general sobre el uso compartido externo](https://docs.microsoft.com/sharepoint/external-sharing-overview). En general, el proceso difiere de Azure AD B2B de las siguientes maneras:

- OneDrive y SharePoint Online agregan usuarios al directorio después de que los usuarios han canjeado sus invitaciones. Por lo tanto, antes del canje, el usuario no se muestra en el portal de Azure AD. Si, mientras tanto, un usuario recibe una invitación de otro sitio, se genera una nueva invitación. Sin embargo, cuando se usa la colaboración B2B de Azure AD, los usuarios se agregan inmediatamente cuando se les invita por lo que se muestran en todas partes.

- La experiencia de canje en OneDrive y SharePoint Online parece diferente de la experiencia de colaboración B2B de Azure AD. Después de que un usuario canjea una invitación, las experiencias se asemejan.

- Los usuarios invitados a la colaboración B2B de Azure AD se pueden seleccionar en los cuadros de diálogo de uso compartido de OneDrive y SharePoint Online. Los usuarios invitados a OneDrive y SharePoint Online también se muestran en Azure AD después de que canjean sus invitaciones.

- Los requisitos de concesión de licencia son diferentes. Por cada licencia de Azure AD de pago, puede dejar que hasta 5 usuarios invitados accedan a las características de pago de Azure AD. Para más información sobre licencias, consulte [Concesión de licencias B2B de Azure AD](https://docs.microsoft.com/azure/active-directory/b2b/licensing-guidance) y ["¿Qué es un usuario externo?" en la introducción al uso compartido externo de SharePoint Online](https://docs.microsoft.com/sharepoint/external-sharing-overview#what-is-an-external-user).

Para administrar el uso compartido externo en OneDrive o SharePoint Online con la colaboración B2B de Azure AD, establezca la configuración de uso compartido externo de OneDrive o SharePoint Online en **Allow sharing only with the external users that already exist in your organization's directory** (Permitir uso compartido solo con los usuarios externos que ya existan en el directorio de la organización). Los usuarios pueden acceder a los sitios compartidos externamente y elegir entre colaboradores externos que haya agregado el administrador. El administrador puede agregar los colaboradores externos a través de la API de invitación de colaboración B2B.


![La configuración de uso compartido externo de OneDrive y SharePoint Online](media/o365-external-user/odsp-sharing-setting.png)

## <a name="next-steps"></a>Pasos siguientes

* [¿Qué es la colaboración de Azure AD B2B?](what-is-b2b.md)
* [Incorporación de usuarios de colaboración B2B a un rol](add-guest-to-role.md)
* [Delegación de las invitaciones de colaboración B2B](delegate-invitations.md)
* [Grupos dinámicos y colaboración B2B](use-dynamic-groups.md)