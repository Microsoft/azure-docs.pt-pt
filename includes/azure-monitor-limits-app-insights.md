---
title: incluir ficheiro
description: incluir ficheiro
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: fb57d8322cfd3f72862dc8edd1d2e231338a66de
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "67305321"
---
Há alguns limites no número de métricas e eventos por aplicativo, ou seja, por chave de instrumentação. Os limites dependem do [plano de preços](https://azure.microsoft.com/pricing/details/application-insights/) que escolher.

| Resource | Limite predefinido | Nota
| --- | --- | --- |
| Total de dados por dia | 100 GB | Pode reduzir os dados ao definir um limite. Se você precisar de mais dados, poderá aumentar o limite no portal, até 1.000 GB. Para capacidades maiores que 1.000 GB, envie um email AIDataCap@microsoft.compara.
| Limitação | 32.000 eventos/segundo | O limite é mediso ao longo de um minuto.
| Retenção de dados | 90 dias | Este recurso é para [Pesquisa](../articles/azure-monitor/app/diagnostic-search.md), [Análise](../articles/azure-monitor/app/analytics.md) e [Explorador de métricas](../articles/azure-monitor/app/metrics-explorer.md).
| [Teste de disponibilidade de vários passos](../articles/azure-monitor/app/availability-multistep.md) com retenção de resultados detalhados | 90 dias | Este recurso fornece resultados detalhados de cada passo.
| Tamanho máximo do evento | 64,000 |
| Comprimento do nome da propriedade e da métrica | 150 | Consulte [esquemas de tipo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Comprimento da cadeia de valor da propriedade | 8,192 | Consulte [esquemas de tipo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Comprimento da mensagem de exceção e de rastreio | 32.768  | Consulte [esquemas de tipo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| [Testes de disponibilidade](../articles/azure-monitor/app/monitor-web-app-availability.md) com contagem por aplicação | 100 |
| Retenção de dados do [criador de perfil](../articles/azure-monitor/app/profiler.md) | 5 dias |
| Dados do [criador de perfil](../articles/azure-monitor/app/profiler.md) enviados por dia | 10 GB |

Para mais informações, consulte [About pricing and quotas in Application Insights (Acerca de preços e quotas no Application Insights)](../articles/azure-monitor/app/pricing.md).