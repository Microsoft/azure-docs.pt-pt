---
title: Criação de partições e dimensionamento horizontal no Azure Cosmos DB
description: Saiba como funciona a partilha em Azure Cosmos DB, como configurar as teclas de partição e partição e como escolher a chave de partição certa para a sua aplicação.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 19e4c61ba930bb9b127e2401174bcea3fd240dce
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82234227"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Criação de partições e dimensionamento horizontal no Azure Cosmos DB

Este artigo explica a relação entre divisórias lógicas e físicas. Também discute as melhores práticas para a partilha e dá uma visão aprofundada de como o dimensionamento horizontal funciona em Azure Cosmos DB. Não é necessário compreender estes detalhes internos para selecionar a sua chave de [partição,](partitioning-overview.md#choose-partitionkey) mas nós cobrimo-los para que você tenha clareza sobre como o Azure Cosmos DB funciona.

## <a name="logical-partitions"></a>Partições lógicas

Uma partição lógica consiste num conjunto de itens que têm a mesma chave de partição. Por exemplo, num recipiente que contém dados sobre nutrição `foodGroup` alimentar, todos os itens contêm uma propriedade. Pode utilizar `foodGroup` como chave de partição para o recipiente. Grupos de itens que têm `foodGroup`valores `Beef Products``Baked Products`específicos `Sausages and Luncheon Meats`para, como, como, e , formam divisórias lógicas distintas. Não tem que se preocupar em apagar uma partição lógica quando os dados subjacentes são eliminados.

Uma partição lógica também define o âmbito das transações de base de dados. Pode atualizar itens dentro de uma divisória lógica utilizando uma [transação com isolamento instantâneo](database-transactions-optimistic-concurrency.md). Quando novos itens são adicionados a um recipiente, novas divisórias lógicas são criadas de forma transparente pelo sistema.

Não há limite para o número de divisórias lógicas no seu recipiente. Cada partição lógica pode armazenar até 20GB de dados. As boas opções-chave da partilha têm uma vasta gama de valores possíveis. Por exemplo, num recipiente onde todos `foodGroup`os itens contêm `Beef Products` uma propriedade, os dados dentro da divisória lógica podem crescer até 20GB. [A seleção de uma chave de partição](partitioning-overview.md#choose-partitionkey) com uma ampla gama de valores possíveis garante que o recipiente é capaz de escalar.

## <a name="physical-partitions"></a>Divisórias físicas

Um recipiente Azure Cosmos é dimensionado através da distribuição de dados e de entrada através de divisórias físicas. Internamente, uma ou mais divisórias lógicas são mapeadas para uma única partição física. A maioria dos pequenos recipientes cosmos têm muitas divisórias lógicas, mas só requerem uma única divisória física. Ao contrário das divisórias lógicas, as divisórias físicas são uma implementação interna do sistema e são inteiramente geridas pela Azure Cosmos DB.

O número de divisórias físicas no seu recipiente Cosmos depende do seguinte:

- Quantidade de entrada provisionada (cada partição física individual pode fornecer um misto de até 10.000 unidades de pedido por segundo)
- Armazenamento total de dados (cada partição física individual pode armazenar até 50GB)

Não há limite para o número total de divisórias físicas no seu recipiente. À medida que o seu tamanho de entrada ou de dados aumenta, o Azure Cosmos DB criará automaticamente novas divisórias físicas dividindo as existentes. Divisões físicas de divisórias não afetam a disponibilidade da sua aplicação. Após a divisão física dividida, todos os dados dentro de uma única divisória lógica continuarão a ser armazenados na mesma divisória física. Uma divisão física dividida simplesmente cria um novo mapeamento de divisórias lógicas para divisórias físicas.

A entrada prevista para um recipiente é dividida uniformemente entre divisórias físicas. Um design de chave de divisória que não distribua os pedidos de entrada uniformemente pode criar divisórias "quentes". Divisórias quentes podem resultar na limitação das taxas e na utilização ineficiente do forprovisionado e custos mais elevados.

Pode ver as divisórias físicas do seu recipiente na secção de **armazenamento** da **lâmina métrica** do portal Azure:

[![Número de visualização](./media/partition-data/view-partitions-zoomed-out.png) de divisórias físicas](./media/partition-data/view-partitions-zoomed-in.png#lightbox)

Neste recipiente de exemplo `/foodGroup` onde escolhemos como chave de partição, cada um dos três retângulos representa uma partição física. Na imagem, o alcance da **chave da divisória** é o mesmo que uma partição física. A partição física selecionada `Beef Products`contém `Vegetable and Vegetable Products`três `Soups, Sauces, and Gravies`divisórias lógicas: , e .

Se fornecermos uma produção de 18.000 unidades de pedido por segundo (RU/s), então cada uma das três divisórias físicas pode utilizar 1/3 do total de produção provisionado. Dentro da partição física `Beef Products`selecionada, as teclas de partição lógica , `Vegetable and Vegetable Products`e `Soups, Sauces, and Gravies` podem, coletivamente, utilizar os 6.000 RU/s da partição física. Como a entrada provisionada está igualmente dividida entre as divisórias físicas do seu recipiente, é importante escolher uma chave de partição que distribua uniformemente o consumo de entrada, [escolhendo a chave de partição lógica certa.](partitioning-overview.md#choose-partitionkey) Se escolher uma chave de partição que distribui uniformemente o consumo de entrada através de divisórias lógicas, garantirá que o consumo de entrada em divisórias físicas é equilibrado.

## <a name="replica-sets"></a>Conjuntos de réplicas

Cada partição física consiste num conjunto de réplicas, também referidas como um conjunto de [*réplicas.*](global-dist-under-the-hood.md) Cada conjunto de réplicas acolhe uma instância do motor de base de dados Azure Cosmos. Um conjunto de réplicas torna os dados armazenados dentro da divisória física duráveis, altamente disponíveis e consistentes. Cada réplica que compõe a divisória física herda a quota de armazenamento da partição. Todas as réplicas de uma partição física suportam coletivamente a entrada que é atribuída à partição física. O Azure Cosmos DB gere automaticamente conjuntos de réplicas.

A maioria dos pequenos recipientes cosmos só requer uma única partição física, mas ainda terá pelo menos 4 réplicas.

A imagem que se segue mostra como as divisórias lógicas são mapeadas para divisórias físicas que são distribuídas globalmente:

![Uma imagem que demonstra a divisão de Azure Cosmos DB](./media/partition-data/logical-partitions.png)

## <a name="next-steps"></a>Passos seguintes

* Aprenda sobre escolher uma chave de [partição.](partitioning-overview.md#choose-partitionkey)
* Saiba mais sobre [a provisão disponibilizada em Azure Cosmos DB](request-units.md).
* Conheça a [distribuição global em Azure Cosmos DB.](distribute-data-globally.md)
* Aprenda a [fornecer a entrada num recipiente Azure Cosmos.](how-to-provision-container-throughput.md)
* Aprenda a fornecer a entrada numa base de [dados Azure Cosmos.](how-to-provision-database-throughput.md)
