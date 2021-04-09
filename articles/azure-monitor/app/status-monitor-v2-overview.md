---
title: Visão geral do agente de insights de aplicação Azure | Microsoft Docs
description: Uma visão geral do Agente de Insights de Aplicação. Monitorize o desempenho do site sem reimplantar o site. Funciona com ASP.NET aplicações web hospedadas no local, em VMs ou em Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 69f80856150e461c6edfafdf0aa89de77c4ab0fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100583817"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Implementar o Agente de Insights de Aplicação do Monitor Azure para servidores no local

> [!IMPORTANT]
> Esta orientação é recomendada para implementações em nuvem on-in e não-Azure do Application Insights Agent. Aqui está a abordagem recomendada para [a máquina virtual Azure e implementações de conjunto de escala de máquina virtual](./azure-vm-vmss-apps.md).

Application Insights Agent (anteriormente denominado Status Monitor V2) é um módulo PowerShell publicado na [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Substitui [o Status Monitor.](./monitor-performance-live-website-now.md)
A telemetria é enviada para o portal Azure, onde pode [monitorizar](./app-insights-overview.md) a sua aplicação.

> [!NOTE]
> O módulo suporta apenas atualmente a instrumentação sem código de aplicações web .NET hospedadas com IIS. Utilize um SDK para instrumentar ASP.NET aplicações Core, Java e Node.js.

## <a name="powershell-gallery"></a>Galeria do PowerShell

O Application Insights Agent está localizado aqui: https://www.powershellgallery.com/packages/Az.ApplicationMonitor .

![Galeria do PowerShell](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Instruções
- Consulte as [instruções de arranque](status-monitor-v2-get-started.md) para começar com amostras de código concisas.
- Consulte as [instruções detalhadas](status-monitor-v2-detailed-instructions.md) para um mergulho profundo sobre como começar.

## <a name="powershell-api-reference"></a>Referência API PowerShell
- [Desativação aplicaçõesInsightsMonitoring](./status-monitor-v2-api-reference.md#disable-applicationinsightsmonitoring)
- [Deficiente-InstrumentaçãoEngenhare](./status-monitor-v2-api-reference.md#disable-instrumentationengine)
- [Enable-ApplicationInsightsMonitoring](./status-monitor-v2-api-reference.md#enable-applicationinsightsmonitoring)
- [Enable-InstrumentationEngine](./status-monitor-v2-api-reference.md#enable-instrumentationengine)
- [Get-ApplicationInsightsMonitoringConfig](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringconfig)
- [Get-ApplicationInsightsMonitoringStatus](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringstatus)
- [Set-ApplicationInsightsMonitoringConfig](./status-monitor-v2-api-reference.md#set-applicationinsightsmonitoringconfig)
- [Início-AplicaçãoInsightsMonitoringTrace](./status-monitor-v2-api-reference.md#start-applicationinsightsmonitoringtrace)

## <a name="troubleshooting"></a>Resolução de problemas
- [Resolução de problemas](status-monitor-v2-troubleshoot.md)
- [Problemas conhecidos](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>FAQ

- O Application Insights Agent suporta instalações de procuração?

  *Sim*. Existem várias formas de descarregar o Application Insights Agent. Se o seu computador tiver acesso à Internet, pode embarcar na PowerShell Gallery utilizando `-Proxy` parâmetros.
Também pode descarregar manualmente o módulo e instalá-lo no seu computador ou usá-lo diretamente.
Cada uma destas opções é descrita nas [instruções detalhadas](status-monitor-v2-detailed-instructions.md).

- O Status Monitor v2 suporta ASP.NET aplicações Core?

  *Não*. Para obter instruções para permitir a monitorização das aplicações core ASP.NET, consulte [insights de aplicação para aplicações ASP.NET Core](./asp-net-core.md). Não há necessidade de instalar o StatusMonitor para uma aplicação core ASP.NET. Isto é verdade mesmo que ASP.NET aplicação Core esteja hospedada no IIS.

- Como posso verificar se a habilitação foi bem sucedida?

  - O [get-ApplicationInsightsMonitoringStatus](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringstatus) cmdlet pode ser utilizado para verificar se a ativação foi bem sucedida.
  - Recomendamos que utilize [métricas vivas](./live-stream.md) para determinar rapidamente se a sua aplicação está a enviar telemetria.

  - Também pode utilizar [o Log Analytics](../logs/log-analytics-tutorial.md) para listar todas as funções em nuvem que estão a enviar telemetria:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>Passos seguintes

Ver a telemetria:

* [Explore métricas](../essentials/metrics-charts.md) para monitorizar o desempenho e a utilização.
* [Pesquisar eventos e registos](./diagnostic-search.md) para diagnosticar problemas.
* [Use Analytics](../logs/log-query-overview.md) para consultas mais avançadas.
* [Criar painéis de instrumentos.](./overview-dashboard.md)

Adicionar mais telemetria:

* [Criar testes Web](monitor-web-app-availability.md) para se certificar de que mantém o seu site em direto.
* [Adicione telemetria ao cliente web](./javascript.md) para ver exceções do código da página web e para ativar chamadas de rastreio.
* [Adicione o SDK de Insights de Aplicação ao seu código para](./asp-net.md) que possa inserir rastreios e chamadas de registo.