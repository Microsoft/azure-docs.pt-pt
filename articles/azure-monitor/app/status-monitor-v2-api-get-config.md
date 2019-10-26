---
title: Referência da API do agente do insights Aplicativo Azure
description: Referência de API do agente Application Insights. Get-ApplicationInsightsMonitoringConfig. Monitore o desempenho do site sem reimplantar o site. Funciona com aplicativos Web ASP.NET hospedados localmente, em VMs ou no Azure.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 91e7bcbe79dfde8bbd4011748b40b692015af1c2
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899739"
---
# <a name="application-insights-agent-api-get-applicationinsightsmonitoringconfig"></a>API do agente de Application Insights: Get-ApplicationInsightsMonitoringConfig

Este artigo descreve um cmdlet que é membro do [módulo AZ. ApplicationMonitor do PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Descrição

Obtém o arquivo de configuração e imprime os valores no console.

> [!IMPORTANT] 
> Este cmdlet requer uma sessão do PowerShell com permissões de administrador.

## <a name="examples"></a>Exemplos

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>Parâmetros

Nenhum parâmetro é necessário.

## <a name="output"></a>Saída


#### <a name="example-output-from-reading-the-config-file"></a>Exemplo de saída da leitura do arquivo de configuração

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="next-steps"></a>Passos seguintes

  Ver a telemetria:
 - [Explore as métricas](../../azure-monitor/app/metrics-explorer.md) para monitorar o desempenho e o uso.
- [Pesquise eventos e logs](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas.
- Use a [análise](../../azure-monitor/app/analytics.md) para consultas mais avançadas.
- [Crie painéis](../../azure-monitor/app/overview-dashboard.md).
 
 Adicionar mais telemetria:
 - [Crie testes da Web](monitor-web-app-availability.md) para garantir que seu site permaneça ativo.
- [Adicione telemetria de cliente Web](../../azure-monitor/app/javascript.md) para ver exceções do código de página da Web e para habilitar chamadas de rastreamento.
- [Adicione o SDK do Application insights ao seu código](../../azure-monitor/app/asp-net.md) para que você possa inserir chamadas de rastreamento e log.
 
 Faça mais com Application Insights agente:
 - Use nosso guia para [solucionar problemas](status-monitor-v2-troubleshoot.md) do Application insights Agent.
 - Faça alterações na configuração usando o cmdlet [set config](status-monitor-v2-api-set-config.md) .
