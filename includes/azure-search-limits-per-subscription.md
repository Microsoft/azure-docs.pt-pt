---
title: incluir ficheiro
description: incluir ficheiro
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: ee430241173a6c19e2a32e176f28411631d9cb19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272719"
---
Pode criar vários serviços dentro de uma subscrição. Cada um pode ser provisionado a um nível específico. Está limitado apenas pelo número de serviços permitidos em cada nível. Por exemplo, pode criar até 12 serviços no nível Básico e outros 12 serviços no nível S1 dentro da mesma subscrição. Para mais informações sobre os níveis, consulte [Escolha um SKU ou um nível para Pesquisa Cognitiva Azure](../articles/search/search-sku-tier.md).

Os limites máximos de serviço podem ser aumentados mediante solicitação. Se precisar de mais serviços dentro da mesma subscrição, contacte o Suporte Azure.

| Recurso            | Grátis<sup>1</sup> | Básico | S1  | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| Serviços máximos    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| Escala máxima em unidades de busca (SU)<sup>2</sup> |N/D |3 SU |36 SU |36 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> O Free baseia-se em recursos partilhados, não dedicados. A escala não é apoiada em recursos partilhados.

<sup>2</sup> As unidades de pesquisa são unidades de faturação, atribuídas como *uma réplica* ou uma *divisória*. Precisa de ambos os recursos para operações de armazenamento, indexação e consulta. Para saber mais sobre as computações da SU, consulte [os níveis de recursos de escala para consultas e cargas de trabalho indexadas](../articles/search/search-capacity-planning.md). 