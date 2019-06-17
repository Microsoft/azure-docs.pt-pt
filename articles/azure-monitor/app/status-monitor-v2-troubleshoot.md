---
title: O Azure Monitor de estado v2 de resolução de problemas e problemas conhecidos | Documentos da Microsoft
description: Os problemas conhecidos do Monitor de estado v2 e exemplos de resolução de problemas. Monitorizar o desempenho dos Web sites sem implementar novamente o Web site. Funciona com aplicações web ASP.NET alojadas no local, em VMs ou no Azure.
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
ms.openlocfilehash: f2115ee14b58030f695c9410870615f03d353cd2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734953"
---
# <a name="troubleshooting-status-monitor-v2"></a>Estado de resolução de problemas monitorizar v2

Quando ativa a monitorização, podem ocorrer problemas que impedem a recolha de dados.
Este artigo apresenta uma lista de todos os problemas conhecidos e fornece exemplos de resolução de problemas.
Se se deparou com um problema que não está listado aqui, pode contactar-no [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).


> [!IMPORTANT]
> V2 de Monitor de estado está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é fornecido sem um contrato de nível de serviço, e não o recomendamos para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas e algumas podem ter capacidades restringidas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="known-issues"></a>Problemas conhecidos

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>DLLs em conflito no diretório bin de uma aplicação

Se qualquer uma dessas DLLs estão presentes no diretório bin, a monitorização, poderá falhar:

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

Algumas dessas DLLs são incluídas nos modelos de aplicativos padrão do Visual Studio, mesmo que a sua aplicação não usá-los.
Pode utilizar ferramentas de resolução de problemas para ver o comportamento sintomático:

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

- IISReset e aplicação de carga (sem telemetria). Investigar com Sysinternals (Handle.exe e ListDLLs.exe):
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>Conflito com a configuração partilhada do IIS

Se tiver um cluster de servidores web, poderá estar a utilizar um [configuração partilhada](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
O HttpModule não pode ser inserido para esta configuração partilhada.
Execute o comando de ativação em cada servidor web para instalar a DLL no GAC de cada servidor.

Depois de executar o comando de ativação, conclua estes passos:
1. Ir para o diretório de configuração partilhada e encontrar o ficheiro applicationHost. config.
2. Adicione esta linha para a secção de módulos da sua configuração:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```
## <a name="troubleshooting"></a>Resolução de problemas
    
### <a name="troubleshooting-powershell"></a>Resolução de problemas do PowerShell

#### <a name="determine-which-modules-are-available"></a>Determinar quais os módulos estão disponíveis
Pode utilizar o `Get-Module -ListAvailable` comando para determinar quais os módulos são instalados.

#### <a name="import-a-module-into-the-current-session"></a>Importar um módulo para a sessão atual
Se ainda não foi carregado um módulo para uma sessão do PowerShell, pode manualmente carregá-lo utilizando o `Import-Module <path to psd1>` comando.


### <a name="troubleshooting-the-status-monitor-v2-module"></a>O módulo de v2 do Monitor de estado de resolução de problemas

#### <a name="list-the-commands-available-in-the-status-monitor-v2-module"></a>Lista os comandos disponíveis no módulo de v2 do Monitor de estado
Execute o comando `Get-Command -Module Az.ApplicationMonitor` para obter os comandos disponíveis:

```
Command type     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Disable-ApplicationInsightsMonitoring              0.2.1      Az.ApplicationMonitor
Cmdlet          Disable-InstrumentationEngine                      0.2.1      Az.ApplicationMonitor
Cmdlet          Enable-ApplicationInsightsMonitoring               0.2.1      Az.ApplicationMonitor
Cmdlet          Enable-InstrumentationEngine                       0.2.1      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.2.1      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.2.1      Az.ApplicationMonitor
Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.2.1      Az.ApplicationMonitor
```

#### <a name="determine-the-current-version-of-the-status-monitor-v2-module"></a>Determinar a versão atual do módulo de v2 do Monitor de estado
Execute o `Get-ApplicationInsightsMonitoringStatus` comando para apresentar as seguintes informações sobre o módulo:
   - Versão do módulo do PowerShell
   - Versão do Application Insights SDK
   - Caminhos de ficheiro do módulo do PowerShell
    
Reveja os [referência da API](status-monitor-v2-api-get-status.md) para uma descrição detalhada de como utilizar este cmdlet.


### <a name="troubleshooting-running-processes"></a>Resolução de problemas de processos em execução

Pode inspecionar os processos no computador instrumentado para determinar se todas as DLLs são carregadas.
Se a monitorização está a funcionar, devem ser carregados, pelo menos, 12 DLLs.

Utilize o `Get-ApplicationInsightsMonitoringStatus -InspectProcess` comando para verificar as DLLs.

Reveja os [referência da API](status-monitor-v2-api-get-status.md) para uma descrição detalhada de como utilizar este cmdlet.


### <a name="collect-etw-logs-by-using-perfview"></a>Recolher registos ETW ao utilizar o PerfView

#### <a name="setup"></a>Configurar

1. Baixe PerfView.exe e PerfView64.exe partir [GitHub](https://github.com/Microsoft/perfview/releases).
2. Inicie PerfView64.exe.
3. Expanda **opções avançadas**.
4. Limpe estas caixas de verificação:
    - **Zip**
    - **Intercalar**
    - **Coleção de símbolo de .NET**
5. Defina estes **fornecedores adicionais**: `61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Recolher registos

1. Num console de comando com privilégios de administrador, execute o `iisreset /stop` de comandos para desativar o IIS e todas as aplicações web.
2. No PerfView, selecione **iniciar recolha**.
3. Num console de comando com privilégios de administrador, execute o `iisreset /start` comando para iniciar o IIS.
4. Tente navegar para a sua aplicação.
5. Depois da aplicação é carregada, regresse a PerfView e selecione **parar recolha**.



## <a name="next-steps"></a>Passos Seguintes

- Reveja os [referência da API](status-monitor-v2-overview.md#powershell-api-reference) para saber mais sobre os parâmetros que pode ter perdido.
- Se se deparou com um problema que não está listado aqui, pode contactar-no [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).
