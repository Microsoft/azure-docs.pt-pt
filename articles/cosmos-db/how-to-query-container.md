---
title: Consultar contentores no Azure Cosmos DB
description: Saiba como consultar os contentores em Azure Cosmos DB utilizando consultas de partição e partição cruzada
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 3/18/2019
ms.author: mjbrown
ms.openlocfilehash: 0f08ca84597b08b9a236b7bfb0fc9c849423a752
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93335896"
---
# <a name="query-an-azure-cosmos-container"></a>Consulta de um recipiente Azure Cosmos
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo explica como consultar um recipiente (coleção, gráfico ou tabela) em Azure Cosmos DB. Em particular, abrange como as consultas de partição e partição cruzada funcionam em Azure Cosmos DB.

## <a name="in-partition-query"></a>Consulta na partição

Quando consulta dados de contentores, se a consulta tiver um filtro de chave de partição especificado, a Azure Cosmos DB otimiza automaticamente a consulta. Encaminha a consulta para as [divisórias físicas correspondentes](partitioning-overview.md#physical-partitions) aos valores-chave de partição especificados no filtro.

Por exemplo, considere a consulta abaixo com um filtro de igualdade em `DeviceId` . Se executarmos esta consulta num recipiente `DeviceId` dividido, esta consulta filtra-se para uma única divisória física.

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001'
```

Tal como no exemplo anterior, esta consulta também filtrará para uma única partição. A adição do filtro adicional `Location` não altera isto:

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001' AND c.Location = 'Seattle'
```

Aqui está uma consulta que tem um filtro de alcance na chave de partição e não será mirado a uma única divisória física. Para ser uma consulta de partição, a consulta deve ter um filtro de igualdade que inclua a chave de partição:

```sql
    SELECT * FROM c WHERE c.DeviceId > 'XMS-0001'
```

## <a name="cross-partition-query"></a>Consulta entre partições

A seguinte consulta não tem um filtro na tecla de partição `DeviceId` (). Por isso, deve desatar a todas as divisórias físicas em que é executado contra o índice de cada partição:

```sql
    SELECT * FROM c WHERE c.Location = 'Seattle`
```

Cada divisória física tem o seu próprio índice. Portanto, quando executa uma consulta de divisórias cruzadas num recipiente, está efetivamente a executar uma consulta *por* partição física. A Azure Cosmos DB agregará automaticamente resultados em diferentes divisórias físicas.

Os índices em diferentes divisórias físicas são independentes uns dos outros. Não há índice global na Azure Cosmos DB.

## <a name="parallel-cross-partition-query"></a>Consulta entre partições paralelas

O Azure Cosmos DB SDKs 1.9.0 e posteriormente suporta opções de execução de consultas paralelas. As consultas entre partições paralelas permitem-lhe realizar consultas entre partições de baixa latência.

Pode gerir a execução paralela da consulta ao otimizar os parâmetros abaixo:

- **MaxConcurrency**: Define o número máximo de ligações de rede simultâneas às divisórias do contentor. Se você definir esta propriedade `-1` para, o SDK gere o grau de paralelismo. Se o  `MaxConcurrency` conjunto for , existe uma única `0` ligação de rede às divisórias do recipiente.

- **MaxBufferedItemCount**: negoceia a latência da consulta em relação à utilização da memória do lado do cliente. Se esta opção for omitida ou definida para -1, o SDK gere o número de itens tamponados durante a execução de consultas paralelas.

Devido à capacidade do Azure Cosmos DB de paralelizar consultas de partição cruzada, a latência da consulta irá geralmente escalar, assim como o sistema adiciona [divisórias físicas](partitioning-overview.md#physical-partitions). No entanto, a carga RU aumentará significativamente à medida que o número total de divisórias físicas aumenta.

Quando se faz uma consulta de partição cruzada, está essencialmente a fazer uma consulta separada por partição física individual. Enquanto as consultas de partição cruzadas usarão o índice, se disponíveis, eles ainda não são tão eficientes como consultas de partição.

## <a name="useful-example"></a>Exemplo útil

Aqui está uma analogia para entender melhor as consultas de divisórias cruzadas:

Imaginemos que é um motorista de entregas que tem de entregar pacotes em diferentes complexos de apartamentos. Cada complexo de apartamentos tem uma lista nas instalações que tem todos os números de unidade do residente. Podemos comparar cada complexo de apartamentos com uma divisória física e cada lista com o índice de partição física.

Podemos comparar consultas de partição e partição cruzada usando este exemplo:

### <a name="in-partition-query"></a>Consulta na partição

Se o condutor de entregas conhecer o complexo de apartamentos correto (partição física), então eles podem imediatamente dirigir para o edifício correto. O motorista pode verificar a lista do complexo de apartamentos dos números unitários do residente (o índice) e entregar rapidamente os pacotes apropriados. Neste caso, o condutor não perde tempo ou esforço de condução até um complexo de apartamentos para verificar se vivem lá algum destinatário do pacote.

### <a name="cross-partition-query-fan-out"></a>Consulta de divisórias cruzadas (fan-out)

Se o condutor de entregas não conhecer o complexo de apartamentos correto (partição física), eles terão que dirigir até cada edifício de apartamentos e verificar a lista com todos os números unitários do residente (o índice). Assim que chegarem a cada complexo de apartamentos, ainda poderão usar a lista dos endereços de cada residente. No entanto, eles terão que verificar a lista de todos os complexos de apartamentos, se quaisquer destinatários de pacotes vivem lá ou não. É assim que funcionam as consultas de partição cruzadas. Embora possam usar o índice (não precisam de bater em todas as portas), devem verificar separadamente o índice para cada partição física.

### <a name="cross-partition-query-scoped-to-only-a-few-physical-partitions"></a>Consulta de partição cruzada (apesada a apenas algumas divisórias físicas)

Se o condutor da entrega souber que todos os destinatários do pacote vivem dentro de alguns complexos de apartamentos, eles não precisarão de dirigir para cada um deles. Enquanto conduz para alguns complexos de apartamentos ainda vai exigir mais trabalho do que visitar apenas um único edifício, o motorista de entrega ainda poupa tempo e esforço significativos. Se uma consulta tiver a chave de partição no seu filtro com a `IN` palavra-chave, apenas verificará os índices de partição física relevantes para os dados.

## <a name="avoiding-cross-partition-queries"></a>Evitando consultas de partição cruzada

Para a maioria dos contentores, é inevitável que tenha algumas consultas de divisórias cruzadas. Ter algumas consultas de partição cruzada é ok! Quase todas as operações de consulta são suportadas através de divisórias (ambas as chaves de partição lógica e divisórias físicas). A Azure Cosmos DB também tem muitas otimizações no motor de consulta e SDKs cliente para paralelizar a execução de consultas através de divisórias físicas.

Para a maioria dos cenários pesados de leitura, recomendamos simplesmente selecionar a propriedade mais comum nos filtros de consulta. Deve também certificar-se de que a sua chave de partição adere a outras [práticas de seleção de chaves de partição.](partitioning-overview.md#choose-partitionkey)

Evitar consultas de divisórias cruzadas normalmente só importa com recipientes grandes. É-lhe cobrado um mínimo de cerca de 2,5 RU's cada vez que verificar o índice de uma partição física para obter resultados, mesmo que nenhum item na partição física corresponda ao filtro da consulta. Como tal, se tiver apenas uma (ou apenas algumas) divisórias físicas, as consultas de partição cruzada não consumirão significativamente mais consultas de RU do que consultas de partição.

O número de divisórias físicas está ligado à quantidade de RU a forquisitada. Cada partição física permite até 10.000 RU's a provisionados e pode armazenar até 50 GB de dados. A Azure Cosmos DB irá gerir automaticamente as divisórias físicas para si. O número de divisórias físicas no seu recipiente depende da sua produção a provisionada e do armazenamento consumido.

Deve tentar evitar consultas de partição cruzada se a sua carga de trabalho satisfaça os critérios abaixo:
- Planeia ter mais de 30.000 RU astetados
- Você planeia armazenar mais de 100 GB de dados

## <a name="next-steps"></a>Passos seguintes

Veja os seguintes artigos para aprender sobre a partilha no Azure Cosmos DB:

- [Criação de partições no Azure Cosmos DB](partitioning-overview.md)
- [Chaves de partição sintéticas no Azure Cosmos DB](synthetic-partition-keys.md)
