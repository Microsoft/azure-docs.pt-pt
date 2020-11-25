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
ms.openlocfilehash: 76176c72ad77341d7db1c8f4158a90836b74a91c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011353"
---
Existem alguns limites no número de métricas e eventos por aplicação, isto é, por chave de instrumentação. Os limites dependem do [plano de preços](https://azure.microsoft.com/pricing/details/application-insights/) que escolher.

| Recurso | Limite predefinido | Nota
| --- | --- | --- |
| Total de dados por dia | 100 GB | Pode reduzir os dados ao definir um limite. Se precisar de mais dados, pode aumentar o limite no portal, até 1.000 GB. Para capacidades superiores a 1.000 GB, envie um e-mail para AIDataCap@microsoft.com .
| Limitação | 32.000 eventos/segundo | O limite é mediso ao longo de um minuto.
| Retenção de dados | [30 - 730 dias](../articles/azure-monitor/app/pricing.md#change-the-data-retention-period)  | Este recurso é para [Pesquisa](../articles/azure-monitor/app/diagnostic-search.md), [Análise](../articles/azure-monitor/log-query/log-query-overview.md) e [Explorador de métricas](../articles/azure-monitor/platform/metrics-charts.md).
| [Teste de disponibilidade de vários passos](../articles/azure-monitor/app/availability-multistep.md) com retenção de resultados detalhados | 90 dias | Este recurso fornece resultados detalhados de cada passo.
| Tamanho máximo do item da telemetria | 64 kB |
| Itens máximos de telemetria por lote | 64 K |
| Comprimento do nome da propriedade e da métrica | 150 | Ver [esquemas de tipo](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Comprimento da cadeia de valor da propriedade | 8,192  | Ver [esquemas de tipo](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Comprimento da mensagem de exceção e de rastreio | 32,768  | Ver [esquemas de tipo](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| [Testes de disponibilidade](../articles/azure-monitor/app/monitor-web-app-availability.md) com contagem por aplicação | 100 |
| [Retenção de](../articles/azure-monitor/app/profiler.md) dados do perfis | 5 dias |
| [Dados do perfil](../articles/azure-monitor/app/profiler.md) enviados por dia | 10 GB |

Para mais informações, consulte [About pricing and quotas in Application Insights (Acerca de preços e quotas no Application Insights)](../articles/azure-monitor/app/pricing.md).