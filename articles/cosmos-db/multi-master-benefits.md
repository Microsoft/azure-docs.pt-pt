---
title: Azure Cosmos DB benefícios de vários mestres
description: Entenda os benefícios de vários mestres no Azure Cosmos DB, a comparação de requisitos de latência e SLA em locais de gravação únicos e múltiplos.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: b21b6ba82ba1ada0103501b8beeca270df86abd9
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872014"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>Entenda os benefícios de vários mestres no Azure Cosmos DB

Cosmos DB operadores de conta devem escolher a configuração de distribuição global apropriada para garantir a latência, a disponibilidade e os requisitos de RTO para seus aplicativos. As contas do Azure Cosmos configuradas com vários locais de gravação oferecem benefícios significativos em relação às contas com um único local de gravação, incluindo o SLA de disponibilidade de gravação de 99,999%, < o SLA de latência de gravação de 10 ms no 99 º percentil e RTO = 0 em um desastre regional.

## <a name="comparison-of-features"></a>Comparação de recursos

|Requisito de aplicativo|Vários locais de gravação|Local de gravação única|Nota|
|---|---|---|---|
|SLA de latência de gravação de < 10 ms em P99|**Sim**|Não|Contas com local de gravação única incorrem em latência de rede entre regiões adicional para cada gravação.|
|SLA de latência de leitura de < 10 ms em P99|**Sim**|Sim| |
|Gravar SLA de 99,999%|**Sim**|Não|Contas com local de gravação única garantem SLA de 99,99%|
|RTO = 0|**Sim**|Não|Tempo de inatividade zero para gravações em caso de desastres regionais. As contas com um único local de gravação têm o RTO de 15 min.|

## <a name="next-steps"></a>Próximos Passos

Se você ainda quiser desabilitar o EnableMultipleWriteLocations em sua conta do cosmos do Azure, [abra um tíquete de suporte](https://azure.microsoft.com/support/create-ticket/).
