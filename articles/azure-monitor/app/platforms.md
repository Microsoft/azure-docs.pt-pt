---
title: 'Application Insights: linguagens, plataformas e integrações | Microsoft Docs'
description: Linguagens, plataformas e integrações disponíveis para Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/06/2019
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: db79f2b1d3857aac2f71d2e18a3949f068b746eb
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67990064"
---
# <a name="languages---officially-supported-by-application-insights-team"></a>Idiomas-oficialmente suportados pela equipe de Application Insights

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Páginas Web JavaScript](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)

## <a name="community-sdks"></a>SDKs da Comunidade

Há uma série de SDKs de insights de Aplicativo Azure da Comunidade, muitos dos quais foram criados originalmente pela Microsoft. Os SDKs da Comunidade não são oficialmente mantidos pela Microsoft. Não é possível fornecer suporte para nenhum SDK que não esteja na lista oficialmente suportada. Esses SDKs são considerados experimentais e não são recomendados para uso em produção.

## <a name="platforms-and-frameworks"></a>Plataformas e estruturas
### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Instrumentação para aplicativos já implantados (sem código, baseados em agente)
* [Conjuntos de dimensionamento de máquinas virtuais do Azure e VM do Azure](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Serviço de Aplicações do Azure](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET – para aplicações que já estão em direto](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Serviços de nuvem do Azure](../../azure-monitor/app/cloudservices.md), incluindo funções Web e de trabalho
* [Funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
### <a name="instrumentation-through-code-sdks"></a>Instrumentação por meio de código (SDKs)
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [Núcleo do ASP.NET](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Android](https://github.com/Microsoft/ApplicationInsights-Android) (App Center)
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
