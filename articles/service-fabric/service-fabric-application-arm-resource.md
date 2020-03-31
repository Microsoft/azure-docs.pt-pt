---
title: Implementar e atualizar com o Gestor de Recursos Azure
description: Aprenda a implementar aplicações e serviços para um cluster de Tecido de Serviço usando um modelo de Gestor de Recursos Azure.
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: a2dfe54bf2c6b4fa8814f10c10576a73727a7417
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610255"
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>Gerir aplicações e serviços como recursos do Gestor de Recursos Azure

Pode implementar aplicações e serviços no seu cluster Service Fabric através do Azure Resource Manager. Isto significa que, em vez de implementar e gerir aplicações via PowerShell ou CLI depois de ter de esperar que o cluster esteja pronto, pode agora expressar aplicações e serviços na JSON e implantá-las no mesmo modelo de Gestor de Recursos que o seu cluster. O processo de registo de candidaturas, provisionamento e implantação acontece num só passo.

Esta é a forma recomendada de implementar quaisquer aplicações de configuração, governação ou gestão de clusters que você precisa no seu cluster. Isto inclui a Aplicação de [Orquestração de Patch,](service-fabric-patch-orchestration-application.md)Watchdogs ou quaisquer aplicações que precisem de ser implementadas no seu cluster antes de outras aplicações ou serviços serem implementados. 

Quando aplicável, gerencie as suas aplicações como Recursos Gestores de Recursos para melhorar:
* Pista de auditoria: O Gestor de Recursos audita todas as operações e mantém um Registo de *Atividadedetalhado* que pode ajudá-lo a rastrear quaisquer alterações feitas a estas aplicações e ao seu cluster.
* Controlo de acesso baseado em funções (RBAC): Gerir o acesso a clusters, bem como aplicações implementadas no cluster, pode ser feito através do mesmo modelo de Gestor de Recursos.
* O Azure Resource Manager (via portal Azure) torna-se um balcão único para gerir o seu cluster e implementações críticas de aplicações.

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

1. Prepare o modelo de Gestor de Recursos do seu cluster para a implantação. Consulte criar um cluster de tecido de serviço utilizando o Gestor de [Recursos Azure](service-fabric-cluster-creation-via-arm.md) para obter mais informações sobre o mesmo.
2. Pense em algumas das aplicações que planeia implementar no cluster. Há alguma que esteja sempre a funcionar que outras aplicações possam assumir dependências? Planeia implementar alguma governação de clusterou aplicações de configuração? Este tipo de aplicações são melhor geridas através de um modelo de Gestor de Recursos, como discutido acima. 
3. Depois de ter descoberto que aplicações pretende ser implementadadesta forma, as aplicações têm de ser embaladas, zipped e colocadas numa partilha de ficheiros. A parte precisa de ser acessível através de um ponto final REST para o Gestor de Recursos Azure consumir durante a implantação.
4. No seu modelo de Gestor de Recursos, abaixo da sua declaração de cluster, descrevê-lo as propriedades de cada aplicação. Estas propriedades incluem a contagem de réplicas ou instâncias e quaisquer cadeias de dependência entre recursos (outras aplicações ou serviços). Para obter uma lista de propriedades abrangentes, consulte o [REST API Swagger Spec](https://aka.ms/sfrpswaggerspec). Note que isso não substitui os manifestos de Aplicação ou Serviço, mas sim descreve parte do que está neles como parte do modelo de Gestor de Recursos do cluster. Aqui está um modelo de amostra que inclui a implementação de um serviço apátrida *Service1* e um serviço de serviço estatal *Service2* como parte da *Aplicação1:*

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
   > A *apiVersão* deve `"2019-03-01"`ser definida para . Este modelo também pode ser implantado independentemente do cluster, desde que o cluster já tenha sido implantado.

5. A desloque-se! 

## <a name="remove-service-fabric-resource-provider-application-resource"></a>Remover o recurso de aplicação de aplicação de recursos de tecido de serviço
O seguinte irá desencadear o pacote de aplicações para não ser aprovisionado a partir do cluster, e isso limpará o espaço do disco utilizado:
```powershell
Get-AzureRmResource -ResourceId /subscriptions/{sid}/resourceGroups/{rg}/providers/Microsoft.ServiceFabric/clusters/{cluster}/applicationTypes/{apptType}/versions/{version} -ApiVersion "2019-03-01" | Remove-AzureRmResource -Force -ApiVersion "2017-07-01-preview"
```
Simplesmente remover microsoft.ServiceFabric/clusters/aplicação do seu modelo ARM não irá desfornecer a Aplicação

>[!NOTE]
> Uma vez concluída a remoção, não deve ver mais a versão do pacote em SFX ou ARM. Não é possível eliminar o recurso da versão do tipo de aplicação com o que a aplicação está a executar; A ARM/SFRP impedirá isto. Se tentar desfornecer o pacote de funcionamento, o tempo de funcionamento da SF impedirá-o.


## <a name="manage-an-existing-application-via-resource-manager"></a>Gerir uma aplicação existente via Gestor de Recursos

Se o seu cluster já estiver em alta e algumas aplicações que gostaria de gerir como recursos do Gestor de Recursos já estão implantados no mesmo, em vez de remover as aplicações e reimplantá-las, você pode usar uma chamada PUT usando as mesmas APIs para que as aplicações obtivessem reconhecidocomo Recursos Gestor de Recursos recursos. Para mais informações, consulte o modelo de recurso de [aplicação service Fabric?](https://docs.microsoft.com/azure/service-fabric/service-fabric-concept-resource-model)

> [!NOTE]
> Para permitir que uma atualização de cluster ignore aplicações pouco saudáveis, o cliente pode especificar "maxPercentUnhealthyApplications: 100" na secção "upgradeDescription/healthPolicy"; Descrições detalhadas para todas as definições estão na documentação da política de atualização do cluster de [tecidos de serviço REST API](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterupgradepolicy).

## <a name="next-steps"></a>Passos seguintes

* Utilize o Tecido de [Serviço CLI](service-fabric-cli.md) ou [PowerShell](service-fabric-deploy-remove-applications.md) para implementar outras aplicações para o seu cluster. 
* [Atualize o seu cluster de Tecido de Serviço](service-fabric-cluster-upgrade.md)

