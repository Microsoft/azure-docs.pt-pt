---
title: Monitor aplicações no Serviço Azure Kubernetes (AKS) com Insights de Aplicações - Azure Monitor / Microsoft Docs
description: O Azure Monitor integra-se perfeitamente com a sua aplicação em execução em Kubernetes, e permite-lhe detetar os problemas com as suas apps num instante.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/13/2020
ms.openlocfilehash: 76f9f922697ef6be6c959ea7f9bafd0872dba6ff
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83773764"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes---azure-monitor-application-insights"></a>Monitorização de aplicações de instrumentação zero para Kubernetes - Azure Monitor Application Insights

> [!IMPORTANT]
>  Atualmente pode ativar a monitorização das suas aplicações Java em execução em Kubernetes sem instrumentar o seu código - utilize o [agente autónomo Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent). Enquanto a solução para permitir a monitorização perfeita da aplicação está em funcionamento para outras línguas, utilize os SDKs para monitorizar as suas aplicações em execução em AKS: [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core), [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net), [Node.js, ](https://docs.microsoft.com/azure/azure-monitor/app/nodejs) [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript)e [Python.](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)

## <a name="application-monitoring-without-instrumenting-the-code"></a>Monitorização de aplicações sem instrumentar o código
Atualmente, apenas a Java permite ativar a monitorização da aplicação sem instrumentar o código. Para monitorizar aplicações em outros idiomas, utilize os SDKs. 

## <a name="java"></a>Java
Uma vez ativado, o agente Java recolherá automaticamente uma infinidade de pedidos, dependências, registos e métricas das bibliotecas e quadros mais utilizados.

Siga [as instruções detalhadas](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) para monitorizar as suas aplicações Java em execução em aplicações Kubernetes, bem como em outros ambientes. 

## <a name="other-languages"></a>Outras linguagens

Para as aplicações em outras línguas recomendamos atualmente a utilização dos SDKs:
* [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)
* [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)
* [Node.js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs) 
* [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript)
* [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre [o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) e [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)
* Obtenha uma visão geral do [Rastreio Distribuído](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing) e veja o que o [Mapa de Aplicações](https://docs.microsoft.com/azure/azure-monitor/app/app-map?tabs=net) pode fazer para o seu negócio