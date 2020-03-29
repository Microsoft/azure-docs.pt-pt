---
title: Benefícios multi-mestre sacados da Azure Cosmos DB
description: Compreenda os benefícios do multi-master em Azure Cosmos DB, comparação de requisitos de latência e SLA em locais de escrita simples e múltiplos.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: b21b6ba82ba1ada0103501b8beeca270df86abd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74872014"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>Compreender os benefícios multi-master em Azure Cosmos DB

Os operadores de conta Cosmos DB devem escolher a configuração de distribuição global adequada para garantir os requisitos de latência, disponibilidade e RTO para as suas aplicações. As contas da Azure Cosmos configuradas com múltiplas localizações de escrita oferecem benefícios significativos em contas com localização de escrita única, incluindo, 99,999% de disponibilidade de escrita SLA, <10 ms escrevem latência SLA no percentil 99 e RTO = 0 em um desastre regional.

## <a name="comparison-of-features"></a>Comparação de características

|Requisito de candidatura|Múltiplas localizações de escrita|Localização de escrita única|Nota|
|---|---|---|---|
|Escreva latência SLA de <10ms no P99|**Sim**|Não|As contas com a Localização de Escrita Única incorrem em latência adicional da rede inter-região para cada escrita.|
|Ler Latência SLA de <10ms no P99|**Sim**|Sim| |
|Escreva SLA de 99.999%|**Sim**|Não|Contas com Localização De Escrita Única garantem SLA de 99,99%|
|RTO = 0|**Sim**|Não|Zero tempo de paragem para escritos em caso de desastres regionais. As contas com uma única localização escrita têm RTO de 15 min.|

## <a name="next-steps"></a>Passos Seguintes

Se ainda deseja desativar a EnableMultipleWriteLocations na sua conta Azure Cosmos, por favor [abra um bilhete](https://azure.microsoft.com/support/create-ticket/)de apoio .
