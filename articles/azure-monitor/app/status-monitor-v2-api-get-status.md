---
title: Referência da API do Agente de Insights de Aplicação Azure
description: Referência da API do Agente Insights de Aplicação Insights. Get-ApplicationInsightsMonitoringStatus. Monitorize o desempenho do site sem reimplantar o website. Funciona com aplicações Web ASP.NET alojadas no local, em VMs ou no Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 159dab4a228c822ef62c45c9ccceff638a9bea45
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671261"
---
# <a name="application-insights-agent-api-get-applicationinsightsmonitoringstatus"></a>Agente de Insights de Aplicação API: Get-ApplicationInsightsMonitoringStatus

Este artigo descreve um cmdlet que é membro do [módulo Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Descrição

Este cmdlet fornece informações de resolução de problemas sobre o Monitor de Estado.
Utilize este cmdlet para investigar o estado de monitorização, versão do Módulo PowerShell e para inspecionar o processo de execução.
Este cmdlet irá reportar informações e informações sobre os ficheiros-chave necessários para a monitorização.

> [!IMPORTANT] 
> Este cmdlet requer uma sessão PowerShell com permissões de Administrador.

## <a name="examples"></a>Exemplos

### <a name="example-application-status"></a>Exemplo: Estado da aplicação

Executar o comando `Get-ApplicationInsightsMonitoringStatus` para visualizar o estado de monitorização dos web sites.

```
PS C:\Windows\system32> Get-ApplicationInsightsMonitoringStatus
Machine Identifier:
811D43F7EC807E389FEA2E732381288ACCD70AFFF9F569559AC3A75F023FA639

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


### <a name="example-powershell-module-information"></a>Exemplo: Informação do módulo PowerShell

Executar o comando `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` para mostrar informações sobre o módulo atual:

```
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

### <a name="example-runtime-status"></a>Exemplo: Estado do tempo de execução

Pode inspecionar o processo no computador instrumentado para ver se todos os DLLs estão carregados. Se a monitorização estiver a funcionar, devem ser carregados pelo menos 12 DLLs.

Executar o comando `Get-ApplicationInsightsMonitoringStatus -InspectProcess`:


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

## <a name="parameters"></a>Parâmetros

### <a name="no-parameters"></a>(Sem parâmetros)

Por predefinição, este cmdlet reportará o estado de monitorização das aplicações web.
Utilize esta opção para rever se a sua aplicação foi bem instrumentada.
Também pode rever qual a Chave de Instrumentação que foi correspondida ao seu site.


### <a name="-powershellmodule"></a>-PowerShellModule
**Opcional**. Utilize este interruptor para relatar os números de versão e os caminhos dos DLLs necessários para a monitorização.
Utilize esta opção se precisar de identificar a versão de qualquer DLL, incluindo o SDK de Insights de Aplicação.

### <a name="-inspectprocess"></a>-InspectProcess

**Opcional**. Utilize este interruptor para informar se o IIS está a funcionar.
Também irá descarregar ferramentas externas para determinar se os DLLs necessários são carregados no tempo de funcionação do IIS.


Se este processo falhar por qualquer motivo, pode executar estes comandos manualmente:
- iisreset.exe /status
- [handle64.exe](https://docs.microsoft.com/sysinternals/downloads/handle) -p w3wp  findstr /I "InstrumentationEngine AI. ApplicationInsights"
- [listdlls64.exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp / findstr /I "InstrumentationEngine AI ApplicationInsights"


### <a name="-force"></a>-Force

**Opcional**. Utilizado apenas com o InspectProcess. Utilize este interruptor para ignorar a solicitação do utilizador que aparece antes de serem descarregadas ferramentas adicionais.


## <a name="next-steps"></a>Passos seguintes

 Faça mais com o Agente de Insights de Aplicação:
 - Use o nosso guia para [resolver problemas](status-monitor-v2-troubleshoot.md) no Agente insights de aplicação.
