---
title: 'Insights de aplicação: línguas, plataformas e integrações [ Microsoft Docs'
description: Idiomas, plataformas e integrações disponíveis para Insights de Aplicação
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: 153d4ad3d95c182dcc4f2aa3bad857d7e1984cc2
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891115"
---
# <a name="supported-languages"></a>Linguagens suportadas

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript](../../azure-monitor/app/javascript.md)
* [Nó.JS](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>Plataformas e quadros suportados

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Instrumentação para aplicações já implantadas (sem código, baseada em agentes)
* [Conjuntos de escala de máquinas virtuais Azure VM e Azure](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Serviço de Aplicações do Azure](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET – para aplicações que já estão em direto](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Serviços Azure Cloud](../../azure-monitor/app/cloudservices.md), incluindo funções web e trabalhadores
* [Funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
### <a name="instrumentation-through-code-sdks"></a>Instrumentação através de código (SDKs)
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Nó.JS](https://www.npmjs.com/package/applicationinsights)
* [Python](../../azure-monitor/app/opencensus-python.md)
* [Aplicação Universal do Windows](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Aplicações de ambiente de trabalho, serviços e funções de trabalho do Windows](../../azure-monitor/app/windows-desktop.md)

## <a name="logging-frameworks"></a>Arquiteturas de registo
* [ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)
* [Log4Net, NLog ou System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java, Log4J ou Logback](../../azure-monitor/app/java-trace-logs.md)
* [Plug-in do LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="export-and-data-analysis"></a>Exportação e análise de dados
* [Power BI](https://powerbi.microsoft.com/blog/explore-your-application-insights-data-with-power-bi/)
* [Stream Analytics](../../azure-monitor/app/export-power-bi.md)

## <a name="unsupported-sdks"></a>SDKs não suportados
Sabemos que existem vários outros SDKs apoiados pela comunidade. No entanto, o Monitor Azure apenas fornece suporte ao utilizar os SDKs suportados listados nesta página. Estamos constantemente a avaliar oportunidades para expandir o nosso suporte para outras línguas, por isso siga a nossa página de [Anúncios GitHub](https://github.com/microsoft/ApplicationInsights-Announcements/issues) para receber as últimas novidades do SDK. 
