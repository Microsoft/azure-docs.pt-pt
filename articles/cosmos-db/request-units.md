---
title: Unidades de solicitação como uma moeda de produção e desempenho em Azure Cosmos DB
description: Saiba como especificar e estimar os requisitos da Unidade de Pedido no Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 6831cb3f39c25eb69d16300156f456980cf57fa0
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88604822"
---
# <a name="request-units-in-azure-cosmos-db"></a>Unidades de Pedido no Azure Cosmos DB

Azure Cosmos DB suporta muitas APIs, tais como SQL, MongoDB, Cassandra, Gremlin e Table. Cada API tem o seu próprio conjunto de operações de base de dados. Estas operações vão desde leituras simples de pontos e escritas a consultas complexas. Cada operação de base de dados consome recursos do sistema com base na complexidade da operação.

O custo de todas as operações de base de dados é normalizado pela Azure Cosmos DB e é expresso por *Unidades de Pedido* (ou RUs, para abreviar). Pode pensar em RUs como uma moeda de desempenho que abstrai os recursos do sistema, tais como CPU, IOPS e memória que são necessárias para executar as operações de base de dados suportadas pela Azure Cosmos DB.

O custo para fazer uma leitura de ponto (isto é, buscar um único item pelo seu valor de ID e chave de partição) para um item de 1 KB é 1 Unidade de Pedido (ou 1 RU). É atribuído um custo através das RUs, de forma semelhante, a todas as outras operações de bases de dados. Não interessa que API utiliza para interagir com o contentor do Azure Cosmos: os custos são sempre medidos por RUs. Quer a operação da base de dados seja uma escrita, leitura de ponto ou consulta, os custos são sempre medidos em RUs.

A imagem seguinte mostra a ideia de alto nível das RUs:

:::image type="content" source="./media/request-units/request-units.png" alt-text="Operações de base de dados consomem Unidades de Pedido" border="false":::

Para gerir e planear capacidade, o Azure Cosmos DB assegura que o número de RUs para uma determinada operação de base de dados sobre um determinado conjunto de dados é determinista. Pode examinar o cabeçalho da resposta para monitorizar o número de RUs que são consumidas por qualquer operação de base de dados. Quando compreender os [fatores que afetam os encargos ru](request-units.md#request-unit-considerations) e os requisitos de produção da sua aplicação, pode executar o custo da sua aplicação de forma eficaz.

O tipo de conta Azure Cosmos que está a usar determina a forma como as RUs consumidas são cobradas:

- No modo [de produção previsto,](set-throughput.md) fornece o número de RUs para a sua aplicação por segundo em incrementos de 100 RUs por segundo. Para escalar a produção prevista para a sua aplicação, pode aumentar ou diminuir o número de RUs a qualquer momento em incrementos ou decrépitos de 100 RUs. Pode efetuar as suas alterações através de programação ou do portal do Azure. É cobrado de hora em hora pelo montante de RUs por segundo que a provisionou. Pode provisões com duas granularidades distintas:
  - **Recipientes**: Para obter mais informações, consulte [a produção de provisão num recipiente Azure Cosmos](how-to-provision-container-throughput.md).
  - **Bases de dados**: Para obter mais informações, consulte [a produção de provisões numa base de dados da Azure Cosmos](how-to-provision-database-throughput.md).
- No modo [sem servidor,](serverless.md) não é necessário prever qualquer produção na criação de recursos na sua conta Azure Cosmos. No final do seu período de faturação, é cobrado a quantidade de Unidades de Pedido que foi consumida pelas suas operações de base de dados.

## <a name="request-unit-considerations"></a>Considerações da Unidade de Pedido

Embora calcule o número de RUs consumidos pela sua carga de trabalho, considere os seguintes fatores:

* **Tamanho do item**: à medida que o tamanho de um item aumenta, o número de RUs consumidos para ler ou escrever o item também aumenta.

* **Indexação do item**: por predefinição, cada item é automaticamente indexado. São consumidas menos RUs se optar por não indexar alguns dos seus itens num contentor.

* **Contagem de propriedades do item**: assumindo que a indexação predefinida existe em todas as propriedades, o número de RUs consumidas para escrever um item aumenta à medida que a contagem de propriedades do item aumenta.

* **Propriedades indexadas**: uma política de indexação em cada contentor determina as propriedades indexadas por predefinição. Para reduzir o consumo de RUs para operações de escrita, limite o número de propriedades indexadas.

* **Consistência dos dados**: Os níveis fortes e limitados de consistência da estagnação consomem aproximadamente duas vezes mais RUs enquanto realizam operações de leitura quando comparadas com as de outros níveis de consistência descontraídos.

* **Tipo de leituras**: As leituras de pontos custam significativamente menos RUs do que consultas.

* **Padrões de consulta**: a complexidade de uma consulta afeta a quantidade de RUs consumidas durante uma operação. Os fatores que afetam o custo das operações de consulta incluem: 
    
    - O número de resultados da consulta
    - O número de predicados
    - A natureza dos predicados
    - O número de funções definidas pelo utilizador
    - O tamanho dos dados de origem
    - O tamanho do conjunto de resultados
    - Projeções

  O Azure Cosmos DB garante que a mesma consulta sobre os mesmos dados custa sempre o mesmo número de RUs em execuções repetidas.

* **Utilização do script**: Tal como acontece com consultas, os procedimentos armazenados e os gatilhos consomem RUs com base na complexidade das operações que são realizadas. À medida que desenvolve a sua aplicação, inspecione o [cabeçalho de encargos de pedidos](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query) para compreender melhor quanta capacidade de RUs é consumida pelas operações.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre como [obter produção em contentores e bases de dados da Azure Cosmos.](set-throughput.md)
* Saiba mais sobre [o Azure Cosmos DB](serverless.md).
* Saiba mais sobre [divisórias lógicas.](partition-data.md)
* Saiba mais sobre como escalar globalmente a [produção a provisionada](scaling-throughput.md).
* Saiba como [obter a produção num recipiente Azure Cosmos.](how-to-provision-container-throughput.md)
* Saiba como [obter o rendimento numa base de dados da Azure Cosmos.](how-to-provision-database-throughput.md)
* Saiba como [encontrar a taxa da unidade de pedido para uma operação](find-request-unit-charge.md).
* Saiba como [otimizar o custo de produção a provisionado no Azure Cosmos DB](optimize-cost-throughput.md).
* Saiba como [otimizar as leituras e escreve o custo em Azure Cosmos DB](optimize-cost-reads-writes.md).
* Saiba como otimizar o [custo da consulta em Azure Cosmos DB](optimize-cost-queries.md).
* Aprenda a [usar métricas para monitorizar a produção.](use-metrics.md)
