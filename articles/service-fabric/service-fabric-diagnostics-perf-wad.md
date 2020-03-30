---
title: Monitorização de desempenho com diagnósticos Windows Azure
description: Utilize o Windows Azure Diagnostics para recolher contadores de desempenho para os seus clusters Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 0819ca02d088aeb9ada5de1269467f70242bbcca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609915"
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Monitorização de desempenho com a extensão de Diagnóstico seleções do Windows Azure

Este documento cobre as etapas necessárias para a montagem de contadores de desempenho através da extensão de Diagnósticos Windows Azure (WAD) para clusters Windows. Para os clusters Linux, instale o [agente Log Analytics](service-fabric-diagnostics-oms-agent.md) para recolher balcões de desempenho para os seus nós. 

 > [!NOTE]
> A extensão WAD deve ser implantada no seu cluster para que estes passos funcionem para si. Se não estiver configurado, dirija-se à agregação e recolha do Evento utilizando o [Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md).  


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="collect-performance-counters-via-the-wadcfg"></a>Colete contadores de desempenho através do WadCfg

Para recolher contadores de desempenho via WAD, precisa modificar a configuração adequadamente no modelo de Gestor de Recursos do seu cluster. Siga estes passos para adicionar um contador de desempenho que pretende recolher ao seu modelo e executar uma atualização de recursos do Gestor de Recursos.

1. Encontre a configuração WAD no modelo `WadCfg`do seu cluster - encontre . Você vai adicionar contadores de desempenho `DiagnosticMonitorConfiguration`para recolher sob o .

2. Configura risa a sua configuração para recolher `DiagnosticMonitorConfiguration`contadores de desempenho adicionando a seguinte secção à sua . 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    O `scheduledTransferPeriod` define com que frequência os valores dos balcões recolhidos são transferidos para a sua mesa de armazenamento Azure e para qualquer pia configurada. 

3. Adicione os contadores de desempenho que `PerformanceCounterConfiguration` gostaria de recolher ao que foi declarado no passo anterior. Cada contador que gostaria de recolher `counterSpecifier` `sampleRate`é `unit` `annotation`definido com `sinks`um, , , e qualquer relevante .

Aqui está um exemplo de uma configuração com o contador para o *Tempo Total* do Processador (o tempo que o CPU estava a ser utilizado para operações de processamento) e *invocações*de método de ator de tecido de serviço por segundo , um dos contadores de desempenho personalizados do Tecido de Serviço. Consulte os contadores de [desempenho confiáveis](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) do ator e os contadores de [desempenho de serviço fiáveis](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters) para obter uma lista completa de contadores de perf personalizados de tecido de serviço.

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

 A taxa de amostragem do contador pode ser modificada de acordo com as suas necessidades. O formato `PT<time><unit>`para ele é, por isso, se quiser que `"sampleRate": "PT15S"`o contador seja recolhido a cada segundo, então deve definir o .

 Também pode usar variáveis no seu modelo ARM para recolher uma variedade de contadores de desempenho, que podem ser úteis quando recolhe contadores de desempenho por processo. No exemplo abaixo, estamos a recolher tempo de processador e tempo de recolha de lixo por processo e, em seguida, 2 contadores de desempenho nos próprios nós usando variáveis. 

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

1. Depois de ter adicionado os contadores de desempenho adequados que precisam de ser recolhidos, precisa de atualizar o seu recurso de cluster para que estas alterações se reflitam no seu cluster de execução. Guarde o `template.json` seu PowerShell modificado e abra o PowerShell. Pode atualizar o `New-AzResourceGroupDeployment`seu cluster utilizando . A chamada requer o nome do grupo de recursos, o ficheiro de modelo atualizado e o ficheiro de parâmetros, e solicita ao Gestor de Recursos que eleve as alterações adequadas aos recursos que atualizou. Uma vez que você é assinado na sua conta e está na subscrição certa, use o seguinte comando para executar a atualização:

    ```sh
    New-AzResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

1. Uma vez que a atualização termine (demora entre 15 a 45 minutos dependendo se é a primeira implementação e o tamanho do seu grupo de recursos), a WAD deve estar a recolher os contadores de desempenho e a enviá-los para a mesa chamada WADPerformanceCountersTable na conta de armazenamento associada ao seu cluster. Consulte os seus contadores de desempenho em Insights de Aplicação [adicionando o AI Sink ao modelo de Gestor](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template)de Recursos .

## <a name="next-steps"></a>Passos seguintes
* Colete mais contadores de desempenho para o seu cluster. Consulte [as métricas](service-fabric-diagnostics-event-generation-perf.md) de desempenho para obter uma lista de contadores que deve recolher.
* [Utilize monitorização e diagnósticos com modelos de VM e Azure](../virtual-machines/windows/extensions-diagnostics-template.md) `WadCfg`Resource Manager do Windows para efazer novas modificações no seu , incluindo configurar contas de armazenamento adicionais para enviar dados de diagnóstico para.
* Visite o [construtor WadCfg](https://azure.github.io/azure-diagnostics-tools/config-builder/) para construir um modelo de raiz e certifique-se de que a sua sintaxe está correta. (parahttps://azure.github.io/azure-diagnostics-tools/config-builder/) construir um modelo de raiz e certificar-se de que a sua sintaxe está correta.
