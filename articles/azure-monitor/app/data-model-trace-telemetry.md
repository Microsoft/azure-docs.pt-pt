---
title: Modelo de dados de telemetria de informações de aplicação do Azure - telemetria de rastreio | Documentos da Microsoft
description: Modelo de dados do Application Insights para a telemetria de rastreio
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: df85aafc81b199610c02f0faecb06e804fda24bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60899294"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Telemetria de rastreio: Modelo de dados do Application Insights

Telemetria de rastreio (no [Application Insights](../../azure-monitor/app/app-insights-overview.md)) representa `printf` declarações de rastreio são pesquisados por texto de estilo. `Log4Net`, `NLog`, e outras entradas de ficheiro de registo baseados em texto são convertidas em instâncias deste tipo. O rastreio não tem medidas como uma extensibilidade.

## <a name="message"></a>Message

Mensagem de rastreio.

Comprimento máximo: 32 768 carateres

## <a name="severity-level"></a>Nível de gravidade

Nível de gravidade de rastreio. O valor pode ser `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Passos Seguintes

- [Explore os logs de rastreamento do .NET no Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).
- [Explore Java registos de rastreio no Application Insights](../../azure-monitor/app/java-trace-logs.md).
- Ver [modelo de dados](data-model.md) para o modelo de tipos e dados do Application Insights.
- [Escrever telemetria de rastreio personalizada](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
- Confira [plataformas](../../azure-monitor/app/platforms.md) suportada pelo Application Insights.
