---
title: 'Referência de API do v2 de Monitor de estado do Azure: Definir configuração | Documentos da Microsoft'
description: Referência da API v2 de Monitor de estado. Set-ApplicationInsightsMonitoringConfig. Monitorizar o desempenho dos Web sites sem implementar novamente o Web site. Funciona com aplicações web ASP.NET alojadas no local, em VMs ou no Azure.
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
ms.openlocfilehash: 562ce8a4267370be9b049e3b56f213f82deb89c0
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734994"
---
# <a name="status-monitor-v2-api-set-applicationinsightsmonitoringconfig-v021-alpha"></a>API de v2 de Monitor de estado: Set-ApplicationInsightsMonitoringConfig (v0.2.1-alpha)

Este documento descreve um cmdlet que faz parte do [módulo do Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> V2 de Monitor de estado está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é fornecido sem um contrato de nível de serviço, e não o recomendamos para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas e algumas podem ter capacidades restringidas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Descrição

Define o ficheiro de configuração sem fazer uma reinstalação completa.
Reinicie o IIS para as alterações entrem em vigor.

> [!IMPORTANT] 
> Este cmdlet requer uma sessão do PowerShell com permissões de administrador.


## <a name="examples"></a>Exemplos

### <a name="example-with-a-single-instrumentation-key"></a>Exemplo com uma chave de instrumentação único
Neste exemplo, todas as aplicações no computador atual serão atribuídas uma chave de instrumentação único.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Exemplo com um mapa de chave de instrumentação
Neste exemplo:
- `MachineFilter` corresponde ao computador atual, utilizando o `'.*'` com carateres universais.
- `AppFilter='WebAppExclude'` Fornece um `null` chave de instrumentação. A aplicação especificada não ser instrumentada.
- `AppFilter='WebAppOne'` atribui a aplicação especificada uma chave de instrumentação exclusivo.
- `AppFilter='WebAppTwo'` atribui a aplicação especificada uma chave de instrumentação exclusivo.
- Por fim, `AppFilter` também utiliza o `'.*'` caráter universal para corresponder a todas as aplicações web que não são correspondentes pelas regras anteriores e atribuir uma chave de instrumentação do padrão.
- Os espaços são adicionados para legibilidade.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>Parâmetros

### <a name="-instrumentationkey"></a>-InstrumentationKey
**É necessário.** Utilize este parâmetro para fornecer uma chave de instrumentação único para utilização por todas as aplicações no computador de destino.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**É necessário.** Utilize este parâmetro para disponibilizar várias chaves de instrumentação e um mapeamento das chaves de instrumentação utilizada por cada aplicação.
Pode criar um script de instalação única para vários computadores ao definir `MachineFilter`.

> [!IMPORTANT]
> Aplicações serão correspondência com as regras na ordem em que as regras são fornecidas. Portanto, deve especificar as regras mais específicas primeiro e último as regras mais genéricas.

#### <a name="schema"></a>Esquema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** é necessária C# regex do computador ou o nome da VM.
    - ". *" irá corresponder a todos
    - 'ComputerName' corresponderá ao apenas computadores com o nome especificado.
- **AppFilter** é necessária C# regex do computador ou o nome da VM.
    - ". *" irá corresponder a todos
    - 'ApplicationName' corresponderá ao apenas as aplicações do IIS com o nome especificado.
- **InstrumentationKey** é necessária para ativar a monitorização das aplicações que correspondem aos filtros de duas anteriores.
    - Deixe este valor nulo se pretender definir regras para excluir a monitorização.


### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Utilize este parâmetro para apresentar os registos detalhados.


## <a name="output"></a>Saída

Por predefinição, nenhuma saída.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Saída de exemplo detalhado da definição de ficheiro de configuração por meio de-InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Saída de exemplo detalhado da definição de ficheiro de configuração por meio de-InstrumentationKeyMap

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

## <a name="next-steps"></a>Passos Seguintes

  Ver a telemetria:
 - [Explorar métricas](../../azure-monitor/app/metrics-explorer.md) para monitorizar o desempenho e utilização.
- [Pesquise eventos e registos](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas.
- [Utilizar a análise de](../../azure-monitor/app/analytics.md) para obter mais informações avançadas consultas.
- [Criar dashboards](../../azure-monitor/app/overview-dashboard.md).
 
 Adicionar mais telemetria:
 - [Criar testes web](monitor-web-app-availability.md) para se certificar de que mantém o seu site em direto.
- [Adicionar telemetria de cliente web](../../azure-monitor/app/javascript.md) para ver as exceções de código de página da web e para ativar chamadas de rastreio.
- [Adicionar o Application Insights SDK ao seu código](../../azure-monitor/app/asp-net.md) para que possa inserir o rastreio e chamadas de registo
 
 Faça mais com a v2 de Monitor de estado:
 - Utilize o nosso guia para [resolver problemas de](status-monitor-v2-troubleshoot.md) v2 de Monitor de estado.
 - [Obter a configuração de](status-monitor-v2-api-get-config.md) para confirmar que as suas definições foram registadas corretamente.
 - [Obter o estado](status-monitor-v2-api-get-status.md) para inspecionar a monitorização.
