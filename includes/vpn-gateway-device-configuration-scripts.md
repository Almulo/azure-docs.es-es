---
title: archivo de inclusión
description: archivo de inclusión
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9cdb608505e594e0020eb33abc869c6bf4b6b263
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/20/2018
ms.locfileid: "30326520"
---
| **Proveedor** | **Familia de dispositivos** | **Versión de firmware** |
| --- | --- | --- |
|Cisco | ISR| IOS 15.1 (versión preliminar)|
|Cisco | ASA | ASA (*) RouteBased (IKEv2: sin BGP) para versión de ASA anterior a 9.8 |
|Cisco | ASA | ASA RouteBased (IKEv2: sin BGP) para versión de ASA superior a 9.8 |
|Juniper | SRX_GA | 12.x|
|Juniper | SSG_GA | ScreenOS 6.2.x|
|Juniper | JSeries_GA | JunOS 12.x|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased VTI|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased BGP|

> [!NOTE]
> (*) Obligatorio: NarrowAzureTrafficSelectors y CustomAzurePolicies (IKE/IPsec)
>