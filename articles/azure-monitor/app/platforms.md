---
title: 'Insights de Aplicação: idiomas, plataformas e integrações Microsoft Docs'
description: Idiomas, plataformas e integrações disponíveis para Insights de Aplicações
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: a5403162a2511862dd1c649dc273a35a550abaaf
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87024287"
---
# <a name="supported-languages"></a>Linguagens suportadas

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>Plataformas e quadros apoiados

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Instrumentação para aplicações já implantadas (codificada, baseada em agentes)
* [Conjuntos de escala de máquina virtual Azure VM e Azure](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Serviço de Aplicações do Azure](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET – para aplicações que já estão em direto](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Cloud Services](../../azure-monitor/app/cloudservices.md), incluindo papéis web e trabalhadores
* [Funções do Azure](../../azure-functions/functions-monitoring.md)
### <a name="instrumentation-through-code-sdks"></a>Instrumentação através do código (SDKs)
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [Python](../../azure-monitor/app/opencensus-python.md)
* [Aplicação Universal do Windows](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Aplicações de ambiente de trabalho, serviços e funções de trabalho do Windows](../../azure-monitor/app/windows-desktop.md)

## <a name="logging-frameworks"></a>Arquiteturas de registo
* [ILogger](./ilogger.md)
* [Log4Net, NLog ou System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java, Log4J ou Logback](../../azure-monitor/app/java-trace-logs.md)
* [Plug-in do LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](/archive/blogs/msoms/application-insights-connector-in-oms)

## <a name="export-and-data-analysis"></a>Exportação e análise de dados
* [Power BI](https://powerbi.microsoft.com/blog/explore-your-application-insights-data-with-power-bi/)
* [Stream Analytics](../../azure-monitor/app/export-power-bi.md)

## <a name="unsupported-sdks"></a>SDKs não suportados
Sabemos que existem vários outros SDKs apoiados pela comunidade. No entanto, o Azure Monitor só fornece suporte quando utiliza os SDKs suportados listados nesta página. Estamos constantemente a avaliar oportunidades para expandir o nosso suporte para outras línguas, por isso siga a nossa página [de Anúncios GitHub](https://github.com/microsoft/ApplicationInsights-Announcements/issues) para receber as últimas novidades da SDK. 
