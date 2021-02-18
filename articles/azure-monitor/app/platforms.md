---
title: 'Application Insights: idiomas, plataformas e integrações | Microsoft Docs'
description: Idiomas, plataformas e integrações disponíveis para Insights de Aplicações
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: 399e57377a779622aa3073dfd3313cee1db345f8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100583852"
---
# <a name="supported-languages"></a>Linguagens suportadas

* [C#|VB (.NET)](./asp-net.md)
* [Java](./java-get-started.md)
* [JavaScript](./javascript.md)
* [Node.JS](./nodejs.md)
* [Python](./opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>Plataformas e quadros apoiados

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Instrumentação para aplicações já implantadas (codificada, baseada em agentes)
* [Conjuntos de escala de máquina virtual Azure VM e Azure](./azure-vm-vmss-apps.md)
* [Serviço de Aplicações do Azure](./azure-web-apps.md)
* [ASP.NET – para aplicações que já estão em direto](./monitor-performance-live-website-now.md)
* [Azure Cloud Services](./cloudservices.md), incluindo papéis web e trabalhadores
* [Funções do Azure](../../azure-functions/functions-monitoring.md)
### <a name="instrumentation-through-code-sdks"></a>Instrumentação através do código (SDKs)
* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Android](../app/mobile-center-quickstart.md) (App Center)
* [iOS](../app/mobile-center-quickstart.md) (App Center)
* [Java EE](./java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [Python](./opencensus-python.md)
* [Aplicação Universal do Windows](../app/mobile-center-quickstart.md) (App Center)
* [Aplicações de ambiente de trabalho, serviços e funções de trabalho do Windows](./windows-desktop.md)
* [React](./javascript-react-plugin.md)
* [React Native](./javascript-react-native-plugin.md)

## <a name="logging-frameworks"></a>Arquiteturas de registo
* [ILogger](./ilogger.md)
* [Log4Net, NLog ou System.Diagnostics.Trace](./asp-net-trace-logs.md)
* [Java, Log4J ou Logback](./java-trace-logs.md)
* [Plug-in do LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](/archive/blogs/msoms/application-insights-connector-in-oms)

## <a name="export-and-data-analysis"></a>Exportação e análise de dados
* [Power BI](https://powerbi.microsoft.com/blog/explore-your-application-insights-data-with-power-bi/)
* [Stream Analytics](./export-power-bi.md)

## <a name="unsupported-sdks"></a>SDKs não suportados
Sabemos que existem vários outros SDKs apoiados pela comunidade. No entanto, o Azure Monitor só fornece suporte quando utiliza os SDKs suportados listados nesta página. Estamos constantemente a avaliar oportunidades para expandir o nosso suporte para outras línguas, por isso siga a nossa página [de Anúncios GitHub](https://github.com/microsoft/ApplicationInsights-Announcements/issues) para receber as últimas novidades da SDK. 

