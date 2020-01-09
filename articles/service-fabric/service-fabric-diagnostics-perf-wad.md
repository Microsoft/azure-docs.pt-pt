---
title: Monitoramento de desempenho com o Windows Diagnóstico do Azure
description: Use o Windows Diagnóstico do Azure para coletar contadores de desempenho para seus clusters de Service Fabric do Azure.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 0819ca02d088aeb9ada5de1269467f70242bbcca
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75609915"
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Monitoramento de desempenho com a extensão de Diagnóstico do Azure do Windows

Este documento aborda as etapas necessárias para configurar a coleta de contadores de desempenho por meio da extensão do Windows Diagnóstico do Azure (WAD) para clusters do Windows. Para clusters do Linux, configure o [agente de log Analytics](service-fabric-diagnostics-oms-agent.md) para coletar contadores de desempenho para seus nós. 

 > [!NOTE]
> A extensão WAD deve ser implantada em seu cluster para que essas etapas funcionem para você. Se não estiver configurado, vá para a [agregação de eventos e a coleção usando o Windows diagnóstico do Azure](service-fabric-diagnostics-event-aggregation-wad.md).  


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="collect-performance-counters-via-the-wadcfg"></a>Coletar contadores de desempenho por meio do WadCfg

Para coletar contadores de desempenho por meio do WAD, você precisa modificar a configuração adequadamente no modelo do Resource Manager do cluster. Siga estas etapas para adicionar um contador de desempenho que você deseja coletar ao seu modelo e executar uma atualização de recurso do Resource Manager.

1. Localize a configuração do WAD no modelo do cluster-encontre `WadCfg`. Você adicionará contadores de desempenho para coletar sob o `DiagnosticMonitorConfiguration`.

2. Configure sua configuração para coletar contadores de desempenho adicionando a seção a seguir ao seu `DiagnosticMonitorConfiguration`. 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    A `scheduledTransferPeriod` define com que frequência os valores dos contadores que são coletados são transferidos para a tabela de armazenamento do Azure e para qualquer coletor configurado. 

3. Adicione os contadores de desempenho que você gostaria de coletar para o `PerformanceCounterConfiguration` que foi declarado na etapa anterior. Cada contador que você gostaria de coletar é definido com um `counterSpecifier`, `sampleRate`, `unit`, `annotation`e qualquer `sinks`relevante.

