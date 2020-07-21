---
title: Obtenha mais informações sobre a Azure Application Insights Microsoft Docs
description: Depois de começar com o Application Insights, aqui está um resumo das funcionalidades que pode explorar.
ms.topic: conceptual
ms.date: 02/03/2017
ms.openlocfilehash: 6d30833840b62412ae03c761baacf54c939c9d33
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86540031"
---
# <a name="more-telemetry-from-application-insights"></a>Mais telemetria de Insights de Aplicação
Depois de ter [adicionado Application Insights ao seu código ASP.NET,](../../azure-monitor/app/asp-net.md)há algumas coisas que pode fazer para obter ainda mais telemetria. 

| Ação | O que obtém|
|---|---|
|(Servidores IIS) [Instale o Monitor de Estado](https://go.microsoft.com/fwlink/?LinkId=506648) em cada máquina de servidor.<br/>(Aplicativos web Azure) No painel de controlo Azure para a aplicação web, abra a lâmina Application Insights.| [**Contadores de desempenho**](../../azure-monitor/app/performance-counters.md)<br/>[**Exceções**](asp-net-exceptions.md) - traços de pilha detalhados<br/>[**Dependências**](../../azure-monitor/app/asp-net-dependencies.md)|
|[Adicione o snippet JavaScript às suas páginas web](../../azure-monitor/app/javascript.md)|[Desempenho da página,](../../azure-monitor/app/usage-overview.md)exceções do navegador, desempenho do AJAX. Telemetria personalizada do lado do cliente.|
|[Criar testes web de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md)|Receba alertas se o seu site ficar indisponível|
|[Certifique-se de que buildinfo.config](/visualstudio/debugger/diagnose-problems-after-deployment?view=vs-2015) é gerado pela MSBuild|[Constroem anotações em gráficos métricos](./annotations.md)
|[Escreva eventos e métricas personalizadas](../../azure-monitor/app/api-custom-events-metrics.md)|Conte eventos e métricas de negócios, rastreia o uso detalhado, e muito mais.|
|[Perfilar o seu site ao vivo](https://aka.ms/AIProfilerPreview)|Timings de função detalhados da sua aplicação web ao vivo|
