---
title: Enviar dados de Diagnósticos Azure para Insights de Aplicação
description: Atualize a configuração pública do Azure Diagnostics para enviar dados para Insights de Aplicação.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/19/2016
ms.openlocfilehash: 5af0eb20f9766369caa7351719b63b213c394e5d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100617331"
---
# <a name="send-cloud-service-virtual-machine-or-service-fabric-diagnostic-data-to-application-insights"></a>Enviar dados de diagnóstico de Cloud Service, Virtual Machine ou Service Fabric para Application Insights
Serviços em nuvem, máquinas virtuais, conjuntos de balanças de máquinas virtuais e tecido de serviço todos usam a extensão Azure Diagnostics para recolher dados.  O diagnóstico Azure envia dados para as tabelas de armazenamento Azure.  No entanto, também pode canalizar a totalidade ou um subconjunto dos dados para outros locais utilizando a extensão Azure Diagnostics 1.5 ou posterior.

Este artigo descreve como enviar dados da extensão Azure Diagnostics para Insights de Aplicação.

## <a name="diagnostics-configuration-explained"></a>Configuração de diagnóstico explicada
A extensão de diagnóstico Azure 1.5 introduziu pias, que são locais adicionais onde pode enviar dados de diagnóstico.

Configuração de exemplo de um lavatório para Insights de Aplicação:

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
- O atributo *do nome* **Sink** é um valor de corda que identifica exclusivamente a pia.

- O elemento **ApplicationInsights** especifica a chave de instrumentação do recurso de insights de aplicação onde os dados de diagnóstico do Azure são enviados.
    - Se não tiver um recurso de Insights de Aplicação existente, consulte [criar um novo recurso Application Insights](../app/create-new-resource.md) para obter mais informações sobre a criação de um recurso e obter a chave de instrumentação.
    - Se estiver a desenvolver um Serviço cloud com Azure SDK 2.8 e mais tarde, esta chave de instrumentação é automaticamente povoada. O valor baseia-se na configuração da configuração do serviço **APPINSIGHTS_INSTRUMENTATIONKEY** ao embalar o projeto Cloud Service. Consulte [o Uso de Insights de Aplicações com Serviços na Nuvem](../app/cloudservices.md).

- O elemento **Channels** contém um ou mais elementos **do Canal.**
    - O *atributo de nome* refere-se exclusivamente a esse canal.
    - O atributo *loglevel* permite especificar o nível de registo que o canal permite. Os níveis de registo disponíveis na ordem da maioria das informações são:
        - Verboso
        - Informações
        - Aviso
        - Erro
        - Crítico

Um canal funciona como um filtro e permite-lhe selecionar níveis de registo específicos para enviar para a pia do alvo. Por exemplo, pode recolher registos verbosos e enviá-los para armazenamento, mas enviar apenas Erros para a pia.

O gráfico que se segue mostra esta relação.

![Configuração pública de diagnóstico](media/diagnostics-extension-to-application-insights/AzDiag_Channels_App_Insights.png)

O gráfico que se segue resume os valores de configuração e como funcionam. Pode incluir vários lavatórios na configuração em diferentes níveis da hierarquia. A pia ao nível superior funciona como um cenário global e a especificada no elemento individual age como uma sobreposição a esse cenário global.

![Configuração de sumidouros de diagnóstico com insights de aplicação](media/diagnostics-extension-to-application-insights/Azure_Diagnostics_Sinks.png)

## <a name="complete-sink-configuration-example"></a>Exemplo completo de configuração do lavatório
Aqui está um exemplo completo do ficheiro de configuração pública que
1. envia todos os erros para o Application Insights (especificado no nó **de Confirmação do DiagnosticMonitor)**
2. também envia registos de nível Verbose para os Registos de Aplicação (especificados no nó **de Logs).**

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

### <a name="send-all-the-data-that-is-being-collected-by-azure-diagnostics"></a>Envie todos os dados que estão a ser recolhidos pelos diagnósticos da Azure

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights",
}
```

### <a name="send-only-error-logs-to-the-application-insights-sink"></a>Envie apenas registos de erro para a pia Application Insights

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyTopDiagData",
}
```

### <a name="send-verbose-application-logs-to-application-insights"></a>Enviar registos de aplicações verbose para Insights de Aplicação

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

- **Os canais apenas registam o tipo e não os contadores de desempenho.** Se especificar um canal com um elemento de contador de desempenho, este é ignorado.
- **O nível de registo de um canal não pode exceder o nível de registo para o que está a ser recolhido pelos diagnósticos Azure.** Por exemplo, não é possível recolher erros de Registo de Aplicações no elemento Logs e tentar enviar registos Verbose para a pia 'Insight de aplicação'. O atributo *DesferrLogLevelFilter programado* deve sempre recolher registos iguais ou mais do que os registos que está a tentar enviar para uma pia.
- **Não é possível enviar dados blob recolhidos pela extensão de diagnóstico da Azure para a Application Insights.** Por exemplo, qualquer coisa especificada no nó *de Diretórios.* Para crash dumps o depósito de colisão real é enviado para o armazenamento de bolhas e apenas uma notificação de que o depósito de colisão foi gerado é enviado para a Application Insights.

## <a name="next-steps"></a>Passos Seguintes
* Saiba como ver as informações de [diagnóstico do Azure](../app/cloudservices.md) em Application Insights.
* Utilize [o PowerShell](../../cloud-services/cloud-services-diagnostics-powershell.md) para ativar a extensão de diagnóstico Azure para a sua aplicação.
* Utilize o [Visual Studio](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) para ativar a extensão de diagnóstico Azure para a sua aplicação

