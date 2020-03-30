---
title: Escolha o nível de consistência certo para a sua app Azure Cosmos DB
description: Escolhendo o nível de consistência certo para a sua aplicação no Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: cea157e272a2bf464141e592b8e742f314a83233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441909"
---
# <a name="choose-the-right-consistency-level"></a>Escolher o nível de consistência certo 

As bases de dados distribuídas que dependem da replicação para alta disponibilidade, baixa latência ou ambas, fazem a compensação fundamental entre a consistência da leitura vs. disponibilidade, latência e saída. A maioria das bases de dados distribuídas comercialmente disponíveis pedem aos desenvolvedores que escolham entre os dois modelos de consistência extrema: *forte* consistência e *eventual* consistência. O Azure Cosmos DB permite que os desenvolvedores escolham entre os cinco modelos de consistência bem definidos: *estagnação forte,* *limitada,* *sessão,* *prefixo consistente* e *eventual .* Cada um destes modelos de consistência é bem definido, intuitivo e pode ser usado para cenários específicos do mundo real. Cada um dos cinco modelos de consistência proporciona uma disponibilidade precisa [e trocas](consistency-levels-tradeoffs.md) de desempenho e é apoiado por SLAs abrangentes. As seguintes considerações simples irão ajudá-lo a fazer a escolha certa em muitos cenários comuns.

## <a name="sql-api-and-table-api"></a>API sQL e Mesa API

Considere os seguintes pontos se a sua aplicação for construída utilizando API SQL ou API de tabela:

- Para muitos cenários do mundo real, a consistência da sessão é ideal e é a opção recomendada. Para mais informações, consulte, Como gerir o token da [sessão para a sua candidatura](how-to-manage-consistency.md#utilize-session-tokens).

- Se a sua aplicação necessitar de uma forte consistência, recomenda-se que utilize um nível de consistência de tensão limitado.

- Se necessitar de garantias de consistência mais rigorosas do que as fornecidas pela consistência da sessão e latência de um dígito de milissegundopara as escritas, recomenda-se que utilize um nível de consistência de staleness limitado.  

- Se a sua aplicação necessitar de uma eventual consistência, recomenda-se que utilize um nível de consistência de prefixo consistente.

- Se necessitar de garantias de consistência menos rigorosas do que as fornecidas pela consistência da sessão, recomenda-se que utilize um nível de consistência de prefixo consistente.

- Se precisar da maior disponibilidade e da latência mais baixa, use um eventual nível de consistência.

- Se necessitar de uma durabilidade de dados ainda maior sem sacrificar o desempenho, pode criar um nível de consistência personalizado na camada de aplicação. Para mais informações consulte, [Como implementar sincronização personalizada nas suas aplicações](how-to-custom-synchronization.md).

## <a name="cassandra-mongodb-and-gremlin-apis"></a>Cassandra, MongoDB e Gremlin APIs

- Para mais detalhes sobre o mapeamento entre "Read Consistência Nível" oferecido nos níveis de consistência apache Cassandra e Cosmos DB, consulte os níveis de [consistência e as APIs db cosmos](consistency-levels-across-apis.md#cassandra-mapping).

- Para mais detalhes sobre o mapeamento entre os níveis de consistência "Read Concern" do MongoDB e do Azure Cosmos DB, consulte os níveis de consistência e as [APIs do Cosmos DB](consistency-levels-across-apis.md#mongo-mapping).

## <a name="consistency-guarantees-in-practice"></a>Garantias de coerência na prática

Na prática, muitas vezes pode obter garantias de consistência mais fortes. As garantias de coerência para uma operação de leitura correspondem à frescura e ordenamento da base de dados que solicita. A consistência da leitura está ligada à ordenação e propagação das operações de escrita/atualização.  

* Quando o nível de consistência está definido para a **estagnação limitada,** a Cosmos DB garante que os clientes lêem sempre o valor de uma escrita anterior, com um desfasamento limitado pela janela de estagnação.

* Quando o nível de consistência é definido para **forte**, a janela de estagnação é equivalente a zero, e os clientes são garantidos para ler o mais recente valor comprometido da operação de escrita.

* Para os restantes três níveis de consistência, a janela de estagnação depende em grande parte da sua carga de trabalho. Por exemplo, se não houver operações de escrita na base de dados, uma operação de leitura com **níveis de**consistência eventual , **sessão**ou **prefixo consistente** é suscetível de produzir os mesmos resultados que uma operação de leitura com forte nível de consistência.

Se a sua conta Azure Cosmos estiver configurada com um nível de consistência diferente da forte consistência, pode descobrir a probabilidade de os seus clientes poderem obter leituras fortes e consistentes para as suas cargas de trabalho, olhando para a métrica *Probabilistly Bounded Staleness* (PBS). Esta métrica está exposta no portal Azure, para saber mais, ver [Monitor Probabilistly Bounded Staleness (PBS) métrica](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

A estagnação delimitada probabilística mostra como eventual é a sua eventual consistência. Esta métrica fornece uma visão da frequência com que pode obter uma consistência mais forte do que o nível de consistência que configura atualmente na sua conta Azure Cosmos. Por outras palavras, pode ver a probabilidade (medida em milissegundos) de obter leituras fortemente consistentes para uma combinação de regiões de escrita e leitura.

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre os níveis de consistência nos seguintes artigos:

* [Mapeamento de nível de consistência em ApIs DB Cosmos](consistency-levels-across-apis.md)
* [Trocas de disponibilidade e desempenho para vários níveis de consistência](consistency-levels-tradeoffs.md)
* [Como gerir o token da sessão para a sua aplicação](how-to-manage-consistency.md#utilize-session-tokens)
* [Monitorizar a métrica de Estagnação Limitada Probabilisticamente (PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)
