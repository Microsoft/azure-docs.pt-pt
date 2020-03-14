---
title: Unidades de Pedido e entrada em Azure Cosmos DB
description: Saiba como especificar e estimar os requisitos da Unidade de Pedido em Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: a0058bf309e0ff4fbe687731d676e907d1c3fd82
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246594"
---
# <a name="request-units-in-azure-cosmos-db"></a>Unidades de Pedido em Azure Cosmos DB

Com o Azure Cosmos DB, paga pelo débito que aprovisiona e o armazenamento que consome por hora. O débito deve ser aprovisionado para assegurar que estão sempre disponíveis recursos do sistema suficientes para a base de dados do Azure Cosmos. Você precisa de recursos suficientes para cumprir ou exceder o [Azure Cosmos DB SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

A Azure Cosmos DB suporta muitas APIs, tais como SQL, MongoDB, Cassandra, Gremlin e Table. Cada API tem o seu próprio conjunto de operações de base de dados. Estas operações vão desde leituras e escritos simples até consultas complexas. Cada operação de base de dados consome recursos do sistema com base na complexidade da operação. 

O custo de todas as operações de base de dados é normalizado pela Azure Cosmos DB e é expresso pelas *Unidades* de Pedido (ou RUs, para abreviar). Pode pensar nas RUs por segundo como a moeda de débito. As RUs por segundo são uma moeda baseada em taxas. Extraem os recursos do sistema, como CPU, IOPS e memória, que são necessários para executar as operações de bases de dados suportadas pelo Azure Cosmos DB. 

O custo para ler um item de 1 KB é 1 Unidade de Pedido (ou 1 RU). É necessário um mínimo de 10 RU/s para armazenar cada 1 GB de dados. É atribuído um custo através das RUs, de forma semelhante, a todas as outras operações de bases de dados. Não interessa que API utiliza para interagir com o contentor do Azure Cosmos: os custos são sempre medidos por RUs. Seja a operação da base de dados de escrita, de leitura ou de consulta, os custos são sempre medidos em RUs.

A imagem seguinte mostra a ideia de alto nível das RUs:

![Operações de base de dados consomem Unidades de Pedido](./media/request-units/request-units.png)

Para gerir e planear capacidade, o Azure Cosmos DB assegura que o número de RUs para uma determinada operação de base de dados sobre um determinado conjunto de dados é determinista. Pode examinar o cabeçalho da resposta para monitorizar o número de RUs que são consumidas por qualquer operação de base de dados. Quando compreender os [fatores que afetam as taxas de RU](request-units.md#request-unit-considerations) e os requisitos de entrada da sua aplicação, pode executar o custo da sua aplicação de forma eficaz.

Aprovisiona o número de RUs para a aplicação por segundo em incrementos de 100 RUs por segundo. Para dimensionar o débito aprovisionado à aplicação, pode aumentar ou diminuir o número de RUs a qualquer momento. Pode dimensionar em incrementos ou decrementos de 100 RUs. Pode fazer alterações através de programação ou ao utilizar o portal do Azure. Ser-lhe-á faturado à hora.

Pode fornecer a entrada em duas granularidades distintas: 

* **Recipientes**: Para mais informações, consulte a [entrada de provisão num recipiente Azure Cosmos](how-to-provision-container-throughput.md).
* **Bases de dados**: Para mais informações, consulte a entrada de provisionamento numa base de [dados Azure Cosmos](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Considerações da Unidade de Pedido

Embora calcule o número de RUs por segundo para a provisão, considere os seguintes fatores:

* **Tamanho do artigo**: À medida que o tamanho de um item aumenta, o número de RUs consumidos para ler ou escrever o item também aumenta.

* **Indexação do item**: Por padrão, cada item é automaticamente indexado. Menos RUs são consumidos se optar por não indexar alguns dos seus itens num recipiente.

* **Contagem de propriedades**do artigo : Assumindo que a indexação por defeito está em todas as propriedades, o número de RUs consumidos para escrever um item aumenta à medida que a contagem de imóveis aumenta.

* **Propriedades indexadas**: Uma política de índice em cada recipiente determina quais as propriedades indexadas por padrão. Para reduzir o consumo de RU para operações de escrita, limite o número de propriedades indexadas.

* **Consistência dos dados**: Os níveis de consistência forte e limitado consomem aproximadamente duas vezes mais RUs enquanto realizam operações de leitura em comparação com os de outros níveis de consistência descontraídos.

* **Padrões**de consulta : A complexidade de uma consulta afeta quantas RUs são consumidas para uma operação. Os fatores que afetam o custo das operações de consulta incluem: 
    
    - O número de resultados de consulta
    - O número de predicados
    - A natureza dos predicados
    - O número de funções definidas pelo utilizador
    - O tamanho dos dados de origem
    - O tamanho do conjunto de resultados
    - Projeções

  A Azure Cosmos DB garante que a mesma consulta nos mesmos dados custa sempre o mesmo número de RUs em execuções repetidas.

* **Utilização**do script : Tal como acontece com consultas, os procedimentos armazenados e os gatilhos consomem RUs com base na complexidade das operações que são realizadas. À medida que desenvolve a sua aplicação, inspecione o [cabeçalho](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query) de carga de pedido para entender melhor a quantidade de capacidade de RU que cada operação consome.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre como fornecer a entrada em contentores e bases de [dados da Azure Cosmos.](set-throughput.md)
* Saiba mais sobre [divisórias lógicas.](partition-data.md)
* Saiba mais sobre como [escalar globalmente a produção aprovisionada.](scaling-throughput.md)
* Aprenda a [fornecer a entrada num recipiente Azure Cosmos.](how-to-provision-container-throughput.md)
* Aprenda a fornecer a entrada numa base de [dados Azure Cosmos.](how-to-provision-database-throughput.md)
* Saiba como [encontrar a taxa da unidade](find-request-unit-charge.md)de pedido para uma operação .
* Saiba como otimizar o [custo de entrada provisionado no Azure Cosmos DB](optimize-cost-throughput.md).
* Saiba como [otimizar as leituras e os escritos no Azure Cosmos DB.](optimize-cost-reads-writes.md)
* Saiba como otimizar o custo da [consulta em Azure Cosmos DB](optimize-cost-queries.md).
* Aprenda a [usar métricas para monitorizar a entrada](use-metrics.md).
