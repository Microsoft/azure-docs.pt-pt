---
title: Implementar e atualizar aplicações e serviços com o Azure Resource Manager | Documentos da Microsoft
description: Saiba como implementar aplicações e serviços em cluster do Service Fabric com um modelo Azure Resource Manager.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: dekapur
ms.openlocfilehash: e41647140373fcf637cad55af62764bd87826a62
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57849351"
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>Gerir aplicações e serviços como recursos do Azure Resource Manager

Pode implementar aplicações e serviços em cluster do Service Fabric através do Azure Resource Manager. Isso significa que, em vez de implementar e gerir aplicações através do PowerShell ou CLI depois de ter de esperar para o cluster estar pronto, pode agora express aplicações e serviços em JSON e implementá-los no mesmo modelo do Resource Manager em seu cluster. O processo de registo de aplicação, aprovisionamento e implementação de todos os acontece num único passo.

Esta é a forma recomendada para a implementação de qualquer programa de configuração, governação ou aplicativos de gerenciamento de cluster que necessita no seu cluster. Isto inclui a [Patch Orchestration Application](service-fabric-patch-orchestration-application.md), Watchdogs ou aplicações que têm de estar em execução no seu cluster antes de outros serviços ou aplicações serem implementados. 

Quando aplicável, gerir as suas aplicações como recursos do Resource Manager para melhorar:
* Registo de auditoria: Gestor de recursos todas as operações de auditorias e mantém uma detalhada *registo de atividades* que pode ajudá-lo a rastrear todas as alterações efetuadas a esses aplicativos e o seu cluster.
* Controlo de acesso baseado em funções (RBAC): Gerir o acesso aos clusters, bem como as aplicações implementadas no cluster pode ser feito por meio do mesmo modelo do Resource Manager.
* O Azure Resource Manager (através do portal do Azure) se torna um um--centralizar a gestão de cluster e implementações de aplicações críticas.

O fragmento seguinte mostra os diferentes tipos de recursos que podem ser geridos através de um modelo:

```json
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```


## <a name="add-a-new-application-to-your-resource-manager-template"></a>Adicionar uma nova aplicação ao modelo do Resource Manager

