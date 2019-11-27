---
title: Unidades de solicitação e taxa de transferência no Azure Cosmos DB
description: Saiba mais sobre como especificar e estimar os requisitos de unidade de solicitação no Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: a0058bf309e0ff4fbe687731d676e907d1c3fd82
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383116"
---
# <a name="request-units-in-azure-cosmos-db"></a>Unidades de solicitação no Azure Cosmos DB

Com o Azure Cosmos DB, paga pelo débito que aprovisiona e o armazenamento que consome por hora. O débito deve ser aprovisionado para assegurar que estão sempre disponíveis recursos do sistema suficientes para a base de dados do Azure Cosmos. Você precisa de recursos suficientes para atender ou exceder os [SLAs de Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

O Azure Cosmos DB dá suporte a várias APIs, como SQL, MongoDB, Cassandra, Gremlin e Table. Cada API tem seu próprio conjunto de operações de banco de dados. Essas operações variam desde leituras de ponto simples e gravações em consultas complexas. Cada operação de banco de dados consome recursos do sistema com base na complexidade da operação. 

O custo de todas as operações de banco de dados é normalizado por Azure Cosmos DB e é expresso por *unidades de solicitação* (ou RUs, para curto). Pode pensar nas RUs por segundo como a moeda de débito. As RUs por segundo são uma moeda baseada em taxas. Extraem os recursos do sistema, como CPU, IOPS e memória, que são necessários para executar as operações de bases de dados suportadas pelo Azure Cosmos DB. 

O custo para ler um item de 1 KB é 1 Unidade de Pedido (ou 1 RU). Um mínimo de 10 RU/s é necessário para armazenar cada 1 GB de dados. É atribuído um custo através das RUs, de forma semelhante, a todas as outras operações de bases de dados. Não interessa que API utiliza para interagir com o contentor do Azure Cosmos: os custos são sempre medidos por RUs. Seja a operação da base de dados de escrita, de leitura ou de consulta, os custos são sempre medidos em RUs.

A imagem seguinte mostra a ideia de alto nível das RUs:

![Operações de banco de dados consumir unidades de solicitação](./media/request-units/request-units.png)

Para gerir e planear capacidade, o Azure Cosmos DB assegura que o número de RUs para uma determinada operação de base de dados sobre um determinado conjunto de dados é determinista. Pode examinar o cabeçalho da resposta para monitorizar o número de RUs que são consumidas por qualquer operação de base de dados. Ao entender os [fatores que afetam os encargos de ru](request-units.md#request-unit-considerations) e os requisitos de taxa de transferência do aplicativo, você pode executar o aplicativo de forma econômica.

Aprovisiona o número de RUs para a aplicação por segundo em incrementos de 100 RUs por segundo. Para dimensionar o débito aprovisionado à aplicação, pode aumentar ou diminuir o número de RUs a qualquer momento. Pode dimensionar em incrementos ou decrementos de 100 RUs. Pode fazer alterações através de programação ou ao utilizar o portal do Azure. Ser-lhe-á faturado à hora.

Você pode provisionar a taxa de transferência em duas granularidades distintas: 

* **Contêineres**: para obter mais informações, consulte [provisionar taxa de transferência em um contêiner Cosmos do Azure](how-to-provision-container-throughput.md).
* **Bancos**de dados: para obter mais informações, consulte [provisionar taxa de transferência em um Azure Cosmos](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Considerações de unidade de solicitação

Enquanto você estima o número de RUs por segundo para provisionar, considere os seguintes fatores:

* **Tamanho do item**: à medida que o tamanho de um item aumenta, o número de RUs consumidas para ler ou gravar o item também aumenta.

* **Indexação de itens**: por padrão, cada item é indexado automaticamente. Menos RUs serão consumidos se você optar por não indexar alguns de seus itens em um contêiner.

* **Contagem de propriedades do item**: supondo que a indexação padrão esteja em todas as propriedades, o número de RUs consumidas para gravar um item aumenta conforme a contagem de propriedades do item aumenta.

* **Propriedades indexadas**: uma política de índice em cada contêiner determina quais propriedades são indexadas por padrão. Para reduzir o consumo de RU para operações de gravação, limite o número de propriedades indexadas.

* **Consistência de dados**: os níveis de consistência forte e limitado de desatualização consomem aproximadamente duas vezes mais RUs durante a execução de operações de leitura em comparação com a de outros níveis de consistência relaxados.

* **Padrões de consulta**: a complexidade de uma consulta afeta o número de RUs consumidas para uma operação. Os fatores que afetam o custo das operações de consulta incluem: 
    
    - O número de resultados da consulta
    - O número de predicados
    - A natureza dos predicados
    - O número de funções definidas pelo usuário
    - O tamanho dos dados de origem
    - O tamanho do conjunto de resultados
    - Projeções

  Azure Cosmos DB garante que a mesma consulta nos mesmos dados sempre custa o mesmo número de RUs em execuções repetidas.

* **Uso de script**: assim como acontece com consultas, procedimentos armazenados e gatilhos consomem RUs com base na complexidade das operações que são executadas. Ao desenvolver seu aplicativo, inspecione o [cabeçalho de encargo de solicitação](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query) para entender melhor a quantidade de capacidade de ru consumida por cada operação.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre como [provisionar a taxa de transferência em bancos de dados e contêineres do Azure Cosmos](set-throughput.md).
* Saiba mais sobre [partições lógicas](partition-data.md).
* Saiba mais sobre como [dimensionar globalmente a taxa de transferência provisionada](scaling-throughput.md).
* Saiba como [provisionar a taxa de transferência em um contêiner Cosmos do Azure](how-to-provision-container-throughput.md).
* Saiba como [provisionar a taxa de transferência em um banco de dados Cosmos do Azure](how-to-provision-database-throughput.md).
* Saiba como [localizar o encargo de unidade de solicitação para uma operação](find-request-unit-charge.md).
* Saiba como [otimizar o custo de taxa de transferência provisionada em Azure Cosmos DB](optimize-cost-throughput.md).
* Saiba como [otimizar o custo de leituras e gravações no Azure Cosmos DB](optimize-cost-reads-writes.md).
* Saiba como [otimizar o custo da consulta no Azure Cosmos DB](optimize-cost-queries.md).
* Saiba como [usar métricas para monitorar a taxa de transferência](use-metrics.md).
