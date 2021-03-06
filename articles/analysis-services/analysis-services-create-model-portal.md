---
title: Creación de un modelo tabular con Azure Analysis Services Web Designer | Microsoft Docs
description: Aprenda a crear un modelo tabular de Azure Analysis Services mediante Web Designer en Azure Portal.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: dcfcfb24d2b47a8272c576856fc3accc547f354a
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2018
ms.locfileid: "42146036"
---
# <a name="create-a-model-in-azure-portal"></a>Creación de un modelo en Azure Portal

La característica Azure Analysis Services Web Designer (versión preliminar) de Azure Portal proporciona una manera rápida y sencilla de crear y editar los modelos tabulares y consultar datos del modelo directamente en el explorador. 

Tenga en cuenta que Web Designer está en **versión preliminar**. La funcionalidad es limitada. Para llevar a cabo el desarrollo y las pruebas de modelos más avanzados, es mejor usar Visual Studio (SSDT) y SQL Server Management Studio (SSMS).

## <a name="before-you-begin"></a>Antes de empezar

- El servidor de Azure Analysis Services debe estar en el nivel Standard o Developer. Los modelos nuevos creados con Web Designer son DirectQuery, y solo son compatibles con estos niveles.
- Un archivo Power BI Desktop (.pbix), Azure SQL Database o Azure SQL Data Warehouse como origen de datos. Los nuevos modelos creados a partir de archivos de Power BI Desktop admiten Azure SQL Database y Azure SQL Data Warehouse.
- Una cuenta de SQL Server y la contraseña para conectarse a orígenes de datos de Azure SQL Database o Azure SQL Data Warehouse.
- Debe tener privilegios de administrador de servidor para crear un nuevo modelo. Es necesario tener privilegios de administrador de base de datos para editar y consultar un modelo con el diseñador.

## <a name="sign-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal

Inicie sesión en el [Azure Portal](https://portal.azure.com/).

## <a name="to-create-a-new-tabular-model"></a>Para crear un modelo tabular

1. En la opción **Información general** > **Web designer** (Diseñador web) del servidor, haga clic en **Abrir**.

    ![Creación de un modelo en Azure Portal](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. En **Web designer** > **Modelos**, haga clic en **+ Agregar**.

    ![Creación de un modelo en Azure Portal](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. En **Nuevo modelo**, escriba un nombre de modelo y, a continuación, seleccione un origen de datos.

    ![Cuadro de diálogo Nuevo modelo en Azure Portal](./media/analysis-services-create-model-portal/aas-create-portal-new-model.png)

4. En **Connect** (Conexión), especifique las propiedades de conexión. El nombre de usuario y la contraseña deben ser de una cuenta de SQL Server.

     ![Cuadro de diálogo Connect (Conexión) en Azure Portal](./media/analysis-services-create-model-portal/aas-create-portal-connect.png)

5. En **Tablas y vistas**, seleccione las tablas que desee incluir en el modelo y, a continuación, haga clic en **Crear**. Se crean automáticamente relaciones entre las tablas con un par de claves.

     ![Selección de tablas y vistas](./media/analysis-services-create-model-portal/aas-create-portal-tables.png)

El nuevo modelo aparece en el explorador. Desde aquí puede:   

- Consultar los datos del modelo arrastrando campos al diseñador de consultas y agregando filtros.
- Crear nuevas medidas en tablas.
- Editar los metadatos del modelo mediante el editor json.
- Abrir el modelo en Visual Studio (SSDT), Power BI Desktop o Excel.

![Selección de tablas y vistas](./media/analysis-services-create-model-portal/aas-create-portal-query.png)

> [!NOTE]
> Al editar los metadatos del modelo o crear nuevas medidas en el explorador, está guardando los cambios del modelo en Azure. Si también está trabajando en el modelo en SSDT, Power BI Desktop o Excel, el modelo puede dejar de estar sincronizado.


## <a name="next-steps"></a>Pasos siguientes 
[Administración de usuarios y roles de base de datos](analysis-services-database-users.md)  
[Conexión con Excel](analysis-services-connect-excel.md)  


