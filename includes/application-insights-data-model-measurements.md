---
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 11/09/2018
ms.author: mbullwin
ms.openlocfilehash: 91141e314bf276d9138157c8a9d85d5262ac5907
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67184017"
---
Coleção de medições personalizadas. Utilize esta coleção para reportar a medição nomeada associada ao item de telemetria. Os casos de uso típico são:
- o tamanho da carga útil da Telemetria dependência
- o número de itens de fila processados pela Request Telemettry
- tempo que o cliente demorou a completar o passo na conclusão do passo do assistente Telemettry Event.

Pode consultar [medições personalizadas](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA2WLOw6DMAyGd07hZoLeoRPqyMaGGAL8aiPhGCV2kKoeHsHK%2Bj1myyr8LoiaqfrT%2FkUCzRft4LMl8OUeL3LuLLIx%2BxR%2BIF8%2BtcoiNq2o78vgWuFthQaJ1AeGGxt6UlBwKxa1qQ6EpLhAfQAAAA%3D%3D&timespan=PT24H) no Application Analytics:

```
customEvents
| where customMeasurements != ""
| summarize avg(todouble(customMeasurements["Completion Time"]) * itemCount)
```

 > [!NOTE]
 > As medições personalizadas estão associadas ao item de telemetria a que pertencem. Estão sujeitos a amostragem com o artigo de telemetria que contém essas medições. Para rastrear uma medição que tenha um valor independente de outros tipos de telemetria, utilize [a telemetria métrica.](../articles/azure-monitor/app/api-custom-events-metrics.md)

Comprimento máximo da tecla: 150
