---
title: Descrição geral v2 do Monitor de estado do Azure | Documentos da Microsoft
description: Uma visão geral do Monitor de estado v2. Monitorizar o desempenho dos Web sites sem implementar novamente o Web site. Funciona com aplicações web ASP.NET alojadas no local, em VMs ou no Azure.
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
ms.openlocfilehash: 2126408222433e6339723dc2da0d2611bb234fe8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734150"
---
# <a name="status-monitor-v2"></a>Monitor de Estado v2

V2 de Monitor de estado é um PowerShell módulo publicado para o [galeria do PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Ele substitui [Monitor de estado](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
O módulo fornece uma instrumentação sem código das aplicações do .NET web hospedados no IIS.
Telemetria é enviada para o portal do Azure, onde pode [monitor](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) seu aplicativo.

> [!IMPORTANT]
> V2 de Monitor de estado está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é fornecido sem um contrato de nível de serviço, e não o recomendamos para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas e algumas podem ter capacidades restringidas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="powershell-gallery"></a>Galeria do PowerShell

A galeria do PowerShell está localizada aqui: https://www.powershellgallery.com/packages/Az.ApplicationMonitor.


## <a name="instructions"></a>Instruções
- Consulte a [instruções de introdução](status-monitor-v2-get-started.md) para obter um início com exemplos de código conciso.
- Consulte a [instruções detalhadas](status-monitor-v2-detailed-instructions.md) para uma descrição profunda sobre como começar.

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
- [Problemas conhecidos](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>FAQ

- O Monitor de estado v2 oferece suporte a instalações de proxy?

  *Sim*. Existem várias formas para transferir o Monitor de estado v2. Se o seu computador tiver acesso à internet, pode carregar a galeria do PowerShell, utilizando `-Proxy` parâmetros.
Pode transferir manualmente o módulo e instalá-lo no seu computador ou utilizá-la diretamente.
Cada uma destas opções é descrita a [instruções detalhadas](status-monitor-v2-detailed-instructions.md).
  
- Como posso verificar se a ativação teve êxito?

   Não há nenhum cmdlet para verificar a ativação foi concluída com êxito.
Recomendamos que utilize [métricas em direto](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) para determinar rapidamente se a aplicação está a enviar telemetria.

   Também pode utilizar [do Log Analytics](../log-query/get-started-portal.md) para listar todas as funções de cloud atualmente a enviar telemetria:
   ```Kusto
   union * | summarize count() by cloud_RoleName, cloud_RoleInstance
   ```

## <a name="next-steps"></a>Passos Seguintes

Ver a telemetria:

* [Explorar métricas](../../azure-monitor/app/metrics-explorer.md) para monitorizar o desempenho e utilização.
* [Pesquise eventos e registos](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas.
* [Utilizar a análise de](../../azure-monitor/app/analytics.md) para obter mais informações avançadas consultas.
* [Criar dashboards](../../azure-monitor/app/overview-dashboard.md).

Adicionar mais telemetria:

* [Criar testes web](monitor-web-app-availability.md) para se certificar de que mantém o seu site em direto.
* [Adicionar telemetria de cliente web](../../azure-monitor/app/javascript.md) para ver as exceções de código de página da web e para ativar chamadas de rastreio.
* [Adicionar o Application Insights SDK ao seu código](../../azure-monitor/app/asp-net.md) para que possa inserir o rastreio e chamadas de registo.