Aqui está um exemplo de uma configuração com o contador para o *tempo total do processador* (a quantidade de tempo que a CPU estava em uso para operações de processamento) e *Service Fabric invocações de método de ator por segundo*, um dos contadores de desempenho personalizados Service Fabric. Consulte contadores de [desempenho de ator confiável](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) e [contadores de desempenho de serviço confiável](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters) para obter uma lista completa de Service Fabric contadores de perf personalizados.

 ```json
 "WadCfg": {
         "DiagnosticMonitorConfiguration": {
           "overallQuotaInMB": "50000",
           "EtwProviders": {
             "EtwEventSourceProviderConfiguration": [
               {
                 "provider": "Microsoft-ServiceFabric-Actors",
                 "scheduledTransferKeywordFilter": "1",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableActorEventTable"
                 }
               },
               {
                 "provider": "Microsoft-ServiceFabric-Services",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableServiceEventTable"
                 }
               }
             ],
             "EtwManifestProviderConfiguration": [
               {
                 "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                 "scheduledTransferLogLevelFilter": "Information",
                 "scheduledTransferKeywordFilter": "4611686018427387904",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricSystemEventTable"
                 }
               }
             ]
           },
           "PerformanceCounters": {
                 "scheduledTransferPeriod": "PT1M",
                 "PerformanceCounterConfiguration": [
                     {
                         "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                         "sampleRate": "PT1M",
                         "unit": "Percent",
                         "annotation": [
                         ],
                         "sinks": ""
                     },
                     {
                         "counterSpecifier": "\\Service Fabric Actor Method(*)\\Invocations/Sec",
                         "sampleRate": "PT1M",
                     }
                 ]
             }
         }
       },
  ```

 A taxa de amostragem para o contador pode ser modificada de acordo com suas necessidades. O formato para ele é `PT<time><unit>`, portanto, se você quiser que o contador seja coletado a cada segundo, defina o `"sampleRate": "PT15S"`.

 Você também pode usar variáveis em seu modelo ARM para coletar uma matriz de contadores de desempenho, o que pode ser útil quando você coleta contadores de desempenho por processo. No exemplo abaixo, estamos coletando o tempo do processador e o tempo do coletor de lixo por processo e depois dois contadores de desempenho nos próprios nós usando variáveis. 

 ```json
"variables": {
  "copy": [
      {
        "name": "processorTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\Process([parameters('monitoredProcesses')[copyIndex('processorTimeCounters')]])\\% Processor Time",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('processorTimeCounters')],' Processor Time')]",
              "locale": "en-us"
            }
          ]
        }
      },
      {
        "name": "gcTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\.NET CLR Memory([parameters('monitoredProcesses')[copyIndex('gcTimeCounters')]])\\% Time in GC",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('gcTimeCounters')],' Time in GC')]",
              "locale": "en-us"
            }
          ]
        }
      }
    ],
    "machineCounters": [
      {
        "counterSpecifier": "\\Memory\\Available Bytes",
        "sampleRate": "PT1M",
        "unit": "KB",
        "sinks": "applicationInsights",
        "annotation": [
          {
            "displayName": "Memory Available Kb",
            "locale": "en-us"
          }
        ]
      },
      {
        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
        "sampleRate": "PT15S",
        "unit": "percent",
        "annotation": [
          {
            "displayName": "Memory usage",
            "locale": "en-us"
          }
        ]
      }
    ]
  }
....
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": "50000",
      "Metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', variables('vmNodeTypeApp2Name'))]"
      },
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": "[concat(variables ('processorTimeCounters'), variables('gcTimeCounters'),  variables('machineCounters'))]"
      },
....
```

1. Depois de adicionar os contadores de desempenho apropriados que precisam ser coletados, você precisará atualizar o recurso de cluster para que essas alterações sejam refletidas no cluster em execução. Salve o `template.json` modificado e abra o PowerShell. Você pode atualizar seu cluster usando `New-AzResourceGroupDeployment`. A chamada requer o nome do grupo de recursos, o arquivo de modelo atualizado e o arquivo de parâmetros e solicita que o Resource Manager faça as alterações apropriadas nos recursos que você atualizou. Quando você estiver conectado à sua conta e estiver na assinatura correta, use o seguinte comando para executar a atualização:

    ```sh
    New-AzResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

1. Depois que a atualização concluir a distribuição (leva entre 15-45 minutos, dependendo se é a primeira implantação e o tamanho do seu grupo de recursos), WAD deve coletar os contadores de desempenho e enviá-los para a tabela chamada WADPerformanceCountersTable na conta de armazenamento associada ao cluster. Consulte seus contadores de desempenho em Application Insights [adicionando o coletor de ai ao modelo do Resource Manager](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template).

## <a name="next-steps"></a>Passos seguintes
* Colete mais contadores de desempenho para o cluster. Consulte [métricas de desempenho](service-fabric-diagnostics-event-generation-perf.md) para obter uma lista de contadores que você deve coletar.
* [Use monitoramento e diagnóstico com uma VM do Windows e modelos de Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md) para fazer mais modificações em seus `WadCfg`, incluindo a configuração de contas de armazenamento adicionais para envio de dados de diagnóstico.
* Visite o [WadCfg Builder](https://azure.github.io/azure-diagnostics-tools/config-builder/) para criar um modelo do zero e verifique se a sintaxe está correta. (https://azure.github.io/azure-diagnostics-tools/config-builder/) criar um modelo a partir do zero e verificar se a sintaxe está correta.
