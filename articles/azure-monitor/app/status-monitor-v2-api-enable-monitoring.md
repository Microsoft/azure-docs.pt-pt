---
title: 'Referência de API do v2 de Monitor do Estado do Azure: Ativar a monitorização | Documentos da Microsoft'
description: Estado Monitor v2 API referência Enable-ApplicationInsightsMonitoring. Monitorizar o desempenho dos Web sites sem implementar novamente o Web site. Funciona com aplicações Web ASP.NET alojadas no local, em VMs ou no Azure.
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
ms.openlocfilehash: 0a443df0c55dc916ef6d12d53811e9d9932370e7
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255905"
---
# <a name="status-monitor-v2-api-enable-applicationinsightsmonitoring-v021-alpha"></a>API de v2 de Monitor de estado: Enable-ApplicationInsightsMonitoring (v0.2.1-alpha)

Este documento descreve um cmdlet que é enviado como parte da [módulo do Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> V2 de Monitor de estado está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>Descrição

Ativar sem código anexar a monitorização de aplicações do IIS num computador de destino.
Este cmdlet irá modificar o applicationHost. config do IIS e definir algumas chaves de registo.
Este cmdlet também irá criar um applicationinsights.ikey.config, que define qual chave de instrumentação é utilizado por qual aplicativo.
IIS carregará o RedfieldModule na inicialização, que será injetar o Application Insights SDK em aplicativos como iniciar esses aplicativos.
Reinicie o IIS para as alterações entrem em vigor.

Depois de ativar a monitorização, recomendamos que utilize [métricas em direto](live-stream.md) para observar rapidamente se a aplicação está a enviar-na telemetria.


> [!NOTE] 
> Para começar, tem de ter uma chave de instrumentação. Para obter mais informações, leia [criar novo recurso](create-new-resource.md#copy-the-instrumentation-key).


> [!IMPORTANT] 
> Este cmdlet requer uma sessão do PowerShell com permissões de administrador e com uma diretiva de execução privilegiada. Leia [aqui](status-monitor-v2-detailed-instructions.md#run-powershell-as-administrator-with-an-elevated-execution-policy) para obter mais informações.

> [!NOTE] 
> Este cmdlet irá solicitar-lhe rever e aceitar a nossa declaração de licença e de privacidade.


## <a name="examples"></a>Exemplos

### <a name="example-with-single-instrumentation-key"></a>Exemplo com a chave de instrumentação único
Neste exemplo, todas as aplicações no computador atual serão atribuídas uma chave de instrumentação único.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-instrumentation-key-map"></a>Exemplo com o mapa de chave de instrumentação
Neste exemplo, 
- `MachineFilter` corresponderá a máquina atual utilizando o `'.*'` com carateres universais.
- `AppFilter='WebAppExclude'` Fornece um `null` InstrumentationKey. Esta aplicação não ser instrumentada.
- `AppFilter='WebAppOne'` irá atribuir esta aplicação específica uma chave de instrumentação exclusivo.
- `AppFilter='WebAppTwo'` também atribuir esta aplicação específica uma chave de instrumentação exclusivo.
- Por último, `AppFilter` também utiliza o `'.*'` caráter universal para corresponder a todas as outras aplicações web não correspondentes pelas regras anteriores e atribui uma chave de instrumentação do padrão.
- Adicionado para legibilidade apenas de espaços.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>Parâmetros 

### <a name="-instrumentationkey"></a>-InstrumentationKey
**É necessário.** Utilize este parâmetro para disponibilizar uma iKey único para utilização por todos os aplicativos no computador de destino.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**É necessário.** Utilize este parâmetro para disponibilizar as ikeys vários e um mapeamento de que as aplicações para utilizar a ikey. Pode criar um script de instalação única para várias máquinas, definindo o MachineFilter. 

> [!IMPORTANT] 
> Aplicativos corresponderá com regras na ordem em que elas são fornecidas. Como tal, deve especificar as regras mais específicas primeiro e último as regras mais genéricas.

#### <a name="schema"></a>Esquema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** está um regex necessário c# do nome do computador ou vm.
    - ". *" irá corresponder a todos
    - 'ComputerName' corresponderá ao apenas computadores com esse nome exato.
- **AppFilter** está um regex necessário c# do nome do computador ou vm.
    - ". *" irá corresponder a todos
    - 'ApplicationName' corresponderá ao apenas as aplicações do IIS com esse nome exato.
- **InstrumentationKey** é necessária para ativar a monitorização das aplicações que correspondem aos filtros de dois acima.
    - Deixe este valor nulo se pretender definir regras para excluir a monitorização


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Opcional.** Utilize este parâmetro para permitir que o mecanismo de instrumentação recolher eventos e mensagens do que acontece durante a execução de um processo gerenciado. Incluindo mas não limitado a códigos de resultado de dependência, verbos HTTP e texto do comando SQL. O mecanismo de instrumentação adicionará a sobrecarga adicional e está desativada por predefinição.

### <a name="-acceptlicense"></a>-AcceptLicense
**Opcional.** Utilize este parâmetro para aceitar a licença e declaração de privacidade em instalações sem periféricos.

### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Utilize este parâmetro para a saída de registos detalhados.

### <a name="-whatif"></a>-WhatIf 
**Parâmetro comum.** Utilize este parâmetro para testar e validar seus parâmetros de entrada sem, na verdade, a ativação da monitorização.

## <a name="output"></a>Saída


#### <a name="example-output-from-a-successful-enablement"></a>Saída de exemplo de uma ativação com êxito

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

## <a name="next-steps"></a>Passos Seguintes

  Ver a telemetria:
 - [Explore as métricas](../../azure-monitor/app/metrics-explorer.md) para monitorizar o desempenho e a utilização
- [Pesquise eventos e registos](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas
- [Análise](../../azure-monitor/app/analytics.md) para obter mais informações avançadas consultas
- [Criar dashboards](../../azure-monitor/app/overview-dashboard.md)
 
 Adicionar mais telemetria:
 - [Criar testes web](monitor-web-app-availability.md) para se certificar de que mantém o seu site em direto.
- [Adicionar telemetria de cliente web](../../azure-monitor/app/javascript.md) para ver as exceções de código de página da web e para permitir a inserção de chamadas de rastreio.
- [Adicionar o Application Insights SDK ao seu código](../../azure-monitor/app/asp-net.md) para que possa inserir o rastreio e chamadas de registo
 
 Faça mais com a v2 de Monitor de estado:
 - Utilize o nosso guia para [resolução de problemas](status-monitor-v2-troubleshoot.md) v2 de Monitor de estado.
 - [Obter a configuração de](status-monitor-v2-api-get-config.md) para confirmar que as suas definições foram registadas corretamente.
 - [Obter o estado](status-monitor-v2-api-get-status.md) para inspecionar a monitorização.
