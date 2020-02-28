---
title: 'Insights de aplicação: línguas, plataformas e integrações  Microsoft Docs'
description: Idiomas, plataformas e integrações disponíveis para Insights de Aplicação
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: d87b1d0aa74263f249db2030f2366b93b8c2b421
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669833"
---
# <a name="supported-languages"></a>Linguagens suportadas

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)
* [Python (pré-visualização)](../../azure-monitor/app/opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>Plataformas e quadros suportados

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Instrumentação para aplicações já implantadas (sem código, baseada em agentes)
* [Conjuntos de escala de máquinas virtuais Azure VM e Azure](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [App Service do Azure](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET – para aplicações que já estão em direto](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Serviços Azure Cloud](../../azure-monitor/app/cloudservices.md), incluindo funções web e trabalhadores
* [Funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
### <a name="instrumentation-through-code-sdks"></a>Instrumentação através de código (SDKs)
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [Núcleo do ASP.NET](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [Python (pré-visualização)](../../azure-monitor/app/opencensus-python.md)
* [Aplicação Universal do Windows](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Aplicações de ambiente de trabalho, serviços e funções de trabalho do Windows](../../azure-monitor/app/windows-desktop.md)

## <a name="logging-frameworks"></a>Arquiteturas de registo
* [ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)
* [Log4Net, NLog ou System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java, Log4J ou Logback](../../azure-monitor/app/java-trace-logs.md)
* [Plug-in do LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="export-and-data-analysis"></a>Exportação e análise de dados
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Stream Analytics](../../azure-monitor/app/export-power-bi.md)

## <a name="unsupported-sdks"></a>SDKs não suportados
Sabemos que existem vários outros SDKs apoiados pela comunidade. No entanto, o Monitor Azure apenas fornece suporte ao utilizar os SDKs suportados listados nesta página. Estamos constantemente a avaliar oportunidades para expandir o nosso suporte para outras línguas, por isso siga a nossa página de [Anúncios GitHub](https://github.com/microsoft/ApplicationInsights-Announcements/issues) para receber as últimas novidades do SDK. 
