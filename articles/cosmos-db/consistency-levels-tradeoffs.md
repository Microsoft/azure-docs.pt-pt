---
title: Azure Cosmos DB consistência, disponibilidade e trocas de desempenho
description: Trocas de disponibilidade e desempenho para vários níveis de consistência em Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/23/2020
ms.reviewer: sngun
ms.openlocfilehash: 4de696e2538bf1fa4823aafe30f931b7852535a7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82191741"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>Compromissos de consistência, disponibilidade e desempenho

As bases de dados distribuídas que dependem da replicação para elevada disponibilidade, baixa latência ou ambos, têm vantagens e desvantagens. As vantagens e desvantagens estão entre a consistência da leitura vs disponibilidade, latência e débito.

Azure Cosmos DB aborda a consistência dos dados como um espectro de escolhas. Esta abordagem inclui mais opções do que os dois extremos de consistência forte e eventual. Pode escolher entre cinco níveis bem definidos no espectro de consistência. Dos mais fortes aos mais fracos, os níveis são:

- *Forte*
- *Estagnação limitada*
- *Sessão*
- *Prefixo consistente*
- *Eventual*

Cada nível proporciona trocas de disponibilidade e desempenho e é apoiado por SLAs abrangentes.

## <a name="consistency-levels-and-latency"></a>Níveis de consistência e latência

A latência de leitura para todos os níveis de consistência é sempre garantida a menos de 10 milissegundos no percentil 99. Esta latência lida é apoiada pelo SLA. A latência média de leitura, no percentil 50, é tipicamente de 4 milissegundos ou menos.

A latência de escrita para todos os níveis de consistência é sempre garantida a menos de 10 milissegundos no percentil 99. Esta latência escrita é apoiada pelo SLA. A latência média de escrita, no percentil 50, é geralmente de 5 milissegundos ou menos. As contas da Azure Cosmos que se estendem por várias regiões e estão configuradas com forte consistência são uma exceção a esta garantia.

### <a name="write-latency-and-strong-consistency"></a>Escreva latência e forte consistência

Para as contas da Azure Cosmos configuradas com forte consistência com mais de uma região, a latência por escrito é igual a duas vezes o tempo de ida e volta (RTT) entre qualquer uma das duas regiões mais distantes, mais 10 milissegundos no percentil 99. A alta rede RTT entre as regiões traduzir-se-á numa maior latência para os pedidos de DB da Cosmos, uma vez que a forte consistência só completa uma operação depois de garantir que se comprometeu com todas as regiões dentro de uma conta.

A latência RTT exata é uma função da distância de velocidade da luz e da topologia de rede Azure. A rede Azure não fornece SLAs de latência para o RTT entre duas regiões Azure. Para a sua conta Azure Cosmos, as tardios de replicação são exibidas no portal Azure. Pode utilizar o portal Azure (vá à lâmina Métricas, selecione separador Consistência) para monitorizar as tardios de replicação entre várias regiões que estão associadas à sua conta Azure Cosmos.

> [!IMPORTANT]
> A forte consistência das contas com regiões com mais de 8000 quilómetros é bloqueada por defeito devido à elevada latência escrita. Para ativar esta capacidade contacte o suporte.

## <a name="consistency-levels-and-throughput"></a>Níveis de consistência e de entrada

- Para uma estagnação forte e limitada, são feitas leituras contra duas réplicas num conjunto de quatro réplicas (quórum minoritário) para fornecer garantias de consistência. Sessão, prefixo consistente e eventualmente fazer leituras de réplica única. O resultado é que, para o mesmo número de unidades de pedido, a leitura da entrada para estagnação forte e limitada é metade dos outros níveis de consistência.

- Para um determinado tipo de operação de escrita, como inserir, substituir, sersert e eliminar, a entrada de escrita para unidades de pedido é idêntica para todos os níveis de consistência.

|**Nível de Consistência**|**Leituras de Quórum**|**Quorum escreve**|
|--|--|--|
|**Forte**|Minoria Local|Maioria Global|
|**Estagnação Limitada**|Minoria Local|Maioria Local|
|**Sessão**|Réplica única (usando ficha de sessão)|Maioria Local|
|**Prefixo Consistente**|Réplica única|Maioria Local|
|**Eventual**|Réplica única|Maioria Local|

## <a name="consistency-levels-and-data-durability"></a><a id="rto"></a>Níveis de consistência e durabilidade dos dados

Num ambiente de base de dados distribuído globalmente, existe uma relação direta entre o nível de consistência e a durabilidade dos dados na presença de uma paralisação em toda a região. À medida que desenvolve o seu plano de continuidade de negócios, precisa de compreender o tempo máximo aceitável antes que a aplicação recupere totalmente após um evento disruptivo. O tempo necessário para uma candidatura para recuperar totalmente é conhecido como objetivo de tempo de **recuperação** **(RTO).** Também precisa entender o período máximo de atualizações de dados recentes que a aplicação pode tolerar perder quando se recupera após um evento disruptivo. O período de tempo de atualizações que pode supor perder é conhecido como objetivo do ponto de **recuperação** **(RPO).**

O quadro abaixo define a relação entre o modelo de consistência e a durabilidade dos dados na presença de uma paragem em toda a região. É importante notar que num sistema distribuído, mesmo com uma forte coerência, é impossível dispor de uma base de dados distribuída com um RPO e rTO de zero devido ao teorema da PAC. Para saber mais sobre o porquê, consulte os níveis de [consistência em Azure Cosmos DB](consistency-levels.md).

|**Região(s)**|**Modo de replicação**|**Nível de consistência**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Single ou Multi-Master|Qualquer Nível de Consistência|< 240 Minutos|<1 Semana|
|>1|Mestre Único|Sessão, Prefixo Consistente, Eventual|< 15 minutos|< 15 minutos|
|>1|Mestre Único|Estagnação Limitada|*K* & *T*|< 15 minutos|
|>1|Mestre Único|Forte|0|< 15 minutos|
|>1|Multi-Master|Sessão, Prefixo Consistente, Eventual|< 15 minutos|0|
|>1|Multi-Master|Estagnação Limitada|*K* & *T*|0|

*K* = O número de versões *"K"* (isto é, atualizações) de um item.

*T* = O intervalo de tempo *"T"* desde a última atualização.

## <a name="strong-consistency-and-multi-master"></a>Forte consistência e multi-mestre

As contas cosmos configuradas para multi-master não podem ser configuradas para uma forte consistência, uma vez que não é possível que um sistema distribuído forneça um RPO de zero e um RTO de zero. Além disso, não existem benefícios de latência por escrito para usar uma forte consistência com o multi-mestre, uma vez que qualquer escrita em qualquer região deve ser replicada e comprometida com todas as regiões configuradas dentro da conta. Isto resulta na mesma latência escrita que uma única conta principal.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a distribuição global e as trocas gerais de consistência em sistemas distribuídos. Consulte os seguintes artigos:

- [Trocas de consistência no design moderno de sistemas de base de dados distribuídos](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Elevada disponibilidade](high-availability.md)
- [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
