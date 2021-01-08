---
title: Use repartição para otimizar os empregos Azure Stream Analytics
description: Este artigo descreve como usar a repartição para otimizar os trabalhos do Azure Stream Analytics que não podem ser paralelos.
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.date: 09/19/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 72f81a0eac81acdca71c8ed81695789c417898ca
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98014200"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>Use a repartição para otimizar o processamento com a Azure Stream Analytics

Este artigo mostra-lhe como usar a repartição para escalar a sua consulta Azure Stream Analytics para cenários que não podem ser totalmente [paralelos](stream-analytics-scale-jobs.md).

Pode não ser capaz de utilizar a [paralelização](stream-analytics-parallelization.md) se:

* Não se controla a chave de partição para o fluxo de entrada.
* A sua fonte "pulveriza" a entrada em várias divisórias que mais tarde precisam de ser fundidas.

A repartição, ou remodelação, é necessária quando processa dados num fluxo que não é cacitado de acordo com um esquema de entrada natural, como **o PartitionId** para Os Centros de Eventos. Ao repartir, cada fragmento pode ser processado de forma independente, o que lhe permite escalar linearmente o seu gasoduto de streaming.

## <a name="how-to-repartition"></a>Como repartir

Para repartir, utilize a palavra-chave **INTO** depois de uma declaração **PARTITION BY** na sua consulta. O exemplo seguinte partilha os dados pelo **DeviceID** numa contagem de partição de 10. O hashing do **DeviceID** é utilizado para determinar qual a divisória que deve aceitar qual substream. Os dados são lavados de forma independente para cada fluxo dividido, assumindo que a saída suporta escritas divididas, e tem 10 divisórias.

```sql
SELECT * 
INTO output
FROM input
PARTITION BY DeviceID 
INTO 10
```

A seguinte consulta de exemplo junta dois fluxos de dados repartidos. Ao unir dois fluxos de dados repartidos, os fluxos devem ter a mesma chave de partição e contar. O resultado é um riacho que tem o mesmo esquema de partição.

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID INTO 10),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID INTO 10)

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


## <a name="next-steps"></a>Próximos passos

* [Começa com o Azure Stream Analytics](stream-analytics-introduction.md)
* [Paralelização de consulta de alavancagem em Azure Stream Analytics](stream-analytics-parallelization.md)
