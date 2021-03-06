---
title: Tutorial de Computer Vision API para Python | Microsoft Docs
description: Obtenga información acerca de cómo usar Computer Vision API con Python utilizando Jupyter Notebook en Microsoft Cognitive Services. Visualice los resultados mediante bibliotecas populares.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 02/25/2017
ms.author: kefre
ms.openlocfilehash: a093c2d066e70a8daf1fe1cd33ccf794ecb196af
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380675"
---
# <a name="computer-vision-api-python-tutorial"></a>Tutorial de Computer Vision API para Python

En este tutorial se muestra cómo utilizar Computer Vision API en Python y cómo visualizar los resultados mediante algunas bibliotecas populares. Utilice Jupyter para ejecutar el tutorial. Para obtener información sobre cómo empezar a trabajar con cuadernos interactivos de Jupyter Notebook, consulte la [documentación de Jupyter](http://jupyter.readthedocs.io/en/latest/index.html). 

### <a name="opening-the-tutorial-notebook-in-jupyter"></a>Apertura del cuaderno del tutorial en Jupyter 

1. Navegue hasta el [cuaderno del tutorial en GitHub](https://github.com/Microsoft/Cognitive-Vision-Python). 
2. Haga clic en el botón verde para clonar o descargar el tutorial. 
3. Abra un símbolo del sistema y vaya a la carpeta _Cognitive-Vision-Python-master\Jupyter Notebook_. 
4. Ejecute el comando **jupyter notebook** desde el símbolo del sistema. Esto iniciará Jupyter.
5. En la ventana de Jupyter, haga clic en _Computer Vision API Example.ipynb_ para abrir el cuaderno del tutorial. 

### <a name="running-the-tutorial"></a>Ejecución del tutorial

Para utilizar este cuaderno, necesitará una clave de suscripción para Computer Vision API. Visite la [página de suscripción](https://azure.microsoft.com/try/cognitive-services/) para registrarse. En la página "Inicio de sesión", use su cuenta de Microsoft para iniciar sesión y podrá suscribirse y obtener claves gratuitas. Después de completar el proceso de suscripción, pegue la clave en la sección de variables del cuaderno (se reproduce a continuación). Funcionará la clave principal o la secundaria. Asegúrese de encerrar la clave entre comillas para que sea una cadena.

```python
# Variables

_url = 'https://westcentralus.api.cognitive.microsoft.com/vision/v1/analyses'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10
```
