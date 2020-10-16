---
title: Escala automática uma aplicação em execução na malha de tecido de serviço Azure
description: Saiba como configurar políticas de auto-escala para os serviços de uma aplicação de malha de tecido de serviço.
author: georgewallace
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: f65fcfa76069a3de37fd3a76e38e38fba40e04ac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843061"
---
# <a name="create-autoscale-policies-for-a-service-fabric-mesh-application"></a>Criar políticas de autoescala para uma aplicação de malha de tecido de serviço
Uma das principais vantagens de implementar aplicações para a Rede de Tecidos de Serviço é a capacidade de escalar facilmente os seus serviços dentro ou fora. Isto deve ser usado para lidar com quantidades variadas de carga nos seus serviços ou melhorar a disponibilidade. Pode escalar manualmente os seus serviços dentro ou fora ou definir políticas de autoscalagem.

[O dimensionamento automático](service-fabric-mesh-scalability.md#autoscaling-service-instances) permite-lhe escalar dinamicamente o número de instâncias de serviço (escala horizontal). A escala automática proporciona uma grande elasticidade e permite o fornecimento ou remoção de instâncias de serviço com base na CPU ou na utilização da memória.

## <a name="options-for-creating-an-auto-scaling-policy-trigger-and-mechanism"></a>Opções para criar uma política de escala, gatilho e mecanismo de auto-escala
Uma política de escala automática é definida para cada serviço que pretende escalar. A política é definida no ficheiro de recursos de serviço YAML ou no modelo de implementação JSON. Cada política de escala é constituída por duas partes: um gatilho e um mecanismo de escala.

O gatilho define quando uma política de autoscalagem é invocada.  Especifique o tipo de gatilho (carga média) e a métrica para monitorizar (CPU ou memória).  Limiares de carga superior e inferior especificados em percentagem. O intervalo de escala define a frequência de verificar (em segundos) a utilização especificada (tal como a carga média de CPU) em todas as instâncias de serviço atualmente implantadas.  O mecanismo é acionado quando a métrica monitorizada desce abaixo do limiar inferior ou aumenta acima do limiar superior.  

O mecanismo de escala define como executar a operação de escala quando a política é desencadeada.  Especificar o tipo de mecanismo (adicionar/remover réplica), as contagens de réplica mínima e máxima (como inteiros).  O número de réplicas de serviço nunca será dimensionado abaixo da contagem mínima ou acima da contagem máxima.  Especificar também o incremento de escala como um inteiro, que é o número de réplicas que serão adicionadas ou removidas numa operação de escala.  

## <a name="define-an-auto-scaling-policy-in-a-json-template"></a>Defina uma política de escalagem automática num modelo JSON

O exemplo a seguir mostra uma política de autoscalagem num modelo de implementação JSON.  A política de autoscalagem é declarada numa propriedade do serviço a ser dimensionada.  Neste exemplo, é definido um gatilho de carga médio do CPU.  O mecanismo será acionado se a carga média de CPU de todas as instâncias implantadas cair abaixo de 0,2 (20%) ou vai acima de 0,8 (80%).  A carga do CPU é verificada a cada 60 segundos.  O mecanismo de escala é definido para adicionar ou remover instâncias se a política for ativada.  As instâncias de serviço serão adicionadas ou removidas em incrementos de um.  É também definida uma contagem mínima de um caso e uma contagem máxima de 40.

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

## <a name="define-an-autoscale-policy-in-a-serviceyaml-resource-file"></a>Defina uma política de escala automática num ficheiro de recursos do serviço.yaml
O exemplo a seguir mostra uma política de autoscalagem num ficheiro de recurso de serviço (YAML).  A política de autoscalagem é declarada como uma propriedade do serviço a ser dimensionada.  Neste exemplo, é definido um gatilho de carga médio do CPU.  O mecanismo será acionado se a carga média de CPU de todas as instâncias implantadas cair abaixo de 0,2 (20%) ou vai acima de 0,8 (80%).  A carga do CPU é verificada a cada 60 segundos.  O mecanismo de escala é definido para adicionar ou remover instâncias se a política for ativada.  As instâncias de serviço serão adicionadas ou removidas em incrementos de um.  É também definida uma contagem mínima de um caso e uma contagem máxima de 40.

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
Saiba como [escalar manualmente um serviço](service-fabric-mesh-tutorial-template-scale-services.md)
