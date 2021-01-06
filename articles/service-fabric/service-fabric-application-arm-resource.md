---
title: Implementar e atualizar com o Azure Resource Manager
description: Saiba como implementar aplicações e serviços num cluster de Tecidos de Serviço utilizando um modelo de Gestor de Recursos Azure.
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: ed6bc7d96cb3ea0934929e6543c5e637a9f42c1f
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97930842"
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>Gerir aplicações e serviços como recursos do Gestor de Recursos Azure

Pode implementar aplicações e serviços no seu cluster de Tecidos de Serviço através do Azure Resource Manager. Isto significa que, em vez de implementar e gerir aplicações através do PowerShell ou do CLI depois de ter de esperar que o cluster esteja pronto, pode agora expressar aplicações e serviços no JSON e implantá-las no mesmo modelo de Gestor de Recursos que o seu cluster. O processo de registo de pedidos, provisionamento e implantação acontece numa só etapa.

Esta é a forma recomendada para implementar quaisquer aplicações de configuração, governação ou gestão de clusters que necessite no seu cluster. Isto inclui a [Aplicação de Orquestração de Patch,](service-fabric-patch-orchestration-application.md)Watchdogs ou quaisquer aplicações que precisem de ser executadas no seu cluster antes de serem implementadas outras aplicações ou serviços. 

Quando aplicável, gerencie as suas aplicações como recursos do Gestor de Recursos para melhorar:
* Pista de auditoria: Gestor de Recursos audita cada operação e mantém um Registo de *Atividades* detalhado que pode ajudá-lo a rastrear quaisquer alterações feitas a estas aplicações e ao seu cluster.
* Controlo de acesso baseado em funções (Azure RBAC): Gerir o acesso a clusters, bem como aplicações implementadas no cluster, pode ser feito através do mesmo modelo de Gestor de Recursos.
* O Azure Resource Manager (via portal Azure) torna-se um balcão único para gerir o seu cluster e implementações de aplicações críticas.

O seguinte corte mostra os diferentes tipos de recursos que podem ser geridos através de um modelo:

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

## <a name="add-a-new-application-to-your-resource-manager-template"></a>Adicione uma nova aplicação ao seu modelo de Gestor de Recursos

1. Prepare o modelo de Gestor de Recursos do seu cluster para a implementação. Consulte [Criar um cluster de tecido de serviço utilizando o Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) para obter mais informações sobre este.
2. Pense em algumas das aplicações que planeia implementar no cluster. Há alguma que esteja sempre a funcionar que outras aplicações possam ter dependências? Planeia implementar qualquer candidatura de governação de clusters ou de configuração? Este tipo de aplicações são melhor geridas através de um modelo de Gestor de Recursos, como discutido acima. 
3. Depois de ter descoberto quais as aplicações que pretende ser implementada desta forma, as aplicações têm de ser embaladas, fechadas e colocadas numa parte de armazenamento. A partilha precisa de ser acessível através de um ponto final REST para o Azure Resource Manager consumir durante a implementação. Consulte [criar uma conta de armazenamento](service-fabric-concept-resource-model.md#create-a-storage-account) para obter detalhes.
4. No seu modelo de Gestor de Recursos, abaixo da declaração do cluster, descreva as propriedades de cada aplicação. Estas propriedades incluem réplica ou contagem de instâncias e quaisquer cadeias de dependência entre recursos (outras aplicações ou serviços). Note que isto não substitui os manifestos de Aplicação ou Serviço, mas descreve parte do que está neles como parte do modelo de Gestor de Recursos do cluster. Aqui está um modelo de amostra que inclui a implementação de um serviço apátrida *Service1* e um serviço stateful *Service2* como parte da *Aplicação1*:

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
   > Consulte a referência do Service Fabric [Azure Resource Manager](/azure/templates/microsoft.servicefabric/clusters/applicationtypes) para encontrar utilização e detalhes sobre as propriedades individuais do modelo.

5. Desdobre-se! 

## <a name="remove-service-fabric-resource-provider-application-resource"></a>Remover recurso de aplicação do fornecedor de recursos de tecido de serviço
O seguinte irá desencadear o pacote de aplicações a não ser aprovisionado a partir do cluster, e isso irá limpar o espaço do disco utilizado:
```powershell
Get-AzureRmResource -ResourceId /subscriptions/{sid}/resourceGroups/{rg}/providers/Microsoft.ServiceFabric/clusters/{cluster}/applicationTypes/{apptType}/versions/{version} -ApiVersion "2019-03-01" | Remove-AzureRmResource -Force -ApiVersion "2017-07-01-preview"
```
Simplesmente remover Microsoft.ServiceFabric/clusters/aplicação do seu modelo ARM não irá desprovisionar a Aplicação

>[!NOTE]
> Uma vez concluída a remoção, não deverá mais ver a versão do pacote em SFX ou ARM. Não é possível eliminar o recurso de versão tipo de aplicação com o qual a aplicação está a ser executada; A ARM/SFRP evitará isto. Se tentar desprovisionar o pacote de funcionamento, o tempo de execução da SF irá impedi-lo.


## <a name="manage-an-existing-application-via-resource-manager"></a>Gerir uma aplicação existente através do Gestor de Recursos

Se o seu cluster já está em funcionamento e algumas aplicações que gostaria de gerir como recursos do Gestor de Recursos já estão implantadas no mesmo, em vez de remover as aplicações e reimplantá-las, pode utilizar uma chamada PUT usando as mesmas APIs para que as aplicações sejam reconhecidas como recursos do Gestor de Recursos. Para obter informações adicionais, consulte [o modelo de recurso de aplicação do Tecido de Serviço?](./service-fabric-concept-resource-model.md)

> [!NOTE]
> Para permitir que uma atualização de cluster ignore aplicações não saudáveis, o cliente pode especificar "maxPercentUnhealthyApplications: 100" na secção "upgradeDescription/healthPolicy"; descrições detalhadas para todas as configurações estão na [documentação da Política de Upgrade do Cluster API dos Tecidos](/rest/api/servicefabric/sfrp-model-clusterupgradepolicy)de Serviço .

## <a name="next-steps"></a>Passos seguintes

* Utilize o [Tecido de Serviço CLI](service-fabric-cli.md) ou [PowerShell](service-fabric-deploy-remove-applications.md) para implementar outras aplicações no seu cluster. 
* [Atualize o seu cluster de tecido de serviço](service-fabric-cluster-upgrade.md)
