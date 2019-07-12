---
title: 'Referência de API do v2 de Monitor de estado do Azure: Iniciar o rastreio | Documentos da Microsoft'
description: Referência da API v2 de Monitor de estado. Rastreio de início. Recolha os registos ETW do Monitor de estado e o SDK do Application Insights.
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
ms.openlocfilehash: b6787134707273a76290adb723a9bc9012252ebd
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807061"
---
# <a name="status-monitor-v2-api-start-applicationinsightsmonitoringtrace-v040-alpha"></a>API de v2 de Monitor de estado: Start-ApplicationInsightsMonitoringTrace (v0.4.0-alpha)

Este artigo descreve um cmdlet que faz parte do [módulo do Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> V2 de Monitor de estado está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é fornecido sem um contrato de nível de serviço, e não o recomendamos para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas e algumas podem ter capacidades restringidas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Descrição

Recolhe [Eventos ETW](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) sem o código de anexar o tempo de execução. Este cmdlet é uma alternativa à execução [PerfView](https://github.com/microsoft/perfview).

Eventos recolhidos serão impressos na consola em tempo real e guardada num arquivo ETL. O ficheiro de ETL de saída pode ser aberto por [PerfView](https://github.com/microsoft/perfview) para uma investigação mais aprofundada.

Este cmdlet será executado até atingir a duração de tempo limite (predefinição 5 minutos) ou está parado manualmente (`Ctrl + C`).

> [!IMPORTANT] 
> Este cmdlet requer uma sessão do PowerShell com permissões de administrador.

## <a name="examples"></a>Exemplos

### <a name="how-to-collect-events"></a>Como recolher eventos

Normalmente pediríamos que recolher eventos para investigar por que seu aplicativo não está sendo instrumentado.

Tempo de execução de anexar o sem código emitirá Eventos ETW quando IIS é iniciado e quando seu aplicativo é iniciado.

Para recolher esses eventos:
1. Na consola cmd com privilégios de administrador, execute `iisreset /stop` para desativar o IIS e todas as aplicações web.
2. Execute este cmdlet
3. Na consola cmd com privilégios de administrador, execute `iisreset /start` para iniciar o IIS.
4. Tente navegar para a sua aplicação.
5. Depois da aplicação concluir o carregamento, pode manualmente impedi-lo (`Ctrl + C`) ou aguardar que o tempo limite.

### <a name="what-events-to-collect"></a>O evento a recolher

Tem três opções aquando da recolha de eventos:
1. Utilizar o comutador `-CollectSdkEvents` para recolher eventos emitidos o Application Insights SDK.
2. Utilizar o comutador `-CollectRedfieldEvents` para recolher eventos emitidos pelo Monitor de estado e o tempo de execução Redfield. Estes registos são úteis quando diagnosticar o IIS e inicialização do aplicativo.
3. Utilize ambas as opções para recolher os dois tipos de evento.
4. Por predefinição, se não for especificado nenhum comutador ambos os tipos de eventos serão recolhidos.


## <a name="parameters"></a>Parâmetros

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Opcional.** Utilize este parâmetro para definir o tempo em que este script deve recolher eventos. A predefinição é 5 minutos.

### <a name="-logdirectory"></a>-LogDirectory
**Opcional.** Utilize este parâmetro para definir o diretório de saída do ficheiro ETL. Por predefinição, este ficheiro será criado no diretório de módulos do PowerShell. O caminho completo será apresentado durante a execução do script.


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Opcional.** Utilize este parâmetro para recolher eventos do SDK do Application Insights.

### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Opcional.** Utilize este parâmetro para recolher eventos do Monitor de estado e o tempo de execução Redfield.

### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Utilize este parâmetro para a saída de registos detalhados.



## <a name="output"></a>Output


### <a name="example-of-application-startup-logs"></a>Exemplo de registos de arranque da aplicação
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


## <a name="next-steps"></a>Passos Seguintes

Resolução de problemas adicionais:

- Reveja adicionais aqui com passos de resolução de problemas: https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- Reveja os [referência da API](status-monitor-v2-overview.md#powershell-api-reference) para saber mais sobre os parâmetros que pode ter perdido.
- Se precisar de ajuda adicional, pode contactar-no [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).



 Faça mais com a v2 de Monitor de estado:
 - Utilize o nosso guia para [resolver problemas de](status-monitor-v2-troubleshoot.md) v2 de Monitor de estado.
 - [Obter a configuração de](status-monitor-v2-api-get-config.md) para confirmar que as suas definições foram registadas corretamente.
 - [Obter o estado](status-monitor-v2-api-get-status.md) para inspecionar a monitorização.
