---
title: incluir ficheiro
description: incluir ficheiro
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 04/04/2018
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: a466ea29fa31c4c628724f3d5138a1612ef7a0f4
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553915"
---
Pode criar vários serviços numa subscrição. Cada um deles pode ser aprovisionado num escalão específico. Está limitado apenas pelo número de serviços permitidas em cada escalão. Por exemplo, pode criar até 12 serviços no escalão básico e outro serviços 12 no escalão S1 dentro da mesma subscrição. Para obter mais informações sobre as camadas, consulte [escolher um SKU ou camada para o Azure Search](../articles/search/search-sku-tier.md).

Limites do serviço máximo podem ser tratados mediante pedido. Se precisar de mais serviços na mesma subscrição, contacte o suporte do Azure.

| Recurso            | Gratuito<sup>1</sup> | Básica | S1  | S2 | S3 | S3&nbsp;HD |
| ------------------- | ---- | ----- | --- | -- | -- | ----- |
| Serviços máximos    |1     | 12    | 12  | 6  | 6  | 6     |
| Dimensionamento máximo em unidades de pesquisa (SU)<sup>2</sup> |N/A |3 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup>gratuito baseia-se nos recursos não dedicados, e partilhados. Aumentar verticalmente não é suportada em recursos partilhados.

<sup>2</sup>unidades de pesquisa são faturação unidades, alocadas como um *réplica* ou uma *partição*. Precisa de ambos os recursos de armazenamento, indexação e operações de consulta. Para saber mais sobre os cálculos de SU, veja [Dimensionar níveis de recursos para cargas de trabalho de consulta e índice](../articles/search/search-capacity-planning.md). 