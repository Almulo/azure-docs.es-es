---
title: Inicio rápido de Python para Azure Cognitive Services, Bing News Search API | Microsoft Docs
description: Obtenga información y ejemplos de código que le ayuden a empezar a usar rápidamente Bing News Search API en Microsoft Cognitive Services en Azure.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: 0fde478b650513aa1527c1d47f5b453ba094506c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380659"
---
# <a name="quickstart-for-bing-news-search-api-with-python"></a>Inicio rápido para Bing News Search API con Python
Este tutorial muestra un ejemplo sencillo de llamada a Bing News Search API y procesamiento posterior del objeto JSON resultante. Para obtener más información, consulte la [documentación de Bing News Search](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference).  

Para ejecutar este ejemplo como Jupyter Notebook en [MyBinder](https://mybinder.org), puede hacer clic en el distintivo launch Binder: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

## <a name="prerequisites"></a>requisitos previos

Debe tener una [cuenta de Cognitive Services API](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con **Bing Search APIs**. La [cuenta de evaluación gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) es suficiente para esta guía de inicio rápido. Necesita la clave de acceso que se le proporciona al activar la versión de evaluación gratuita, o puede usar una clave de suscripción de pago desde su panel de Azure.

## <a name="running-the-walkthrough"></a>Ejecución del tutorial
En primer lugar, establezca `subscription_key` como la clave de API para el servicio de la API de Bing.


```python
subscription_key = None
assert subscription_key
```

A continuación, compruebe que el punto de conexión `search_url` sea correcto. En este documento, se utiliza un único punto de conexión Bing Search APIs. Si se producen errores de autorización, vuelva a comprobar este valor frente al punto de conexión de búsqueda de Bing en el panel de Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

Establezca `search_term` para buscar nuevos artículos acerca de Microsoft.


```python
search_term = "Microsoft"
```

El siguiente bloque usa la biblioteca `requests` de Python para llamar a Bing Search APIs y devolver los resultados como objeto JSON. Observe que se pasa la clave de API a través del diccionario `headers`, y el término de búsqueda a través del diccionario `params`. Para ver la lista completa de opciones que puede usarse para filtrar los resultados de la búsqueda, consulte la documentación de la [API de REST](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference).


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

El objeto `search_results` contiene los nuevos artículos pertinentes, junto con metadatos enriquecidos. Por ejemplo, la siguiente línea de código extrae las descripciones de los artículos.


```python
descriptions = [article["description"] for article in search_results["value"]]
```

A continuación, estas descripciones se pueden representar como una tabla con la palabra clave de búsqueda resaltada en **negrita**.


```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc) for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Paginación de noticias](paging-news.md)
> [Uso de marcadores de decoración para resaltar texto](hit-highlighting.md)

## <a name="see-also"></a>Otras referencias 

 [Búsqueda de noticias en la web](search-the-web.md)  
 [Pruébelo](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/)
