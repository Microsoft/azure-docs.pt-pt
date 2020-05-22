---
title: Monitorize aplicações no Serviço Azure Kubernetes (AKS) com Insights de Aplicação - Monitor Azure [ Monitor De Aplicação ] Microsoft Docs
description: O Azure Monitor integra-se perfeitamente com a sua aplicação em execução na Kubernetes, e permite-lhe detetar os problemas com as suas apps num instante.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/13/2020
ms.openlocfilehash: 76f9f922697ef6be6c959ea7f9bafd0872dba6ff
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773764"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes---azure-monitor-application-insights"></a>Monitorização de aplicações de instrumentação zero para Kubernetes - Insights de aplicação do Monitor Azure

> [!IMPORTANT]
>  Atualmente pode ativar a monitorização das suas aplicações Java em execução em Kubernetes sem instrumentar o seu código - use o [agente autónomo Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent). Enquanto a solução para permitir a monitorização perfeita da aplicação está em andamento para outros idiomas, use os SDKs para monitorizar as suas aplicações em execução em AKS: [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core), [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net), [Node.js,](https://docs.microsoft.com/azure/azure-monitor/app/nodejs) [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript), e [Python.](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)

## <a name="application-monitoring-without-instrumenting-the-code"></a>Monitorização da aplicação sem instrumentalização do código
Atualmente, apenas java permite ativar a monitorização da aplicação sem instrumentalizar o código. Para monitorizar aplicações noutras línguas, utilize os SDKs. 

## <a name="java"></a>Java
Uma vez ativado, o agente Java recolherá automaticamente uma multiplicidade de pedidos, dependências, registos e métricas das bibliotecas e estruturas mais utilizadas.

Siga [as instruções detalhadas](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) para monitorizar as suas aplicações Java em execução em aplicações Kubernetes, bem como em outros ambientes. 

## <a name="other-languages"></a>Outras línguas

Para as aplicações em outros idiomas recomendamos atualmente a utilização dos SDKs:
* [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)
* [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)
* [Node.js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs) 
* [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript)
* [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/overview) e [insights de aplicação](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)
* Obtenha uma visão geral do [Rastreio Distribuído](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing) e veja o que o Mapa de [Aplicações](https://docs.microsoft.com/azure/azure-monitor/app/app-map?tabs=net) pode fazer para o seu negócio