---
title: Visão geral do Agente de Insights de Aplicação Azure / Microsoft Docs
description: Uma visão geral do Agente de Insights de Aplicação. Monitorize o desempenho do site sem reimplantar o site. Funciona com ASP.NET aplicações web hospedadas no local, em VMs ou em Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 626a8c96fdc247db174e2cbcd2e5c99cec43d2ea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81770969"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Implementar o Agente de Insights de Aplicação do Monitor Azure para servidores no local

> [!IMPORTANT]
> Esta orientação é recomendada para implementações em nuvem on-in e não-Azure do Application Insights Agent. Aqui está a abordagem recomendada para [a máquina virtual Azure e implementações de conjunto de escala de máquina virtual](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps).

Application Insights Agent (anteriormente denominado Status Monitor V2) é um módulo PowerShell publicado na [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Substitui [o Status Monitor.](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now)
A telemetria é enviada para o portal Azure, onde pode [monitorizar](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) a sua aplicação.

> [!NOTE]
> O módulo suporta apenas atualmente a instrumentação sem código de aplicações web .NET hospedadas com IIS. Utilize um SDK para instrumentar ASP.NET aplicações Core, Java e Node.js.

## <a name="powershell-gallery"></a>Galeria do PowerShell

O Application Insights Agent está localizado aqui: https://www.powershellgallery.com/packages/Az.ApplicationMonitor .

![Galeria do PowerShell](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Instruções
- Consulte as [instruções de arranque](status-monitor-v2-get-started.md) para começar com amostras de código concisas.
- Consulte as [instruções detalhadas](status-monitor-v2-detailed-instructions.md) para um mergulho profundo sobre como começar.

## <a name="powershell-api-reference"></a>Referência API PowerShell
- [Desativação aplicaçõesInsightsMonitoring](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#disable-applicationinsightsmonitoring)
- [Deficiente-InstrumentaçãoEngenhare](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#disable-instrumentationengine)
- [Enable-ApplicationInsightsMonitoring](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#enable-applicationinsightsmonitoring)
- [Enable-InstrumentationEngine](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#enable-instrumentationengine)
- [Get-ApplicationInsightsMonitoringConfig](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#get-applicationinsightsmonitoringconfig)
- [Get-ApplicationInsightsMonitoringStatus](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#get-applicationinsightsmonitoringstatus)
- [Set-ApplicationInsightsMonitoringConfig](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#set-applicationinsightsmonitoringconfig)
- [Início-AplicaçãoInsightsMonitoringTrace](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#start-applicationinsightsmonitoringtrace)

## <a name="troubleshooting"></a>Resolução de problemas
- [Resolução de problemas](status-monitor-v2-troubleshoot.md)
- [Problemas conhecidos](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>FAQ

- O Application Insights Agent suporta instalações de procuração?

  *Sim, é um*trabalho que sim. Existem várias formas de descarregar o Application Insights Agent. Se o seu computador tiver acesso à Internet, pode embarcar na PowerShell Gallery utilizando `-Proxy` parâmetros.
Também pode descarregar manualmente o módulo e instalá-lo no seu computador ou usá-lo diretamente.
Cada uma destas opções é descrita nas [instruções detalhadas](status-monitor-v2-detailed-instructions.md).

- O Status Monitor v2 suporta ASP.NET aplicações Core?

  *Não, não, não,* não. Para obter instruções para permitir a monitorização das aplicações core ASP.NET, consulte [insights de aplicação para aplicações ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core). Não há necessidade de instalar o StatusMonitor para uma aplicação core ASP.NET. Isto é verdade mesmo que ASP.NET aplicação Core esteja hospedada no IIS.

- Como posso verificar se a habilitação foi bem sucedida?

  - O [get-ApplicationInsightsMonitoringStatus](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#get-applicationinsightsmonitoringstatus) cmdlet pode ser utilizado para verificar se a ativação foi bem sucedida.
  - Recomendamos que utilize [métricas vivas](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) para determinar rapidamente se a sua aplicação está a enviar telemetria.

  - Também pode utilizar [o Log Analytics](../log-query/get-started-portal.md) para listar todas as funções em nuvem que estão a enviar telemetria:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>Próximos passos

Ver a telemetria:

* [Explore métricas](../../azure-monitor/platform/metrics-charts.md) para monitorizar o desempenho e a utilização.
* [Pesquisar eventos e registos](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas.
* [Use Analytics](../../azure-monitor/app/analytics.md) para consultas mais avançadas.
* [Criar painéis de instrumentos.](../../azure-monitor/app/overview-dashboard.md)

Adicionar mais telemetria:

* [Criar testes Web](monitor-web-app-availability.md) para se certificar de que mantém o seu site em direto.
* [Adicione telemetria ao cliente web](../../azure-monitor/app/javascript.md) para ver exceções do código da página web e para ativar chamadas de rastreio.
* [Adicione o SDK de Insights de Aplicação ao seu código para](../../azure-monitor/app/asp-net.md) que possa inserir rastreios e chamadas de registo.

