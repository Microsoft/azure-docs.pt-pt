---
title: Azure Cosmos DB consistência, disponibilidade e compensações de desempenho
description: Disponibilidade e compensações de desempenho para vários níveis de consistência em Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/23/2020
ms.reviewer: sngun
ms.openlocfilehash: 4e2cb2b93010478338cd40236403da4ca0ca99fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91825265"
---
# <a name="latency-availability-and-performance-tradeoffs-with-different-azure-cosmos-db-consistency-levels"></a>Trocas de latência, disponibilidade e desempenho com diferentes níveis de consistência do Azure Cosmos DB

As bases de dados distribuídas que dependem da replicação para elevada disponibilidade, baixa latência ou ambos, têm vantagens e desvantagens. As vantagens e desvantagens estão entre a consistência da leitura vs disponibilidade, latência e débito.

Azure Cosmos DB aborda a consistência dos dados como um espectro de escolhas. Esta abordagem inclui mais opções do que os dois extremos de forte e eventual consistência. Pode escolher entre cinco níveis bem definidos no espectro de consistência. Dos mais fortes aos mais fracos, os níveis são:

- *Forte*
- *Estagnação limitada*
- *Sessão*
- *Prefixo consistente*
- *Eventual*

Cada nível fornece compensações de disponibilidade e desempenho e é apoiado por SLAs abrangentes.

## <a name="consistency-levels-and-latency"></a>Níveis de consistência e latência

A latência lida para todos os níveis de consistência é sempre garantida ser inferior a 10 milissegundos no percentil 99. Esta latência lida é apoiada pelo SLA. A latência média de leitura, no percentil 50, é tipicamente de 4 milissegundos ou menos.

A latência da escrita para todos os níveis de consistência é sempre garantida ser inferior a 10 milissegundos no percentil 99. Esta latência escrita é apoiada pelo SLA. A latência média da escrita, no percentil 50, é geralmente de 5 milissegundos ou menos. As contas da Azure Cosmos que abrangem várias regiões e estão configuradas com forte consistência são uma exceção a esta garantia.

### <a name="write-latency-and-strong-consistency"></a>Escreva latência e forte consistência

Para as contas de Azure Cosmos configuradas com forte consistência com mais de uma região, a latência da escrita é igual a duas vezes tempo de ida e volta (RTT) entre qualquer uma das duas regiões mais distantes, mais 10 milissegundos no percentil 99. A alta rede RTT entre as regiões traduzir-se-á numa maior latência para os pedidos de DB da Cosmos, uma vez que a forte consistência só completa uma operação depois de garantir que foi comprometida com todas as regiões dentro de uma conta.

A latência exata do RTT é uma função da distância de velocidade da luz e da topologia de rede Azure. A rede Azure não fornece SLAs de latência para o RTT entre duas regiões de Azure. Para a sua conta Azure Cosmos, as latências de replicação são exibidas no portal Azure. Pode utilizar o portal Azure (vá à lâmina Métricas, selecione 'Aba de Consistência' para monitorizar as latências de replicação entre várias regiões que estão associadas à sua conta Azure Cosmos.

> [!IMPORTANT]
> A forte consistência das contas com regiões com uma extensão superior a 8000 km é bloqueada por defeito devido à elevada latência escrita. Para ativar esta capacidade contacte o suporte.

## <a name="consistency-levels-and-throughput"></a>Níveis de consistência e produção

- Para uma estagnação forte e limitada, as leituras são feitas contra duas réplicas num conjunto de réplicas (quórum minoritário) para fornecer garantias de consistência. Sessão, prefixo consistente e, eventualmente, leituras de réplica única. O resultado é que, para o mesmo número de unidades de pedido, ler a produção para uma estagnação forte e limitada é metade dos outros níveis de consistência.

- Para um determinado tipo de operação de escrita, como inserir, substituir, aumentar e apagar, o resultado de escrita para unidades de pedido é idêntico para todos os níveis de consistência.

|**Nível de consistência**|**Leituras de quórum**|**Quorum escreve**|
|--|--|--|
|**Forte**|Minoria Local|Maioria Global|
|**Estagnação limitada**|Minoria Local|Maioria Local|
|**Sessão**|Réplica única (token de sessão)|Maioria Local|
|**Prefixo consistente**|Réplica única|Maioria Local|
|**Eventual**|Réplica única|Maioria Local|

## <a name="consistency-levels-and-data-durability"></a><a id="rto"></a>Níveis de consistência e durabilidade dos dados

Dentro de um ambiente de base de dados distribuído globalmente, existe uma relação direta entre o nível de consistência e a durabilidade dos dados na presença de uma paralisação a nível regional. À medida que desenvolve o seu plano de continuidade de negócios, precisa entender o tempo máximo aceitável antes que a aplicação recupere totalmente após um evento disruptivo. O tempo necessário para uma aplicação de recuperação total é conhecido como **objetivo de tempo de recuperação** **(RTO).** Também precisa entender o período máximo de atualizações de dados recentes que a aplicação pode tolerar perder ao recuperar após um evento disruptivo. O período de tempo das atualizações que pode perder é conhecido como **objetivo de ponto de recuperação** **(RPO).**

O quadro abaixo define a relação entre o modelo de consistência e a durabilidade dos dados na presença de uma paragem generalizada da região. É importante notar que num sistema distribuído, mesmo com forte consistência, é impossível dispor de uma base de dados distribuída com um RPO e RTO de zero devido ao Teorema da PAC. Para saber mais sobre o porquê, consulte [os níveis de consistência no Azure Cosmos DB](consistency-levels.md).

|**Regiões(s)**|**Modo de replicação**|**Nível de consistência**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Regiões de escrita únicas ou múltiplas|Qualquer nível de consistência|< 240 Minutos|<1 Semana|
|>1|Região de escrita única|Sessão, Prefixo Consistente, Eventual|< 15 minutos|< 15 minutos|
|>1|Região de escrita única|Estagnação Limitada|*K*  &  *T*|< 15 minutos|
|>1|Região de escrita única|Forte|0|< 15 minutos|
|>1|Múltiplas regiões de escrita|Sessão, Prefixo Consistente, Eventual|< 15 minutos|0|
|>1|Múltiplas regiões de escrita|Estagnação Limitada|*K*  &  *T*|0|

*K* = O número de *versões "K"* (isto é, atualizações) de um item.

*T* = O intervalo de tempo *"T"* desde a última atualização.

## <a name="strong-consistency-and-multiple-write-regions"></a>Forte consistência e múltiplas regiões de escrita

As contas cosmos configuradas com múltiplas regiões de escrita não podem ser configuradas para uma forte consistência, uma vez que não é possível que um sistema distribuído forneça um RPO de zero e um RTO de zero. Além disso, não existem benefícios de latência de escrita na utilização de uma forte consistência com múltiplas regiões de escrita, porque o pedido de escrita a qualquer região deve ser replicado e comprometido com todas as regiões configuradas dentro da conta. Isto resulta na mesma latência escrita que uma única conta de uma região de escrita.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre distribuição global e trocas de consistência geral em sistemas distribuídos. Consulte os seguintes artigos:

- [Tradeoffs de consistência no design moderno de sistemas de base de dados distribuídos](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Elevada disponibilidade](high-availability.md)
- [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)
