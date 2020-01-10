---
title: Enviar dados de Diagnóstico do Azure para Application Insights
description: Atualize a configuração pública do Diagnóstico do Azure para enviar dados para Application Insights.
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/19/2016
ms.openlocfilehash: b89e7d93113990e032f526d1f32e4e6acddffa75
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450570"
---
# <a name="send-cloud-service-virtual-machine-or-service-fabric-diagnostic-data-to-application-insights"></a>Enviar dados de diagnóstico de serviço de nuvem, máquina virtual ou Service Fabric para Application Insights
Serviços de nuvem, máquinas virtuais, conjuntos de dimensionamento de máquinas virtuais e Service Fabric todos usam a extensão Diagnóstico do Azure para coletar dados.  O diagnóstico do Azure envia dados para tabelas de armazenamento do Azure.  No entanto, você também pode canalizar todos ou um subconjunto dos dados para outros locais usando Diagnóstico do Azure extensão 1,5 ou posterior.

Este artigo descreve como enviar dados da extensão de Diagnóstico do Azure para Application Insights.

## <a name="diagnostics-configuration-explained"></a>Configuração de diagnóstico explicada
A extensão de diagnóstico do Azure 1,5 introduziu coletores, que são locais adicionais em que você pode enviar dados de diagnóstico.

Exemplo de configuração de um coletor para Application Insights:

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
- O atributo de *nome* do **coletor** é um valor de cadeia de caracteres que identifica exclusivamente o coletor.

- O elemento **ApplicationInsights** especifica a chave de instrumentação do recurso do Application insights em que os dados do diagnóstico do Azure são enviados.
    - Se você não tiver um recurso de Application Insights existente, consulte [criar um novo recurso de Application insights](../../azure-monitor/app/create-new-resource.md ) para obter mais informações sobre como criar um recurso e obter a chave de instrumentação.
    - Se você estiver desenvolvendo um serviço de nuvem com o SDK do Azure 2,8 e posteriores, essa chave de instrumentação será preenchida automaticamente. O valor é baseado na definição de configuração do serviço de **APPINSIGHTS_INSTRUMENTATIONKEY** ao empacotar o projeto de serviço de nuvem. Consulte [usar Application insights com serviços de nuvem](../../azure-monitor/app/cloudservices.md).

- O elemento **Channels** contém um ou mais elementos **Channel** .
    - O atributo *Name* se refere exclusivamente a esse canal.
    - O atributo *logLevel* permite especificar o nível de log que o canal permite. Os níveis de log disponíveis em ordem de mais de menos informações são:
        - Verboso
        - Proteção das
        - Aviso
        - Erro
        - Crítico

Um canal atua como um filtro e permite que você selecione níveis de log específicos para enviar ao coletor de destino. Por exemplo, você pode coletar logs detalhados e enviá-los para o armazenamento, mas enviar apenas erros ao coletor.

O gráfico a seguir mostra essa relação.

![Configuração pública de diagnóstico](media/diagnostics-extension-to-application-insights/AzDiag_Channels_App_Insights.png)

O gráfico a seguir resume os valores de configuração e como eles funcionam. Você pode incluir vários coletores na configuração em diferentes níveis na hierarquia. O coletor no nível superior atua como uma configuração global e o especificado no elemento individual atua como uma substituição para essa configuração global.

![Configuração de coletores de diagnóstico com Application Insights](media/diagnostics-extension-to-application-insights/Azure_Diagnostics_Sinks.png)

## <a name="complete-sink-configuration-example"></a>Exemplo de configuração de coletor completo
Aqui está um exemplo completo do arquivo de configuração pública que
1. envia todos os erros para Application Insights (especificado no nó **DiagnosticMonitorConfiguration** )
2. também envia logs de nível detalhado para os logs de aplicativo (especificados no nó **logs** ).

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
Na configuração anterior, as seguintes linhas têm os seguintes significados:

### <a name="send-all-the-data-that-is-being-collected-by-azure-diagnostics"></a>Enviar todos os dados que estão sendo coletados pelo diagnóstico do Azure

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights",
}
```

### <a name="send-only-error-logs-to-the-application-insights-sink"></a>Enviar somente os logs de erro para o coletor de Application Insights

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyTopDiagData",
}
```

### <a name="send-verbose-application-logs-to-application-insights"></a>Enviar logs de aplicativo detalhados para Application Insights

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

- **Canais apenas o tipo de log e não os contadores de desempenho.** Se você especificar um canal com um elemento de contador de desempenho, ele será ignorado.
- **O nível de log para um canal não pode exceder o nível de log para o que está sendo coletado pelo diagnóstico do Azure.** Por exemplo, você não pode coletar erros de log do aplicativo no elemento logs e tentar enviar logs detalhados para o coletor do Application insights. O atributo *scheduledTransferLogLevelFilter* sempre deve coletar igual ou mais logs do que os logs que você está tentando enviar para um coletor.
- **Não é possível enviar dados de blob coletados pela extensão de diagnóstico do Azure para Application Insights.** Por exemplo, qualquer coisa especificada no nó *diretórios* . Para despejos de memória, o despejo de memória real é enviado para o armazenamento de BLOB e apenas uma notificação de que o despejo de memória foi gerado é enviada para Application Insights.

## <a name="next-steps"></a>Próximos Passos
* Saiba como [exibir suas informações de diagnóstico do Azure](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices) em Application insights.
* Use o [PowerShell](../../cloud-services/cloud-services-diagnostics-powershell.md) para habilitar a extensão de diagnóstico do Azure para seu aplicativo.
* Use o [Visual Studio](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) para habilitar a extensão de diagnóstico do Azure para seu aplicativo

