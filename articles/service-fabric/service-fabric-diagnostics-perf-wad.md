---
title: Monitorização de desempenho com o Windows Azure Diagnostics
description: Utilize o Windows Azure Diagnostics para recolher contadores de desempenho para os seus clusters de Tecido de Serviço Azure.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: c2114f5392da788bb440589e69e704a148731e02
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86258613"
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Monitorização de desempenho com a extensão de Diagnóstico do Windows Azure

Este documento cobre as etapas necessárias para a criação de contadores de desempenho através da extensão do Windows Azure Diagnostics (WAD) para clusters Windows. Para os clusters Linux, crie o [agente Log Analytics](service-fabric-diagnostics-oms-agent.md) para recolher contadores de desempenho para os seus nós. 

 > [!NOTE]
> A extensão DO WAD deve ser implantada no seu cluster para que estes passos funcionem para si. Se não estiver configurado, dirija-se à [agregação e recolha do Evento utilizando o Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md).  


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="collect-performance-counters-via-the-wadcfg"></a>Recolha balcões de desempenho através do WadCfg

Para recolher contadores de desempenho via WAD, é necessário modificar a configuração adequadamente no modelo de Gestor de Recursos do seu cluster. Siga estes passos para adicionar um contador de desempenho que pretende recolher ao seu modelo e executar uma atualização de recursos do Resource Manager.

1. Encontre a configuração DO WAD no modelo do seu cluster - encontre `WadCfg` . Irá adicionar contadores de desempenho para recolher sob o `DiagnosticMonitorConfiguration` .

2. Configurar a sua configuração para recolher contadores de desempenho adicionando a seguinte secção ao seu `DiagnosticMonitorConfiguration` . 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    O `scheduledTransferPeriod` define com que frequência os valores dos balcões que são recolhidos são transferidos para a sua mesa de armazenamento Azure e para qualquer pia configurada. 

3. Adicione os contadores de desempenho que gostaria de recolher ao `PerformanceCounterConfiguration` que foi declarado no passo anterior. Cada contador que pretende recolher é definido com `counterSpecifier` `sampleRate` um, `unit` , , , e qualquer relevante `annotation` `sinks` .

Aqui está um exemplo de uma configuração com o contador para o *tempo total* do processador (o tempo que o CPU estava a ser utilizado para operações de processamento) e *invocações do método do ator* de tecido de serviço por segundo , um dos contadores de desempenho personalizados do Service Fabric. Consulte os [contadores de desempenho](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) do ator fiável e [os contadores de desempenho de serviço fiável](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters) para obter uma lista completa de contadores perf personalizados do Service Fabric.

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

 A taxa de amostra para o contador pode ser modificada de acordo com as suas necessidades. O formato para tal é `PT<time><unit>` , por isso, se pretender que o contador seja recolhido a cada segundo, então deve definir o `"sampleRate": "PT15S"` .

 Também pode utilizar variáveis no seu modelo ARM para recolher uma série de contadores de desempenho, que podem ser úteis quando recolhe contadores de desempenho por processo. No exemplo abaixo, estamos a recolher tempo de processador e tempo de coletor de lixo por processo e, em seguida, 2 contadores de desempenho nos próprios nós, todos usando variáveis. 

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

1. Depois de ter adicionado os contadores de desempenho apropriados que precisam de ser recolhidos, precisa de atualizar o seu recurso de cluster para que estas alterações sejam refletidas no seu cluster de funcionamento. Guarde o seu `template.json` PowerShell modificado e abra o PowerShell. Pode atualizar o seu cluster utilizando `New-AzResourceGroupDeployment` . A chamada requer o nome do grupo de recursos, o ficheiro de modelo atualizado e o ficheiro de parâmetros, e pede ao Gestor de Recursos que esça as alterações adequadas aos recursos que atualizou. Uma vez que você é assinado na sua conta e está na subscrição certa, use o seguinte comando para executar a atualização:

    ```sh
    New-AzResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

1. Uma vez terminada a atualização (demora entre 15 a 45 minutos, dependendo se é a primeira implantação e o tamanho do seu grupo de recursos), o WAD deve estar a recolher os contadores de desempenho e a enviá-los para a tabela chamada WADPerformanceCountersTable na conta de armazenamento associada ao seu cluster. Consulte os seus contadores de desempenho em Application Insights [adicionando o lavatório de IA ao modelo de Gestor de Recursos.](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template)

## <a name="next-steps"></a>Passos seguintes
* Recolha mais contadores de desempenho para o seu cluster. Consulte [as métricas](service-fabric-diagnostics-event-generation-perf.md) de desempenho para obter uma lista de contadores que deve recolher.
* [Utilize monitores e diagnósticos com modelos de Windows VM e Azure Resource Manager](../virtual-machines/extensions/diagnostics-template.md) para efec se modificarem `WadCfg` mais, incluindo configurar contas de armazenamento adicionais para enviar dados de diagnóstico para.
* Visite o [construtor WadCfg](https://azure.github.io/azure-diagnostics-tools/config-builder/) para construir um modelo de raiz e certifique-se de que a sintaxe está correta. (para https://azure.github.io/azure-diagnostics-tools/config-builder/) construir um modelo de raiz e certificar-se de que a sintaxe está correta.
