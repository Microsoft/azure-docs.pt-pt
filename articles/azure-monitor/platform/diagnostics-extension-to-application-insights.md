---
title: Configurar os diagnósticos do Azure para enviar dados para o Application Insights
description: Atualize a configuração pública de diagnóstico do Azure para enviar dados para o Application Insights.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2016
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: f7e21b805c64522005dce3e7d04aa158e1c21032
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60396143"
---
# <a name="send-cloud-service-virtual-machine-or-service-fabric-diagnostic-data-to-application-insights"></a>Enviar dados de diagnóstico do serviço em nuvem, a Máquina Virtual ou o Service Fabric para o Application Insights
Serviços cloud, máquinas virtuais, os conjuntos de dimensionamento de máquinas virtuais e recursos de infraestrutura do serviço de todos os utilizam a extensão de diagnóstico do Azure para recolher dados.  Diagnóstico do Azure envia dados para tabelas de armazenamento do Azure.  No entanto, pode também todos os de pipe ou um subconjunto dos dados para outras localizações com a extensão de diagnóstico do Azure versão 1.5 ou posterior.

Este artigo descreve como enviar dados a partir da extensão de diagnóstico do Azure para o Application Insights.

## <a name="diagnostics-configuration-explained"></a>Configuração de diagnósticos explicada
A extensão de diagnóstico do Azure 1.5 introduzida coletores, que são localizações adicionais, onde pode enviar dados de diagnóstico.

Exemplo de configuração de um coletor para o Application Insights:

```XML
<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
</SinksConfig>
```
```JSON
"SinksConfig": {
    "Sink": [
        {
            "name": "ApplicationInsights",
            "ApplicationInsights": "{Insert InstrumentationKey}",
            "Channels": {
                "Channel": [
                    {
                        "logLevel": "Error",
                        "name": "MyTopDiagData"
                    },
                    {
                        "logLevel": "Error",
                        "name": "MyLogData"
                    }
                ]
            }
        }
    ]
}
```
- O **Sink** *nome* atributo é um valor de cadeia que identifica exclusivamente o sink.

- O **Application Insights** elemento Especifica a chave de instrumentação do recurso do Application insights onde os dados de diagnóstico do Azure são enviados.
    - Se não tiver um recurso do Application Insights existente, consulte [criar um novo recurso do Application Insights](../../azure-monitor/app/create-new-resource.md ) para obter mais informações sobre como criar um recurso e obter a chave de instrumentação.
    - Se estiver a desenvolver um serviço em nuvem com o Azure SDK 2.8 e posterior, esta chave de instrumentação é preenchida automaticamente. O valor se baseia a **APPINSIGHTS_INSTRUMENTATIONKEY** definição de configuração do serviço quando empacotar o projeto de serviço em nuvem. Ver [utilizar o Application Insights com os serviços Cloud](../../azure-monitor/app/cloudservices.md).

- O **canais** elemento contém um ou mais **canal** elementos.
    - O *nome* atributo refere-se exclusivamente para esse canal.
    - O *loglevel* atributo permite-lhe especificar o nível de registo que permite o canal. Os níveis de registo disponíveis por ordem de mais informações, pelo menos, são:
        - Verboso
        - Informações
        - Aviso
        - Erro
        - Crítico

Um canal funciona como um filtro e permite-lhe selecionar níveis de registo específicos para enviar para o sink de destino. Por exemplo, pode recolher registos verbosos e enviá-los para armazenamento, mas enviar apenas erros para o sink.

O gráfico seguinte mostra esta relação.

![Configuração pública de diagnóstico](media/diagnostics-extension-to-application-insights/AzDiag_Channels_App_Insights.png)

O gráfico a seguir resume os valores de configuração e como elas funcionam. Pode incluir vários coletores na configuração em diferentes níveis da hierarquia. O coletor no nível superior age como uma configuração global e um serviço especificado no elemento individual funciona como uma substituição para essa definição global.

![Coletores de diagnóstico de configuração com o Application Insights](media/diagnostics-extension-to-application-insights/Azure_Diagnostics_Sinks.png)

