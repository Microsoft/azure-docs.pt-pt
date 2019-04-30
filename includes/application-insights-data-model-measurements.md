---
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 11/09/2018
ms.author: mbullwin
ms.openlocfilehash: 91141e314bf276d9138157c8a9d85d5262ac5907
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60900523"
---
Coleção de medidas personalizadas. Utilize esta coleção para o relatório com o nome medidas associadas ao item de telemetria. Casos de utilização típicos são:
- o tamanho do payload de telemetria de dependência
- o número de itens de fila, processadas por pedido de telemetria
- tempo que o cliente demorou a concluir a etapa de conclusão do Assistente de passo telemetria dos eventos.

Pode consultar [medições personalizadas](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA2WLOw6DMAyGd07hZoLeoRPqyMaGGAL8aiPhGCV2kKoeHsHK%2Bj1myyr8LoiaqfrT%2FkUCzRft4LMl8OUeL3LuLLIx%2BxR%2BIF8%2BtcoiNq2o78vgWuFthQaJ1AeGGxt6UlBwKxa1qQ6EpLhAfQAAAA%3D%3D&timespan=PT24H) no Application Analytics:

```
customEvents
| where customMeasurements != ""
| summarize avg(todouble(customMeasurements["Completion Time"]) * itemCount)
```

 > [!NOTE]
 > Medições personalizadas estão associadas com o item de telemetria que pertencem. Estão sujeitas à amostragem com o item de telemetria que contém essas medidas. Para controlar uma medida com um valor independente dos outros tipos de telemetria, utilize [telemetria de métrica](../articles/azure-monitor/app/api-custom-events-metrics.md).

Comprimento máximo da chave: 150