1. Prepare o modelo do Resource Manager do seu cluster para a implementação. Ver [criar um cluster do Service Fabric com o Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) para obter mais informações sobre isso.
2. Pense sobre algumas das aplicações que planeia implementar no cluster. Existem qualquer um que será sempre executado que outros aplicativos podem assumir dependências? Planeja implantar qualquer governação de cluster ou aplicações de programa de configuração? Esses tipos de aplicativos melhor geridos através de um modelo do Resource Manager, conforme mencionado acima. 
3. Assim que já descobriu que aplicações pretende ser implementado desta forma, as aplicações têm de ser empacotado, comprimido e colocar numa partilha de ficheiros. A partilha tem de ser acessíveis através de um ponto de final do REST para o Azure Resource Manager para consumir durante a implementação.
4. No modelo do Resource Manager, abaixo de sua declaração de cluster, descrevem propriedades de cada aplicativo. Essas propriedades incluem a contagem de instâncias ou de réplica e de quaisquer cadeias de dependência entre recursos (outros serviços ou aplicações). Para obter uma lista de propriedades completas, consulte a [especificações do Swagger da API REST](https://aka.ms/sfrpswaggerspec). Tenha em atenção que isto não substitui a aplicação ou serviço manifestos, mas em vez disso, descreve algumas das novidades no-los como parte do modelo do Resource Manager do cluster. Aqui está um modelo de exemplo que inclui a implantação de um serviço sem estado *Service1* e um serviço com estado *Service2* como parte da *Application1*:

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "clusterName": {
        "type": "string",
        "defaultValue": "Cluster",
        "metadata": {
          "description": "Name of your cluster - Between 3 and 23 characters. Letters and numbers only."
        }
      },
      "applicationTypeName": {
        "type": "string",
        "defaultValue": "ApplicationType",
        "metadata": {
          "description": "The application type name."
        }
      },
      "applicationTypeVersion": {
        "type": "string",
        "defaultValue": "1",
        "metadata": {
          "description": "The application type version."
        }
      },
      "appPackageUrl": {
        "type": "string",
        "metadata": {
          "description": "The URL to the application package sfpkg file."
        }
      },
      "applicationName": {
        "type": "string",
        "defaultValue": "Application1",
        "metadata": {
          "description": "The name of the application resource."
        }
      },
      "serviceName": {
        "type": "string",
        "defaultValue": "Application1~Service1",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName": {
        "type": "string",
        "defaultValue": "Service1Type",
        "metadata": {
          "description": "The name of the service type."
        }
      },
      "serviceName2": {
        "type": "string",
        "defaultValue": "Application1~Service2",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName2": {
        "type": "string",
        "defaultValue": "Service2Type",
        "metadata": {
          "description": "The name of the service type."
        }
      }
    },
    "variables": {
      "clusterLocation": "[resourcegroup().location]"
    },
    "resources": [
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [],
        "properties": {
          "provisioningState": "Default"
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "appPackageUrl": "[parameters('appPackageUrl')]"
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "typeName": "[parameters('applicationTypeName')]",
          "typeVersion": "[parameters('applicationTypeVersion')]",
          "parameters": {},
          "upgradePolicy": {
            "upgradeReplicaSetCheckTimeout": "01:00:00.0",
            "forceRestart": "false",
            "rollingUpgradeMonitoringPolicy": {
              "healthCheckWaitDuration": "00:02:00.0",
              "healthCheckStableDuration": "00:05:00.0",
              "healthCheckRetryTimeout": "00:10:00.0",
              "upgradeTimeout": "01:00:00.0",
              "upgradeDomainTimeout": "00:20:00.0"
            },
            "applicationHealthPolicy": {
              "considerWarningAsError": "false",
              "maxPercentUnhealthyDeployedApplications": "50",
              "defaultServiceTypeHealthPolicy": {
                "maxPercentUnhealthyServices": "50",
                "maxPercentUnhealthyPartitionsPerService": "50",
                "maxPercentUnhealthyReplicasPerPartition": "50"
              }
            }
          }
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateless",
          "serviceTypeName": "[parameters('serviceTypeName')]",
          "instanceCount": "-1",
          "partitionDescription": {
            "partitionScheme": "Singleton"
          },
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": []
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName2'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateful",
          "serviceTypeName": "[parameters('serviceTypeName2')]",
          "targetReplicaSetSize": "3",
          "minReplicaSetSize": "2",
          "replicaRestartWaitDuration": "00:01:00.0",
          "quorumLossWaitDuration": "00:02:00.0",
          "standByReplicaKeepDuration": "00:00:30.0",
          "partitionDescription": {
            "partitionScheme": "UniformInt64Range",
            "count": "5",
            "lowKey": "1",
            "highKey": "5"
          },
          "hasPersistedState": "true",
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": [],
          "defaultMoveCost": "Low"
        }
      }
    ]
   }
   ```

   > [!NOTE] 
   > O *apiVersion* tem de ser definido como `"2017-07-01-preview"`. Também é possível implementar este modelo independentemente do cluster, desde que o cluster já foi implementado.

5. Implemente! 

## <a name="manage-an-existing-application-via-resource-manager"></a>Gerir uma aplicação existente através do Resource Manager

Se o cluster já está ativo e obtém alguns aplicativos que gostaria de gerir recursos do Gestor de recursos já estão implementados na mesma, em vez de remover as aplicações e reimplementá-los, pode usar uma chamada PUT usando as mesmas APIs para que as aplicações confirmou como recursos do Resource Manager. 

> [!NOTE]
> Para permitir uma atualização de cluster para ignorar as aplicações de mau estado de funcionamento do cliente pode especificar "maxPercentUnhealthyApplications: 100" na secção" upgradeDescription/healthPolicy"; descrições detalhadas para todas as definições estão na [documentação de recursos de infraestrutura REST API Cluster atualizar política do serviço](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterupgradepolicy).

## <a name="next-steps"></a>Passos Seguintes

* Utilize o [CLI do Service Fabric](service-fabric-cli.md) ou [PowerShell](service-fabric-deploy-remove-applications.md) para implementar outras aplicações no seu cluster. 
* [Atualizar o seu cluster do Service Fabric](service-fabric-cluster-upgrade.md)

