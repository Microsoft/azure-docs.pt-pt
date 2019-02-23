---
title: Utilizar um modelo para criar uma área de trabalho
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar um modelo Azure Resource Manager para criar uma área de trabalho de serviço do Azure Machine Learning nova.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: haining
author: hning86
ms.date: 02/11/2019
ms.openlocfilehash: d91cb2a98c6861695ffdc231bd979abaf24b2c6e
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669997"
---
# <a name="create-an-azure-machine-learning-service-workspace-by-using-a-template"></a>Criar uma área de trabalho do serviço do Azure Machine Learning utilizando um modelo

Neste artigo, irá aprender as várias formas de criar uma área de trabalho de serviço do Azure Machine Learning utilizando modelos Azure Resource Manager. 

Para obter mais informações, consulte [implementar uma aplicação com o modelo Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma **subscrição do Azure**. Se não tiver um, experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](http://aka.ms/AMLFree).

* Para utilizar um modelo a partir de uma CLI, é necessário um [do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) ou o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Modelo do Resource Manager

Um modelo do Resource Manager torna fácil criar recursos como uma operação única e coordenada. Um modelo é um documento JSON que define os recursos que são necessários para uma implementação. Ele também pode especificar parâmetros de implementação. Parâmetros são usados para fornecer valores de entrada ao utilizar o modelo.

O modelo seguinte pode ser utilizado para criar uma área de trabalho do serviço do Azure Machine Learning e os recursos do Azure associados:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "The name of the Azure Machine Learning service workspace."
            }
        },
        "location": {
            "type": "string",
            "allowedValues": [
                "eastus",
                "eastus2",
                "southcentralus",
                "southeastasia",
                "westcentralus",
                "westeurope",
                "westus2"
            ],
            "metadata": {
                "description": "The location where the workspace will be created."
            }
        }
    },
    "variables": {
        "storageAccount": {
            "name": "[concat('sa',uniqueString(resourceGroup().id))]",
            "type": "Standard_LRS"
        },
        "keyVault": {
            "name": "[concat('kv',uniqueString(resourceGroup().id))]",
            "tenantId": "[subscription().tenantId]"
        },
        "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]",
        "containerRegistryName": "[concat('cr',uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "name": "[variables('storageAccount').name]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2018-07-01",
            "sku": {
                "name": "[variables('storageAccount').type]"
            },
            "kind": "StorageV2",
            "properties": {
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        },
                        "file": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "supportHttpsTrafficOnly": true
            }
        },
        {
            "name": "[variables('keyVault').name]",
            "type": "Microsoft.KeyVault/vaults",
            "apiVersion": "2018-02-14",
            "location": "[parameters('location')]",
            "properties": {
                "tenantId": "[variables('keyVault').tenantId]",
                "sku": {
                    "name": "standard",
                    "family": "A"
                },
                "accessPolicies": []
            }
        },
        {
            "name": "[variables('applicationInsightsName')]",
            "type": "Microsoft.Insights/components",
            "apiVersion": "2015-05-01",
            "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
            "kind": "web",
            "properties": {
                "Application_Type": "web"
            }
        },
        {
            "name": "[variables('containerRegistryName')]",
            "type": "Microsoft.ContainerRegistry/registries",
            "apiVersion": "2017-10-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard"
            },
            "properties": {
                "adminUserEnabled": true
            }
        },
        {
            "name": "[parameters('workspaceName')]",
            "type": "Microsoft.MachineLearningServices/workspaces",
            "apiVersion": "2018-11-19",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[variables('storageAccount').name]",
                "[variables('keyVault').name]",
                "[variables('applicationInsightsName')]",
                "[variables('containerRegistryName')]"
            ],
            "identity": {
                "type": "systemAssigned"
            },
            "properties": {
                "friendlyName": "[parameters('workspaceName')]",
                "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVault').name)]",
                "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
                "containerRegistry": "[resourceId('Microsoft.ContainerRegistry/registries',variables('containerRegistryName'))]",
                "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccount').name)]"
            }
        }
    ]
}
```

Este modelo cria os seguintes serviços do Azure:

* Grupo de Recursos do Azure
* Conta de Armazenamento do Azure
* Azure Key Vault
* Azure Application Insights
* Registo de Contentores do Azure
* Área de trabalho do Azure Machine Learning

O grupo de recursos é o contentor que retém os serviços. Os serviços são necessárias para a área de trabalho do Azure Machine Learning.

O modelo de exemplo tem dois parâmetros:

* O **localização** onde serão criados o grupo de recursos e serviços.

    O modelo irá utilizar a localização que selecionou para a maioria dos recursos. A exceção é o serviço do Application Insights, que não está disponível em todas as localizações que são os outros serviços. Se selecionar uma localização para onde ele não está disponível, o serviço será criado na localização Centro-Sul.

* O **nome da área de trabalho**, que é o nome amigável da área de trabalho do Azure Machine Learning.

    Os nomes dos outros serviços são gerados aleatoriamente.

Para obter mais informações sobre modelos, consulte os artigos seguintes:

* [Criar modelos do Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md)
* [Implementar uma aplicação com modelos Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md)
* [Tipos de recursos de Microsoft.MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

1. Siga os passos em [implementar recursos de modelo personalizado](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Quando deparar-se com o __Editar modelo__ ecrã, cole o modelo a partir deste documento.
1. Selecione __guardar__ para utilizar o modelo. Forneça as seguintes informações e os Concordo com os listados termos e condições:

    * Subscrição: Selecione a subscrição do Azure a utilizar para estes recursos.
    * Grupo de recursos: Selecione ou crie um grupo de recursos para conter os serviços.
    * Nome da área de trabalho: O nome a utilizar para a área de trabalho do Azure Machine Learning que vai ser criada. O nome de área de trabalho tem de ter entre 3 e 33 carateres. Pode conter apenas carateres alfanuméricos e "-".
    * Localização: Selecione a localização onde os recursos serão criados.

    ![Os parâmetros de modelo no portal do Azure](media/how-to-create-workspace-template/template-parameters.png)

Para obter mais informações, consulte [implementar recursos de modelo personalizado](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

Este exemplo assume que guardou o modelo num arquivo chamado `azuredeploy.json` no diretório atual:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace"
```

Para obter mais informações, consulte [implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md) e [implementar privado modelo do Resource Manager com o SAS token e o Azure PowerShell](../../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="use-azure-cli"></a>Utilizar a CLI do Azure

Este exemplo assume que guardou o modelo num arquivo chamado `azuredeploy.json` no diretório atual:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace
```

Para obter mais informações, consulte [implementar recursos com modelos do Resource Manager e o Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md) e [implementar privado modelo do Resource Manager com CLI do Azure e o SAS token](../../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="next-steps"></a>Passos Seguintes

* [Implementar recursos com modelos do Resource Manager e API de REST do Resource Manager](../../azure-resource-manager/resource-group-template-deploy-rest.md).
* [Criar e implementar grupos de recursos do Azure através do Visual Studio](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
