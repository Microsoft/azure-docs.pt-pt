---
title: O Azure Monitor de estado v2 de resolução de problemas e problemas conhecidos | Documentos da Microsoft
description: Os problemas conhecidos do Monitor de estado v2 e exemplos de resolução de problemas. Monitorizar o desempenho dos Web sites sem implementar novamente o Web site. Funciona com aplicações Web ASP.NET alojadas no local, em VMs ou no Azure.
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
ms.openlocfilehash: d640206fd72b4eb89afe5ed1750627823bca9637
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415883"
---
# <a name="troubleshooting-status-monitor-v2"></a>Estado de resolução de problemas monitorizar v2

Quando ativa a monitorização, podem ocorrer problemas que impedem a recolha de dados. Este documento apresenta uma lista de todos os problemas conhecidos e resolução de problemas de exemplos.
Se se deparou com um problema não listado aqui, pode contactar-nos [aqui](https://github.com/Microsoft/ApplicationInsights-Home/issues).


> [!IMPORTANT]
> V2 de Monitor de estado está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="known-issues"></a>Problemas conhecidos

### <a name="conflicting-dlls-in-an-applications-bin-directory"></a>DLLs em conflito no diretório bin de um aplicativo

Se qualquer uma dessas DLLs estão presentes no diretório bin, monitorização poderá falhar.

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

Algumas dessas DLLs são incluídas nos modelos de aplicativo do Visual Studio padrão, mesmo que seu aplicativo não usá-los.
Comportamento sintomático pode ser visto com as ferramentas de resolução de problemas:

- PerfView:
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- iisreset + aplicação de carga (sem TELEMETRIA). Investigar com Sysinternals (Handle.exe e ListDLLs.exe)
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>Conflito com a configuração partilhada do IIS

Se tiver um cluster de servidores web, poderá estar a utilizar um [configuração partilhada](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211). Automaticamente, não é possível inserir o nosso HttpModule nesta configuração partilhada. Cada servidor web tem de executar primeiro o comando de ativação para instalar o nosso DLL em seu GAC.

Depois de executar o comando de ativação, 
1. Navegue para o seu diretório de configuração partilhada e encontrar seu `applicationHost.config` ficheiro.
2. Adicione esta linha à configuração na secção módulos:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```
    


## <a name="troubleshooting"></a>Resolução de problemas
    
### <a name="troubleshooting-powershell"></a>Resolução de problemas do PowerShell

#### <a name="how-to-inspect-what-modules-are-available"></a>Como inspecionar quais módulos estão disponíveis?
É possível auditar os módulos instalados com o comando: `Get-Module -ListAvailable`

#### <a name="how-to-import-a-module-into-the-current-session"></a>Como importar um módulo para a sessão atual?
Se o módulo não foi carregado para uma sessão do PowerShell, pode carregar manualmente utilizando o comando: `Import-Module <path to psd1>`


### <a name="troubleshooting-the-status-monitor-v2-module"></a>O módulo de v2 do Monitor de estado de resolução de problemas

#### <a name="how-to-review-what-commands-are-available-in-the-status-monitor-v2-module"></a>Como rever quais comandos estão disponíveis no módulo de v2 do Monitor de estado?
- Execute o comando: `Get-Command -Module Az.ApplicationMonitor` para obter os comandos disponíveis:

    ```
    CommandType     Name                                               Version    Source
    -----------     ----                                               -------    ------
    Cmdlet          Disable-ApplicationInsightsMonitoring              0.2.1      Az.ApplicationMonitor
    Cmdlet          Disable-InstrumentationEngine                      0.2.1      Az.ApplicationMonitor
    Cmdlet          Enable-ApplicationInsightsMonitoring               0.2.1      Az.ApplicationMonitor
    Cmdlet          Enable-InstrumentationEngine                       0.2.1      Az.ApplicationMonitor
    Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.2.1      Az.ApplicationMonitor
    Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.2.1      Az.ApplicationMonitor
    Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.2.1      Az.ApplicationMonitor
    ```

#### <a name="what-is-the-current-version-of-the-status-monitor-v2-module"></a>O que é a versão atual do módulo de v2 do Monitor de estado?
- Execute o comando: `Get-ApplicationInsightsMonitoringStatus` para obter uma saída de informações sobre este módulo:
    - Versão do módulo do PowerShell
    - Versão do Application Insights SDK
    - Caminhos de ficheiro do módulo do PowerShell
    
Reveja nossa [referência da API](status-monitor-v2-api-get-status.md) para uma descrição detalhada de como utilizar este cmdlet.



### <a name="troubleshooting-running-processes"></a>Resolução de problemas de processos em execução

Pode inspecionar o processo na máquina instrumentado para ver se todas as DLLs são carregadas.
Se a monitorização está a funcionar, DLLS, pelo menos, 12 devem ser carregados.

- Cmd: `Get-ApplicationInsightsMonitoringStatus -InspectProcess`

Reveja nossa [referência da API](status-monitor-v2-api-get-status.md) para uma descrição detalhada de como utilizar este cmdlet.


### <a name="collect-etw-logs-with-perfview"></a>Recolher registos ETW com PerfView

#### <a name="setup"></a>Configuração

1. Baixe PerfView.exe e PerfView64.exe do https://github.com/Microsoft/perfview/releases
2. Iniciar PerfView64.exe
3. Expanda "Opções avançadas"
4. Desmarque a opção:
    - Zip
    - Unir
    - Coleção de símbolo de .NET
5. Conjunto de fornecedores adicionais: `61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>A recolher registos

1. Na consola cmd com privilégios de administrador, execute `iisreset /stop` para desativar o IIS e todas as aplicações web.
2. No PerfView, clique em "Iniciar recolha"
3. Na consola cmd com privilégios de administrador, execute `iisreset /start` para iniciar o IIS.
4. Tente navegar para a sua aplicação.
5. Depois da aplicação concluir o carregamento, regresse ao PerfView e clique em "Parar a coleção"



## <a name="next-steps"></a>Passos Seguintes

- Reveja nossa [referência da API](status-monitor-v2-overview.md#powershell-api-reference) para encontrar um parâmetro que possa ter perdido.
- Se se deparou com um problema não listado aqui, pode contactar-nos [aqui](https://github.com/Microsoft/ApplicationInsights-Home/issues).
