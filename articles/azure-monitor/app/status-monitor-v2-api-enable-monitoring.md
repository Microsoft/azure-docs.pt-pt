---
title: 'Referência de API do v2 de Monitor de estado do Azure: Ativar a monitorização | Documentos da Microsoft'
description: Referência da API v2 de Monitor de estado. Enable-ApplicationInsightsMonitoring. Monitorizar o desempenho dos Web sites sem implementar novamente o Web site. Funciona com aplicações web ASP.NET alojadas no local, em VMs ou no Azure.
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
ms.openlocfilehash: e87bfad11eee5b86d35e6b4f2846b094c467e0ef
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734176"
---
# <a name="status-monitor-v2-api-enable-applicationinsightsmonitoring-v021-alpha"></a>API de v2 de Monitor de estado: Enable-ApplicationInsightsMonitoring (v0.2.1-alpha)

Este artigo descreve um cmdlet que faz parte do [módulo do Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> V2 de Monitor de estado está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é fornecido sem um contrato de nível de serviço, e não o recomendamos para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas e algumas podem ter capacidades restringidas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Descrição

Ativa sem código anexar a monitorização de aplicações do IIS num computador de destino.

Este cmdlet irá modificar o applicationHost. config do IIS e definir algumas chaves de registo.
Ele também criará um arquivo de applicationinsights.ikey.config, que define a chave de instrumentação utilizada por cada aplicação.
IIS carregará o RedfieldModule na inicialização, que será injetar o Application Insights SDK em aplicativos, como iniciar os aplicativos.
Reinicie o IIS para as alterações entrem em vigor.

Depois de ativar a monitorização, recomendamos que utilize [métricas em direto](live-stream.md) para verificar rapidamente se a aplicação está a enviar-na telemetria.


> [!NOTE] 
> - Para começar, terá de uma chave de instrumentação. Para obter mais informações, consulte [criar um recurso](create-new-resource.md#copy-the-instrumentation-key).
> - Este cmdlet requer que reveja e aceite a nossa declaração de licença e de privacidade.

> [!IMPORTANT] 
> Este cmdlet requer uma sessão do PowerShell com permissões de administrador e uma política de execução privilegiada. Para obter mais informações, consulte [executar o PowerShell como administrador com uma política de execução elevada](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).

## <a name="examples"></a>Exemplos

### <a name="example-with-a-single-instrumentation-key"></a>Exemplo com uma chave de instrumentação único
Neste exemplo, todas as aplicações no computador atual são atribuídas uma chave de instrumentação único.

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
    - 'ComputerName' corresponderá ao apenas computadores com o nome exato especificado.
- **AppFilter** é necessária C# regex do computador ou o nome da VM.
    - ". *" irá corresponder a todos
    - 'ApplicationName' corresponderá ao apenas as aplicações IIS com o nome exato especificado.
- **InstrumentationKey** é necessária para ativar a monitorização de aplicações que correspondem aos filtros de duas anteriores.
    - Deixe este valor nulo se pretender definir regras para excluir a monitorização.


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Opcional.** Utilize este parâmetro para permitir que o mecanismo de instrumentação recolher eventos e mensagens sobre o que acontece durante a execução de um processo gerenciado. Estes eventos e mensagens incluem códigos de resultado de dependência, verbos HTTP e texto do comando SQL.

O mecanismo de instrumentação adiciona sobrecarga e está desativada por predefinição.

### <a name="-acceptlicense"></a>-AcceptLicense
**Opcional.** Utilize este parâmetro para aceitar a licença e declaração de privacidade em instalações sem periféricos.

### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Utilize este parâmetro para apresentar os registos detalhados.

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
 - [Explorar métricas](../../azure-monitor/app/metrics-explorer.md) para monitorizar o desempenho e utilização.
- [Pesquise eventos e registos](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas.
- [Utilizar a análise de](../../azure-monitor/app/analytics.md) para obter mais informações avançadas consultas.
- [Criar dashboards](../../azure-monitor/app/overview-dashboard.md).
 
 Adicionar mais telemetria:
 - [Criar testes web](monitor-web-app-availability.md) para se certificar de que mantém o seu site em direto.
- [Adicionar telemetria de cliente web](../../azure-monitor/app/javascript.md) para ver as exceções de código de página da web e para ativar chamadas de rastreio.
- [Adicionar o Application Insights SDK ao seu código](../../azure-monitor/app/asp-net.md) para que possa inserir o rastreio e chamadas de registo.
 
 Faça mais com a v2 de Monitor de estado:
 - Utilize o nosso guia para [resolver problemas de](status-monitor-v2-troubleshoot.md) v2 de Monitor de estado.
 - [Obter a configuração de](status-monitor-v2-api-get-config.md) para confirmar que as suas definições foram registadas corretamente.
 - [Obter o estado](status-monitor-v2-api-get-status.md) para inspecionar a monitorização.
