---
title: Monitor aplicações no Serviço Azure Kubernetes (AKS) com Insights de Aplicações - Azure Monitor / Microsoft Docs
description: O Azure Monitor integra-se perfeitamente com a sua aplicação em execução em Kubernetes, e permite-lhe detetar os problemas com as suas apps num instante.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/13/2020
ms.openlocfilehash: 52bd6d2a98e5126ff2463de1ef99da03ca555567
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87075304"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes---azure-monitor-application-insights"></a>Monitorização de aplicações de instrumentação zero para Kubernetes - Azure Monitor Application Insights

> [!IMPORTANT]
>  Atualmente pode ativar a monitorização das suas aplicações Java em execução em Kubernetes sem instrumentar o seu código - utilize o [agente autónomo Java](./java-in-process-agent.md). Enquanto a solução para permitir a monitorização perfeita da aplicação está em funcionamento para outras línguas, utilize os SDKs para monitorizar as suas aplicações em execução em AKS: [ASP.NET Core](./asp-net-core.md), [ASP.NET](./asp-net.md), [Node.js, ](./nodejs.md) [JavaScript](./javascript.md)e [Python.](./opencensus-python.md)

## <a name="application-monitoring-without-instrumenting-the-code"></a>Monitorização de aplicações sem instrumentar o código
Atualmente, apenas a Java permite ativar a monitorização da aplicação sem instrumentar o código. Para monitorizar aplicações em outros idiomas, utilize os SDKs. 

## <a name="java"></a>Java
Uma vez ativado, o agente Java recolherá automaticamente uma infinidade de pedidos, dependências, registos e métricas das bibliotecas e quadros mais utilizados.

Siga [as instruções detalhadas](./java-in-process-agent.md) para monitorizar as suas aplicações Java em execução em aplicações Kubernetes, bem como em outros ambientes. 

## <a name="other-languages"></a>Outras linguagens

Para as aplicações em outras línguas recomendamos atualmente a utilização dos SDKs:
* [ASP.NET Core](./asp-net-core.md)
* [ASP.NET](./asp-net.md)
* [Node.js](./nodejs.md) 
* [JavaScript](./javascript.md)
* [Python](./opencensus-python.md)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o Azure Monitor](../overview.md) e [Application Insights](./app-insights-overview.md)
* Obtenha uma visão geral do [Rastreio Distribuído](./distributed-tracing.md) e veja o que o [Mapa de Aplicações](./app-map.md?tabs=net) pode fazer para o seu negócio
