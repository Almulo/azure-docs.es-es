---
title: archivo de inclusión
description: archivo de inclusión
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: a806cac410eb57e59dacb42da9be954b2f962956
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2018
ms.locfileid: "35381311"
---
<a name="paths"></a>
## <a name="paths"></a>Rutas de acceso

<a name="anomalydetection-post"></a>
### <a name="detect-anomaly-points-for-the-time-series-data-points-requested"></a>Detección de puntos de anomalías para los puntos de datos de serie temporal solicitados
```
POST /anomalydetection
```


#### <a name="parameters"></a>Parámetros

|Escriba|NOMBRE|DESCRIPCIÓN|Esquema|
|---|---|---|---|
|**Cuerpo**|**body**  <br>*requerido*|Los puntos de datos de serie temporal y el período, si es necesario.|[Solicitud](#request)|


#### <a name="responses"></a>Respuestas

|Código HTTP|DESCRIPCIÓN|Esquema|
|---|---|---|
|**200**|Operación correcta.|< [response](#response) > array|
|**400**|No se puede analizar la solicitud JSON.|Ningún contenido|
|**403**|El servidor no acepta el certificado proporcionado.|Ningún contenido|
|**405**|Método no permitido.|Ningún contenido|
|**500**|Error interno del servidor.|Ningún contenido|


#### <a name="consumes"></a>Consume

* `application/json`


#### <a name="produces"></a>Produce

* `application/json`


#### <a name="tags"></a>Etiquetas

* anomalydetection



