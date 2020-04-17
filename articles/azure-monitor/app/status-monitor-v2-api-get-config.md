---
title: Referência da API do Agente de Insights de Aplicação Azure
description: Referência da API do Agente Insights de Aplicação Insights. Get-ApplicationInsightsMonitoringConfig. Monitorize o desempenho do site sem reimplantar o website. Trabalha com ASP.NET aplicações web hospedadas no local, em VMs ou no Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 0cb5991b381acc9a0ec36113d0e15a7731b5074d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537496"
---
# <a name="application-insights-agent-api-get-applicationinsightsmonitoringconfig"></a>Agente de Insights de Aplicação API: Get-ApplicationInsightsMonitoringConfig

Este artigo descreve um cmdlet que é membro do [módulo Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Descrição

Obtém o ficheiro config e imprime os valores para a consola.

> [!IMPORTANT] 
> Este cmdlet requer uma sessão PowerShell com permissões de Administrador.

## <a name="examples"></a>Exemplos

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>Parâmetros

Não são necessários parâmetros.

## <a name="output"></a>Saída


#### <a name="example-output-from-reading-the-config-file"></a>Saída de exemplo da leitura do ficheiro config

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="next-steps"></a>Passos seguintes

  Ver a telemetria:
 - [Explore as métricas](../../azure-monitor/platform/metrics-charts.md) para monitorizar o desempenho e o uso.
- [Pesquisar eventos e registos](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas.
- Use [analítica](../../azure-monitor/app/analytics.md) para consultas mais avançadas.
- [Criar dashboards](../../azure-monitor/app/overview-dashboard.md).
 
 Adicionar mais telemetria:
 - [Criar testes Web](monitor-web-app-availability.md) para se certificar de que mantém o seu site em direto.
- [Adicione telemetria](../../azure-monitor/app/javascript.md) de cliente web para ver exceções do código da página web e para ativar chamadas de rastreio.
- [Adicione o SDK de Insights de Aplicação ao seu código](../../azure-monitor/app/asp-net.md) para que possa inserir chamadas de rastreio e log.
 
 Faça mais com o Agente de Insights de Aplicação:
 - Use o nosso guia para [resolver problemas](status-monitor-v2-troubleshoot.md) no Agente insights de aplicação.
 - Faça alterações na config utilizando o conjunto de [config](status-monitor-v2-api-set-config.md) cmdlet.
