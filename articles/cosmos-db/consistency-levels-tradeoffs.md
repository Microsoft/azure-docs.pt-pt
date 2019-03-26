---
title: Desvantagens de desempenho e disponibilidade para vários níveis de consistência no Azure Cosmos DB
description: Desvantagens de desempenho e disponibilidade para vários níveis de consistência no Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 2/13/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ac5b6e0d44376332e005d30b4a8fcc97021c4eda
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407526"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>Compromissos de consistência, disponibilidade e desempenho 

Bases de dados distribuídas que dependem de replicação para elevada disponibilidade, latência baixa ou ambos têm de fazer cedências. Os compromissos são entre a consistência de leitura versus disponibilidade, latência e débito.

O Azure Cosmos DB se aproxima de consistência de dados como uma gama de opções. Esta abordagem inclui opções mais do que os dois extremos de consistência eventual e forte. Pode escolher entre cinco modelos bem definidos do espetro de consistência. A partir de mais forte para o mais fraco, os modelos são:

- *Forte*
- *Estagnação limitada*
- *Sessão*
- *Prefixo consistente*
- *Eventual*

Cada modelo fornece as vantagens e desvantagens de desempenho e disponibilidade e suporte dos SLAs abrangentes.

## <a name="consistency-levels-and-latency"></a>Níveis de consistência e a latência

A latência de leitura para todos os níveis de consistência é sempre garantida ao ser inferior a 10 milissegundos no percentil 99. Latência de leitura conta com o SLA. A média de leitura de latência no percentil 50th, normalmente é 2 milissegundos ou menos. Contas Cosmos do Azure, que abrangem várias regiões e são configuradas com consistência forte são uma exceção a essa garantia.

A latência de escrita para todos os níveis de consistência é sempre garantida ao ser inferior a 10 milissegundos no percentil 99. Esta latência de escrita conta com o SLA. A latência média de escrita, no percentil 50th, costuma ser 5 milissegundos ou menos.

Para contas do Cosmos do Azure configuradas com consistência forte com mais de uma região de, a latência de escrita é garantida para ser inferior a duas vezes ida e volta (RTT) de tempo entre qualquer uma das duas regiões de mais distantes, além de 10 milissegundos no percentil 99.

A latência RTT exata é uma função de distância de velocidade de luz e a topologia de rede do Azure. Redes do Azure não oferecem qualquer latência SLAs de RTT entre quaisquer duas regiões do Azure. Para a sua conta do Cosmos do Azure, latências de replicação são apresentadas no portal do Azure. Pode utilizar o portal do Azure (vá para o painel de métricas) para monitorizar as latências de replicação entre várias regiões associadas a sua conta do Cosmos do Azure.

## <a name="consistency-levels-and-throughput"></a>Níveis de consistência e o débito

- Para o mesmo número de unidades de pedido, a sessão, prefixo consistente e níveis de consistência eventual fornecem cerca de duas vezes o débito de leitura em comparação com a forte e estagnação limitada.

- Para um determinado tipo de operação de escrita, por exemplo, inserir, substituir, upsert e eliminação, o débito de escrita para unidades de pedido é idêntico para todos os níveis de consistência.

## <a id="rto"></a>Durabilidade de dados e níveis de consistência

Dentro de um ambiente de base de dados globalmente distribuída existe uma relação direta entre a durabilidade de dados e de nível de consistência na presença de uma interrupção de toda a região. Desenvolver o seu plano de continuidade do negócio, precisa entender o tempo máximo aceitável antes da aplicação recuperar totalmente após um evento problemático. O tempo necessário para uma aplicação recuperar totalmente é conhecido como **objetivo de tempo de recuperação** (**RTO**). Também precisa entender o período máximo de atualizações de dados recentes, a aplicação pode tolerar perder ao recuperar após um evento problemático. O período de tempo de atualizações que poderá estar a perder é conhecido como **objetivo de ponto de recuperação** (**RPO**).

A tabela a seguir define a relação entre a durabilidade de dados e o modelo de consistência na presença de indisponibilidade ampla de região. É importante observar que, num sistema distribuído, mesmo com consistência forte, é impossível ter uma base de dados distribuída com um RPO e RTO de zero devido a Teorema CAP. Para saber mais sobre as razões, veja [níveis de consistência no Azure Cosmos DB](consistency-levels.md).

|**Regiões**|**Modo de replicação**|**Nível de consistência**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Único ou vários mestres|Qualquer nível de consistência|< A 240 minutos|< 1 semana|
|>1|Único do controlador|Sessão, prefixo consistente, Eventual|< a 15 minutos|< a 15 minutos|
|>1|Único do controlador|Estagnação Limitada|*K* & *T*|< a 15 minutos|
|>1|Com vários mestres|Sessão, prefixo consistente, Eventual|< a 15 minutos|0|
|>1|Com vários mestres|Estagnação Limitada|*K* & *T*|0|
|>1|Único ou vários mestres|Forte|0|< a 15 minutos|

*K* = o número de *"K"* versões (ou seja, atualizações) de um item.

*T* = o intervalo de tempo *"T"* desde a última atualização.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre distribuição global e os compromissos de consistência geral em sistemas distribuídos. Consulte os seguintes artigos:

- [Vantagens e desvantagens de consistência no design de sistemas de banco de dados distribuída Moderno](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Elevada disponibilidade](high-availability.md)
- [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
