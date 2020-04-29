---
title: Modelo de dados de insights de aplicação azure - Trace Telemettry
description: Modelo de dados de Insights de Aplicação para telemetria de vestígios
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 31958b26cdb8a7897cf0051af6600014c07949fd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77671958"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Telemetria de vestígios: Modelo de dados de Insights de Aplicação

A telemetria de vestígios `printf` (em Insights de [Aplicação)](../../azure-monitor/app/app-insights-overview.md)representa declarações de traços de estilo que são pesquisadas por texto. `Log4Net`, `NLog`e outras entradas de ficheiros de registo baseadas em texto são traduzidas em casos deste tipo. O traço não tem medidas como uma extensibility.

## <a name="message"></a>Mensagem

Mensagem de rastreio.

Comprimento máximo: 32768 caracteres

## <a name="severity-level"></a>Nível de gravidade

Nível de gravidade do traço. O valor `Verbose` `Information`pode `Warning` `Error`ser, . . . `Critical`

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Passos seguintes

- [Explore os registos de rastreio .NET em Insights de Aplicação](../../azure-monitor/app/asp-net-trace-logs.md).
- Explore os registos de [rastreio de Java em Insights de Aplicação](../../azure-monitor/app/java-trace-logs.md).
- Consulte o modelo de [dados](data-model.md) para tipos de Insights de Aplicação e modelo de dados.
- [Escreva telemetria de vestígios personalizados](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
- Confira [as plataformas](../../azure-monitor/app/platforms.md) suportadas por Application Insights.
