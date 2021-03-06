---
title: 'Adición de intenciones y entidades creadas previamente para extraer datos comunes en Language Understanding: Azure | Microsoft Docs'
description: Aprenda a usar intenciones y entidades creadas previamente para extraer distintos tipos de datos de entidad.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/03/2018
ms.author: diberry
ms.openlocfilehash: 0e45b659508c71a9f1220ef5e76b9a95438fa1e6
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162247"
---
# <a name="tutorial-2-add-prebuilt-intents-and-entities"></a>Tutorial: 2. Agregar entidades e intenciones creados previamente
Agregue intenciones y entidades creadas previamente a la aplicación de tutorial de recursos humanos para obtener de manera inmediata predicciones de intenciones y extracciones de datos. 

En este tutorial, aprenderá a:

> [!div class="checklist"]
* Agregar intenciones creadas previamente 
* Agregar las entidades creadas previamente datetimeV2 y number
* Entrenar y publicar
* Consultar a LUIS y recibir una respuesta de predicción

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Antes de empezar
Si no tiene la aplicación de [Recursos humanos](luis-quickstart-intents-only.md) del tutorial anterior, [importe](luis-how-to-start-new-app.md#import-new-app) el archivo JSON a una nueva aplicación en el sitio web de [LUIS](luis-reference-regions.md#luis-website), desde el repositorio Github [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-intent-only-HumanResources.json).

Si quiere conservar la aplicación original de Recursos humanos, clone la versión en la página [Configuración](luis-how-to-manage-versions.md#clone-a-version) y llámela `prebuilts`. La clonación es una excelente manera de trabajar con distintas características de LUIS sin que afecte a la versión original. 

## <a name="add-prebuilt-intents"></a>Agregar intenciones creadas previamente
LUIS proporciona varias intenciones creadas previamente para ayudarle con las intenciones de usuario comunes.  

1. Asegúrese de que la aplicación se encuentra en la sección **Build** (Crear) de LUIS. Para cambiar a esta sección, seleccione **Build** (Crear) en la barra de menús superior derecha. 

2. Haga clic en **Add prebuilt domain intent** (Agregar intención de dominio creado previamente). 

    [ ![Captura de pantalla de la página Intents (Intenciones) con el botón Add prebuilt domain intent (Agregar intención de dominio creado previamente) resaltado](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png) ](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png#lightbox)

3. Busque `Utilities`. 

    [ ![Captura de pantalla del cuadro de diálogo de intenciones creadas previamente con las utilidades en el cuadro de búsqueda](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

4. Seleccione las siguientes intenciones y haga clic en **Done** (Listo): 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.StartOver
    * Utilities.Stop


## <a name="add-prebuilt-entities"></a>Agregar entidades creadas previamente
LUIS proporciona varias entidades creadas previamente para la extracción de datos comunes. 

1. Seleccione **Entities** (Entidades) en el menú de navegación izquierdo.

    [ ![Captura de pantalla de la lista de intenciones con el botón Entities (Entidades) resaltado en el panel de navegación izquierdo](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png)](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png#lightbox)

2. Haga clic en el botón **Manage prebuilt entities** (Administrar entidades creadas previamente).

    [ ![Captura de pantalla de la lista de entidades con el botón Manage prebuilt entities (Administrar entidades creadas previamente) resaltado](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png)](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png#lightbox)

3. Seleccione **number** (número) y **datetimeV2** en la lista de entidades creadas previamente y, después, haga clic en **Done** (Listo).

    ![Captura de pantalla del cuadro de diálogo de la selección de número en entidades creadas previamente](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train-and-publish-the-app"></a>Entrenamiento y publicación de la aplicación

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-app-to-endpoint"></a>Publicación de la aplicación en un punto de conexión

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-endpoint-with-an-utterance"></a>Consultar un punto de conexión con una expresión

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Vaya al final de la dirección URL en la dirección y escriba `I want to cancel on March 3`. El último parámetro de la cadena de consulta es `q`, la expresión **query**. 

    El resultado predice la intención Utilities.Cancel y extrae la fecha del 3 de marzo y el número 3. 

    ```
    {
      "query": "I want to cancel on March 3",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.7818295
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.7818295
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0237864349
        },
        {
          "intent": "GetJobInformation",
          "score": 0.017576348
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0130122062
        },
        {
          "intent": "Utilities.Help",
          "score": 0.006731322
        },
        {
          "intent": "None",
          "score": 0.00524190161
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.004912514
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00092950504
        }
      ],
      "entities": [
        {
          "entity": "march 3",
          "type": "builtin.datetimeV2.date",
          "startIndex": 20,
          "endIndex": 26,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2018-03-03"
              },
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2019-03-03"
              }
            ]
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 26,
          "endIndex": 26,
          "resolution": {
            "value": "3"
          }
        }
      ]
    }
    ```

    Hay dos valores para el 3 de marzo porque la expresión no indicaba si el 3 de marzo es pasado o futuro. Depende de la solicitud de llamada a LUIS hacer una suposición o pedir aclaraciones, si es necesario. 

    Agregando intenciones y entidades creadas previamente de forma fácil y rápida, la aplicación cliente puede agregar una administración de conversaciones y extraer tipos de datos comunes. 

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Adición de una entidad de expresión regular a la aplicación](luis-quickstart-intents-regex-entity.md)

