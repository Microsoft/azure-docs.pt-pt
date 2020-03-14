---
title: Visão geral do Agente de Insights de Aplicação Azure  Microsoft Docs
description: Uma visão geral do Agente de Insights de Aplicação. Monitorize o desempenho do site sem reimplantar o website. Trabalha com ASP.NET aplicações web hospedadas no local, em VMs ou no Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 4a240bc62816a46bc37108777a8b081b74047738
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275714"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Implementar o Agente de Insights de Aplicação do Monitor Azure para servidores no local

> [!IMPORTANT]
> Esta orientação é recomendada para as implementações em nuvem não-Azure do Agente de Insights de Aplicação. Aqui está a abordagem recomendada para [a máquina virtual Azure e implementações de conjuntos de máquinas virtuais.](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps)

Application Insights Agent (anteriormente chamado Status Monitor V2) é um módulo PowerShell publicado na [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Substitui o Monitor de [Estado](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
A telemetria é enviada para o portal Azure, onde pode [monitorizar](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) a sua aplicação.

> [!NOTE]
> O módulo apenas suporta atualmente uma instrumentação sem código de aplicações web .NET alojadas com o IIS. Utilize um SDK para instrumentos ASP.NET aplicações Core, Java e Node.js.

## <a name="powershell-gallery"></a>Galeria do PowerShell

O Agente insights de aplicação está localizado aqui: https://www.powershellgallery.com/packages/Az.ApplicationMonitor.

![Galeria do PowerShell](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Instruções
- Veja as [instruções de início](status-monitor-v2-get-started.md) para começar com amostras de código conciso.
- Consulte as [instruções detalhadas](status-monitor-v2-detailed-instructions.md) para um mergulho profundo sobre como começar.

## <a name="powershell-api-reference"></a>Referência powerShell API
- [Monitorização de Insights de aplicação de deficientes](status-monitor-v2-api-disable-monitoring.md)
- [Motor de instrumentação para desativação](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Monitorização de Insights de aplicação de ativação](status-monitor-v2-api-enable-monitoring.md)
- [Motor de instrumentação de ativação](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Estatuto de Monitorização Get-ApplicationInsights](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)
- [Start-ApplicationInsightsMonitoringTrace](status-monitor-v2-api-start-trace.md)

## <a name="troubleshooting"></a>Resolução de Problemas
- [Resolução de problemas](status-monitor-v2-troubleshoot.md)
- [Problemas conhecidos](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>FAQ

- O Agente de Insights de Aplicação suporta instalações por procuração?

  *Sim, é*um pouco. Existem várias formas de descarregar o Agente de Insights de Aplicações. Se o seu computador tiver acesso à Internet, pode embarcar na PowerShell Gallery utilizando `-Proxy` parâmetros.
Também pode descarregar manualmente o módulo e instalá-lo no computador ou utilizá-lo diretamente.
Cada uma destas opções é descrita nas [instruções detalhadas](status-monitor-v2-detailed-instructions.md).

- O Monitor de Estado v2 suporta ASP.NET aplicações Core?

  *Não, não, não, não.* Para obter instruções que permitam a monitorização das aplicações core ASP.NET, consulte os Insights de [aplicação para aplicações ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core). Não há necessidade de instalar o StatusMonitor para uma aplicação ASP.NET Core. Isto é verdade mesmo que ASP.NET aplicação Core esteja alojada no IIS.

- Como posso verificar se a habilitação foi bem sucedida?

  - O [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md) cmdlet pode ser usado para verificar se a habilitação foi bem sucedida.
  - Recomendamos que utilize [Métricas Ao Vivo](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) para determinar rapidamente se a sua aplicação está a enviar telemetria.

  - Também pode utilizar [o Log Analytics](../log-query/get-started-portal.md) para listar todas as funções na nuvem que atualmente enviam telemetria:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>Passos Seguintes

Ver a telemetria:

* [Explore as métricas](../../azure-monitor/app/metrics-explorer.md) para monitorizar o desempenho e o uso.
* [Pesquisar eventos e registos](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas.
* [Use o Analytics](../../azure-monitor/app/analytics.md) para consultas mais avançadas.
* [Criar dashboards](../../azure-monitor/app/overview-dashboard.md).

Adicionar mais telemetria:

* [Crie testes web](monitor-web-app-availability.md) para garantir que o seu site permaneça ao vivo.
* [Adicione telemetria](../../azure-monitor/app/javascript.md) de cliente web para ver exceções do código da página web e para ativar chamadas de rastreio.
* [Adicione o SDK de Insights de Aplicação ao seu código](../../azure-monitor/app/asp-net.md) para que possa inserir chamadas de rastreio e log.

