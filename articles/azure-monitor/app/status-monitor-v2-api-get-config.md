---
title: 'Referência da API do Azure Status Monitor v2: Obter configuração | Microsoft Docs'
description: Referência da API do Status Monitor v2. Get-ApplicationInsightsMonitoringConfig. Monitore o desempenho do site sem reimplantar o site. Funciona com aplicativos Web ASP.NET hospedados localmente, em VMs ou no Azure.
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 1da24a93a671b388c44d96711bc2fe007f33d197
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033163"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringconfig"></a>API Status Monitor v2: Get-ApplicationInsightsMonitoringConfig

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

## <a name="output"></a>Output


#### <a name="example-output-from-reading-the-config-file"></a>Exemplo de saída da leitura do arquivo de configuração

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="next-steps"></a>Passos Seguintes

  Ver a telemetria:
 - [Explore](../../azure-monitor/app/metrics-explorer.md) as métricas para monitorar o desempenho e o uso.
- [Pesquise eventos e logs](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas.
- Use a [análise](../../azure-monitor/app/analytics.md) para consultas mais avançadas.
- [Crie painéis](../../azure-monitor/app/overview-dashboard.md).
 
 Adicionar mais telemetria:
 - [Crie testes da Web](monitor-web-app-availability.md) para garantir que seu site permaneça ativo.
- [Adicione telemetria de cliente Web](../../azure-monitor/app/javascript.md) para ver exceções do código de página da Web e para habilitar chamadas de rastreamento.
- [Adicione o SDK do Application insights ao seu código](../../azure-monitor/app/asp-net.md) para que você possa inserir chamadas de rastreamento e log.
 
 Faça mais com Status Monitor v2:
 - Use nosso guia para [solucionar problemas](status-monitor-v2-troubleshoot.md) status monitor v2.
 - Faça alterações na configuração usando o cmdlet [set config](status-monitor-v2-api-set-config.md) .
