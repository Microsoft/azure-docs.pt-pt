---
title: Azure Application Insights .Net Agent API referência
description: Referência da API do Agente Insights de Aplicação Insights. Monitorize o desempenho do site sem reimplantar o website. Trabalha com ASP.NET aplicações web hospedadas no local, em VMs ou no Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 02762c4b3af735eb0b4c19aaf450b2b3a416a2be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733677"
---
# <a name="azure-monitor-application-insights-agent-api-reference"></a>Referência do Agente de Insights de Aplicação Azure Monitor API

Este artigo descreve um cmdlet que é membro do [módulo Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!NOTE] 
> - Para começar, precisa de uma chave de instrumentação. Para mais informações, consulte [Criar um recurso](create-new-resource.md#copy-the-instrumentation-key).
> - Este cmdlet requer que você reveja e aceite a nossa declaração de licença e privacidade.

> [!IMPORTANT] 
> Este cmdlet requer uma sessão powerShell com permissões de administração e uma política de execução elevada. Para mais informações, consulte [a Run PowerShell como administrador com uma política de execução elevada](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).
> - Este cmdlet requer que você reveja e aceite a nossa declaração de licença e privacidade.
> - O motor de instrumentação adiciona sobrecarga adicional e está desligado por defeito.


## <a name="enable-instrumentationengine"></a>Motor de instrumentação de ativação

Ativa o motor de instrumentação, definindo algumas teclas de registo.
Reinicie o IIS para que as alterações entrem em vigor.

O motor de instrumentação pode complementar os dados recolhidos pelos SDKs .NET.
Recolhe eventos e mensagens que descrevem a execução de um processo gerido. Estes eventos e mensagens incluem códigos de resultados de dependência, verbos HTTP e texto de [comando SQL](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query).

Ativar o motor de instrumentação se:
- Já ativou a monitorização com o cmdlet Enable, mas não ativou o motor de instrumentação.
- Você instrumentou manualmente a sua aplicação com os SDKs .NET e quer recolher telemetria adicional.

### <a name="examples"></a>Exemplos

```powershell
PS C:\> Enable-InstrumentationEngine
```

### <a name="parameters"></a>Parâmetros

#### <a name="-acceptlicense"></a>-AcceptLicense
**Opcional.** Utilize este interruptor para aceitar a declaração de licença e privacidade em instalações sem cabeça.

#### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Utilize este interruptor para registos detalhados de saída.

### <a name="output"></a>Saída


##### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Saída de exemplo de permitir com sucesso o motor de instrumentação

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="enable-applicationinsightsmonitoring"></a>Monitorização de Insights de aplicação de ativação

Permite a monitorização de anexação sem código das aplicações IIS num computador-alvo.

Este cmdlet modificará a aplicação IISHost.config e definirá algumas teclas de registo.
Também criará um ficheiro applicationinsights.ikey.config, que define a chave de instrumentação utilizada por cada aplicação.
O IIS carregará o RedfieldModule no arranque, que injetará o SDK de Insights de Aplicação nas aplicações à medida que as aplicações começarem.
Reinicie o IIS para que as suas alterações entrem em vigor.

Depois de ativar a monitorização, recomendamos que utilize [Métricas Ao Vivo](live-stream.md) para verificar rapidamente se a sua aplicação está a enviar-nos telemetria.

### <a name="examples"></a>Exemplos

#### <a name="example-with-a-single-instrumentation-key"></a>Exemplo com uma única chave de instrumentação
Neste exemplo, todas as aplicações no computador atual são atribuídas a uma única chave de instrumentação.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Exemplo com um mapa chave de instrumentação
Neste exemplo:
- `MachineFilter`corresponde ao computador atual `'.*'` utilizando o wildcard.
- `AppFilter='WebAppExclude'`fornece uma `null` chave de instrumentação. A aplicação especificada não será instrumentada.
- `AppFilter='WebAppOne'`atribui à aplicação especificada uma chave de instrumentação única.
- `AppFilter='WebAppTwo'`atribui à aplicação especificada uma chave de instrumentação única.
- Finalmente, `AppFilter` também `'.*'` usa o wildcard para combinar com todas as aplicações web que não são correspondidas às regras anteriores e atribuir uma chave de instrumentação padrão.
- Os espaços são adicionados para a legibilidade.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```


### <a name="parameters"></a>Parâmetros

#### <a name="-instrumentationkey"></a>-InstrumentaçãoChave
**Necessário.** Utilize este parâmetro para fornecer uma única chave de instrumentação para utilização por todas as aplicações no computador-alvo.

#### <a name="-instrumentationkeymap"></a>-InstrumentaçãoKeyMap
**Necessário.** Utilize este parâmetro para fornecer várias chaves de instrumentação e um mapeamento das teclas de instrumentação utilizadas por cada aplicação.
Pode criar um único script de `MachineFilter`instalação para vários computadores, definindo .

> [!IMPORTANT]
> As aplicações vão corresponder às regras no despacho que as regras são fornecidas. Por isso, deve especificar as regras mais específicas primeiro e as regras mais genéricas.

##### <a name="schema"></a>Esquema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **O Filtro de Máquina** saem-se a um regex C# obrigatório do computador ou do nome VM.
    - '.*' vai corresponder a todos
    - O 'Nome do Computador' corresponderá apenas aos computadores com o nome exato especificado.
- **AppFilter** é um C# regex necessário do Nome do Site IIS. Pode obter uma lista de sites no seu servidor executando o site [get-iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite)do comando .
    - '.*' vai corresponder a todos
    - 'SiteName' corresponderá apenas ao Site IIS com o nome exato especificado.
- **A InstrumentationKey** é necessária para permitir a monitorização de aplicações que correspondam aos dois filtros anteriores.
    - Deixe este valor nulo se quiser definir regras para excluir a monitorização.


#### <a name="-enableinstrumentationengine"></a>-Motor de Instrumentação de Habilitação
**Opcional.** Utilize este interruptor para permitir que o motor de instrumentação recolha eventos e mensagens sobre o que está a acontecer durante a execução de um processo gerido. Estes eventos e mensagens incluem códigos de resultados de dependência, verbos HTTP e texto de comando SQL.

O motor de instrumentação adiciona sobrecarga e está desligado por defeito.

#### <a name="-acceptlicense"></a>-AcceptLicense
**Opcional.** Utilize este interruptor para aceitar a declaração de licença e privacidade em instalações sem cabeça.

#### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
Quando se tem um conjunto de servidores web, pode estar a utilizar uma [configuração partilhada](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
O HttpModule não pode ser injetado nesta configuração partilhada.
Este script falhará com a mensagem de que são necessários passos de instalação extra.
Utilize este interruptor para ignorar esta verificação e continuar a instalar pré-requisitos. Para mais informações, consulte a [configuração conhecida do conflito com a iis-partilhada](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

#### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Utilize este interruptor para visualizar registos detalhados.

#### <a name="-whatif"></a>- O que se 
**Parâmetro comum.** Utilize este interruptor para testar e validar os seus parâmetros de entrada sem permitir a monitorização.

### <a name="output"></a>Saída

#### <a name="example-output-from-a-successful-enablement"></a>Saída de exemplo de uma habilitação bem sucedida

```powershell
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

## <a name="disable-instrumentationengine"></a>Motor de instrumentação para desativação

Desativa o motor de instrumentação removendo algumas teclas de registo.
Reinicie o IIS para que as alterações entrem em vigor.

### <a name="examples"></a>Exemplos

```powershell
PS C:\> Disable-InstrumentationEngine
```

### <a name="parameters"></a>Parâmetros 

#### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Utilize este interruptor para registos detalhados de saída.

### <a name="output"></a>Saída


##### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Saída de exemplo de desativar com sucesso o motor de instrumentação

```powershell
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```

## <a name="disable-applicationinsightsmonitoring"></a>Monitorização de Insights de aplicação de deficientes

Desativa a monitorização no computador-alvo.
Este cmdlet removerá as edições para a aplicação IISHost.config e removerá as teclas de registo.

### <a name="examples"></a>Exemplos

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

### <a name="parameters"></a>Parâmetros 

#### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Utilize este interruptor para visualizar registos detalhados.

### <a name="output"></a>Saída


##### <a name="example-output-from-successfully-disabling-monitoring"></a>Saída de exemplo de monitorização com sucesso

```powershell
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-00z'
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
```


## <a name="get-applicationinsightsmonitoringconfig"></a>Get-ApplicationInsightsMonitoringConfig

Obtém o ficheiro config e imprime os valores para a consola.

### <a name="examples"></a>Exemplos

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

### <a name="parameters"></a>Parâmetros

Não são necessários parâmetros.

### <a name="output"></a>Saída


##### <a name="example-output-from-reading-the-config-file"></a>Saída de exemplo da leitura do ficheiro config

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="get-applicationinsightsmonitoringstatus"></a>Estatuto de Monitorização Get-ApplicationInsights

Este cmdlet fornece informações de resolução de problemas sobre o Monitor de Estado.
Utilize este cmdlet para investigar o estado de monitorização, versão do Módulo PowerShell e para inspecionar o processo de execução.
Este cmdlet irá reportar informações e informações sobre os ficheiros-chave necessários para a monitorização.

### <a name="examples"></a>Exemplos

#### <a name="example-application-status"></a>Exemplo: Estado da aplicação

Executar o `Get-ApplicationInsightsMonitoringStatus` comando para visualizar o estado de monitorização dos web sites.

```powershell

PS C:\Windows\system32> Get-ApplicationInsightsMonitoringStatus

IIS Websites:

SiteName               : Default Web Site
ApplicationPoolName    : DefaultAppPool
SiteId                 : 1
SiteState              : Stopped

SiteName               : DemoWebApp111
ApplicationPoolName    : DemoWebApp111
SiteId                 : 2
SiteState              : Started
ProcessId              : not found

SiteName               : DemoWebApp222
ApplicationPoolName    : DemoWebApp222
SiteId                 : 3
SiteState              : Started
ProcessId              : 2024
Instrumented           : true
InstrumentationKey     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123

SiteName               : DemoWebApp333
ApplicationPoolName    : DemoWebApp333
SiteId                 : 4
SiteState              : Started
ProcessId              : 5184
AppAlreadyInstrumented : true
```

Neste exemplo;
- **O Identificador de Máquinas** é um ID anónimo usado para identificar exclusivamente o seu servidor. Se criar um pedido de suporte, precisaremos deste ID para encontrar registos para o seu servidor.
- **O Web Site padrão** está parado no IIS
- **O DemoWebApp111** foi iniciado no IIS, mas não recebeu nenhum pedido. Este relatório mostra que não há processo de execução (ProcessId: não encontrado).
- **O DemoWebApp222** está a funcionar e está a ser monitorizado (Instrumentado: verdadeiro). Com base na configuração do utilizador, a Chave de Instrumentação xxxxxxxx-xxxx-xxxx-xxxxxxxxx123 foi igualada para este site.
- **O DemoWebApp333** foi manualmente instrumentado utilizando o SDK de Insights de Aplicação. O Monitor de Estado detetou o SDK e não monitoriza este site.


#### <a name="example-powershell-module-information"></a>Exemplo: Informação do módulo PowerShell

Executar o `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` comando para mostrar informações sobre o módulo atual:

```powershell

PS C:\> Get-ApplicationInsightsMonitoringStatus -PowerShellModule

PowerShell Module version:
0.4.0-alpha

Application Insights SDK version:
2.9.0.3872

Executing PowerShell Module Assembly:
Microsoft.ApplicationInsights.Redfield.Configurator.PowerShell, Version=2.8.14.11432, Culture=neutral, PublicKeyToken=31bf3856ad364e35

PowerShell Module Directory:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\PowerShell

Runtime Paths:
ParentDirectory (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content

ConfigurationPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config

ManagedHttpModuleHelperPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll

RedfieldIISModulePath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

InstrumentationEngine86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\MicrosoftInstrumentationEngine_x86.dll

InstrumentationEngine64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll

InstrumentationEngineExtensionHost86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.ApplicationInsights.ExtensionsHost_x86.dll

InstrumentationEngineExtensionHost64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll

InstrumentationEngineExtensionConfig86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.InstrumentationEngine.Extensions.config

InstrumentationEngineExtensionConfig64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.InstrumentationEngine.Extensions.config

ApplicationInsightsSdkPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll
```

#### <a name="example-runtime-status"></a>Exemplo: Estado do tempo de execução

Pode inspecionar o processo no computador instrumentado para ver se todos os DLLs estão carregados. Se a monitorização estiver a funcionar, devem ser carregados pelo menos 12 DLLs.

Executar o `Get-ApplicationInsightsMonitoringStatus -InspectProcess`comando:


```
PS C:\> Get-ApplicationInsightsMonitoringStatus -InspectProcess

iisreset.exe /status
Status for IIS Admin Service ( IISADMIN ) : Running
Status for Windows Process Activation Service ( WAS ) : Running
Status for Net.Msmq Listener Adapter ( NetMsmqActivator ) : Running
Status for Net.Pipe Listener Adapter ( NetPipeActivator ) : Running
Status for Net.Tcp Listener Adapter ( NetTcpActivator ) : Running
Status for World Wide Web Publishing Service ( W3SVC ) : Running

handle64.exe -accepteula -p w3wp
BF0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.ServerTelemetryChannel.dll
C58: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.AzureAppServices.dll
C68: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.DependencyCollector.dll
C78: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.WindowsServer.dll
C98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Web.dll
CBC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.PerfCounterCollector.dll
DB0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Agent.Intercept.dll
B98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll
BB4: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.Contracts.dll
BCC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll
BE0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll

listdlls64.exe -accepteula w3wp
0x0000000019ac0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
0x00000000198b0000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
0x000000000c460000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
0x000000000ad60000  0x108000  C:\Windows\TEMP\2.4.0.0.Microsoft.ApplicationInsights.Extensions.Intercept_x64.dll
```

### <a name="parameters"></a>Parâmetros

#### <a name="no-parameters"></a>(Sem parâmetros)

Por predefinição, este cmdlet reportará o estado de monitorização das aplicações web.
Utilize esta opção para rever se a sua aplicação foi bem instrumentada.
Também pode rever qual a Chave de Instrumentação que foi correspondida ao seu site.


#### <a name="-powershellmodule"></a>-PowerShellModule
**Opcional.** Utilize este interruptor para relatar os números de versão e os caminhos dos DLLs necessários para a monitorização.
Utilize esta opção se precisar de identificar a versão de qualquer DLL, incluindo o SDK de Insights de Aplicação.

#### <a name="-inspectprocess"></a>-Inspeção Processo

**Opcional.** Utilize este interruptor para informar se o IIS está a funcionar.
Também irá descarregar ferramentas externas para determinar se os DLLs necessários são carregados no tempo de funcionação do IIS.


Se este processo falhar por qualquer motivo, pode executar estes comandos manualmente:
- iisreset.exe /status
- [handle64.exe](https://docs.microsoft.com/sysinternals/downloads/handle) -p w3wp ] findstr /I "InstrumentationEngine AI. ApplicationInsights"
- [listdlls64.exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp / findstr /I "InstrumentationEngine AI ApplicationInsights"


#### <a name="-force"></a>-Force

**Opcional.** Utilizado apenas com o InspectProcess. Utilize este interruptor para ignorar a solicitação do utilizador que aparece antes de serem descarregadas ferramentas adicionais.


## <a name="set-applicationinsightsmonitoringconfig"></a>Set-ApplicationInsightsMonitoringConfig

Define o ficheiro de config sem fazer uma reinstalação completa.
Reinicie o IIS para que as suas alterações entrem em vigor.

> [!IMPORTANT] 
> Este cmdlet requer uma sessão PowerShell com permissões de Administrador.


### <a name="examples"></a>Exemplos

#### <a name="example-with-a-single-instrumentation-key"></a>Exemplo com uma única chave de instrumentação
Neste exemplo, todas as aplicações no computador atual serão atribuídas uma única chave de instrumentação.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Exemplo com um mapa chave de instrumentação
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

### <a name="parameters"></a>Parâmetros

#### <a name="-instrumentationkey"></a>-InstrumentaçãoChave
**Necessário.** Utilize este parâmetro para fornecer uma única chave de instrumentação para utilização por todas as aplicações no computador-alvo.

#### <a name="-instrumentationkeymap"></a>-InstrumentaçãoKeyMap
**Necessário.** Utilize este parâmetro para fornecer várias chaves de instrumentação e um mapeamento das teclas de instrumentação utilizadas por cada aplicação.
Pode criar um único script de `MachineFilter`instalação para vários computadores, definindo .

> [!IMPORTANT]
> As aplicações vão corresponder às regras no despacho que as regras são fornecidas. Por isso, deve especificar as regras mais específicas primeiro e as regras mais genéricas.

##### <a name="schema"></a>Esquema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **O Filtro de Máquina** saem-se a um regex C# obrigatório do computador ou do nome VM.
    - '.*' vai corresponder a todos
    - O 'Nome do Computador' corresponderá apenas aos computadores com o nome especificado.
- **AppFilter** é um c# regex exigido do nome do computador ou VM.
    - '.*' vai corresponder a todos
    - 'ApplicationName' corresponderá apenas a aplicações IIS com o nome especificado.
- **A InstrumentationKey** é necessária para permitir a monitorização das aplicações que correspondam aos dois filtros anteriores.
    - Deixe este valor nulo se quiser definir regras para excluir a monitorização.


#### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Utilize este interruptor para visualizar registos detalhados.


### <a name="output"></a>Saída

Por defeito, sem saída.

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Saída verbosa de exemplo da definição do ficheiro de config através de -InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Saída verbosa de exemplo da definição do ficheiro config via -InstrumentationKeyMap

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

## <a name="start-applicationinsightsmonitoringtrace"></a>Start-ApplicationInsightsMonitoringTrace

Recolhe [eventos ETW](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) a partir do tempo de execução da anexação sem código. Este cmdlet é uma alternativa ao [perfView](https://github.com/microsoft/perfview)de execução .

Os eventos recolhidos serão impressos na consola em tempo real e guardados para um ficheiro ETL. O ficheiro ETL de saída pode ser aberto pela [PerfView](https://github.com/microsoft/perfview) para posterior investigação.

Este cmdlet funcionará até atingir a duração do tempo limite (predefinido 5 minutos) ou parar manualmente ().`Ctrl + C`

### <a name="examples"></a>Exemplos

#### <a name="how-to-collect-events"></a>Como recolher eventos

Normalmente pedimos que recolha eventos para investigar por que a sua aplicação não está a ser instrumentada.

O tempo de execução da ligação sem código emitirá eventos ETW quando o IIS começar e quando a sua aplicação começar.

Para recolher estes eventos:
1. Numa consola cmd com privilégios `iisreset /stop` de administração, execute Para desligar o IIS e todas as aplicações web.
2. Execute este cmdlet
3. Numa consola cmd com privilégios `iisreset /start` de administração, execute Para iniciar o IIS.
4. Tente navegar na sua aplicação.
5. Depois de a sua aplicação terminar de`Ctrl + C`carregar, pode detê-la manualmente ou esperar pelo intervalo.

#### <a name="what-events-to-collect"></a>Que eventos recolher

Tem três opções na recolha de eventos:
1. Utilize o `-CollectSdkEvents` interruptor para recolher eventos emitidos pelo SDK de Insights de Aplicação.
2. Utilize o `-CollectRedfieldEvents` interruptor para recolher os eventos emitidos pelo Status Monitor e pelo Tempo de Execução de Redfield. Estes registos são úteis no diagnóstico do IIS e no arranque da aplicação.
3. Utilize ambos os interruptores para recolher ambos os tipos de eventos.
4. Por predefinição, se não for especificado qualquer interruptor, ambos os tipos de eventos serão recolhidos.


### <a name="parameters"></a>Parâmetros

#### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Opcional.** Utilize este parâmetro para definir quanto tempo este script deve recolher eventos. A predefinição é 5 minutos.

#### <a name="-logdirectory"></a>-LogDirectory
**Opcional.** Utilize este interruptor para definir o diretório de saída do ficheiro ETL. Por predefinição, este ficheiro será criado no diretório de Módulos PowerShell. O caminho completo será exibido durante a execução do guião.


#### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Opcional.** Utilize este interruptor para recolher eventos SDK de Insights de Aplicação.

#### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Opcional.** Utilize este interruptor para recolher eventos do Status Monitor e do tempo de execução de Redfield.

#### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Utilize este interruptor para registos detalhados de saída.



### <a name="output"></a>Saída


#### <a name="example-of-application-startup-logs"></a>Exemplo de registos de startups de aplicação
```powershell
PS C:\Windows\system32> Start-ApplicationInsightsMonitoringTrace -ColectRedfieldEvents
Starting...
Log File: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\logs\20190627_144217_ApplicationInsights_ETW_Trace.etl
Tracing enabled, waiting for events.
Tracing will timeout in 5 minutes. Press CTRL+C to cancel.

2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 70 ms
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Current assembly 'Microsoft.ApplicationInsights.RedfieldIISModule, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3' location 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Matched filter '.*'~'STATUSMONITORTE', '.*'~'DemoWithSql'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Lightup assembly calculated path: 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Loaded applicationInsights.config from assembly's resource Microsoft.ApplicationInsights.Redfield.Lightup, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3/Microsoft.ApplicationInsights.Redfield.Lightup.ApplicationInsights-recommended.config
2:42:34 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Successfully attached ApplicationInsights SDK
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.LoadLightupAssemblyAndGetLightupHttpModuleClass, success, 2687 ms
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:34 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 3288 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 0 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 0 ms
Timeout Reached. Stopping...
```

## <a name="next-steps"></a>Passos seguintes

  Ver a telemetria:
 - [Explore as métricas](../../azure-monitor/app/metrics-explorer.md) para monitorizar o desempenho e o uso.
- [Pesquisar eventos e registos](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas.
- Use [analítica](../../azure-monitor/app/analytics.md) para consultas mais avançadas.
- [Criar dashboards](../../azure-monitor/app/overview-dashboard.md).
 
 Adicionar mais telemetria:
 - [Criar testes Web](monitor-web-app-availability.md) para se certificar de que mantém o seu site em direto.
- [Adicione telemetria](../../azure-monitor/app/javascript.md) de cliente web para ver exceções do código da página web e para ativar chamadas de rastreio.
- [Adicione o SDK de Insights de Aplicação ao seu código](../../azure-monitor/app/asp-net.md) para que possa inserir chamadas de rastreio e log.
 
 Faça mais com o Agente de Insights de Aplicação:
 - Use o nosso guia para [resolver problemas](status-monitor-v2-troubleshoot.md) no Agente insights de aplicação.






