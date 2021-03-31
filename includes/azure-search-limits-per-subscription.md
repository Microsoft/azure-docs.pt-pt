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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "80272719"
---
Pode criar vários serviços dentro de uma subscrição. Cada um pode ser a provisionado a um nível específico. Está limitado apenas pelo número de serviços permitidos em cada nível. Por exemplo, pode criar até 12 serviços no nível Básico e outros 12 no nível S1 dentro da mesma subscrição. Para obter mais informações sobre níveis, consulte [Escolha um SKU ou nível para Azure Cognitive Search](../articles/search/search-sku-tier.md).

Os limites máximos de serviço podem ser aumentados mediante solicitação. Se precisar de mais serviços dentro da mesma subscrição, contacte o Azure Support.

| Recurso            | Grátis<sup>1</sup> | Básico | S1  | S2 | S3 | S3 &nbsp; HD | L1 | L2 |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| Serviços máximos    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| Escala máxima nas unidades de busca (SU)<sup>2</sup> |N/D |3 SU |36 SU |36 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> O livre baseia-se em recursos partilhados, não dedicados. A escala não é suportada em recursos partilhados.

<sup>2</sup> As unidades de busca são unidades de faturação, atribuídas como *uma réplica* ou uma *divisória.* Precisa de ambos os recursos para armazenamento, indexação e operações de consulta. Para saber mais sobre os cálculos de SU, consulte [os níveis de recursos de escala para consultas e cargas de trabalho de índice.](../articles/search/search-capacity-planning.md) 