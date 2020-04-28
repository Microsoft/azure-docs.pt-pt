---
title: Use a repartição para otimizar os trabalhos da Azure Stream Analytics
description: Este artigo descreve como usar a repartição para otimizar trabalhos da Azure Stream Analytics que não podem ser paralelos.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 09/19/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: c70cfb6c1626908a2ba4e707a890f6dc7481c51a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75732387"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>Utilize a repartição para otimizar o processamento com o Azure Stream Analytics

Este artigo mostra-lhe como usar a repartição para escalar a sua consulta azure Stream Analytics para cenários que não podem ser totalmente [paralelos](stream-analytics-scale-jobs.md).

Pode não ser capaz de utilizar a [paralelização](stream-analytics-parallelization.md) se:

* Não controlas a chave de partição para o teu fluxo de entrada.
* A sua entrada de "sprays" de origem em várias divisórias que mais tarde precisam de ser fundidas.

A repartição, ou remodelação, é necessária quando processa dados num fluxo que não é esfarto de acordo com um esquema de entrada natural, como **partitionId** for Event Hubs. Quando se repartição, cada fragmento pode ser processado de forma independente, o que lhe permite reduzir linearmente o seu pipeline de streaming.

## <a name="how-to-repartition"></a>Como repartição

Para sedividir, utilize a palavra-chave **para depois** de uma declaração **de partição por** parte da sua consulta. O exemplo seguinte dividiu os dados do **DeviceID** numa contagem de divisórias de 10. O hashing do **DeviceID** é utilizado para determinar qual a divisória que deve aceitar qual subfluxo. Os dados são lavados independentemente para cada fluxo dividido, assumindo que a saída suporta escritos divididos, e tem 10 divisórias.

```sql
SELECT * 
INTO output
FROM input
PARTITION BY DeviceID 
INTO 10
```

A consulta de exemplo seguinte junta dois fluxos de dados reparticionados. Ao juntar dois fluxos de dados repartiados, os fluxos devem ter a mesma chave de partição e contagem. O resultado é um fluxo que tem o mesmo esquema de partição.

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID INTO 10),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID INTO 10)

SELECT * INTO output FROM step1 PARTITION BY DeviceID UNION step2 PARTITION BY DeviceID
```

O regime de saída deve corresponder à chave do regime de fluxo e contar de modo a que cada subfluxo possa ser lavado de forma independente. O fluxo também poderia ser fundido e repartiquilado novamente por um esquema diferente antes de descarregar, mas deve evitar esse método porque aumenta a latência geral do processamento e aumenta a utilização dos recursos.

## <a name="streaming-units-for-repartitions"></a>Unidades de streaming para repartipartações

Experimente e observe o uso de recursos do seu trabalho para determinar o número exato de divisórias que precisa. O número de unidades de [streaming (SU)](stream-analytics-streaming-unit-consumption.md) deve ser ajustado de acordo com os recursos físicos necessários para cada partição. Em geral, são necessárias seis USS para cada partição. Se não houver recursos suficientes atribuídos ao trabalho, o sistema só aplicará a repartição se beneficiar o trabalho.

## <a name="repartitions-for-sql-output"></a>Repartições para saída SQL

Quando o seu trabalho utilizar a base de dados SQL para a saída, utilize a repartição explícita para corresponder à contagem de partição ideal para maximizar a produção. Uma vez que a SQL funciona melhor com oito escritores, a divisão do fluxo para oito antes de flushing, ou em algum lugar mais a montante, pode beneficiar o desempenho do trabalho. 

Quando existam mais de 8 divisórias de entrada, herdar o regime de partição de entrada pode não ser uma escolha adequada. Considere usar [o INTO](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) na sua consulta para especificar explicitamente o número de escritores de saída. 

O exemplo seguinte lê-se a partir da entrada, independentemente de ser naturalmente dividida, e repartipartide o fluxo dez vezes de acordo com a dimensão do Dispositivo ID e coloca os dados à saída. 

```sql
SELECT * INTO [output] FROM [input] PARTITION BY DeviceID INTO 10
```

Para mais informações, consulte a saída do Azure Stream Analytics para a Base de [Dados Azure SQL](stream-analytics-sql-output-perf.md).


## <a name="next-steps"></a>Passos seguintes

* [Começar com azure stream analytics](stream-analytics-introduction.md)
* [Alavancar a paralelização da consulta no Azure Stream Analytics](stream-analytics-parallelization.md)
