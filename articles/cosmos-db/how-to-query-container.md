---
title: Consultar contentores no Azure Cosmos DB
description: Saiba como consultar recipientes em Azure Cosmos DB usando consultas de partição e divisórias cruzadas
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 3/18/2019
ms.author: mjbrown
ms.openlocfilehash: 299980b67caaea85fbfb40cb1a30ee50fa32d0f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131404"
---
# <a name="query-an-azure-cosmos-container"></a>Consulta um recipiente Azure Cosmos

Este artigo explica como consultar um recipiente (coleção, gráfico ou mesa) em Azure Cosmos DB. Em particular, abrange como as consultas de partição e de partição cruzada funcionam em Azure Cosmos DB.

## <a name="in-partition-query"></a>Consulta na partição

Quando se consulta ma dados dos contentores, se a consulta tiver um filtro de chave de partição especificado, o Azure Cosmos DB otimiza automaticamente a consulta. Encaminha a consulta para as [divisórias físicas correspondentes](partition-data.md#physical-partitions) aos valores-chave da divisória especificados no filtro.

Por exemplo, considere a consulta abaixo com `DeviceId`um filtro de igualdade ligado . Se fizermos esta consulta num recipiente `DeviceId`dividido, esta consulta filtrar-se-á para uma única divisória física.

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001'
```

Tal como acontece com o exemplo anterior, esta consulta também filtrará para uma única partição. Adicionar o filtro `Location` adicional não altera isto:

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001' AND c.Location = 'Seattle'
```

Aqui está uma consulta que tem um filtro de alcance na chave da partição e não será telescópio para uma única partição física. Para ser uma consulta de partição, a consulta deve ter um filtro de igualdade que inclua a chave de partição:

```sql
    SELECT * FROM c WHERE c.DeviceId > 'XMS-0001'
```

## <a name="cross-partition-query"></a>Consulta entre partições

A seguinte consulta não tem um filtro na`DeviceId`tecla de partição ( ). Por conseguinte, deve abanar todas as divisórias físicas onde é executada contra o índice de cada partição:

```sql
    SELECT * FROM c WHERE c.Location = 'Seattle`
```

Cada partição física tem o seu próprio índice. Portanto, quando você executa uma consulta de divisória cruzada em um recipiente, você está efetivamente executando uma consulta *por* partição física. O Azure Cosmos DB agregará automaticamente os resultados em diferentes divisórias físicas.

Os índices em diferentes divisórias físicas são independentes uns dos outros. Não existe nenhum índice global no Azure Cosmos DB.

## <a name="parallel-cross-partition-query"></a>Consulta entre partições paralelas

O Azure Cosmos DB SDKs 1.9.0 e posteriormente suportam opções paralelas de execução de consultas. As consultas entre partições paralelas permitem-lhe realizar consultas entre partições de baixa latência.

Pode gerir a execução paralela da consulta ao otimizar os parâmetros abaixo:

- **MaxConcurrency**: Define o número máximo de ligações de rede simultâneas às divisórias do contentor. Se você definir `-1`esta propriedade para , o SDK gere o grau de paralelismo. Se `MaxConcurrency` o `0`conjunto para , existe uma única ligação de rede às divisórias do recipiente.

- **MaxBufferedItemCount**: negoceia a latência da consulta em relação à utilização da memória do lado do cliente. Se esta opção for omitida ou definida para -1, o SDK gere o número de itens tamponados durante a execução de consultas paralelas.

Devido à capacidade do Azure Cosmos DB de paralelizar consultas de divisóriacruzada, a latência da consulta geralmente escalará bem como o sistema adiciona [divisórias físicas.](partition-data.md#physical-partitions) No entanto, a carga de RU aumentará significativamente à medida que o número total de divisórias físicas aumenta.

Quando executa uma consulta de partição cruzada, está essencialmente a fazer uma consulta separada por partição física individual. Enquanto as consultas de divisóriacruzada saem do índice, se disponíveis, ainda não são tão eficientes como consultas de partição.

## <a name="useful-example"></a>Exemplo útil

Aqui está uma analogia para entender melhor as consultas de partição cruzada:

Imaginemos que é um motorista de entregas que tem de entregar pacotes em diferentes complexos de apartamentos. Cada complexo de apartamentos tem uma lista no local que tem todos os números unitários do residente. Podemos comparar cada complexo de apartamentos com uma divisória física e cada lista com o índice da partição física.

Podemos comparar consultas de partição e divisórias cruzadas usando este exemplo:

### <a name="in-partition-query"></a>Consulta na partição

Se o condutor de entregas souber o complexo de apartamentos correto (partição física), então podem dirigir imediatamente para o edifício correto. O condutor pode verificar a lista do complexo de apartamentos dos números unitários do residente (o índice) e entregar rapidamente os pacotes apropriados. Neste caso, o condutor não perde tempo ou esforço dirigindo para um complexo de apartamentos para verificar se algum destinatário do pacote vive lá.

### <a name="cross-partition-query-fan-out"></a>Consulta de divisória cruzada (fan-out)

Se o condutor de entregas não conhecer o complexo de apartamentos correto (partição física), terá de dirigir até cada edifício de apartamentos e verificar a lista com todos os números unitários do residente (o índice). Assim que chegarem a cada complexo de apartamentos, ainda poderão usar a lista dos endereços de cada residente. No entanto, terão de verificar a lista de todos os complexos de apartamentos, quer algum destinatário de pacotes viva lá ou não. É assim que as consultas de partição cruzada funcionam. Embora possam usar o índice (não precisa bater em todas as portas), devem verificar separadamente o índice para cada partição física.

### <a name="cross-partition-query-scoped-to-only-a-few-physical-partitions"></a>Consulta de divisória cruzada (com o alcance de apenas algumas divisórias físicas)

Se o condutor de entregas souber que todos os destinatários do pacote vivem dentro de alguns complexos de apartamentos, eles não precisarão de conduzir até cada um deles. Enquanto conduz a alguns complexos de apartamentos ainda vai exigir mais trabalho do que visitar apenas um único edifício, o motorista de entrega ainda poupa tempo e esforço significativos. Se uma consulta tiver a chave de `IN` partição no seu filtro com a palavra-chave, apenas verificará os índices de partição física relevantes para obter dados.

## <a name="avoiding-cross-partition-queries"></a>Evitando consultas de divisória cruzada

Para a maioria dos contentores, é inevitável que tenha algumas consultas de divisórias cruzadas. Ter algumas consultas de divisória cruzada é ok! Quase todas as operações de consulta são suportadas através de divisórias (ambas as chaves de partição lógica seletiva e divisórias físicas). A Azure Cosmos DB também tem muitas otimizações no motor de consulta e sDKs clientes para paralelor a execução de consultas através de divisórias físicas.

Para a maioria dos cenários de leitura pesada, recomendamos simplesmente selecionar a propriedade mais comum nos seus filtros de consulta. Deve também certificar-se de que a chave de partição adere a outras práticas de seleção de chaves de [partição.](partitioning-overview.md#choose-partitionkey)

Evitar consultas de divisórias cruzadas normalmente só importa com recipientes grandes. É-lhe cobrado um mínimo de cerca de 2,5 RU's cada vez que verifica o índice de uma divisória física para obter resultados, mesmo que nenhum itens na divisória física correspondam ao filtro da consulta. Como tal, se tiver apenas uma (ou apenas algumas) divisórias físicas, as consultas de divisóriacruzada não consumirão significativamente mais consultas de RU do que consultas de partição.

O número de divisórias físicas está ligado à quantidade de RU's provisionados. Cada partição física permite até 10.000 RU's provisionados e pode armazenar até 50 GB de dados. O Azure Cosmos DB irá gerir automaticamente as divisórias físicas para si. O número de divisórias físicas no seu recipiente depende da sua entrada e armazenamento consumido.

Deve tentar evitar consultas de divisóriacruzada se a sua carga de trabalho cumprir os critérios abaixo:
- Planeia ter mais de 30.000 RU's provisionados
- Planeia armazenar mais de 100 GB de dados

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para aprender sobre a partilha em Azure Cosmos DB:

- [Criação de partições no Azure Cosmos DB](partitioning-overview.md)
- [Chaves de partição sintéticas no Azure Cosmos DB](synthetic-partition-keys.md)
