---
title: 'Referência de API do v2 de Monitor de estado do Azure: Obter o Estado | Documentos da Microsoft'
description: Referência da API v2 de Monitor de estado. Get-ApplicationInsightsMonitoringStatus. Monitorizar o desempenho dos Web sites sem implementar novamente o Web site. Funciona com aplicações Web ASP.NET alojadas no local, em VMs ou no Azure.
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
ms.openlocfilehash: 860226320fe1a546798cc462e4e5c06d4b9228cf
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514313"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringstatus-v022-alpha"></a>API de v2 de Monitor de estado: Get-ApplicationInsightsMonitoringStatus (v0.2.2-alpha)

Este artigo descreve um cmdlet que faz parte do [módulo do Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> V2 de Monitor de estado está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é fornecido sem um contrato de nível de serviço, e não o recomendamos para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas e algumas podem ter capacidades restringidas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Descrição

Permite que a resolução do módulo do PowerShell que está a ser utilizado.
Este cmdlet irá comunicar informações de versão e informações sobre ficheiros de chave necessários para a monitorização.
Parâmetros adicionais fornecem relatórios adicionais sobre o estado de monitorização.

> [!IMPORTANT] 
> Este cmdlet requer uma sessão do PowerShell com permissões de administrador.

## <a name="examples"></a>Exemplos


### <a name="example-basic-information"></a>Exemplo: Informações básicas

Executar `Get-ApplicationInsightsMonitoringStatus` para apresentar informações sobre o módulo atual:

```
PS C:\> Get-ApplicationInsightsMonitoringStatus

PowerShell Module version:
0.2.2-alpha

Application Insights SDK version:
2.9.0.3872

Executing PowerShell Module Assembly:
Microsoft.ApplicationInsights.Redfield.Configurator.PowerShell, Version=2.8.14.11432, Culture=neutral, PublicKeyToken=31bf3856ad364e35

PowerShell Module Directory:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\PowerShell

Runtime Paths:
ParentDirectory: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content Exists: False
ConfigurationPath: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\applicationInsights.ikey.config Exists: True
ManagedHttpModuleHelperPath: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll Exists: True
RedfieldIISModulePath: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll Exists: True
InstrumentationEngine86Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Instrumentation32\MicrosoftInstrumentationEngine_x86.dll Exists: True
InstrumentationEngine64Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll Exists: True
InstrumentationEngineExtensionHost86Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Instrumentation32\Microsoft.ApplicationInsights.ExtensionsHost_x86.dll Exists: True
InstrumentationEngineExtensionHost64Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll Exists: True
InstrumentationEngineExtensionConfig86Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Instrumentation32\Microsoft.InstrumentationEngine.Extensions.config Exists: True
InstrumentationEngineExtensionConfig64Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Instrumentation64\Microsoft.InstrumentationEngine.Extensions.config Exists: True
ApplicationInsightsSdkPath: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Runtime\Microsoft.ApplicationInsights.dll Exists: True


Machine Identifier:
0123456789ABCDEF0123456789ABCDEF0123456789ABCDEF0123456789ABCDEF
```

### <a name="example-runtime-status"></a>Exemplo: Estado de Runtime

Pode inspecionar o processo no computador instrumentado para ver se todas as DLLs são carregadas. Se a monitorização está a funcionar, devem ser carregados, pelo menos, 12 DLLs.

Execute o comando `Get-ApplicationInsightsMonitoringStatus -InspectProcess`:


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

Por predefinição, este cmdlet irá reportar os números de versão e os caminhos de DLLs necessários para a monitorização.

Utilize esta opção se tiver de identificar a versão de qualquer DLL, incluindo o Application Insights SDK.


### <a name="-inspectprocess"></a>-InspectProcess

**Opcional**. Utilize este parâmetro para comunicar se o IIS está em execução.
Também será transferida ferramentas externas para determinar se as DLLs necessárias são carregadas para o tempo de execução do IIS.


Se este processo falhar por algum motivo, pode executar estes comandos manualmente:
- iisreset.exe /status
- [handle64.exe](https://docs.microsoft.com/sysinternals/downloads/handle) w3wp -p | findstr /I "InstrumentationEngine IA. ApplicationInsights"
- [listdlls64.exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp | findstr /I "InstrumentationEngine IA Application Insights"


### <a name="-force"></a>-Force

**Opcional**. Utilizado apenas com InspectProcess. Utilize este parâmetro para ignorar o pedido de utilizador que aparece antes de ferramentas adicionais são transferidas.


## <a name="next-steps"></a>Passos Seguintes

 Faça mais com a v2 de Monitor de estado:
 - Utilize o nosso guia para [resolver problemas de](status-monitor-v2-troubleshoot.md) v2 de Monitor de estado.
