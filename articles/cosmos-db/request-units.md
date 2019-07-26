---
title: Unidades de solicitação e taxa de transferência no Azure Cosmos DB
description: Saiba mais sobre como especificar e estimar os requisitos de unidade de solicitação no Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.openlocfilehash: a1143f912d894c1219de05b03a2338dc4e5bdc5f
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467660"
---
# <a name="request-units-in-azure-cosmos-db"></a>Unidades de solicitação no Azure Cosmos DB

Com o Azure Cosmos DB, você paga pela taxa de transferência provisionada e pelo armazenamento que consome por hora. A taxa de transferência deve ser provisionada para garantir que recursos de sistema suficientes estejam disponíveis para o banco de dados Cosmos do Azure em todos os momentos. Você precisa de recursos suficientes para atender ou exceder os [SLAs de Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

O Azure Cosmos DB dá suporte a várias APIs, como SQL, MongoDB, Cassandra, Gremlin e Table. Cada API tem seu próprio conjunto de operações de banco de dados. Essas operações variam desde leituras de ponto simples e gravações em consultas complexas. Cada operação de banco de dados consome recursos do sistema com base na complexidade da operação. 

O custo de todas as operações de banco de dados é normalizado por Azure Cosmos DB e é expresso por *unidades de solicitação* (ou RUs, para curto). Você pode considerar o RUs por segundo como a moeda para a taxa de transferência. RUs por segundo é uma moeda baseada em taxas. Ele abstrai os recursos do sistema, como CPU, IOPS e memória, necessários para executar as operações de banco de dados com suporte pelo Azure Cosmos DB. 

O custo para ler um item de 1 KB é 1 unidade de solicitação (ou 1 RU). De forma semelhante, todas as outras operações de banco de dados recebem um custo usando RUs. Não importa qual API você usa para interagir com o contêiner Cosmos do Azure, os custos são sempre medidos por RUs. Se a operação do banco de dados for de gravação, leitura ou consulta, os custos sempre serão medidos em RUs.

A imagem a seguir mostra a ideia de alto nível do RUs:

![Operações de banco de dados consumir unidades de solicitação](./media/request-units/request-units.png)

Para gerenciar e planejar a capacidade, o Azure Cosmos DB garante que o número de RUs para uma determinada operação de banco de dados em um determinado conjunto é determinístico. Você pode examinar o cabeçalho de resposta para controlar o número de RUs consumidas por qualquer operação de banco de dados. Ao entender os [fatores que afetam](request-units.md#request-unit-considerations) os encargos de ru e os requisitos de taxa de transferência do aplicativo, você pode executar o aplicativo de forma econômica.

Você provisiona o número de RUs para seu aplicativo por segundo em incrementos de 100 RUs por segundo. Para dimensionar a taxa de transferência provisionada para seu aplicativo, você pode aumentar ou diminuir o número de RUs a qualquer momento. Você pode dimensionar em incrementos ou decrementos de 100 RUs. Você pode fazer suas alterações de forma programática ou usando o portal do Azure. Você é cobrado por hora.

Você pode provisionar a taxa de transferência em duas granularidades distintas: 

* **Contêineres**: Para obter mais informações, consulte provisionar [taxa de transferência em um contêiner Cosmos do Azure](how-to-provision-container-throughput.md).
* **Bancos de dados**: Para obter mais informações, consulte provisionar [taxa de transferência em um banco de dados Cosmos do Azure](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Considerações de unidade de solicitação

Enquanto você estima o número de RUs por segundo para provisionar, considere os seguintes fatores:

* **Tamanho do item**: À medida que o tamanho de um item aumenta, o número de RUs consumidas para ler ou gravar o item também aumenta.

* **Indexação de itens**: Por padrão, cada item é indexado automaticamente. Menos RUs serão consumidos se você optar por não indexar alguns de seus itens em um contêiner.

* **Contagem de propriedades do item**: Supondo que a indexação padrão esteja em todas as propriedades, o número de RUs consumidas para gravar um item aumenta conforme a contagem de propriedades do item aumenta.

* **Propriedades indexadas**: Uma política de índice em cada contêiner determina quais propriedades são indexadas por padrão. Para reduzir o consumo de RU para operações de gravação, limite o número de propriedades indexadas.

* **Consistência de dados**: Os níveis de consistência de desatualização rígida e vinculada consomem aproximadamente duas vezes mais RUs durante a execução de operações de leitura em comparação com a de outros níveis de consistência relaxados.

* **Padrões de consulta**: A complexidade de uma consulta afeta o número de RUs consumidas para uma operação. Os fatores que afetam o custo das operações de consulta incluem: 
    
    - O número de resultados da consulta
    - O número de predicados
    - A natureza dos predicados
    - O número de funções definidas pelo usuário
    - O tamanho dos dados de origem
    - O tamanho do conjunto de resultados
    - Projeções

  Azure Cosmos DB garante que a mesma consulta nos mesmos dados sempre custa o mesmo número de RUs em execuções repetidas.

* **Uso do script**: Assim como ocorre com consultas, procedimentos armazenados e gatilhos consomem RUs com base na complexidade das operações que são executadas. Ao desenvolver seu aplicativo, inspecione o [cabeçalho de encargo de solicitação](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query) para entender melhor a quantidade de capacidade de ru consumida por cada operação.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre como provisionar a [taxa de transferência em bancos de dados e contêineres do Azure Cosmos](set-throughput.md).
* Saiba mais sobre [partições lógicas](partition-data.md).
* Saiba mais sobre como [dimensionar globalmente a taxa de transferência](scaling-throughput.md)provisionada.
* Saiba como provisionar a [taxa de transferência em um contêiner Cosmos do Azure](how-to-provision-container-throughput.md).
* Saiba como provisionar a [taxa de transferência em um banco de dados Cosmos do Azure](how-to-provision-database-throughput.md).
* Saiba como [localizar o encargo de unidade de solicitação para uma operação](find-request-unit-charge.md).
* Saiba como [otimizar o custo de taxa de transferência provisionada em Azure Cosmos DB](optimize-cost-throughput.md).
* Saiba como [otimizar o custo de leituras e gravações no Azure Cosmos DB](optimize-cost-reads-writes.md).
* Saiba como [otimizar o custo da consulta no Azure Cosmos DB](optimize-cost-queries.md).
* Saiba como [usar métricas para monitorar a taxa de transferência](use-metrics.md).
