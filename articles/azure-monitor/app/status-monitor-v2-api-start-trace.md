---
title: Referência da API do Agente de Insights de Aplicação Azure
description: Referência da API do Agente Insights de Aplicação Insights. Início de rastreio. Recolher registos de ETW do Status Monitor e dos Insights de Aplicação SDK.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: b9680101f1a22dd6d9c1617c8afc13a10ad1c594
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671227"
---
# <a name="application-insights-agent-api-start-applicationinsightsmonitoringtrace"></a>Agente de Insights de Aplicação API: Start-ApplicationInsightsMonitoringTrace

Este artigo descreve um cmdlet que é membro do [módulo Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Descrição

Recolhe [eventos ETW](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) a partir do tempo de execução da anexação sem código. Este cmdlet é uma alternativa ao [perfView](https://github.com/microsoft/perfview)de execução .

Os eventos recolhidos serão impressos na consola em tempo real e guardados para um ficheiro ETL. O ficheiro ETL de saída pode ser aberto pela [PerfView](https://github.com/microsoft/perfview) para posterior investigação.

Este cmdlet funcionará até atingir a duração do tempo limite (predefinido 5 minutos) ou for interrompido manualmente (`Ctrl + C`).

> [!IMPORTANT] 
> Este cmdlet requer uma sessão PowerShell com permissões de Administrador.

## <a name="examples"></a>Exemplos

### <a name="how-to-collect-events"></a>Como recolher eventos

Normalmente pedimos que recolha eventos para investigar por que a sua aplicação não está a ser instrumentada.

O tempo de execução da ligação sem código emitirá eventos ETW quando o IIS começar e quando a sua aplicação começar.

Para recolher estes eventos:
1. Numa consola cmd com privilégios de administração, execute `iisreset /stop` Desligue o IIS e todas as aplicações web.
2. Execute este cmdlet
3. Numa consola cmd com privilégios de administração, execute `iisreset /start` Iniciar o IIS.
4. Tente navegar na sua aplicação.
5. Depois de a sua aplicação terminar de carregar, pode detê-la manualmente (`Ctrl + C`) ou esperar pelo intervalo.

### <a name="what-events-to-collect"></a>Que eventos recolher

Tem três opções na recolha de eventos:
1. Utilize o comutador `-CollectSdkEvents` para recolher eventos emitidos pelo SDK de Insights de Aplicação.
2. Utilize o interruptor `-CollectRedfieldEvents` para recolher os eventos emitidos pelo Status Monitor e pelo Tempo de Execução de Redfield. Estes registos são úteis no diagnóstico do IIS e no arranque da aplicação.
3. Utilize ambos os interruptores para recolher ambos os tipos de eventos.
4. Por predefinição, se não for especificado qualquer interruptor, ambos os tipos de eventos serão recolhidos.


## <a name="parameters"></a>Parâmetros

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Opcional.** Utilize este parâmetro para definir quanto tempo este script deve recolher eventos. A predefinição é 5 minutos.

### <a name="-logdirectory"></a>-LogDirectory
**Opcional.** Utilize este interruptor para definir o diretório de saída do ficheiro ETL. Por predefinição, este ficheiro será criado no diretório de Módulos PowerShell. O caminho completo será exibido durante a execução do guião.


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Opcional.** Utilize este interruptor para recolher eventos SDK de Insights de Aplicação.

### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Opcional.** Utilize este interruptor para recolher eventos do Status Monitor e do tempo de execução de Redfield.

### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Utilize este interruptor para registos detalhados de saída.



## <a name="output"></a>Saída


### <a name="example-of-application-startup-logs"></a>Exemplo de registos de startups de aplicação
```
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

Resolução adicional de problemas:

- Reveja passos adicionais de resolução de problemas aqui: https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- Reveja a [Referência Da API](status-monitor-v2-overview.md#powershell-api-reference) para saber sobre os parâmetros que pode ter perdido.
- Se precisar de ajuda adicional, pode contactar-nos no [GitHub.](https://github.com/Microsoft/ApplicationInsights-Home/issues)



 Faça mais com o Agente de Insights de Aplicação:
 - Use o nosso guia para [resolver problemas](status-monitor-v2-troubleshoot.md) no Agente insights de aplicação.
 - [Faça com que o config](status-monitor-v2-api-get-config.md) confirme que as suas definições foram gravadas corretamente.
 - [Obtenha o estado](status-monitor-v2-api-get-status.md) para inspecionar a monitorização.
