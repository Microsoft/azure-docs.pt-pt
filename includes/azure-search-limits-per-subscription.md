---
title: incluir ficheiro
description: incluir ficheiro
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: 1e147e8bd9260cd1ece60b70641968a229995ec1
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "67184638"
---
Você pode criar vários serviços em uma assinatura. Cada uma pode ser provisionada em uma camada específica. Você está limitado apenas pelo número de serviços permitidos em cada camada. Por exemplo, você pode criar até 12 serviços na camada básica e outros 12 serviços na camada S1 dentro da mesma assinatura. Para obter mais informações sobre camadas, consulte [escolher uma SKU ou camada para Azure Search](../articles/search/search-sku-tier.md).

Limites de serviço máximos podem ser gerados mediante solicitação. Se você precisar de mais serviços na mesma assinatura, entre em contato com o suporte do Azure.

| Resource            | Gratuito<sup>1</sup> | Básica | S1  | S2 | S3 | S3&nbsp;HD | L1 | ERROS DE L2 |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| Máximo de serviços    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| Escala máxima em unidades de pesquisa (SU)<sup>2</sup> |N/A |3 SU |36 SU |36 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> o gratuito é baseado em recursos compartilhados, não dedicados. Não há suporte para a escala vertical em recursos compartilhados.

<sup>2</sup> as unidades de pesquisa são unidades de cobrança, alocadas como uma *réplica* ou uma *partição*. Você precisa de ambos os recursos para armazenamento, indexação e operações de consulta. Para saber mais sobre os cálculos de SU, consulte [dimensionar níveis de recursos para cargas de trabalho de consulta e índice](../articles/search/search-capacity-planning.md). 