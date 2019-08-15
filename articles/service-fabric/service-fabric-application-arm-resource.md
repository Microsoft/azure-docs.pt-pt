---
title: Implantar e atualizar aplicativos e serviços com o Azure Resource Manager | Microsoft Docs
description: Saiba como implantar aplicativos e serviços em um Cluster Service Fabric usando um modelo de Azure Resource Manager.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: atsenthi
ms.openlocfilehash: 3810afa7ad00aa731751aa1f0bfe38d503de5850
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68953210"
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>Gerenciar aplicativos e serviços como Azure Resource Manager recursos

Pode implementar serviços e aplicações no seu cluster do Service Fabric através do Azure Resource Manager. Isso significa que, em vez de implantar e gerenciar aplicativos por meio do PowerShell ou da CLI depois de esperar que o cluster esteja pronto, agora você pode expressar aplicativos e serviços em JSON e implantá-los no mesmo modelo do Resource Manager que o cluster. Pode realizar o processo de registo, aprovisionamento e implementação de aplicações com um único passo.

Essa é a maneira recomendada para implantar qualquer configuração, governança ou aplicativos de gerenciamento de cluster que você precisa em seu cluster. Isso inclui o [aplicativo](service-fabric-patch-orchestration-application.md)de orquestração de patches, Watchdogs ou quaisquer aplicativos que precisam estar em execução no cluster antes que outros aplicativos ou serviços sejam implantados. 

Quando aplicável, gerencie seus aplicativos como recursos do Resource Manager para melhorar:
* Trilha de auditoria: O Resource Manager audita cada operação e mantém um *log de atividades* detalhado que pode ajudá-lo a rastrear todas as alterações feitas nesses aplicativos e no cluster.
* RBAC (controle de acesso baseado em função): O gerenciamento de acesso a clusters, bem como aplicativos implantados no cluster, pode ser feito por meio do mesmo modelo do Resource Manager.
* Azure Resource Manager (via portal do Azure) se torna uma loja única para gerenciar o cluster e implantações de aplicativos críticos.

O trecho a seguir mostra os diferentes tipos de recursos que podem ser gerenciados por meio de um modelo:

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```


## <a name="add-a-new-application-to-your-resource-manager-template"></a>Adicionar um novo aplicativo ao seu modelo do Resource Manager

1. Prepare o modelo do Resource Manager do cluster para implantação. Consulte [criar um cluster Service Fabric usando Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) para obter mais informações sobre isso.
2. Pense em alguns dos aplicativos que você planeja implantar no cluster. Há algum que sempre estará executando que outros aplicativos possam assumir dependências? Você planeja implantar qualquer controle de cluster ou aplicativos de instalação? Esses tipos de aplicativos são melhor gerenciados por meio de um modelo do Resource Manager, conforme discutido acima. 
3. Depois de descobrir quais aplicativos você deseja implantar dessa maneira, os aplicativos precisam ser empacotados, compactados e colocados em um compartilhamento de arquivos. O compartilhamento precisa ser acessível por meio de um ponto de extremidade REST para Azure Resource Manager consumir durante a implantação.
4. No modelo do Resource Manager, abaixo da declaração de cluster, descreva as propriedades de cada aplicativo. Essas propriedades incluem a contagem de réplicas ou de instâncias e quaisquer cadeias de dependência entre recursos (outros aplicativos ou serviços). Para obter uma lista de propriedades abrangentes, consulte a [especificação Swagger da API REST](https://aka.ms/sfrpswaggerspec). Observe que isso não substitui os manifestos do aplicativo ou do serviço, mas descreve alguns dos que estão neles como parte do modelo do Resource Manager do cluster. Aqui está um modelo de exemplo que inclui a implantação de um Service *Service1* sem estado e um serviço com estado *Service2* como parte do *Application1*:

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
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [],
        "properties": {
          "provisioningState": "Default"
        }
      },
      {
        "apiVersion": "2019-03-01",
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
        "apiVersion": "2019-03-01",
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
        "apiVersion": "2019-03-01",
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
        "apiVersion": "2019-03-01",
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
   > O *apiVersion* deve ser definido como `"2019-03-01"`. Esse modelo também pode ser implantado independentemente do cluster, desde que o cluster já tenha sido implantado.

5. Instalação! 

## <a name="remove-service-fabric-resource-provider-application-resource"></a>Remover Service Fabric recurso de aplicativo do provedor de recursos
O seguinte irá disparar o pacote do aplicativo para ser desprovisionado do cluster, e isso limpará o espaço em disco usado:
```powershell
Get-AzureRmResource -ResourceId /subscriptions/{sid}/resourceGroups/{rg}/providers/Microsoft.ServiceFabric/clusters/{cluster}/applicationTypes/{apptType}/versions/{version} -ApiVersion "2019-03-01" | Remove-AzureRmResource -Force -ApiVersion "2017-07-01-preview"
```
A simples remoção de Microsoft. infabric/clusters/aplicativo do modelo do ARM não desprovisionará o aplicativo

>[!NOTE]
> Depois que a remoção for concluída, você não verá mais a versão do pacote no SFX ou no ARM. Não é possível excluir o recurso de versão do tipo de aplicativo com o qual o aplicativo está sendo executado; O ARM/SFRP impedirá isso. Se você tentar desprovisionar o pacote em execução, o runtime do it o impedirá.


## <a name="manage-an-existing-application-via-resource-manager"></a>Gerenciar um aplicativo existente por meio do Resource Manager

Se o cluster já estiver ativo e alguns aplicativos que você gostaria de gerenciar como recursos do Resource Manager já estiverem implantados nele, em vez de remover os aplicativos e reimplantá-los, você poderá usar uma chamada PUT usando as mesmas APIs para que os aplicativos obtenham reconhecido como recursos do Resource Manager. 

> [!NOTE]
> Para permitir que uma atualização de cluster ignore aplicativos não íntegros, o cliente pode especificar "maxPercentUnhealthyApplications: 100 "na seção" upgradeDescription/healthPolicy "; descrições detalhadas de todas as configurações estão na [documentação da política de atualização de cluster da API REST](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterupgradepolicy)de malhas de serviço.

## <a name="next-steps"></a>Passos Seguintes

* Use a [CLI do Service Fabric](service-fabric-cli.md) ou o [PowerShell](service-fabric-deploy-remove-applications.md) para implantar outros aplicativos em seu cluster. 
* [Atualizar seu cluster de Service Fabric](service-fabric-cluster-upgrade.md)

