---
title: Escala automática de uma aplicação em funcionamento em Malha de Tecido de Serviço Azure
description: Saiba configurar políticas de escala automática para os serviços de uma aplicação de malha de tecido de serviço.
author: dkkapur
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: fb72806dd7ba838ba7170bda409715bc074e1d99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75461967"
---
# <a name="create-autoscale-policies-for-a-service-fabric-mesh-application"></a>Criar políticas de escala automática para uma aplicação de malha de tecido de serviço
Uma das principais vantagens de implementar aplicações para a Malha de Tecido de Serviço é a capacidade de você facilmente escalar os seus serviços dentro ou fora. Isto deve ser utilizado para lidar com quantidades variadas de carga nos seus serviços ou para melhorar a disponibilidade. Pode escalar manualmente os seus serviços para dentro ou para fora ou configurar políticas de autoscalcificação.

[A escala automática](service-fabric-mesh-scalability.md#autoscaling-service-instances) permite-lhe escalar dinamicamente o número das suas instâncias de serviço (escala horizontal). A escala automática proporciona uma grande elasticidade e permite o fornecimento ou remoção de instâncias de serviço com base na CPU ou na utilização da memória.

## <a name="options-for-creating-an-auto-scaling-policy-trigger-and-mechanism"></a>Opções para criar uma política de escalação automática, gatilho e mecanismo
Uma política de escala automática é definida para cada serviço que pretende escalar. A política é definida no ficheiro de recursos de serviço YAML ou no modelo de implementação JSON. Cada política de escala consiste em duas partes: um gatilho e um mecanismo de escala.

O gatilho define quando uma política de autoscalcificação é invocada.  Especifique o tipo de gatilho (carga média) e a métrica para monitorizar (CPU ou memória).  Limiares de carga superior e inferior especificados em percentagem. O intervalo de escala define com que frequência verificar (em segundos) a utilização especificada (como a carga média de CPU) em todas as instâncias de serviço atualmente implantadas.  O mecanismo é acionado quando a métrica monitorizada desce abaixo do limiar inferior ou aumenta acima do limiar superior.  

O mecanismo de escala define como executar a operação de escala quando a política é desencadeada.  Especifique o tipo de mecanismo (adicionar/remover réplica), as contagens mínimas e máximas de réplica (como inteiros).  O número de réplicas de serviço nunca será dimensionado abaixo da contagem mínima ou acima da contagem máxima.  Especifique também o incremento de escala como um inteiro, que é o número de réplicas que serão adicionadas ou removidas numa operação de escala.  

## <a name="define-an-auto-scaling-policy-in-a-json-template"></a>Defina uma política de escala automática num modelo JSON

O exemplo seguinte mostra uma política de autoscalcificação num modelo de implantação JSON.  A política de autoscalcificação é declarada numa propriedade do serviço a ser dimensionada.  Neste exemplo, é definido um gatilho de carga média cpU.  O mecanismo será acionado se a carga média de CPU de todas as instâncias implantadas descer abaixo de 0,2 (20%) ou vai acima de 0,8 (80%).  A carga do CPU é verificada a cada 60 segundos.  O mecanismo de escala é definido para adicionar ou remover instâncias se a política for desencadeada.  As instâncias de serviço serão adicionadas ou removidas em incrementos de um.  É também definida uma contagem mínima de um e uma contagem máxima de 40 instâncias.

```json
{
"apiVersion": "2018-09-01-preview",
"name": "WorkerApp",
"type": "Microsoft.ServiceFabricMesh/applications",
"location": "[parameters('location')]",
"dependsOn": [
"Microsoft.ServiceFabricMesh/networks/worker-app-network"
],
"properties": {
"services": [   
    { ... },       
    {
    "name": "WorkerService",
    "properties": {
        "description": "Worker Service",
        "osType": "linux",
        "codePackages": [
        {  ...              }
        ],
        "replicaCount": 1,
        "autoScalingPolicies": [
        {
            "name": "AutoScaleWorkerRule",
            "trigger": {
                "kind": "AverageLoad",
                "metric": {
                    "kind": "Resource",
                    "name": "cpu"
                },
                "lowerLoadThreshold": "0.2",
                "upperLoadThreshold": "0.8",
                "scaleIntervalInSeconds": "60"
            },
            "mechanism": {
                "kind": "AddRemoveReplica",
                "minCount": "1",
                "maxCount": "40",
                "scaleIncrement": "1"
            }
        }
        ],        
        ...
    }
    }
]
}
}
```

## <a name="define-an-autoscale-policy-in-a-serviceyaml-resource-file"></a>Defina uma política de escala automática num ficheiro de recursos service.yaml
O exemplo seguinte mostra uma política de autoscalcificação num ficheiro de recurso de serviço (YAML).  A política de autoscalcificação é declarada como uma propriedade do serviço a ser dimensionada.  Neste exemplo, é definido um gatilho de carga média cpU.  O mecanismo será acionado se a carga média de CPU de todas as instâncias implantadas descer abaixo de 0,2 (20%) ou vai acima de 0,8 (80%).  A carga do CPU é verificada a cada 60 segundos.  O mecanismo de escala é definido para adicionar ou remover instâncias se a política for desencadeada.  As instâncias de serviço serão adicionadas ou removidas em incrementos de um.  É também definida uma contagem mínima de um e uma contagem máxima de 40 instâncias.

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: WorkerApp
  properties:
    services:
      - name: WorkerService
        properties:
          description: WorkerService description.
          osType: Linux
          codePackages:
            ...
          replicaCount: 1
          autoScalingPolicies:
            - name: AutoScaleWorkerRule
              trigger:
                kind: AverageLoad
                metric:
                  kind: Resource
                  name: cpu
                lowerLoadThreshold: 0.2
                upperLoadThreshold: 0.8
                scaleIntervalInSeconds: 60
              mechanism:
                kind: AddRemoveReplica
                minCount: 1
                maxCount: 40
                scaleIncrement: 1
          ...
```

## <a name="next-steps"></a>Passos seguintes
Aprenda a [escalar manualmente um serviço](service-fabric-mesh-tutorial-template-scale-services.md)
