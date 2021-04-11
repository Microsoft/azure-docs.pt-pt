---
title: incluir ficheiro
description: incluir ficheiro
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: include
ms.date: 04/05/2021
ms.author: duau
ms.custom: include file
ms.openlocfilehash: 27f5755ce8b7d204cad6cdc2281d7992bf86615a
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504709"
---
Quando cria um gateway de rede virtual, tem de especificar o SKU de gateway que pretende utilizar. Quando seleciona um SKU de gateway superior, são alocadas mais CPUs e largura de banda ao gateway e, como resultado, este consegue suportar um débito de rede mais alto para a rede virtual. 

Os gateways de rede virtuais ExpressRoute podem utilizar os seguintes SKUs:

|     | Coexistência do ExpressRoute e do VPN Gateway | FastPath | Número máximo de ligações de circuitos |
| --- | --- | --- | --- |
| **Standard** | Sim | No | 4 |
| **HighPerformance** | Sim | No | 4 |
| **UltraPerformance** | Sim | Sim | 16 |