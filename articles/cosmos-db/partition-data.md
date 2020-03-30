---
title: Criação de partições e dimensionamento horizontal no Azure Cosmos DB
description: Saiba como funciona a partilha em Azure Cosmos DB, como configurar as teclas de partição e partição e como escolher a chave de partição certa para a sua aplicação.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: cbd171e10cc1a8b27de98d9d4d779f345ac5a3ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246620"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Criação de partições e dimensionamento horizontal no Azure Cosmos DB

Este artigo explica as partições físicas e lógicas no Azure Cosmos DB. Também aborda as melhores práticas de dimensionamento e criação de partições. 

## <a name="logical-partitions"></a>Partições lógicas

Uma partição lógica consiste num conjunto de itens que têm a mesma chave de partição. Por exemplo, num recipiente onde todos `City` os itens contêm `City` uma propriedade, pode usar como chave de partição para o recipiente. Grupos de itens que têm `City`valores `London` `Paris`específicos `NYC`para, como, como, e , formam divisórias lógicas distintas. Não tem que se preocupar em apagar uma partição quando os dados subjacentes são eliminados.

Em Azure Cosmos DB, um recipiente é a unidade fundamental de escalabilidade. Os dados adicionados ao recipiente e à entrada que você disponibiliza no recipiente são automaticamente (horizontalmente) divididos através de um conjunto de divisórias lógicas. Os dados e a entrada são divididos com base na chave de partição que especifica para o recipiente Azure Cosmos. Para mais informações, consulte [Criar um recipiente Azure Cosmos.](how-to-create-container.md)

Uma partição lógica também define o âmbito das transações de base de dados. Pode atualizar itens dentro de uma divisória lógica utilizando uma [transação com isolamento instantâneo](database-transactions-optimistic-concurrency.md). Quando novos itens são adicionados a um recipiente, novas divisórias lógicas são criadas de forma transparente pelo sistema.

## <a name="physical-partitions"></a>Divisórias físicas

Um recipiente Azure Cosmos é dimensionado distribuindo dados e entrada em um grande número de divisórias lógicas. Internamente, uma ou mais divisórias lógicas são mapeadas para uma partição física que consiste num conjunto de réplicas, também referidas como um conjunto de [*réplicas.*](global-dist-under-the-hood.md) Cada conjunto de réplicas acolhe uma instância do motor de base de dados Azure Cosmos. Um conjunto de réplicas torna os dados armazenados dentro da divisória física duráveis, altamente disponíveis e consistentes. Uma partição física suporta a quantidade máxima de unidades de armazenamento e pedido (RUs). Cada réplica que compõe a divisória física herda a quota de armazenamento da partição. Todas as réplicas de uma partição física suportam coletivamente a entrada que é atribuída à partição física. 

A imagem que se segue mostra como as divisórias lógicas são mapeadas para divisórias físicas que são distribuídas globalmente:

![Uma imagem que demonstra a divisão de Azure Cosmos DB](./media/partition-data/logical-partitions.png)

A entrada prevista para um recipiente é dividida uniformemente entre divisórias físicas. Um design de chave de divisória que não distribua os pedidos de entrada uniformemente pode criar divisórias "quentes". Divisórias quentes podem resultar na limitação das taxas e na utilização ineficiente do forprovisionado e custos mais elevados.

Ao contrário das divisórias lógicas, as divisórias físicas são uma implementação interna do sistema. Não se pode controlar o tamanho, colocação ou contagem de divisórias físicas, e não se pode controlar o mapeamento entre divisórias lógicas e divisórias físicas. No entanto, pode controlar o número de divisórias lógicas e a distribuição de dados, carga de trabalho e [entrada, escolhendo a chave de partição lógica certa](partitioning-overview.md#choose-partitionkey).

## <a name="next-steps"></a>Passos seguintes

* Aprenda sobre escolher uma chave de [partição.](partitioning-overview.md#choose-partitionkey)
* Saiba mais sobre [a provisão disponibilizada em Azure Cosmos DB](request-units.md).
* Conheça a [distribuição global em Azure Cosmos DB.](distribute-data-globally.md)
* Aprenda a [fornecer a entrada num recipiente Azure Cosmos.](how-to-provision-container-throughput.md)
* Aprenda a fornecer a entrada numa base de [dados Azure Cosmos.](how-to-provision-database-throughput.md)
