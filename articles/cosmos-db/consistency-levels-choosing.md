---
title: Escolhendo o nível de consistência certo para seu aplicativo que usa Azure Cosmos DB
description: Escolhendo o nível de consistência certo para seu aplicativo no Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 89c81e978c5f3dbbb8fac1ea5e75fc506612308f
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384901"
---
# <a name="choose-the-right-consistency-level"></a>Escolher o nível de consistência certo 

Os bancos de dados distribuídos que dependem da replicação para alta disponibilidade, baixa latência ou ambos, tornam a compensação fundamental entre a consistência de leitura versus disponibilidade, latência e taxa de transferência. A maioria dos bancos de dados distribuídos comercialmente disponíveis solicita que os desenvolvedores escolham entre os dois modelos de consistência extremos: consistência *forte* e consistência *eventual* . Azure Cosmos DB permite que os desenvolvedores escolham entre os cinco modelos de consistência bem definidos: *forte*, desatualização *limitada*, *sessão*, *prefixo consistente* e *eventual*. Cada um desses modelos de consistência é bem definido, intuitivo e pode ser usado para cenários do mundo real específicos. Cada um dos cinco modelos de consistência fornece compensações de [desempenho e disponibilidade](consistency-levels-tradeoffs.md) precisas e é apoiado por SLAs abrangentes. As considerações simples a seguir o ajudarão a fazer a escolha certa em muitos cenários comuns.

## <a name="sql-api-and-table-api"></a>API e API de Tabela do SQL

Considere os seguintes pontos se seu aplicativo for criado usando a API do SQL ou API de Tabela:

- Para muitos cenários do mundo real, a consistência da sessão é ideal e é a opção recomendada. Para obter mais informações, consulte [como gerenciar o token de sessão para seu aplicativo](how-to-manage-consistency.md#utilize-session-tokens).

- Se seu aplicativo exigir uma consistência forte, é recomendável usar o nível de consistência de desatualização limitada.

- Se você precisar de garantias de consistência mais rígidas do que aquelas fornecidas pela consistência de sessão e latência de milissegundos de um dígito para gravações, é recomendável usar o nível de consistência de desatualização limitada.  

- Se seu aplicativo exigir consistência eventual, é recomendável que você use o nível de consistência de prefixo consistente.

- Se você precisar de garantias de consistência menos rígidas do que aquelas fornecidas pela consistência de sessão, é recomendável usar o nível de consistência de prefixo consistente.

- Se você precisar da maior disponibilidade e da menor latência, use o nível de consistência eventual.

- Se você precisar de uma durabilidade de dados ainda maior sem sacrificar o desempenho, poderá criar um nível de consistência personalizado na camada de aplicativo. Para obter mais informações, consulte [como implementar a sincronização personalizada em seus aplicativos](how-to-custom-synchronization.md).

## <a name="cassandra-mongodb-and-gremlin-apis"></a>APIs Cassandra, MongoDB e Gremlin

- Para obter detalhes sobre o mapeamento entre o "nível de consistência de leitura" oferecido no Apache Cassandra e os níveis de consistência Cosmos DB, consulte [níveis de consistência e APIs de Cosmos DB](consistency-levels-across-apis.md#cassandra-mapping).

- Para obter detalhes sobre o mapeamento entre "preocupação de leitura" dos níveis de consistência do MongoDB e do Azure Cosmos DB, consulte [níveis de consistência e APIs de Cosmos DB](consistency-levels-across-apis.md#mongo-mapping).

## <a name="consistency-guarantees-in-practice"></a>Garantias de consistência na prática

Na prática, muitas vezes você pode obter garantias de consistência mais fortes. As garantias de consistência para uma operação de leitura correspondem à atualização e à ordenação do estado do banco de dados solicitado. A consistência de leitura é vinculada à ordenação e à propagação das operações de gravação/atualização.  

* Quando o nível de consistência é definido como desatualização **limitada**, o cosmos DB garante que os clientes sempre leiam o valor de uma gravação anterior, com um retardo limitado pela janela de desatualização.

* Quando o nível de consistência é definido como **forte**, a janela de desatualização é equivalente a zero e os clientes têm a garantia de ler o valor confirmado mais recente da operação de gravação.

* Para os três níveis de consistência restantes, a janela de desatualização é amplamente dependente de sua carga de trabalho. Por exemplo, se não houver nenhuma operação de gravação no banco de dados, uma operação de leitura com níveis de consistência **eventual**, de **sessão**ou de **prefixo consistente** provavelmente produzirá os mesmos resultados que uma operação de leitura com um nível de consistência forte.

Se sua conta do Azure Cosmos estiver configurada com um nível de consistência diferente da consistência forte, você poderá descobrir a probabilidade de que os clientes possam obter leituras fortes e consistentes para suas cargas de trabalho examinando o *Probabilistic limitado* Métrica de desatualização (PBS). Essa métrica é exposta na portal do Azure para saber mais, consulte monitorar a [métrica do PBS (](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)desatualização vinculada Probabilistic).

A desatualização limitada por probabilística mostra como sua consistência eventual é eventual. Essa métrica fornece uma percepção da frequência com que você pode obter uma consistência mais forte do que o nível de consistência configurado atualmente em sua conta do Azure Cosmos. Em outras palavras, você pode ver a probabilidade (medida em milissegundos) de obter leituras fortemente consistentes para uma combinação de regiões de gravação e leitura.

## <a name="next-steps"></a>Passos Seguintes

Leia mais sobre os níveis de consistência nos seguintes artigos:

* [Mapeamento de nível de consistência entre Cosmos DB APIs](consistency-levels-across-apis.md)
* [Desvantagens de desempenho e disponibilidade para vários níveis de consistência](consistency-levels-tradeoffs.md)
* [Como gerenciar o token de sessão para seu aplicativo](how-to-manage-consistency.md#utilize-session-tokens)
* [Monitorar a métrica do PBS (desatualização limitada) do Probabilistic](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)
