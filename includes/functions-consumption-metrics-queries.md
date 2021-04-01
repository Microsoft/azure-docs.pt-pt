---
ms.openlocfilehash: 0bae8946b7d81fbf45698cefe7c8b00bbc94d00c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92168120"
---
#### <a name="determine-memory-usage"></a>Determinar o uso da memória 

Em **Monitorização**, selecione **Logs (Analytics)**, em seguida, copie a seguinte consulta de telemetria e cole-a na janela de consulta e selecione **Executar**. Esta consulta devolve o uso total da memória em cada tempo amostrado.

```
performanceCounters
| where name == "Private Bytes"
| project timestamp, name, value
```

Os resultados parecem ser o seguinte exemplo:

| \[timetamp UTC\]          | name          | valor       |
|----------------------------|---------------|-------------|
| 9/12/2019, 1:05:14 \. 947 AM | Bytes privados | 209,932,288 |
| 9/12/2019, 1:06:14 \. 994 AM | Bytes privados | 212,189,184 |
| 9/12/2019, 1:06:30 \. 010 | Bytes privados | 231,714,816 |
| 9/12/2019, 1:07:15 \. 040 AM | Bytes privados | 210,591,744 |
| 9/12/2019, 1:12:16 \. 285 AM | Bytes privados | 216,285,184 |
| 9/12/2019, 1:12:31 \. 376 AM | Bytes privados | 235,806,720 |

#### <a name="determine-duration"></a>Determinar a duração 

O Azure Monitor rastreia métricas ao nível do recurso, que para funções é a aplicação de função. A integração de Insights de Aplicação emite métricas por função. Aqui está um exemplo de consulta de análise para obter a duração média de uma função:

```
customMetrics
| where name contains "Duration"
| extend averageDuration = valueSum / valueCount
| summarize averageDurationMilliseconds=avg(averageDuration) by name
```

| name                       | médiaDurationMilliseconds |
|----------------------------|-----------------------------|
| QueueTrigger AvgDurationMs | 16 \. 087                     |
| FilaTrigger MaxDurationMs | 90 \. 249                     |
| FilaTrigger MinDurationMs | 8 \. 522                      |
