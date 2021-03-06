---
title: Creación de una base de conocimiento que no está en inglés - QnA Maker - Azure Cognitive Services | Microsoft Docs
description: Creación de una base de conocimiento que no está en inglés.
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 3fbd590229044af0daa60968fd8d556d539a58c9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2018
ms.locfileid: "35381791"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Compatibilidad de idioma del contenido de la base de conocimiento para QnA Maker
QnA Maker admite el contenido de la base de conocimiento en varios idiomas. Sin embargo, cada servicio QnA Maker se debe reservar para un único idioma. La primera base de conocimiento creada para un servicio QnA Maker particular establece el idioma de dicho servicio. Consulte [aquí](../Overview/languages-supported.md) la lista de idiomas admitidos.

El idioma se reconoce automáticamente a partir del contenido de los orígenes de datos que se van a extraer. Tras crear un servicio QnA Maker y una base de conocimiento en dicho servicio, puede verificar que el idioma se ha establecido correctamente.

1. Vaya a [Azure Portal](https://portal.azure.com/).

2. Seleccione **grupos de recursos** y vaya al grupo de recursos en que se implementa el servicio QnA Maker y seleccione el recurso **Azure Search**.

    ![Selección del recurso Azure Search](../media/qnamaker-how-to-language-kb/select-azsearch.png)

3. Seleccione el índice **testkb**. Este índice de Azure Search siempre es el primero que se crea y contiene el contenido guardado de todas las bases de conocimiento de dicho servicio. 

    ![Selección de la base de conocimiento de prueba](../media/qnamaker-how-to-language-kb/select-testkb.png)

4. Seleccione la sección **Campos** que muestra los detalles de testkb.

    ![Selección de campos](../media/qnamaker-how-to-language-kb/selectfields.png)

5. Marque la casilla del **analizador** para ver los detalles del idioma.

    ![Selección del analizador](../media/qnamaker-how-to-language-kb/select-analyzer.png)

6. Debería encontrarse con que el analizador está establecido en un idioma determinado. Este idioma se detecta automáticamente durante el paso de creación de la base de conocimiento. Este idioma no se puede cambiar una vez creado el recurso.

    ![Analizador seleccionado](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Crear un bot QnA con Azure Bot Service](../Tutorials/create-qna-bot.md)
