---
title: Adición o eliminación de usuarios en Azure Active Directory | Microsoft Docs
description: Describe como agregar usuarios nuevos o eliminar usuarios existentes en Azure Active Directory.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 01/08/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: e6e21ea09909a3bd92a21e15428af347e3f20b93
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/03/2018
ms.locfileid: "37767469"
---
# <a name="quickstart-add-new-users-to-azure-active-directory"></a>Inicio rápido: incorporación de nuevos usuarios a Azure Active Directory
En este artículo se explica cómo eliminar o agregar usuarios de su organización en el inquilino de Azure Active Directory (Azure AD) de la organización mediante Azure Portal o mediante la sincronización de los datos de las cuentas de usuario de Windows Server AD locales. 

## <a name="add-cloud-based-users"></a>Adición de usuarios basados en la nube
1. Inicie sesión en el [Centro de administración de Azure Active Directory](https://aad.portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.
2. Seleccione **Azure Active Directory** y, después, **Usuarios y grupos**.
3. En **Usuarios y grupos**, seleccione **Todos los usuarios** y **Nuevo usuario**.
   ![Selección del comando Agregar](./media/add-users-azure-active-directory/add-user.png)
4. Especifique los detalles del usuario, como el **nombre** y el **nombre de usuario**. La parte del nombre de dominio del nombre de usuario debe ser el nombre de dominio del nombre de dominio predeterminado inicial "[nombre de dominio].onmicrosoft.com" o un [nombre de dominio personalizado](add-custom-domain.md) comprobado y no federado, como "contoso.com".
5. Copie o anote la contraseña de usuario generada para poder proporcionársela al usuario cuando finalice este proceso.
6. También puede abrir y rellenar la información de **Perfil**, **Grupos** o **Rol del directorio** del usuario. Para obtener más información acerca de los roles del usuario y el administrador, consulte [Asignación de roles de administrador en Azure AD](../users-groups-roles/directory-assign-admin-roles.md).
7. En **Usuario**, seleccione **Crear**.
8. Distribuya de manera segura la contraseña generada al nuevo usuario para que pueda iniciar sesión.

> [!TIP]
> También puede sincronizar datos de las cuentas de usuario desde una instancia local de Windows Server AD. Las soluciones de identidad de Microsoft abarcan funcionalidades locales y de nube, de forma que se crea una sola identidad de usuario para la autenticación y la autorización en todos los recursos, sin importar su ubicación. A esto le llamamos identidad híbrida. [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) se puede usar para integrar directorios locales con Azure Active Directory para obtener escenarios de identidad híbridos. Esto le permite proporcionar una identidad común para los usuarios de aplicaciones de Office 365, Azure y SaaS integradas con Azure AD. 

## <a name="delete-users-from-azure-ad"></a>Eliminación de usuarios desde Azure AD
1. Inicie sesión en el [Centro de administración de Azure Active Directory](https://aad.portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.
2. Seleccione **Usuarios y grupos**.
3. En la hoja **Usuarios y grupos**, seleccione el usuarios que va a eliminar de la lista. 
4. En la hoja del usuario seleccionado, seleccione **Información general** y, después, en la barra de comandos, seleccione **Eliminar**.
   ![Selección del comando Agregar](./media/add-users-azure-active-directory/delete-user.png)


### <a name="learn-more"></a>Más información 
* [Adición de usuarios invitados de otro directorio](../b2b/what-is-b2b.md) 
* [Asignar a un usuario a un rol de Azure AD](active-directory-users-assign-role-azure-portal.md)
* [Administrar perfiles de usuario](active-directory-users-profile-azure-portal.md)
* [Restaurar un usuario eliminado](active-directory-users-restore.md)



## <a name="next-steps"></a>Pasos siguientes
En esta guía de inicio rápido, ha aprendido a agregar usuarios nuevos a Azure AD Premium. 

Puede usar el vínculo siguiente para crear un usuario nuevo en Azure AD desde Azure Portal.

>[!div class="nextstepaction"]
>[Adición de usuarios a Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/)