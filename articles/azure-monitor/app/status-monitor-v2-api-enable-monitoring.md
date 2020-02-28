---
title: Referência da API do Agente de Insights de Aplicação Azure
description: Referência da API do Agente Insights de Aplicação Insights. Enable-ApplicationInsightsMonitoring. Monitorize o desempenho do site sem reimplantar o website. Trabalha com ASP.NET aplicações web hospedadas no local, em VMs ou no Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 8bbdc96a49fffc91f80d24a9eb0926766f86ee16
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671312"
---
# <a name="application-insights-agent-api-enable-applicationinsightsmonitoring"></a>Agente de Insights de Aplicação API: Monitorização de Insights de aplicação de ativação

Este artigo descreve um cmdlet que é membro do [módulo Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Descrição

Permite a monitorização de anexação sem código das aplicações IIS num computador-alvo.

Este cmdlet modificará a aplicação IISHost.config e definirá algumas teclas de registo.
Também criará um ficheiro applicationinsights.ikey.config, que define a chave de instrumentação utilizada por cada aplicação.
O IIS carregará o RedfieldModule no arranque, que injetará o SDK de Insights de Aplicação nas aplicações à medida que as aplicações começarem.
Reinicie o IIS para que as suas alterações entrem em vigor.

Depois de ativar a monitorização, recomendamos que utilize [Métricas Ao Vivo](live-stream.md) para verificar rapidamente se a sua aplicação está a enviar-nos telemetria.


> [!NOTE] 
> - Para começar, precisa de uma chave de instrumentação. Para mais informações, consulte [Criar um recurso](create-new-resource.md#copy-the-instrumentation-key).
> - Este cmdlet requer que você reveja e aceite a nossa declaração de licença e privacidade.

> [!IMPORTANT] 
> Este cmdlet requer uma sessão powerShell com permissões de administração e uma política de execução elevada. Para mais informações, consulte [a Run PowerShell como administrador com uma política de execução elevada](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).

## <a name="examples"></a>Exemplos

### <a name="example-with-a-single-instrumentation-key"></a>Exemplo com uma única chave de instrumentação
Neste exemplo, todas as aplicações no computador atual são atribuídas a uma única chave de instrumentação.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Exemplo com um mapa chave de instrumentação
Neste exemplo:
- `MachineFilter` corresponde ao computador atual utilizando o wildcard `'.*'`.
- `AppFilter='WebAppExclude'` fornece uma chave de instrumentação `null`. A aplicação especificada não será instrumentada.
- `AppFilter='WebAppOne'` atribui à aplicação especificada uma chave de instrumentação única.
- `AppFilter='WebAppTwo'` atribui à aplicação especificada uma chave de instrumentação única.
- Finalmente, `AppFilter` também usa o wildcard `'.*'` para combinar com todas as aplicações web que não são correspondidas pelas regras anteriores e atribuir uma chave de instrumentação padrão.
- Os espaços são adicionados para a legibilidade.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```


## <a name="parameters"></a>Parâmetros

### <a name="-instrumentationkey"></a>-InstrumentaçãoChave
**Necessário.** Utilize este parâmetro para fornecer uma única chave de instrumentação para utilização por todas as aplicações no computador-alvo.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Necessário.** Utilize este parâmetro para fornecer várias chaves de instrumentação e um mapeamento das teclas de instrumentação utilizadas por cada aplicação.
Pode criar um único script de instalação para vários computadores, definindo `MachineFilter`.

> [!IMPORTANT]
> As aplicações vão corresponder às regras no despacho que as regras são fornecidas. Por isso, deve especificar as regras mais específicas primeiro e as regras mais genéricas.

#### <a name="schema"></a>Esquema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **O Filtro do** C# Máquina é um regex obrigatório do computador ou nome VM.
    - '.*' vai corresponder a todos
    - O 'Nome do Computador' corresponderá apenas aos computadores com o nome exato especificado.
- **O AppFilter** é C# um regex exigido do Nome do Site IIS. Pode obter uma lista de sites no seu servidor executando o site [get-iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite)do comando .
    - '.*' vai corresponder a todos
    - 'SiteName' corresponderá apenas ao Site IIS com o nome exato especificado.
- **A InstrumentationKey** é necessária para permitir a monitorização de aplicações que correspondam aos dois filtros anteriores.
    - Deixe este valor nulo se quiser definir regras para excluir a monitorização.


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Opcional.** Utilize este interruptor para permitir que o motor de instrumentação recolha eventos e mensagens sobre o que está a acontecer durante a execução de um processo gerido. Estes eventos e mensagens incluem códigos de resultados de dependência, verbos HTTP e texto de comando SQL.

O motor de instrumentação adiciona sobrecarga e está desligado por defeito.

### <a name="-acceptlicense"></a>-AcceptLicense
**Opcional.** Utilize este interruptor para aceitar a declaração de licença e privacidade em instalações sem cabeça.

### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
Quando se tem um conjunto de servidores web, pode estar a utilizar uma [configuração partilhada](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
O HttpModule não pode ser injetado nesta configuração partilhada.
Este script falhará com a mensagem de que são necessários passos de instalação extra.
Utilize este interruptor para ignorar esta verificação e continuar a instalar pré-requisitos. Para mais informações, consulte a [configuração conhecida do conflito com a iis-partilhada](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Utilize este interruptor para visualizar registos detalhados.

### <a name="-whatif"></a>-WhatIf 
**Parâmetro comum.** Utilize este interruptor para testar e validar os seus parâmetros de entrada sem permitir a monitorização.

## <a name="output"></a>Saída


#### <a name="example-output-from-a-successful-enablement"></a>Saída de exemplo de uma habilitação bem sucedida

```
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
Installing GAC module 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.0\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll'
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
Found GAC module Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z_1'
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'enable'
Configuring IIS Environment for codeless attach...
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
Updating app pool permissions...
Successfully enabled Application Insights Status Monitor
```

## <a name="next-steps"></a>Passos seguintes

  Ver a telemetria:
 - [Explore as métricas](../../azure-monitor/app/metrics-explorer.md) para monitorizar o desempenho e o uso.
- [Pesquisar eventos e registos](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas.
- [Use o Analytics](../../azure-monitor/app/analytics.md) para consultas mais avançadas.
- [Criar dashboards](../../azure-monitor/app/overview-dashboard.md).
 
 Adicionar mais telemetria:
 - [Crie testes web](monitor-web-app-availability.md) para garantir que o seu site permaneça ao vivo.
- [Adicione telemetria](../../azure-monitor/app/javascript.md) de cliente web para ver exceções do código da página web e para ativar chamadas de rastreio.
- [Adicione o SDK de Insights de Aplicação ao seu código](../../azure-monitor/app/asp-net.md) para que possa inserir chamadas de rastreio e log.
 
 Faça mais com o Agente de Insights de Aplicação:
 - Use o nosso guia para [resolver problemas](status-monitor-v2-troubleshoot.md) no Agente insights de aplicação.
 - [Faça com que o config](status-monitor-v2-api-get-config.md) confirme que as suas definições foram gravadas corretamente.
 - [Obtenha o estado](status-monitor-v2-api-get-status.md) para inspecionar a monitorização.
