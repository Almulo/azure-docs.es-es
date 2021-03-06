---
title: ¿Qué es QnA Maker?
titleSuffix: Azure Cognitive Services
description: QnA Maker le permite alimentar un servicio de preguntas y respuestas con su contenido semiestructurado, como documentos de preguntas frecuentes o direcciones URL y manuales de productos.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: overview
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: eba7355faf304721fdac4bfdb88f8a69ed41fcba
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038160"
---
# <a name="what-is-qna-maker"></a>¿Qué es QnA Maker?

[QnA Maker](https://qnamaker.ai) le permite alimentar un servicio de preguntas y respuestas con su contenido semiestructurado, como documentos de preguntas frecuentes o direcciones URL y manuales de productos. Puedes crear un modelo de preguntas y respuestas que sea flexible a las consultas de los usuarios, mientras proporciona respuestas con las que se entrenará un bot para que se usen de forma natural y conversacional.

Una interfaz gráfica de usuario fácil de usar le permite crear, administrar, entrenar y poner en marcha su servicio sin necesidad de tener experiencia como desarrollador.

![Información general](../media/qnamaker-overview-learnabout/overview.png)

## <a name="highlights"></a>Aspectos destacados

- Una experiencia **sin código** completa para [crear un bot de preguntas frecuentes](https://aka.ms/qnamaker-docs-create-faqbot).
- **No más limitación de la red**. Pague por el hospedaje del servicio y no por el número de transacciones. Consulte la [página de precios](https://aka.ms/qnamaker-docs-pricing) para más información.
- **Escale según sus necesidades**. Seleccione las SKU apropiadas de los componentes individuales que se adapten a su escenario. Consulte cómo [elegir la capacidad](https://aka.ms/qnamaker-docs-capacity) para el servicio QnA Maker.
- **Compatibilidad completa de datos**. Los datos y los componentes del entorno de tiempo de ejecución se implementan en la suscripción de Azure del usuario y dentro de su límite de cumplimiento. Consulte a continuación para más información.

## <a name="key-qna-maker-processes"></a>Principales procesos de QnA Maker

QnA Maker proporciona dos servicios clave para los datos:

* **Extracción**: los datos de preguntas y respuestas estructurados se extraen de orígenes de datos semiestructurados como preguntas frecuentes y manuales de productos. Esta extracción se realiza al crear la base de conocimiento. Cree [aquí](https://aka.ms/qnamaker-docs-createkb) la base de conocimiento.

* **Coincidencia**: una vez que la base de conocimientos se ha [entrenado y probado](https://aka.ms/qnamaker-docs-trainkb), se [publica](https://aka.ms/qnamaker-docs-publishkb). Esto habilita un punto de conexión a la base de conocimiento de QnA Maker, que puede usar en su bot o aplicación. Este punto de conexión acepta una pregunta del usuario y responde con la mejor coincidencia de pregunta y respuesta de la base de conocimientos, junto con una puntuación de confianza para la coincidencia.

## <a name="qna-maker-architecture"></a>Arquitectura de QnA Maker

La pila de QnA Maker consta de las siguientes partes:

1. **Servicios de administración de QnA Maker (plan de control)**: la experiencia de administración para una base de conocimiento de QnA Maker, que incluye la creación inicial, actualización, entrenamiento y publicación. Estas actividades se pueden realizar mediante el [portal](https://qnamaker.ai) o las [API de administración](https://aka.ms/qnamaker-v4-apis). Los servicios de administración se comunican con el componente del entorno de tiempo de ejecución que aparece a continuación.

2. **Entorno de tiempo de ejecución de QnA Maker (plano de datos)**: los datos y el entorno de tiempo de ejecución se implementan en la suscripción de Azure del usuario en una región de su elección. El contenido de preguntas y respuestas del cliente se almacena en [Azure Search](https://azure.microsoft.com/services/search/) y el entorno de tiempo de ejecución se implementa como un [servicio de aplicaciones](https://azure.microsoft.com/services/app-service/). Si lo desea, también puede elegir implementar un recurso de [Application Insights](https://azure.microsoft.com/services/application-insights/) para el análisis.

![Arquitectura](../media/qnamaker-overview-learnabout/architecture.png)

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Crear un servicio QnA Maker](../how-to/set-up-qnamaker-service-azure.md)
