---
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 11/09/2018
ms.author: mbullwin
ms.openlocfilehash: 91141e314bf276d9138157c8a9d85d5262ac5907
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "67184017"
---
Recolha de medições personalizadas. Utilize esta coleção para reportar a medição indicada associada ao item da telemetria. Os casos de uso típico são:
- o tamanho da carga útil da Telemetria de Dependência
- o número de itens de fila processados por Telemetria Solicit
- tempo que o cliente demorou a completar o passo na conclusão do evento de telemetria de etapa de assistente.

Pode consultar [medições personalizadas](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA2WLOw6DMAyGd07hZoLeoRPqyMaGGAL8aiPhGCV2kKoeHsHK%2Bj1myyr8LoiaqfrT%2FkUCzRft4LMl8OUeL3LuLLIx%2BxR%2BIF8%2BtcoiNq2o78vgWuFthQaJ1AeGGxt6UlBwKxa1qQ6EpLhAfQAAAA%3D%3D&timespan=PT24H) em Application Analytics:

```
customEvents
| where customMeasurements != ""
| summarize avg(todouble(customMeasurements["Completion Time"]) * itemCount)
```

 > [!NOTE]
 > As medições personalizadas estão associadas ao item de telemetria a que pertencem. Estão sujeitos a amostragem com o item da telemetria que contém essas medições. Para acompanhar uma medição que tenha um valor independente de outros tipos de telemetria, utilize [a telemetria métrica](../articles/azure-monitor/app/api-custom-events-metrics.md).

Comprimento da chave máxima: 150
