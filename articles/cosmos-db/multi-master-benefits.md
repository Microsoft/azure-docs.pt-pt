---
title: Benefícios de vários mestres do Azure Cosmos DB
description: Compreenda as vantagens de múltiplos principais no Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: mjbrown
ms.openlocfilehash: c78e5e4f8d396d777738bddfd6baf086c0b2ecf4
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789296"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>Compreender os benefícios de vários mestres no Azure Cosmos DB

Operadores de conta do cosmos DB devem escolher a configuração de distribuição global adequados para garantir que a latência, disponibilidade e requisitos de RTO para seus aplicativos. Contas do Cosmos do Azure configuradas com várias localizações de escrita oferecem benefícios significativos em contas com a inclusão de localização de escrita única, 99,999% escrever SLA, disponibilidade < 10 ms escrever SLA de latência no percentil de 99 e RTO = 0 num desastre regional.

## <a name="comparison-of-features"></a>Comparação de funcionalidades

|Requisito da aplicação|Múltiplas localizações de escrita|Localização de escrita única|Nota|
|---|---|---|---|
|Escrever o SLA de latência de < 10 MS em P99|**Sim**|Não|Contas com a localização de escrita única incorrem a latência de rede de entre regiões adicionais para cada gravação.|
|Ler o SLA de latência de < 10 MS em P99|**Sim**|Sim| |
|SLA de 99,999% de escrita|**Sim**|Não|Contas com a localização de escrita única garantem SLA de 99,99%|
|RTO = 0|**Sim**|Não|Zero tempo para escritas em caso de desastres regionais. As contas com a localização de escrita única têm RTO de 15 minutos.|

## <a name="next-steps"></a>Próximos Passos

Se ainda quiser desativar EnableMultipleWriteLocations na sua conta do Cosmos do Azure, [abra um pedido de suporte](https://azure.microsoft.com/support/create-ticket/).
