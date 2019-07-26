---
title: Compensações de desempenho e disponibilidade para vários níveis de consistência no Azure Cosmos DB
description: Compensações de desempenho e disponibilidade para vários níveis de consistência no Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 2d80e291b3c054fec92b169c8a216a7189e24b79
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384194"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>Compromissos de consistência, disponibilidade e desempenho 

Os bancos de dados distribuídos que dependem da replicação para alta disponibilidade, baixa latência ou ambos devem fazer compensações. As compensações estão entre consistência de leitura versus disponibilidade, latência e taxa de transferência.

Azure Cosmos DB se aproxima da consistência de dados como um espectro de opções. Essa abordagem inclui mais opções do que os dois extremos de consistência forte e eventual. Você pode escolher entre cinco modelos bem definidos no espectro de consistência. Do mais forte ao mais fraco, os modelos são:

- *Forte*
- *Estagnação limitada*
- *Sessão*
- *Prefixo consistente*
- *Eventual*

Cada modelo fornece compensações de desempenho e disponibilidade e é apoiado por SLAs abrangentes.

## <a name="consistency-levels-and-latency"></a>Níveis de consistência e latência

A latência de leitura para todos os níveis de consistência sempre é garantida para menos de 10 milissegundos no 99 º percentil. Essa latência de leitura é apoiada pelo SLA. A latência média de leitura, no 50 º percentil, normalmente é de 2 milissegundos ou menos. As contas do Azure cosmos que abrangem várias regiões e são configuradas com consistência forte são uma exceção a essa garantia.

A latência de gravação para todos os níveis de consistência sempre é garantida para menos de 10 milissegundos no 99 º percentil. Essa latência de gravação é apoiada pelo SLA. A latência média de gravação, no 50 º percentil, geralmente é de 5 milissegundos ou menos.

Para contas do Azure Cosmos configuradas com consistência forte com mais de uma região, é garantido que a latência de gravação seja menor que duas vezes o RTT (tempo de ida e volta) entre qualquer uma das duas regiões mais distantes, mais 10 milissegundos no 99 º percentil.

A latência exata de RTT é uma função de distância de velocidade da luz e da topologia de rede do Azure. A rede do Azure não fornece SLAs de latência para o RTT entre duas regiões do Azure. Para sua conta do Azure Cosmos, as latências de replicação são exibidas no portal do Azure. Você pode usar a portal do Azure (vá para a folha métricas) para monitorar as latências de replicação entre várias regiões associadas à sua conta do Azure Cosmos.

## <a name="consistency-levels-and-throughput"></a>Níveis de consistência e taxa de transferência

- Para o mesmo número de unidades de solicitação, a sessão, o prefixo consistente e os níveis de consistência eventual fornecem cerca de duas vezes a taxa de transferência de leitura quando comparada com a forte e a desatualização limitada.

- Para um determinado tipo de operação de gravação, como inserir, substituir, Upsert e excluir, a taxa de transferência de gravação para unidades de solicitação é idêntica para todos os níveis de consistência.

## <a id="rto"></a>Níveis de consistência e durabilidade de dados

Em um ambiente de banco de dados distribuído globalmente, há uma relação direta entre o nível de consistência e a durabilidade dos dados na presença de uma interrupção em toda a região. Ao desenvolver seu plano de continuidade de negócios, você precisa entender o tempo máximo aceitável antes que o aplicativo se recupere completamente após um evento de interrupção. O tempo necessário para que um aplicativo se recupere totalmente é conhecido como**RTO**( **objetivo de tempo de recuperação** ). Você também precisa entender o período máximo de atualizações de dados recentes que o aplicativo pode tolerar perder ao recuperar após um evento de interrupção. O período de tempo das atualizações que você pode perder é conhecido como**RPO**( **objetivo de ponto de recuperação** ).

A tabela a seguir define a relação entre o modelo de consistência e a durabilidade dos dados na presença de interrupção em toda a região. É importante observar que, em um sistema distribuído, mesmo com uma consistência forte, é impossível ter um banco de dados distribuído com RPO e RTO de zero devido à teorema CAP. Para saber mais sobre o porquê, consulte [níveis de consistência em Azure Cosmos DB](consistency-levels.md).

|**Região (ões)**|**Modo de replicação**|**Nível de consistência**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Único ou vários mestres|Qualquer nível de consistência|< de 240 minutos|< 1 semana|
|>1|Mestre único|Sessão, prefixo consistente, eventual|< 15 minutos|< 15 minutos|
|>1|Mestre único|Estagnação Limitada|*K* & *T*|< 15 minutos|
|>1|Mestre único|Forte|0|< 15 minutos|
|>1|Vários mestres|Sessão, prefixo consistente, eventual|< 15 minutos|0|
|>1|Vários mestres|Estagnação Limitada|*K* & *T*|0|

*K* = o número de versões *"K"* (ou seja, atualizações) de um item.

*T* = o intervalo de tempo *"T"* desde a última atualização.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a distribuição global e as compensações de consistência geral em sistemas distribuídos. Consulte os seguintes artigos:

- [Compensações de consistência no design de sistemas de bancos de dados distribuídos modernos](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Elevada disponibilidade](high-availability.md)
- [SLA de Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
