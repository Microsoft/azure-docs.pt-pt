---
title: Aproveite mais o Aplicativo Azure insights | Microsoft Docs
description: Depois de começar a usar o Application Insights, aqui está um resumo dos recursos que você pode explorar.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 02/03/2017
ms.openlocfilehash: 8c51745c43ced8ad3031a6a01096261ef72b33fc
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678359"
---
# <a name="more-telemetry-from-application-insights"></a>Mais telemetria de Application Insights
Depois de [adicionar Application insights ao seu código ASP.net](../../azure-monitor/app/asp-net.md), há algumas coisas que você pode fazer para obter ainda mais telemetria. 

| Ação | O que recebe|
|---|---|
|(Servidores IIS) [Instale o status monitor](https://go.microsoft.com/fwlink/?LinkId=506648) em cada computador do servidor.<br/>(Aplicativos Web do Azure) No painel de controle do Azure para o aplicativo Web, abra a folha Application Insights.| [**Contadores de desempenho**](../../azure-monitor/app/performance-counters.md)<br/>[**Exceções**](asp-net-exceptions.md) -rastreamentos de pilha detalhados<br/>[**Depend**](../../azure-monitor/app/asp-net-dependencies.md)|
|[Adicionar o trecho de JavaScript às suas páginas da Web](../../azure-monitor/app/javascript.md)|[Desempenho da página](../../azure-monitor/app/usage-overview.md), exceções do navegador, desempenho do Ajax. Telemetria personalizada do lado do cliente.|
|[Criar testes da Web de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md)|Obter alertas se seu site ficar indisponível|
|[Certifique-se de que buildinfo. config](https://msdn.microsoft.com/library/dn449058.aspx) seja gerado pelo MSBuild|[Criar anotações em gráficos de métricas](https://docs.microsoft.com/azure/azure-monitor/app/annotations)
|[Gravar eventos e métricas personalizados](../../azure-monitor/app/api-custom-events-metrics.md)|Conte eventos de negócios e métricas, acompanhe o uso detalhado e muito mais.|
|[Perfil do seu site ativo](https://aka.ms/AIProfilerPreview)|Tempos de função detalhados de seu aplicativo Web em tempo real|






