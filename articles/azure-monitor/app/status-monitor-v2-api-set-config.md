---
title: Referência da API do Agente de Insights de Aplicação Azure
description: Referência da API do Agente Insights de Aplicação Insights. Set-ApplicationInsightsMonitoringConfig. Monitorize o desempenho do site sem reimplantar o website. Trabalha com ASP.NET aplicações web hospedadas no local, em VMs ou no Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 1226b3e10adf786ed3335844a5d3f4e530911705
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671244"
---
# <a name="application-insights-agent-api-set-applicationinsightsmonitoringconfig"></a>Agente de Insights de Aplicação API: Set-ApplicationInsightsMonitoringConfig

Este documento descreve um cmdlet que é membro do [módulo Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Descrição

Define o ficheiro de config sem fazer uma reinstalação completa.
Reinicie o IIS para que as suas alterações entrem em vigor.

> [!IMPORTANT] 
> Este cmdlet requer uma sessão PowerShell com permissões de Administrador.


## <a name="examples"></a>Exemplos

### <a name="example-with-a-single-instrumentation-key"></a>Exemplo com uma única chave de instrumentação
Neste exemplo, todas as aplicações no computador atual serão atribuídas uma única chave de instrumentação.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Exemplo com um mapa chave de instrumentação
Neste exemplo:
- `MachineFilter`corresponde ao computador atual `'.*'` utilizando o wildcard.
- `AppFilter='WebAppExclude'`fornece uma `null` chave de instrumentação. A aplicação especificada não será instrumentada.
- `AppFilter='WebAppOne'`atribui à aplicação especificada uma chave de instrumentação única.
- `AppFilter='WebAppTwo'`atribui à aplicação especificada uma chave de instrumentação única.
- Finalmente, `AppFilter` também `'.*'` usa o wildcard para combinar com todas as aplicações web que não são correspondidas às regras anteriores e atribuir uma chave de instrumentação padrão.
- Os espaços são adicionados para a legibilidade.

```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap `
       @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
          @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
          @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
          @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})
```

## <a name="parameters"></a>Parâmetros

### <a name="-instrumentationkey"></a>-InstrumentaçãoChave
**Necessário.** Utilize este parâmetro para fornecer uma única chave de instrumentação para utilização por todas as aplicações no computador-alvo.

### <a name="-instrumentationkeymap"></a>-InstrumentaçãoKeyMap
**Necessário.** Utilize este parâmetro para fornecer várias chaves de instrumentação e um mapeamento das teclas de instrumentação utilizadas por cada aplicação.
Pode criar um único script de `MachineFilter`instalação para vários computadores, definindo .

> [!IMPORTANT]
> As aplicações vão corresponder às regras no despacho que as regras são fornecidas. Por isso, deve especificar as regras mais específicas primeiro e as regras mais genéricas.

#### <a name="schema"></a>Esquema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **O Filtro de Máquina** saem-se a um regex C# obrigatório do computador ou do nome VM.
    - '.*' vai corresponder a todos
    - O 'Nome do Computador' corresponderá apenas aos computadores com o nome especificado.
- **AppFilter** é um c# regex exigido do nome do computador ou VM.
    - '.*' vai corresponder a todos
    - 'ApplicationName' corresponderá apenas a aplicações IIS com o nome especificado.
- **A InstrumentationKey** é necessária para permitir a monitorização das aplicações que correspondam aos dois filtros anteriores.
    - Deixe este valor nulo se quiser definir regras para excluir a monitorização.


### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Utilize este interruptor para visualizar registos detalhados.


## <a name="output"></a>Saída

Por defeito, sem saída.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Saída verbosa de exemplo da definição do ficheiro de config através de -InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Saída verbosa de exemplo da definição do ficheiro config via -InstrumentationKeyMap

```
VERBOSE: Operation: InstallWithIkeyMap
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

## <a name="next-steps"></a>Passos seguintes

  Ver a telemetria:
 - [Explore as métricas](../../azure-monitor/app/metrics-explorer.md) para monitorizar o desempenho e o uso.
- [Pesquisar eventos e registos](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas.
- [Use o Analytics](../../azure-monitor/app/analytics.md) para consultas mais avançadas.
- [Criar dashboards](../../azure-monitor/app/overview-dashboard.md).
 
 Adicionar mais telemetria:
 - [Criar testes Web](monitor-web-app-availability.md) para se certificar de que mantém o seu site em direto.
- [Adicione telemetria](../../azure-monitor/app/javascript.md) de cliente web para ver exceções do código da página web e para ativar chamadas de rastreio.
- [Adicione o SDK](../../azure-monitor/app/asp-net.md) de Insights de Aplicação ao seu código para que possa inserir chamadas de rastreio e log
 
 Faça mais com o Agente de Insights de Aplicação:
 - Use o nosso guia para [resolver problemas](status-monitor-v2-troubleshoot.md) no Agente insights de aplicação.
 - [Faça com que o config](status-monitor-v2-api-get-config.md) confirme que as suas definições foram gravadas corretamente.
 - [Obtenha o estado](status-monitor-v2-api-get-status.md) para inspecionar a monitorização.
