---
title: incluir ficheiro
description: incluir ficheiro
services: application-insights
author: lgayhardt
ms.service: application-insights
ms.topic: include
ms.date: 08/06/2019
ms.author: lagayhar
ms.custom: include file
ms.openlocfilehash: 1c4f6b876a4aa80c7e51f2bb3ca88234203d0daa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101726188"
---
Existem alguns limites no número de métricas e eventos por aplicação, isto é, por chave de instrumentação. Os limites dependem do [plano de preços](https://azure.microsoft.com/pricing/details/application-insights/) que escolher.

| Recurso | Limite predefinido | Nota
| --- | --- | --- |
| Total de dados por dia | 100 GB | Pode reduzir os dados ao definir um limite. Se precisar de mais dados, pode aumentar o limite no portal, até 1.000 GB. Para capacidades superiores a 1.000 GB, envie um e-mail para AIDataCap@microsoft.com .
| Limitação | 32.000 eventos/segundo | O limite é mediso ao longo de um minuto.
| Registos de retenção de dados | [30 - 730 dias](../articles/azure-monitor/app/pricing.md#change-the-data-retention-period)  | Este recurso [destina-se a Registos.](../articles/azure-monitor/logs/log-query-overview.md)
| Métricas de retenção de dados | 90 dias| Este recurso [destina-se ao Metrics Explorer.](../articles/azure-monitor/essentials/metrics-charts.md)
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