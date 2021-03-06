---
title: 'Tutorial: Integración de Azure Active Directory con Dome9 Arc | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Dome9 Arc.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2018
ms.author: jeedes
ms.openlocfilehash: 934520764749b5abce9aefe22b8eb9a5d8e490f2
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746498"
---
# <a name="tutorial-azure-active-directory-integration-with-dome9-arc"></a>Tutorial: Integración de Azure Active Directory con Dome9 Arc

En este tutorial, obtendrá información sobre cómo integrar Dome9 Arc con Azure Active Directory (Azure AD).

La integración de Dome9 Arc con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Dome9 Arc.
- Puede permitir que los usuarios inicien sesión automáticamente en Dome9 Arc (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Dome9 Arc, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Dome9 Arc

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba.
El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de Dome9 Arc desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-dome9-arc-from-the-gallery"></a>Adición de Dome9 Arc desde la galería

Para configurar la integración de Dome9 Arc en Azure AD, debe agregar Dome9 Arc desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Dome9 Arc desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**.

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]

3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Dome9 Arc**, seleccione **Dome9 Arc** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Dome9 Arc en la lista de resultados](./media/dome9arc-tutorial/tutorial_dome9arc_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, va a configurar y probar el inicio de sesión único de Azure AD con Dome9 Arc con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Dome9 Arc para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Dome9 Arc.

Para configurar y probar el inicio de sesión único de Azure AD con Dome9 Arc, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Dome9 Arc](#create-a-dome9-arc-test-user)**: para tener un homólogo de Britta Simon en Dome9 Arc que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Dome9 Arc.

**Para configurar el inicio de sesión único de Azure AD con Dome9 Arc, siga estos pasos:**

1. En Azure Portal, en la página de integración de la aplicación **Dome9 Arc**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/dome9arc-tutorial/tutorial_dome9arc_samlbase.png)

3. En la sección **Dominio y direcciones URL de Dome9 Arc**, realice los siguientes pasos si desea configurar la aplicación en el modo iniciado por **IDP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de Dome9 Arc](./media/dome9arc-tutorial/tutorial_dome9arc_url.png)

    a. En el cuadro de texto **Identificador**, escriba la dirección URL: `https://secure.dome9.com/`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://secure.dome9.com/sso/saml/yourcompanyname`.

    > [!NOTE]
    > Seleccionará el valor de nombre de la empresa en el portal de administración de dome9, un proceso que se explica más adelante en el tutorial.

4. Active **Mostrar configuración avanzada de URL** y siga estos pasos si desea configurar la aplicación en el modo iniciado por **SP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de Dome9 Arc](./media/dome9arc-tutorial/tutorial_dome9arc_url1.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://secure.dome9.com/sso/saml/<yourcompanyname>`.
 
    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de Dome9 Arc](https://dome9.com/about/contact-us/) para obtener estos valores. 

5. La aplicación de software Dome9 Arc espera las aserciones de SAML en un formato concreto. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la sección "**Atributos de usuario**" de la página de integración de aplicaciones. La siguiente captura de pantalla le muestra un ejemplo de esto.

    ![Configurar el atributo de inicio de sesión único](./media/dome9arc-tutorial/tutorial_dome9arc_attribute.png)

6. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo Token SAML como muestra la imagen anterior y realice los siguientes pasos:
    
    | Nombre del atributo  | Valor de atributo | 
    | --------------- | --------------- | 
    | memberof | user.assignedroles |
    
    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar el atributo para agregar el inicio de sesión único](./media/dome9arc-tutorial/tutorial_dome9_04.png)

    ![Configurar el atributo para editar el inicio de sesión único](./media/dome9arc-tutorial/tutorial_attribute_05.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.

    d. Haga clic en **Aceptar**.
    
    > [!NOTE]
    > Consulte este [vínculo](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-enterprise-app-role-management) para obtener información sobre cómo configurar e instalar los roles de la aplicación.

7. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/dome9arc-tutorial/tutorial_dome9arc_certificate.png) 

8. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/dome9arc-tutorial/tutorial_general_400.png)

9. En la sección **Configuración de Dome9 Arc**, haga clic en **Configurar Dome9 Arc** para abrir la ventana **Configurar inicio de sesión**. Copie **SAML Entity ID and SAML Single Sign-On Service URL** (URL del servicio de inicio de sesión único de SAML e Identificador de entidad de SAML) de la sección **Referencia rápida**.

    ![Configuración de Dome9 Arc](./media/dome9arc-tutorial/tutorial_dome9arc_configure.png) 

10. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de Dome9 Arc.

11. Haga clic en **Profile Settings** (Configuración de perfil) en la esquina superior derecha y, luego, en **Account Settings** (Configuración de la cuenta). 

    ![Configuración de Dome9 Arc](./media/dome9arc-tutorial/configure1.png)

12. Vaya a **SSO** y después haga clic en **ENABLE** (HABILITAR).

    ![Configuración de Dome9 Arc](./media/dome9arc-tutorial/configure2.png)

13. En la sección de configuración de SSO, realice los pasos siguientes:

    ![Configuración de Dome9 Arc](./media/dome9arc-tutorial/configure3.png)

    a. Escriba el nombre de la compañía en el cuadro de texto **Account ID** (Id. de cuenta). Este valor se usará en la dirección URL de respuesta mencionada en la sección de la dirección URL de Azure Portal.

    b. En el cuadro de texto **Issuer** (Emisor), pegue el valor de **SAML Entity ID** (Id. de entidad de SAML) que ha copiado de Azure Portal.

    c. En el cuadro de texto **Idp endpoint url** (Dirección URL del punto de conexión de Idp), pegue el valor de la **dirección URL del servicio de inicio de sesión único de SAML** que ha copiado de Azure Portal.

    d. Abra el certificado descargado con codificación Base64 en el Bloc de notas, copie su contenido en el Portapapeles y luego péguelo en el cuadro de texto **X.509 certificate** (Certificado X.509).

    e. Haga clic en **Save**(Guardar).

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/dome9arc-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/dome9arc-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/dome9arc-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/dome9arc-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).

### <a name="create-a-dome9-arc-test-user"></a>Creación de un usuario de prueba de Dome9 Arc

Para permitir que los usuarios de Azure AD inicien sesión en Dome9 Arc, tienen que aprovisionarse en la aplicación. Dome9 Arc admite el aprovisionamiento Just-In-Time, pero para que funcione correctamente, el usuario debe seleccionar un **rol** determinado y asignarlo al usuario.

   >[!Note]
   >Para la creación del **rol** y otros detalles, póngase en contacto con el [equipo de soporte técnico de Dome9 Arc](https://dome9.com/about/contact-us/).

**Para aprovisionar una cuenta de usuario manualmente, realice estos pasos:**

1. Inicie sesión como administrador en el sitio de la compañía de Dome9 Arc.

2. Haga clic en **Users & Roles** (Usuarios y roles) y luego en **Users** (Usuarios).

    ![Agregar empleado](./media/dome9arc-tutorial/user1.png)

3. Haga clic en **ADD USER** (Agregar usuario).

    ![Agregar empleado](./media/dome9arc-tutorial/user2.png)

4. En la sección **Crear usuario** , lleve a cabo estos pasos:

    ![Agregar empleado](./media/dome9arc-tutorial/user3.png)

    a. En el cuadro de texto **Email** (Correo electrónico), escriba el correo electrónico del usuario, en el ejemplo Brittasimon@contoso.com.

    b. En el cuadro de texto **Nombre**, escriba el nombre del usuario, en este caso, Britta.

    c. En el cuadro de texto **Apellidos**, escriba el apellido del usuario, en este caso Simon.

    d. Asegúrese de que **SSO User** (Usuario de SSO) esté establecido en **On** (Activado).

    e. Haga clic en **CREATE** (Crear).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Dome9 Arc.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a Dome9 Arc, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Dome9 Arc**.

    ![Vínculo a Dome9 Arc en la lista de aplicaciones](./media/dome9arc-tutorial/tutorial_dome9arc_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.

### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Dome9 Arc en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Dome9 Arc.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/dome9arc-tutorial/tutorial_general_01.png
[2]: ./media/dome9arc-tutorial/tutorial_general_02.png
[3]: ./media/dome9arc-tutorial/tutorial_general_03.png
[4]: ./media/dome9arc-tutorial/tutorial_general_04.png

[100]: ./media/dome9arc-tutorial/tutorial_general_100.png

[200]: ./media/dome9arc-tutorial/tutorial_general_200.png
[201]: ./media/dome9arc-tutorial/tutorial_general_201.png
[202]: ./media/dome9arc-tutorial/tutorial_general_202.png
[203]: ./media/dome9arc-tutorial/tutorial_general_203.png

