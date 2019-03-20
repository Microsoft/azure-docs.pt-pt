---
title: O Azure Service Fabric - monitorização de desempenho com a extensão de diagnóstico do Windows Azure | Documentos da Microsoft
description: Utilize o Windows Azure Diagnostics para recolher contadores de desempenho para os seus clusters do Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 0ab14d41c149ec6e0ce76d24afb0e88a6af53935
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57884562"
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Monitorização de desempenho com a extensão de diagnóstico do Windows Azure

Este documento aborda os passos necessários para configurar a recolha de contadores de desempenho através da extensão do Windows Azure Diagnostics (WAD), para clusters do Windows. Para clusters do Linux, configurar o [agente do Log Analytics](service-fabric-diagnostics-oms-agent.md) para recolher contadores de desempenho para os nós. 

 > [!NOTE]
> A extensão WAD deve ser implementada no seu cluster para estes passos a trabalhar para si. Se ele não está configurado, aceda à [agregação de eventos e coleções com o Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md).  

## <a name="collect-performance-counters-via-the-wadcfg"></a>Recolher contadores de desempenho por meio do WadCfg

Para recolher contadores de desempenho por meio de WAD, terá de modificar a configuração adequadamente no modelo do Resource Manager do seu cluster. Siga estes passos para adicionar um contador de desempenho que pretende recolher ao seu modelo e executar uma atualização de recursos do Resource Manager.

1. Encontrar a configuração de WAD no modelo do seu cluster - encontrar `WadCfg`. Irá adicionar contadores de desempenho a recolher sob o `DiagnosticMonitorConfiguration`.

2. Definir a sua configuração para recolher contadores de desempenho ao adicionar a secção seguinte para sua `DiagnosticMonitorConfiguration`. 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    O `scheduledTransferPeriod` define a frequência com que os valores dos contadores que são recolhidos são transferidos para a tabela de armazenamento do Azure e em qualquer sink configurado. 

3. Adicionar os contadores de desempenho que pretende recolher para o `PerformanceCounterConfiguration` que foi declarado no passo anterior. Cada contador que pretende recolher é definida com um `counterSpecifier`, `sampleRate`, `unit`, `annotation`e qualquer relevantes `sinks`.

Eis um exemplo de uma configuração com o contador para o *Total de tempo do processador* (o período de tempo de CPU estava a ser utilizado para operações de processamento) e *invocações do método de Ator de recursos de infraestrutura do serviço por segundo*, um dos contadores de desempenho personalizados de Service Fabric. Consulte a [contadores de desempenho do Reliable Actor](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) e [contadores de desempenho do serviço fiável](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters) para obter uma lista completa dos contadores de desempenho personalizado do Service Fabric.

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

 A taxa de exemplo para o contador pode ser modificada de acordo com suas necessidades. O formato para o mesmo é `PT<time><unit>`, por isso, se pretender que o contador recolhido por cada segundo, em seguida, deve definir o `"sampleRate": "PT15S"`.

 Também pode utilizar variáveis no seu modelo ARM para recolher uma matriz de contadores de desempenho, que pode ser útil quando recolher os contadores de desempenho por processo. No exemplo abaixo, estamos a recolher o tempo de processador e tempo de coletor de lixo por processo e, em seguida, 2 contadores de desempenho em nós próprios através de todas as variáveis. 

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

 >[!NOTE]
 >Embora pode usar `*` para especificar grupos de contadores de desempenho que estão com o nome da mesma forma, envio de quaisquer contadores por meio de um coletor (para o Application Insights) requer que elas são declaradas individualmente. 

1. Depois de adicionar os contadores de desempenho adequado que têm de ser recolhidos, terá de atualizar o seu recurso de cluster para que estas alterações são refletidas no seu cluster em execução. Guardar sua modificado `template.json` e abra o PowerShell. Pode atualizar o seu cluster com `New-AzureRmResourceGroupDeployment`. A chamada requer o nome do grupo de recursos, o ficheiro de modelo atualizado e o ficheiro de parâmetros, pedidos e efetue as alterações necessárias para os recursos de que atualizou o Resource Manager. Depois de iniciado sessão na sua conta e a subscrição correta, utilize o seguinte comando para executar a atualização:

    ```sh
    New-AzureRmResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

1. Após a conclusão da atualização a implementar (demora entre 15 a 45 minutos consoante seja a primeira implementação e o tamanho do seu grupo de recursos), WAD deve ser recolher os contadores de desempenho e enviá-los para a tabela com o nome WADPerformanceCountersTable na conta de armazenamento associada com o seu cluster. Consulte os contadores de desempenho no Application Insights ao [adicionando o Sink de ia para o modelo do Resource Manager](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template).

## <a name="next-steps"></a>Passos Seguintes
* Recolha mais contadores de desempenho para o seu cluster. Ver [métricas de desempenho](service-fabric-diagnostics-event-generation-perf.md) para obter uma lista dos contadores que deve recolher.
* [Monitorização de utilização e diagnóstico com modelos de VM do Windows e do Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md) ainda mais fazer modificações nas suas `WadCfg`, incluindo a configuração de contas de armazenamento adicional para enviar dados de diagnóstico para.
* Visite o [WadCfg builder](https://azure.github.io/azure-diagnostics-tools/config-builder/) para criar um modelo a partir do zero e certificar-se de que a sintaxe está correta.