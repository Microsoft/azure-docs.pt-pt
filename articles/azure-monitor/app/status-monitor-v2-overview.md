---
title: Descrição geral v2 do Monitor de estado do Azure | Documentos da Microsoft
description: Uma visão geral do Monitor de estado v2. Monitorizar o desempenho dos Web sites sem implementar novamente o Web site. Funciona com aplicações Web ASP.NET alojadas no local, em VMs ou no Azure.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 77c2cd9e0eac6717d91a73a6fc033dcaa2390444
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145034"
---
# <a name="status-monitor-v2"></a>Monitor de Estado v2

V2 de Monitor de estado é um módulo do PowerShell publicado para o [PowerShellGallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor) e é o substituto do [Monitor de estado](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now). Este módulo fornece sem código de instrumentação de aplicações web do .NET alojados com o IIS.
Telemetria será enviada para o portal do Azure onde pode [monitor](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) seu aplicativo.

> [!IMPORTANT]
> V2 de Monitor de estado está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="powershell-gallery"></a>Galeria do PowerShell

https://www.powershellgallery.com/packages/Az.ApplicationMonitor


## <a name="instructions"></a>Instruções
- Reveja nossa [instruções de introdução](status-monitor-v2-get-started.md) para começar, agora, com exemplos de código conciso.
- Reveja nossa [instruções detalhadas](status-monitor-v2-detailed-instructions.md) para uma descrição profunda sobre como começar.

## <a name="powershell-api-reference"></a>Referência da API do PowerShell
- [Disable-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Disable-InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Enable-InstrumentationEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)

## <a name="troubleshooting"></a>Resolução de problemas
- [Resolução de problemas](status-monitor-v2-troubleshoot.md)
- [Problemas Conhecidos](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>FAQ

- O Monitor de estado v2 oferece suporte a instalações de proxy?

  **Sim**. Tem várias opções para transferir o Monitor de estado v2. Se o seu computador tiver acesso à internet, pode carregar para a galeria do PowerShell, utilizando `-Proxy` parâmetros. Pode transferir manualmente este módulo e qualquer um, instalá-lo no seu computador ou utilizar o módulo diretamente. Cada uma destas opções está descrita no nosso [instruções detalhadas](status-monitor-v2-detailed-instructions.md).
  
- Como verificar a ativação foi concluída com êxito?

   Não temos um cmdlet para verificar que essa ativação foi bem-sucedida. Recomendamos que utilize [métricas em direto](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) para observar rapidamente se a aplicação está a enviar-na telemetria.

## <a name="next-steps"></a>Passos Seguintes

Ver a telemetria:

* [Explore as métricas](../../azure-monitor/app/metrics-explorer.md) para monitorizar o desempenho e a utilização
* [Pesquise eventos e registos](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas
* [Análise](../../azure-monitor/app/analytics.md) para obter mais informações avançadas consultas
* [Criar dashboards](../../azure-monitor/app/app-insights-dashboards.md)

Adicionar mais telemetria:

* [Criar testes web](monitor-web-app-availability.md) para se certificar de que mantém o seu site em direto.
* [Adicionar telemetria de cliente web](../../azure-monitor/app/javascript.md) para ver as exceções de código de página da web e para permitir a inserção de chamadas de rastreio.
* [Adicionar o Application Insights SDK ao seu código](../../azure-monitor/app/asp-net.md) para que possa inserir o rastreio e chamadas de registo

