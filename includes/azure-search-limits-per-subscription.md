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
ms.openlocfilehash: 380a0a76de763df5f2cc6ff702750b36d233728d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818356"
---
Você pode criar vários serviços em uma assinatura. Cada uma pode ser provisionada em uma camada específica. Você está limitado apenas pelo número de serviços permitidos em cada camada. Por exemplo, você pode criar até 12 serviços na camada básica e outros 12 serviços na camada S1 dentro da mesma assinatura. Para obter mais informações sobre camadas, consulte [escolher uma SKU ou camada para o Azure pesquisa cognitiva](../articles/search/search-sku-tier.md).

Limites de serviço máximos podem ser gerados mediante solicitação. Se você precisar de mais serviços na mesma assinatura, entre em contato com o suporte do Azure.

| Recurso            | Gratuito<sup>1</sup> | Básica | S1  | S2 | S3 | S3&nbsp;HD | L1 | Cache |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| Máximo de serviços    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| Escala máxima em unidades de pesquisa (SU)<sup>2</sup> |N/D |3 SU |36 SU |36 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> o gratuito é baseado em recursos compartilhados, não dedicados. Não há suporte para a escala vertical em recursos compartilhados.

<sup>2</sup> as unidades de pesquisa são unidades de cobrança, alocadas como uma *réplica* ou uma *partição*. Você precisa de ambos os recursos para armazenamento, indexação e operações de consulta. Para saber mais sobre os cálculos de SU, consulte [dimensionar níveis de recursos para cargas de trabalho de consulta e índice](../articles/search/search-capacity-planning.md). 