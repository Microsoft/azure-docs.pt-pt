---
title: 'Application Insights: linguagens, plataformas e integrações | Microsoft Docs'
description: Linguagens, plataformas e integrações disponíveis para Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: 35d1cf37e1df0b2714505833d40163775e3d090c
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819029"
---
# <a name="supported-languages"></a>Linguagens suportadas

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)

## <a name="supported-platforms-and-frameworks"></a>Plataformas e estruturas com suporte

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Instrumentação para aplicativos já implantados (sem código, baseados em agente)
* [Conjuntos de dimensionamento de máquinas virtuais do Azure e VM do Azure](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [App Service do Azure](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET – para aplicações que já estão em direto](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Serviços de nuvem do Azure](../../azure-monitor/app/cloudservices.md), incluindo funções Web e de trabalho
* [Funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
### <a name="instrumentation-through-code-sdks"></a>Instrumentação por meio de código (SDKs)
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [Núcleo do ASP.NET](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [Aplicação Universal do Windows](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Aplicações de ambiente de trabalho, serviços e funções de trabalho do Windows](../../azure-monitor/app/windows-desktop.md)

## <a name="logging-frameworks"></a>Arquiteturas de registo
* [ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)
* [Log4Net, NLog ou System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java, Log4J ou Logback](../../azure-monitor/app/java-trace-logs.md)
* [Plug-in do LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="export-and-data-analysis"></a>Análise de dados e exportação
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Stream Analytics](../../azure-monitor/app/export-power-bi.md)

## <a name="unsupported-sdks"></a>SDKs não suportados
Estamos cientes de que existem vários outros SDKs com suporte da Comunidade. No entanto, Azure Monitor só fornece suporte ao usar os SDKs com suporte listados nesta página. Estamos constantemente avaliando as oportunidades de expandir nosso suporte para outras linguagens, então siga nossa página de [anúncios do GitHub](https://github.com/microsoft/ApplicationInsights-Announcements/issues) para receber as notícias mais recentes do SDK. 