## <a name="complete-sink-configuration-example"></a>Exemplo de configuração de sink concluída
Eis um exemplo completo da configuração pública de ficheiros que
1. envia todos os erros para o Application Insights (especificado na **DiagnosticMonitorConfiguration** nó)
2. também envia registos verbosos de nível para os registos da aplicação (especificado na **registos** nó).

```XML
<WadCfg>
  <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
       sinks="ApplicationInsights.MyTopDiagData"> <!-- All info below sent to this channel -->
    <DiagnosticInfrastructureLogs />
    <PerformanceCounters>
      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
      <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
    </PerformanceCounters>
    <WindowsEventLog scheduledTransferPeriod="PT1M">
      <DataSource name="Application!*" />
    </WindowsEventLog>
    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose"
            sinks="ApplicationInsights.MyLogData"/> <!-- This specific info sent to this channel -->
  </DiagnosticMonitorConfiguration>

<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
  </SinksConfig>
</WadCfg>
```
```JSON
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
        "overallQuotaInMB": 4096,
        "sinks": "ApplicationInsights.MyTopDiagData", "_comment": "All info below sent to this channel",
        "DiagnosticInfrastructureLogs": {
        },
        "PerformanceCounters": {
            "PerformanceCounterConfiguration": [
                {
                    "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                    "sampleRate": "PT3M"
                },
                {
                    "counterSpecifier": "\\Memory\\Available MBytes",
                    "sampleRate": "PT3M"
                }
            ]
        },
        "WindowsEventLog": {
            "scheduledTransferPeriod": "PT1M",
            "DataSource": [
                {
                    "name": "Application!*"
                }
            ]
        },
        "Logs": {
            "scheduledTransferPeriod": "PT1M",
            "scheduledTransferLogLevelFilter": "Verbose",
            "sinks": "ApplicationInsights.MyLogData", "_comment": "This specific info sent to this channel"
        }
    },
    "SinksConfig": {
        "Sink": [
            {
                "name": "ApplicationInsights",
                "ApplicationInsights": "{Insert InstrumentationKey}",
                "Channels": {
                    "Channel": [
                        {
                            "logLevel": "Error",
                            "name": "MyTopDiagData"
                        },
                        {
                            "logLevel": "Verbose",
                            "name": "MyLogData"
                        }
                    ]
                }
            }
        ]
    }
}
```
A configuração anterior, as seguintes linhas têm os significados seguintes:

### <a name="send-all-the-data-that-is-being-collected-by-azure-diagnostics"></a>Enviar todos os dados que estão a ser recolhidos pelo diagnóstico do Azure

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights",
}
```

### <a name="send-only-error-logs-to-the-application-insights-sink"></a>Enviar registos de erros apenas para o sink do Application Insights

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyTopDiagData",
}
```

### <a name="send-verbose-application-logs-to-application-insights"></a>Enviar registos de aplicações verboso ao Application Insights

```XML
<Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyLogData",
}
```

## <a name="limitations"></a>Limitações

- **Canais apenas iniciar contadores de tipo e não o desempenho.** Se especificar um canal com um elemento de contador de desempenho, ela é ignorada.
- **O nível de registo para um canal não pode exceder o nível de registo para o que está a ser recolhido pelo diagnóstico do Azure.** Por exemplo, não é possível recolher erros de registo de aplicações do elemento de Logs e tentar enviar verboso registos para o sink do Application Insights. O *scheduledTransferLogLevelFilter* atributo sempre tem de recolher igual ou mais logs do que os registos que está a tentar enviar para um sink.
- **Não é possível enviar os dados de blob recolhidos por extensão de diagnóstico do Azure para o Application Insights.** Por exemplo, nada especificado sob o *diretórios* nó. Para informações de falhas a informação de falha real é enviada para o armazenamento de BLOBs e apenas uma notificação de que a informação de falha foi gerada é enviada para o Application Insights.

## <a name="next-steps"></a>Próximos Passos
* Saiba como [veja as suas informações de diagnóstico do Azure](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices) no Application Insights.
* Uso [PowerShell](../../cloud-services/cloud-services-diagnostics-powershell.md) para ativar a extensão de diagnóstico do Azure para a sua aplicação.
* Uso [Visual Studio](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) para ativar a extensão de diagnóstico do Azure para a sua aplicação

