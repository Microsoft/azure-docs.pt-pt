---
title: Use repartição para otimizar os empregos Azure Stream Analytics
description: Este artigo descreve como usar a repartição para otimizar os trabalhos do Azure Stream Analytics que não podem ser paralelos.
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 95749f2acea6b605cfdba5a4f3d4f5526e751c5a
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182541"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>Use a repartição para otimizar o processamento com a Azure Stream Analytics

Este artigo mostra-lhe como usar a repartição para escalar a sua consulta Azure Stream Analytics para cenários que não podem ser totalmente [paralelos](stream-analytics-scale-jobs.md).

Pode não ser capaz de utilizar a [paralelização](stream-analytics-parallelization.md) se:

* Não se controla a chave de partição para o fluxo de entrada.
* A sua fonte "pulveriza" a entrada em várias divisórias que mais tarde precisam de ser fundidas.

A repartição, ou remodelação, é necessária quando processa dados num fluxo que não é cacitado de acordo com um esquema de entrada natural, como **o PartitionId** para Os Centros de Eventos. Ao repartir, cada fragmento pode ser processado de forma independente, o que lhe permite escalar linearmente o seu gasoduto de streaming. 

## <a name="how-to-repartition"></a>Como repartir
Pode repartir a sua entrada de 2 maneiras:
1. Use um trabalho separado de Stream Analytics que faça a repartição
2. Use um único trabalho, mas faça a repartição primeiro antes da sua lógica de análise personalizada

### <a name="creating-a-separate-stream-analytics-job-to-repartition-input"></a>Criando um trabalho separado de Stream Analytics para repartição de entrada
Pode criar um trabalho que leia entradas e escreva para uma saída do Event Hub utilizando uma chave de partição. Este Centro de Eventos pode então servir de entrada para outro trabalho de Stream Analytics onde implementa a sua lógica de análise. Ao configurar esta saída do Event Hub no seu trabalho, tem de especificar a chave de partição através da qual o Stream Analytics repartirá os seus dados. 
```sql
-- For compat level 1.2 or higher
SELECT * 
INTO output
FROM input

--For compat level 1.1 or lower
SELECT *
INTO output
FROM input PARTITION BY PartitionId
```

### <a name="repartition-input-within-a-single-stream-analytics-job"></a>Entrada de repartição dentro de um único trabalho stream analytics
Também pode introduzir um passo na sua consulta que primeiro repartis a entrada e que pode ser usada por outros passos na sua consulta. Por exemplo, se pretender repartir a entrada com base no **DeviceId,** a sua consulta será:
```sql
WITH RepartitionedInput AS 
( 
SELECT * 
FROM input PARTITION BY DeviceID
)

SELECT DeviceID, AVG(Reading) as AvgNormalReading  
INTO output
FROM RepartitionedInput  
GROUP BY DeviceId, TumblingWindow(minute, 1)  
```

A seguinte consulta de exemplo junta dois fluxos de dados repartidos. Ao unir dois fluxos de dados repartidos, os fluxos devem ter a mesma chave de partição e contar. O resultado é um riacho que tem o mesmo esquema de partição.

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID)

SELECT * INTO output FROM step1 PARTITION BY DeviceID UNION step2 PARTITION BY DeviceID
```

O regime de saída deve corresponder à chave do esquema de fluxo e contar de modo a que cada substream possa ser lavado de forma independente. O fluxo também poderia ser fundido e repartido novamente por um esquema diferente antes da descarga, mas deve evitar esse método porque aumenta a latência geral do processamento e aumenta a utilização dos recursos.

## <a name="streaming-units-for-repartitions"></a>Unidades de streaming para repartições

Experimente e observe o uso de recursos do seu trabalho para determinar o número exato de divisórias de que necessita. O número de unidades de [streaming (SU)](stream-analytics-streaming-unit-consumption.md) deve ser ajustado de acordo com os recursos físicos necessários para cada partição. Em geral, são necessárias seis SUs para cada partição. Se não houver recursos suficientes atribuídos ao trabalho, o sistema só aplicará a repartição se beneficiar o trabalho.

## <a name="repartitions-for-sql-output"></a>Repartições para a produção de SQL

Quando o seu trabalho utilizar a base de dados SQL para saída, utilize uma repartição explícita para corresponder à contagem de divisórias ideal para maximizar a produção. Uma vez que o SQL funciona melhor com oito escritores, repartindo o fluxo para oito antes do flushing, ou em algum lugar mais a montante, pode beneficiar o desempenho do trabalho. 

Quando existem mais de 8 divisórias de entrada, herdar o sistema de partição de entrada pode não ser uma escolha adequada. Considere usar [o INTO](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) na sua consulta para especificar explicitamente o número de escritores de saída. 

O exemplo a seguir lê-se a partir da entrada, independentemente de ser naturalmente dividido, e repartitions o fluxo dez vezes de acordo com a dimensão DeviceID e lava os dados para a saída. 

```sql
SELECT * INTO [output] FROM [input] PARTITION BY DeviceID INTO 10
```

Para obter mais informações, consulte [a saída Azure Stream Analytics para a Base de Dados Azure SQL](stream-analytics-sql-output-perf.md).


## <a name="next-steps"></a>Passos seguintes

* [Começa com o Azure Stream Analytics](stream-analytics-introduction.md)
* [Paralelização de consulta de alavancagem em Azure Stream Analytics](stream-analytics-parallelization.md)
