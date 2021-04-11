---
title: Azure Application Insights Agente resolução de problemas e problemas conhecidos | Microsoft Docs
description: As questões conhecidas do Application Insights Agent e exemplos de resolução de problemas. Monitorize o desempenho do site sem reimplantar o site. Funciona com ASP.NET aplicações web hospedadas no local, em VMs ou em Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: cf3d8fd1566f3d71541aab7648680063e85079bf
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121834"
---
# <a name="troubleshooting-application-insights-agent-formerly-named-status-monitor-v2"></a>Agente de Insights de Aplicação de Resolução de Problemas (anteriormente denominado Status Monitor v2)

Quando ativa a monitorização, poderá experimentar problemas que impeçam a recolha de dados.
Este artigo lista todos os problemas conhecidos e fornece exemplos de resolução de problemas.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>DLLs contraditórios no diretório de lixo de uma aplicação

Se algum destes DLLs estiver presente no diretório do lixo, a monitorização pode falhar:

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

Alguns destes DLLs estão incluídos nos modelos de aplicações padrão do Estúdio Visual, mesmo que a sua aplicação não os utilize.
Pode utilizar ferramentas de resolução de problemas para ver comportamentos sintomáticos:

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

- IISReset e carga de aplicativo (sem telemetria). Investigar com Sysinternals (Handle.exe e ListDLLs.exe):
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="powershell-versions"></a>Versões PowerShell
Este produto foi escrito e testado utilizando o PowerShell v5.1.
Este módulo não é compatível com as versões PowerShell 6 ou 7.
Recomendamos a utilização do PowerShell v5.1 ao lado de versões mais recentes. Para obter mais informações, consulte [utilizar o PowerShell 7 lado a lado com o PowerShell 5.1](/powershell/scripting/install/migrating-from-windows-powershell-51-to-powershell-7#using-powershell-7-side-by-side-with-windows-powershell-51).

### <a name="conflict-with-iis-shared-configuration"></a>Conflito com a configuração partilhada do IIS

Se tiver um conjunto de servidores web, poderá estar a utilizar uma [configuração partilhada](/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
O HttpModule não pode ser injetado nesta configuração partilhada.
Executar o comando Enable em cada servidor web para instalar o DLL no GAC de cada servidor.

Depois de executar o comando Enable, complete estes passos:
1. Vá ao diretório de configuração partilhado e encontre o ficheiro applicationHost.config.
2. Adicione esta linha à secção de módulos da sua configuração:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```

### <a name="iis-nested-applications"></a>Aplicações Aninhadas IIS

Não instrumentamos aplicações aninhadas no IIS na versão 1.0.

### <a name="advanced-sdk-configuration-isnt-available"></a>A Configuração Avançada SDK não está disponível.

A configuração SDK não está exposta ao utilizador final na versão 1.0.

    
    
## <a name="troubleshooting"></a>Resolução de problemas
    
### <a name="troubleshooting-powershell"></a>Resolução de problemas PowerShell

#### <a name="determine-which-modules-are-available"></a>Determinar quais os módulos disponíveis
Pode utilizar o `Get-Module -ListAvailable` comando para determinar quais os módulos instalados.

#### <a name="import-a-module-into-the-current-session"></a>Importe um módulo na sessão atual
Se um módulo não tiver sido carregado numa sessão PowerShell, pode carregá-lo manualmente utilizando o `Import-Module <path to psd1>` comando.


### <a name="troubleshooting-the-application-insights-agent-module"></a>Resolução de problemas do módulo de agente de insights de aplicação

#### <a name="list-the-commands-available-in-the-application-insights-agent-module"></a>Listar os comandos disponíveis no módulo Application Insights Agent
Executar o comando `Get-Command -Module Az.ApplicationMonitor` para obter os comandos disponíveis:

```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Disable-ApplicationInsightsMonitoring              0.4.0      Az.ApplicationMonitor
Cmdlet          Disable-InstrumentationEngine                      0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-ApplicationInsightsMonitoring               0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-InstrumentationEngine                       0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.4.0      Az.ApplicationMonitor
Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Start-ApplicationInsightsMonitoringTrace           0.4.0      Az.ApplicationMonitor
```

#### <a name="determine-the-current-version-of-the-application-insights-agent-module"></a>Determinar a versão atual do módulo Application Insights Agent
Executar o `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` comando para mostrar as seguintes informações sobre o módulo:
   - Versão do módulo PowerShell
   - Aplicação Insights Versão SDK
   - Percursos de arquivo do módulo PowerShell
    
Reveja a [referência da API](status-monitor-v2-api-reference.md) para uma descrição detalhada de como utilizar este cmdlet.


### <a name="troubleshooting-running-processes"></a>Processos de resolução de problemas

Pode inspecionar os processos no computador instrumentado para determinar se todos os DLLs estão carregados.
Se a monitorização estiver a funcionar, devem ser carregados pelo menos 12 DLLs.

Use o `Get-ApplicationInsightsMonitoringStatus -InspectProcess` comando para verificar os DLLs.

Reveja a [referência da API](status-monitor-v2-api-reference.md) para uma descrição detalhada de como utilizar este cmdlet.


### <a name="collect-etw-logs-by-using-perfview"></a>Colete registos ETW utilizando o PerfView

#### <a name="setup"></a>Configuração

1. Descarregue PerfView.exe e PerfView64.exe do [GitHub.](https://github.com/Microsoft/perfview/releases)
2. Comece PerfView64.exe.
3. Expandir **Opções Avançadas**.
4. Limpe estas caixas de verificação:
    - **Zip**
    - **Intercalar**
    - **Coleção de símbolos .NET**
5. Definir estes **Fornecedores Adicionais**: `61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Recolha de troncos

1. Numa consola de comando com privilégios Admin, executar o comando para desligar o `iisreset /stop` IIS e todas as aplicações web.
2. No PerfView, selecione **Start Collection**.
3. Numa consola de comando com privilégios de Administração, executar o comando para iniciar o `iisreset /start` IIS.
4. Tente navegar na sua aplicação.
5. Depois de a sua aplicação estar carregada, volte ao PerfView e selecione **Stop Collection**.

## <a name="next-steps"></a>Passos seguintes

- Reveja a referência da [API](status-monitor-v2-overview.md#powershell-api-reference) para saber sobre os parâmetros que pode ter perdido.