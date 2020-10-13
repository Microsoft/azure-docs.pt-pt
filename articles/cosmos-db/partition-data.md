---
title: Criação de partições e dimensionamento horizontal no Azure Cosmos DB
description: Saiba como funciona a partição no Azure Cosmos DB, como configurar chaves de partição e partição e como escolher a chave de partição certa para a sua aplicação.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 98cd28e8b770ebfb7ab395fbe7fff16a078e3529
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91826845"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Criação de partições e dimensionamento horizontal no Azure Cosmos DB

Este artigo explica a relação entre divisórias lógicas e físicas. Também discute as melhores práticas para a partição e dá uma visão aprofundada de como a escala horizontal funciona em Azure Cosmos DB. Não é necessário compreender estes detalhes internos para selecionar a [sua chave de partição,](partitioning-overview.md#choose-partitionkey) mas cobrimo-los para que tenha clareza sobre como funciona a Azure Cosmos DB.

## <a name="logical-partitions"></a>Partições lógicas

Uma partição lógica consiste num conjunto de itens que têm a mesma chave de partição. Por exemplo, num recipiente que contém dados sobre nutrição alimentar, todos os itens contêm uma `foodGroup` propriedade. Pode utilizar `foodGroup` como chave de partição para o recipiente. Grupos de itens que têm valores `foodGroup` específicos para, por `Beef Products` `Baked Products` exemplo, e `Sausages and Luncheon Meats` formam divisórias lógicas distintas. Não tens de te preocupar em eliminar uma partição lógica quando os dados subjacentes são eliminados.

Uma divisória lógica também define o âmbito das transações de base de dados. Pode atualizar itens dentro de uma divisória lógica utilizando uma [transação com isolamento instantâneo](database-transactions-optimistic-concurrency.md). Quando novos itens são adicionados a um recipiente, novas divisórias lógicas são criadas de forma transparente pelo sistema.

Não há limite para o número de divisórias lógicas no seu recipiente. Cada divisória lógica pode armazenar até 20GB de dados. Boas escolhas chave de partição têm uma ampla gama de valores possíveis. Por exemplo, num recipiente onde todos os itens contêm uma `foodGroup` propriedade, os dados dentro da `Beef Products` partição lógica podem crescer até 20 GB. [Selecionar uma chave de partição](partitioning-overview.md#choose-partitionkey) com uma ampla gama de valores possíveis garante que o recipiente é capaz de escalar.

## <a name="physical-partitions"></a>Divisórias físicas

Um recipiente Azure Cosmos é dimensionado distribuindo dados e produção através de divisórias físicas. Internamente, uma ou mais divisórias lógicas são mapeadas para uma única partição física. A maioria dos pequenos contentores do Cosmos têm muitas divisórias lógicas, mas só requerem uma única partição física. Ao contrário das divisórias lógicas, as divisórias físicas são uma implementação interna do sistema e são inteiramente geridas pela Azure Cosmos DB.

O número de divisórias físicas no seu recipiente Cosmos depende do seguinte:

- Montante de produção provisida (cada partição física individual pode fornecer um rendimento de até 10.000 unidades de pedido por segundo)
- Total de armazenamento de dados (cada partição física individual pode armazenar até 50GB)

Não há limite para o número total de divisórias físicas no seu recipiente. À medida que o seu rendimento ou tamanho de dados aumenta, a Azure Cosmos DB criará automaticamente novas divisórias físicas dividindo as existentes. As divisórias físicas não afetam a disponibilidade da sua aplicação. Após a divisão física, todos os dados dentro de uma única partição lógica ainda serão armazenados na mesma partição física. Uma divisão de partição física simplesmente cria um novo mapeamento de divisórias lógicas para divisórias físicas.

A produção prevista para um recipiente é dividida uniformemente entre divisórias físicas. Um design chave de partição que não distribui pedidos uniformemente pode resultar em muitos pedidos direcionados para um pequeno subconjunto de divisórias que se tornam "quentes". As divisórias quentes conduzem a uma utilização ineficiente da produção provisitada, o que pode resultar na limitação das taxas e nos custos mais elevados.

Pode ver as divisórias físicas do seu recipiente na secção de **armazenamento** da **lâmina métrica** do portal Azure:

:::image type="content" source="./media/partition-data/view-partitions-zoomed-out.png" alt-text="Número de visualização de divisórias físicas" lightbox="./media/partition-data/view-partitions-zoomed-in.png" ::: 

Neste recipiente de exemplo onde escolhemos `/foodGroup` como a nossa chave de partição, cada um dos três retângulos representa uma divisória física. Na imagem, o **alcance da chave de partição** é o mesmo que uma partição física. A partição física selecionada contém três divisórias lógicas: `Beef Products` `Vegetable and Vegetable Products` , e `Soups, Sauces, and Gravies` .

Se providenciarmos uma produção de 18.000 unidades de pedido por segundo (RU/s), então cada uma das três divisórias físicas pode utilizar 1/3 do total de produção a provisionada. Dentro da partição física selecionada, as teclas de partição `Beef Products` `Vegetable and Vegetable Products` lógica, e `Soups, Sauces, and Gravies` podem, coletivamente, utilizar os 6.000 RU/s provisados da partição física. Como a produção a provisionada está igualmente dividida nas divisórias físicas do seu contentor, é importante escolher uma chave de partição que distribua uniformemente o consumo de [produção, escolhendo a chave de partição lógica certa.](partitioning-overview.md#choose-partitionkey) Se escolher uma chave de partição que distribui uniformemente o consumo de produção através de divisórias lógicas, garantirá que o consumo de produção através de divisórias físicas seja equilibrado.

## <a name="replica-sets"></a>Conjuntos de réplicas

Cada partição física consiste num conjunto de réplicas, também referidas como um [*conjunto de réplicas.*](global-dist-under-the-hood.md) Cada conjunto de réplicas acolhe uma instância do motor de base de dados Azure Cosmos. Um conjunto de réplica torna os dados armazenados dentro da partição física durável, altamente disponíveis e consistentes. Cada réplica que compõe a partição física herda a quota de armazenamento da partição. Todas as réplicas de uma partição física suportam coletivamente a produção que é atribuída à partição física. A Azure Cosmos DB gere automaticamente conjuntos de réplicas.

A maioria dos pequenos contentores do Cosmos só requerem uma única partição física, mas ainda terão pelo menos 4 réplicas.

A imagem a seguir mostra como as divisórias lógicas são mapeadas para divisórias físicas que são distribuídas globalmente:

:::image type="content" source="./media/partition-data/logical-partitions.png" alt-text="Número de visualização de divisórias físicas" border="false":::

## <a name="next-steps"></a>Passos seguintes

* Saiba [como escolher uma chave de partição.](partitioning-overview.md#choose-partitionkey)
* Conheça a [produção a provisionada em Azure Cosmos DB](request-units.md).
* Conheça a [distribuição global em Azure Cosmos DB](distribute-data-globally.md).
* Saiba como [obter a produção num recipiente Azure Cosmos.](how-to-provision-container-throughput.md)
* Saiba como [obter o rendimento numa base de dados da Azure Cosmos.](how-to-provision-database-throughput.md)
