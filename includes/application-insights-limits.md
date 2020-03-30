---
title: incluir ficheiro
description: incluir ficheiro
services: application-insights
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 08/06/2019
ms.author: mbullwin
ms.custom: include file
ms.openlocfilehash: 14e2bd4af2616e9dd33fe8267de132ab6c0f1cfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "69982618"
---
Existem alguns limites para o número de métricas e eventos por aplicação, isto é, por chave de instrumentação. Os limites dependem do [plano de preços](https://azure.microsoft.com/pricing/details/application-insights/) que escolher.

| Recurso | Limite predefinido | Nota
| --- | --- | --- |
| Total de dados por dia | 100 GB | Pode reduzir os dados ao definir um limite. Se precisar de mais dados, pode aumentar o limite no portal, até 1.000 GB. Para capacidades superiores a 1.000 AIDataCap@microsoft.comGB, envie e-mail para .
| Limitação | 32.000 eventos/segundo | O limite é mediso ao longo de um minuto.
| Retenção de dados | [30 - 730 dias](https://docs.microsoft.com/azure/azure-monitor/app/pricing#change-the-data-retention-period)  | Este recurso é para [Pesquisa](../articles/azure-monitor/app/diagnostic-search.md), [Análise](../articles/azure-monitor/app/analytics.md) e [Explorador de métricas](../articles/azure-monitor/app/metrics-explorer.md).
| [Teste de disponibilidade de vários passos](../articles/azure-monitor/app/availability-multistep.md) com retenção de resultados detalhados | 90 dias | Este recurso fornece resultados detalhados de cada passo.
| Tamanho máximo do artigo de telemetria | 64 kB |
| Itens máximos de telemetria por lote | 64 K |
| Comprimento do nome da propriedade e da métrica | 150 | Ver [tipo de schemas.](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond)
| Comprimento da cadeia de valor da propriedade | 8,192  | Ver [tipo de schemas.](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond)
| Comprimento da mensagem de exceção e de rastreio | 32,768  | Ver [tipo de schemas.](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond)
| [Testes de disponibilidade](../articles/azure-monitor/app/monitor-web-app-availability.md) com contagem por aplicação | 100 |
| [Retenção](../articles/azure-monitor/app/profiler.md) de dados do profiler | 5 dias |
| Dados do [perfil enviados](../articles/azure-monitor/app/profiler.md) por dia | 10 GB |

Para mais informações, consulte [About pricing and quotas in Application Insights (Acerca de preços e quotas no Application Insights)](../articles/azure-monitor/app/pricing.md).