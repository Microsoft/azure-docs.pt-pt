---
title: Obtenha mais informações sobre a aplicação do Azure Insights  Microsoft Docs
description: Depois de começar com application Insights, aqui está um resumo das funcionalidades que você pode explorar.
ms.topic: conceptual
ms.date: 02/03/2017
ms.openlocfilehash: 3c4a9a736f34d4f7dbfeb004d0837f1f2efa55dc
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77666029"
---
# <a name="more-telemetry-from-application-insights"></a>Mais telemetria de Insights de Aplicação
Depois de ter [adicionado insights de aplicação ao seu código ASP.NET,](../../azure-monitor/app/asp-net.md)há algumas coisas que pode fazer para obter ainda mais telemetria. 

| Ação | O que obtém|
|---|---|
|(Servidores IIS) [Instale](https://go.microsoft.com/fwlink/?LinkId=506648) o Monitor de Estado em cada máquina do servidor.<br/>(Aplicações web Azure) No painel de controlo Azure para a aplicação web, abra a lâmina De insights de aplicação.| [**Contadores de desempenho**](../../azure-monitor/app/performance-counters.md)<br/>[**Exceções**](asp-net-exceptions.md) - vestígios de pilhas detalhados<br/>[**Dependências**](../../azure-monitor/app/asp-net-dependencies.md)|
|[Adicione o snippet JavaScript às suas páginas web](../../azure-monitor/app/javascript.md)|[Desempenho da página,](../../azure-monitor/app/usage-overview.md)exceções ao navegador, desempenho DO AJAX. Telemetria personalizada do lado do cliente.|
|[Criar testes web de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md)|Receba alertas se o seu site ficar indisponível|
|[Garantir buildinfo.config](https://msdn.microsoft.com/library/dn449058.aspx) é gerado pela MSBuild|[Construir anotações em gráficos métricos](https://docs.microsoft.com/azure/azure-monitor/app/annotations)
|[Escreva eventos e métricas personalizados](../../azure-monitor/app/api-custom-events-metrics.md)|Conte eventos e métricas de negócios, rastreie o uso detalhado, e muito mais.|
|[Perfilie o seu site ao vivo](https://aka.ms/AIProfilerPreview)|Timings de funções detalhados da sua aplicação web ao vivo|






