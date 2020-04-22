---
title: Azure Application Insights Agente de resolução de problemas e questões conhecidas / Microsoft Docs
description: As questões conhecidas do Agente de Insights de Aplicação e exemplos de resolução de problemas. Monitorize o desempenho do site sem reimplantar o website. Trabalha com ASP.NET aplicações web hospedadas no local, em VMs ou no Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 9bb22b12a7b3e972ff144bd121db4288801e2488
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732944"
---
# <a name="troubleshooting-application-insights-agent-formerly-named-status-monitor-v2"></a>Agente de insights de aplicação de resolução de problemas (anteriormente chamado Monitor de Estado v2)

Ao ativar a monitorização, poderá experimentar problemas que impedem a recolha de dados.
Este artigo lista todas as questões conhecidas e fornece exemplos de resolução de problemas.
Se encontrar um problema que não está listado aqui, pode contactar-nos no [GitHub.](https://github.com/Microsoft/ApplicationInsights-Home/issues)

## <a name="known-issues"></a>Problemas conhecidos

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>DLLs conflituosos no diretório de lixo de uma aplicação

Se algum destes DLLs estiver presente no diretório do lixo, a monitorização pode falhar:

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemettryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

Alguns destes DLLs estão incluídos nos modelos de aplicações padrão do Estúdio Visual, mesmo que a sua aplicação não os utilize.
Você pode usar ferramentas de resolução de problemas para ver o comportamento sintomático:

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

- IISReset e carga de aplicações (sem telemetria). Investigue com sysinternals (Handle.exe e ListDLLs.exe):
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>Conflito com configuração partilhada do IIS

Se tiver um conjunto de servidores web, poderá estar a utilizar uma [configuração partilhada](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
O HttpModule não pode ser injetado nesta configuração partilhada.
Executar o comando Ativar em cada servidor web para instalar o DLL no GAC de cada servidor.

Depois de executar o comando Enable, complete estes passos:
1. Vá ao diretório de configuração partilhada e encontre o ficheiro applicationHost.config.
2. Adicione esta linha à secção de módulos da sua configuração:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```

### <a name="iis-nested-applications"></a>Aplicações IIS Aninhadas

Não instrumentamos aplicações aninhadas no IIS na versão 1.0.
Estamos a acompanhar este [assunto.](https://github.com/microsoft/ApplicationInsights-Home/issues/369)

### <a name="advanced-sdk-configuration-isnt-available"></a>A configuração Avançada de SDK não está disponível.

A configuração SDK não está exposta ao utilizador final na versão 1.0.
Estamos a acompanhar este [assunto.](https://github.com/microsoft/ApplicationInsights-Home/issues/375)

    
    
## <a name="troubleshooting"></a>Resolução de problemas
    
### <a name="troubleshooting-powershell"></a>Resolução de problemas PowerShell

#### <a name="determine-which-modules-are-available"></a>Determine quais os módulos disponíveis
Pode utilizar `Get-Module -ListAvailable` o comando para determinar quais os módulos instalados.

#### <a name="import-a-module-into-the-current-session"></a>Importar um módulo para a sessão atual
Se um módulo não tiver sido carregado numa sessão powerShell, `Import-Module <path to psd1>` pode carregá-lo manualmente utilizando o comando.


### <a name="troubleshooting-the-application-insights-agent-module"></a>Resolução de problemas do módulo de agente de insights de aplicação

#### <a name="list-the-commands-available-in-the-application-insights-agent-module"></a>Enumerar os comandos disponíveis no módulo agente de insights de aplicação
Executar o `Get-Command -Module Az.ApplicationMonitor` comando para obter os comandos disponíveis:

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

#### <a name="determine-the-current-version-of-the-application-insights-agent-module"></a>Determine a versão atual do módulo de agente de insights de aplicação
Executar `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` o comando para mostrar as seguintes informações sobre o módulo:
   - Versão do módulo PowerShell
   - Versão SDK insights de aplicação
   - Caminhos de arquivo do módulo PowerShell
    
Reveja a [referência da API](status-monitor-v2-api-reference.md) para obter uma descrição detalhada de como utilizar este cmdlet.


### <a name="troubleshooting-running-processes"></a>Processos de resolução de problemas

Pode inspecionar os processos no computador instrumentado para determinar se todos os DLLs estão carregados.
Se a monitorização estiver a funcionar, devem ser carregados pelo menos 12 DLLs.

Use `Get-ApplicationInsightsMonitoringStatus -InspectProcess` o comando para verificar os DLLs.

Reveja a [referência da API](status-monitor-v2-api-reference.md) para obter uma descrição detalhada de como utilizar este cmdlet.


### <a name="collect-etw-logs-by-using-perfview"></a>Colete registos ETW utilizando o PerfView

#### <a name="setup"></a>Configuração

1. Baixe PerfView.exe e PerfView64.exe do [GitHub](https://github.com/Microsoft/perfview/releases).
2. Iniciar PerfView64.exe.
3. Expandir **opções avançadas.**
4. Limpe estas caixas de verificação:
    - **Zip**
    - **Intercalar**
    - **Coleção de símbolos .NET**
5. Definir estes **Fornecedores Adicionais:**`61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Recolha de registos

1. Numa consola de comando com privilégios `iisreset /stop` Admin, execute o comando para desligar o IIS e todas as aplicações web.
2. No PerfView, selecione **Start Collection**.
3. Numa consola de comando com privilégios `iisreset /start` Admin, execute o comando para iniciar o IIS.
4. Tente navegar na sua aplicação.
5. Depois de a sua aplicação estar carregada, volte ao PerfView e selecione **Stop Collection**.



## <a name="next-steps"></a>Passos seguintes

- Reveja a referência da [API](status-monitor-v2-overview.md#powershell-api-reference) para saber sobre os parâmetros que pode ter perdido.
- Se encontrar um problema que não está listado aqui, pode contactar-nos no [GitHub.](https://github.com/Microsoft/ApplicationInsights-Home/issues)
