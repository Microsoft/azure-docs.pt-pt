---
title: Escolha o nível de consistência certo para a sua aplicação Azure Cosmos DB
description: Escolhendo o nível de consistência certo para a sua aplicação no Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/23/2020
ms.reviewer: sngun
ms.openlocfilehash: 0a79f6883a150cb95724a7be30dcbd2e8e03f01f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91396081"
---
# <a name="how-to-choose-the-right-consistency-level-for-your-azure-cosmos-db-application"></a>Como escolher o nível de consistência certo para a sua aplicação DB Azure Cosmos? 

Bases de dados distribuídas que dependem da replicação para alta disponibilidade, baixa latência ou ambas, fazem a troca fundamental entre a consistência de leitura vs. disponibilidade, latência e produção. A maioria das bases de dados distribuídas comercialmente pedem aos desenvolvedores que escolham entre os dois modelos de consistência extrema: *forte* consistência e *eventual* consistência. A Azure Cosmos DB permite que os desenvolvedores escolham entre os cinco modelos de consistência bem definidos: *forte,* *limitado,* *sessão,* *prefixo consistente* e *eventual*. Cada um destes modelos de consistência é bem definido, intuitivo e pode ser usado para cenários específicos do mundo real. Cada um dos cinco modelos de consistência proporciona uma disponibilidade precisa e trocas de desempenho e são apoiados por SLAs [abrangentes.](consistency-levels-tradeoffs.md) Pode configurar uma consistência predefinida ao nível da conta e [sobrepor-se ao nível de pedido](how-to-manage-consistency.md#override-the-default-consistency-level). As seguintes considerações simples irão ajudá-lo a fazer a escolha certa em muitos cenários comuns.

## <a name="sql-api-and-table-api"></a>API SQL e API de Tabela

Considere os seguintes pontos se a sua aplicação for construída utilizando API SQL ou Table API:

- Para muitos cenários do mundo real, a consistência da sessão é ideal e é a opção recomendada. Para mais informações, consulte, [como gerir o token da sessão para a sua aplicação.](how-to-manage-consistency.md#utilize-session-tokens)

- Se a sua aplicação necessitar de uma forte consistência, recomenda-se que utilize um nível de consistência desliminado.

- Se necessitar de garantias de consistência mais rigorosas do que as fornecidas pela consistência da sessão e latência de um dígito milissegundo para as escritas, recomenda-se que utilize um nível de consistência deslimitado.  

- Se a sua aplicação necessitar de uma eventual consistência, recomenda-se que utilize um nível de consistência de prefixo consistente.

- Se necessitar de garantias de consistência menos rigorosas do que as fornecidas pela consistência da sessão, recomenda-se que utilize um nível de consistência de prefixo consistente.

- Se precisar da maior disponibilidade e da latência mais baixa, use um nível de consistência eventual.

- Se precisar de uma durabilidade de dados ainda maior sem sacrificar o desempenho, pode criar um nível de consistência personalizado na camada de aplicação. Para mais informações consulte, [como implementar sincronização personalizada nas suas aplicações.](how-to-custom-synchronization.md)

## <a name="cassandra-mongodb-and-gremlin-apis"></a>Cassandra, MongoDB e ApIs de Gremlin

- Para obter detalhes sobre o mapeamento entre "Read Consistency Level" oferecido nos níveis de consistência Apache Cassandra e Cosmos DB, consulte [os níveis de consistência e as APIs de Cosmos DB](consistency-levels-across-apis.md#cassandra-mapping).

- Para obter detalhes sobre o mapeamento entre "Read Concern" de MongoDB e Azure Cosmos DB níveis de consistência, consulte [os níveis de consistência e as APIs de Cosmos DB](consistency-levels-across-apis.md#mongo-mapping).

## <a name="consistency-guarantees-in-practice"></a>Garantias de consistência na prática

Na prática, pode muitas vezes obter garantias de consistência mais fortes. As garantias de consistência para uma operação de leitura correspondem à frescura e ao pedido da base de dados que solicita. A consistência de leitura está ligada à ordem e propagação das operações de escrita/atualização.  

* Quando o nível de consistência é definido para **a estagnação limitada,** a Cosmos DB garante que os clientes lêem sempre o valor de uma escrita anterior, com um lag limitado pela janela de estagnação.

* Quando o nível de consistência é definido para **forte,** a janela de estagnação é equivalente a zero, e os clientes têm a garantia de ler o valor mais recente comprometido da operação de escrita.

* Para os restantes três níveis de consistência, a janela de estagnação depende em grande parte da sua carga de trabalho. Por exemplo, se não houver operações de escrita na base de dados, uma operação de leitura com **eventuais** **níveis**de consistência **de prefixo,** ou níveis consistentes de consistência prefixo é suscetível de produzir os mesmos resultados que uma operação de leitura com forte nível de consistência.

Se a sua conta Azure Cosmos estiver configurada com um nível de consistência diferente da forte consistência, pode descobrir a probabilidade de os seus clientes ficarem fortes e consistentes com leituras para as suas cargas de trabalho, olhando para a métrica *Probabilisticamente Bounded Staleness* (PBS). Esta métrica está exposta no portal Azure, para saber mais, consulte a [métrica de Staleness (PBS) (Monitor Probabilisisticamente Bounded).](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)

A estagnação deslimaturada probabilística mostra como a sua eventual consistência é a sua eventual consistência. Esta métrica fornece uma visão de quantas vezes você pode obter uma consistência mais forte do que o nível de consistência que você tem atualmente configurado na sua conta Azure Cosmos. Por outras palavras, pode-se ver a probabilidade (medida em milissegundos) de obter leituras fortemente consistentes para uma combinação de regiões de escrita e leitura.

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre os níveis de consistência nos seguintes artigos:

* [Mapeamento de nível de consistência em APIs de DB do Cosmos](consistency-levels-across-apis.md)
* [Disponibilidade e compensações de desempenho para vários níveis de consistência](consistency-levels-tradeoffs.md)
* [Como gerir o token da sessão para a sua aplicação](how-to-manage-consistency.md#utilize-session-tokens)
* [Monitorizar a métrica de Estagnação Limitada Probabilisticamente (PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)
