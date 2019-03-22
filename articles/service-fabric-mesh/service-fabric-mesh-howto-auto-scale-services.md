---
title: Dimensionar automaticamente uma aplicação em execução no Azure Service Fabric em malha | Documentos da Microsoft
description: Saiba como configurar políticas de dimensionamento automático para os serviços de um aplicativo de malha do Service Fabric.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 2233dffabd7c76ca55cf215f8bc04e66134f5799
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58338807"
---
# <a name="create-autoscale-policies-for-a-service-fabric-mesh-application"></a>Criar políticas de dimensionamento automático para um aplicativo de malha do Service Fabric
Uma das principais vantagens da implementação de aplicações no Service Fabric Mesh é a capacidade de reduzir ou aumentar horizontalmente os seus serviços. Deve utilizar a redução ou o aumento para processar quantidades diferentes de carga nos serviços ou melhorar a disponibilidade. Pode dimensionar os seus serviços dentro ou para fora manualmente ou configurar políticas de dimensionamento automático.

[Dimensionamento automático](service-fabric-mesh-scalability.md#autoscaling-service-instances) permite-lhe dimensionar dinamicamente o número de instâncias de serviço (dimensionamento horizontal). Dimensionamento automático oferece excelente elasticidade e permite o aprovisionamento ou remoção de instâncias de serviço com base na utilização de CPU ou memória.

## <a name="options-for-creating-an-auto-scaling-policy-trigger-and-mechanism"></a>Opções para criar um dimensionamento de política, o acionador e o mecanismo de automático
Uma política de dimensionamento de automática é definida para cada serviço que pretende dimensionar. A política é definida no arquivo de recursos de serviço YAML ou o modelo de implementação de JSON. Cada política de dimensionamento consiste em duas partes: um acionador e um mecanismo de dimensionamento.

O acionador define quando uma política de dimensionamento automático é invocada.  Especifique o tipo de Acionador (carga média) e a métrica de monitorização (CPU ou memória).  Limiares de carga superior e inferior especificadas como uma percentagem. O intervalo de dimensionamento define a frequência de (em segundos), verifique a utilização especificada (por exemplo, a carga média da CPU) em todas as instâncias de serviço atualmente implementado.  O mecanismo é acionado quando a métrica monitorizada cai abaixo do limiar inferior ou aumenta acima do limiar superior.  

O mecanismo de dimensionamento define como realizar a operação de dimensionamento quando a política é acionada.  Especifique o tipo de mecanismo (Adicionar/remover réplica), a réplica mínima e máxima conta (como números inteiros).  O número de réplicas do serviço nunca será redimensionado abaixo a contagem mínima ou superior a contagem máxima.  Especifica também o incremento de dimensionamento como um inteiro, o que é o número de réplicas que vão ser adicionados ou removidos numa operação de dimensionamento.  

## <a name="define-an-auto-scaling-policy-in-a-json-template"></a>Definir uma política de dimensionamento num modelo JSON de automática

O exemplo seguinte mostra uma política de dimensionamento automático num modelo de implementação de JSON.  A política de dimensionamento automático é declarada numa propriedade do serviço de ser dimensionada.  Neste exemplo, é definido um acionador de carga média da CPU.  O mecanismo será acionado se a CPU média carregar de todas as remoções de instâncias implantadas abaixo 0,2 (20%) ou ficar acima 0,8 (80%).  A carga da CPU é verificada a cada 60 segundos.  O mecanismo de dimensionamento é definido para adicionar ou remover instâncias se a política é acionada.  Instâncias de serviço serão adicionadas ou removidas em incrementos de um.  Também é definida uma contagem de instâncias mínima de um e um número máximo de instâncias de 40.

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

## <a name="define-an-autoscale-policy-in-a-serviceyaml-resource-file"></a>Definir uma política de dimensionamento automático num arquivo de recurso service.yaml
O exemplo seguinte mostra uma política de dimensionamento automático num arquivo de recursos (YAML) do serviço.  A política de dimensionamento automático é declarada como uma propriedade do serviço de ser dimensionada.  Neste exemplo, é definido um acionador de carga média da CPU.  O mecanismo será acionado se a CPU média carregar de todas as remoções de instâncias implantadas abaixo 0,2 (20%) ou ficar acima 0,8 (80%).  A carga da CPU é verificada a cada 60 segundos.  O mecanismo de dimensionamento é definido para adicionar ou remover instâncias se a política é acionada.  Instâncias de serviço serão adicionadas ou removidas em incrementos de um.  Também é definida uma contagem de instâncias mínima de um e um número máximo de instâncias de 40.

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

## <a name="next-steps"></a>Passos Seguintes
Saiba como [Dimensionar manualmente um serviço](service-fabric-mesh-tutorial-template-scale-services.md)
