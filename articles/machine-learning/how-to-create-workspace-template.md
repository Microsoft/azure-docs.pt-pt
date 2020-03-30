---
title: Criar um espaço de trabalho com modelo de Gestor de Recursos Azure
titleSuffix: Azure Machine Learning
description: Aprenda a usar um modelo de Gestor de Recursos Azure para criar um novo espaço de trabalho azure machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/05/2020
ms.custom: seoapril2019
ms.openlocfilehash: 9403cc05ed5b31f3b76c16c4232506e2ddc5da2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402920"
---
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]
<br>

# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Use um modelo de Gestor de Recursos Azure para criar um espaço de trabalho para o Azure Machine Learning

Neste artigo, você aprende várias maneiras de criar um espaço de trabalho Azure Machine Learning usando modelos de Gestor de Recursos Azure. Um modelo de Gestor de Recursos facilita a criação de recursos como uma única operação coordenada. Um modelo é um documento JSON que define os recursos necessários para uma implantação. Também pode especificar parâmetros de implantação. Os parâmetros são utilizados para fornecer valores de entrada ao utilizar o modelo.

Para mais informações, consulte [Implementar uma aplicação com o modelo de Gestor](../azure-resource-manager/templates/deploy-powershell.md)de Recursos Azure .

## <a name="prerequisites"></a>Pré-requisitos

* Uma **subscrição Azure.** Se não tiver uma, experimente a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

* Para utilizar um modelo a partir de um CLI, precisa do [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) ou do [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Modelo do Resource Manager

O seguinte modelo de Gestor de Recursos pode ser usado para criar um espaço de trabalho Azure Machine Learning e recursos Azure associados:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

Este modelo cria os seguintes serviços Azure:

* Grupo de Recursos Azure
* Conta de Armazenamento do Azure
* Azure Key Vault
* Azure Application Insights
* Registo de Contentores do Azure
* Área de trabalho do Azure Machine Learning

O grupo de recursos é o contentor que detém os serviços. Os vários serviços são exigidos pelo espaço de trabalho Azure Machine Learning.

O modelo de exemplo tem dois parâmetros:

* O **local** onde o grupo de recursos e os serviços serão criados.

    O modelo utilizará a localização que seleccionarpara a maioria dos recursos. A exceção é o serviço Application Insights, que não está disponível em todos os locais que os outros serviços são. Se selecionar um local onde não esteja disponível, o serviço será criado na localização do Centro Sul dos EUA.

* O **nome do espaço**de trabalho , que é o nome amigável do espaço de trabalho Azure Machine Learning.

    > [!NOTE]
    > O nome do espaço de trabalho é insensível a casos.

    Os nomes dos outros serviços são gerados aleatoriamente.

> [!TIP]
> Enquanto o modelo associado a este documento cria um novo Registo de Contentores Azure, também pode criar um novo espaço de trabalho sem criar um registo de contentores. Um será criado quando executar uma operação que requer um registo de contentores. Por exemplo, treinar ou implementar um modelo.
>
> Também pode fazer referência a uma conta de registo ou armazenamento de contentores existente no modelo do Gestor de Recursos Azure, em vez de criar uma nova.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

Para obter mais informações sobre os modelos, consulte os seguintes artigos:

* [Modelos de Gestor de Recursos Do Autor Azure](../azure-resource-manager/templates/template-syntax.md)
* [Implementar uma aplicação com modelos de Gestor de Recursos Azure](../azure-resource-manager/templates/deploy-powershell.md)
* [Microsoft.MachineLearningServices tipos de recursos](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

### <a name="advanced-template"></a>Modelo avançado

O seguinte modelo de exemplo demonstra como criar um espaço de trabalho com três configurações:

* Ativar configurações de alta confidencialidade para o espaço de trabalho
* Ativar encriptação para o espaço de trabalho
* Usa um cofre de chaves azure existente

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the Azure Machine Learning workspace."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus",
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
        "description": "Specifies the location for all resources."
      }
    },
    "sku":{
      "type": "string",
      "defaultValue": "basic",
      "allowedValues": [
        "basic",
        "enterprise"
      ],
      "metadata": {
        "description": "Specifies the sku, also referred to as 'edition' of the Azure Machine Learning workspace."
      }
    },
    "hbi_workspace":{
      "type": "string",
      "defaultValue": "false",
      "allowedValues": [
        "false",
        "true"
      ],
      "metadata": {
        "description": "Specifies that the Azure Machine Learning workspace holds highly confidential data."
      }
    },
    "encryption_status":{
      "type": "string",
      "defaultValue": "Disabled",
      "allowedValues": [
        "Enabled",
        "Disabled"
      ],
      "metadata": {
        "description": "Specifies if the Azure Machine Learning workspace should be encrypted with the customer managed key."
      }
    },
    "cmk_keyvault":{
      "type": "string",
      "metadata": {
        "description": "Specifies the customer managed keyvault Resource Manager ID."
      }
    },
    "resource_cmk_uri":{
      "type": "string",
      "metadata": {
        "description": "Specifies the customer managed keyvault key uri."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('sa',uniqueString(resourceGroup().id))]",
    "storageAccountType": "Standard_LRS",
    "keyVaultName": "[concat('kv',uniqueString(resourceGroup().id))]",
    "tenantId": "[subscription().tenantId]",
    "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]",
    "containerRegistryName": "[concat('cr',uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2018-07-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[variables('storageAccountType')]"
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
        "supportsHttpsTrafficOnly": true
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2018-02-14",
      "name": "[variables('keyVaultName')]",
      "location": "[parameters('location')]",
      "properties": {
        "tenantId": "[variables('tenantId')]",
        "sku": {
          "name": "standard",
          "family": "A"
        },
        "accessPolicies": []
      }
    },
    {
      "type": "Microsoft.Insights/components",
      "apiVersion": "2015-05-01",
      "name": "[variables('applicationInsightsName')]",
      "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
      "kind": "web",
      "properties": {
        "Application_Type": "web"
      }
    },
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2017-10-01",
      "name": "[variables('containerRegistryName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "adminUserEnabled": true
      }
    },
    {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2020-01-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",
        "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]",
        "[resourceId('Microsoft.ContainerRegistry/registries', variables('containerRegistryName'))]"
      ],
      "identity": {
        "type": "systemAssigned"
      },
      "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
      },
      "properties": {
        "friendlyName": "[parameters('workspaceName')]",
        "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVaultName'))]",
        "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
        "containerRegistry": "[resourceId('Microsoft.ContainerRegistry/registries',variables('containerRegistryName'))]",
        "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]",
         "encryption": {
                "status": "[parameters('encryption_status')]",
                "keyVaultProperties": {
                    "keyVaultArmId": "[parameters('cmk_keyvault')]",
                    "keyIdentifier": "[parameters('resource_cmk_uri')]"
                  }
            },
        "hbi_workspace": "[parameters('hbi_workspace')]"
      }
    }
  ]
}
```

Para obter a identificação do Cofre chave, e a chave URI necessária por este modelo, você pode usar o Azure CLI. O seguinte comando é um exemplo de utilização do CLI Azure para obter o ID de recurso key vault e URI:

```azurecli-interactive
az keyvault show --name mykeyvault --resource-group myresourcegroup --query "[id, properties.vaultUri]"
```

Este comando devolve um valor semelhante ao seguinte texto. O primeiro valor é o ID e o segundo é o URI:

```text
[
  "/subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault",
  "https://mykeyvault.vault.azure.net/"
]
```

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

1. Siga os passos em [Recursos de implantação a partir de modelo personalizado](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Quando chegar ao ecrã do __modelo Editar,__ cola no modelo deste documento.
1. Selecione __Guardar__ para utilizar o modelo. Forneça as seguintes informações e concorde com os termos e condições listados:

   * Subscrição: Selecione a subscrição Azure para utilizar para estes recursos.
   * Grupo de recursos: Selecione ou crie um grupo de recursos para conter os serviços.
   * Nome do espaço de trabalho: O nome a utilizar para o espaço de trabalho Azure Machine Learning que será criado. O nome do espaço de trabalho deve estar entre 3 e 33 caracteres. Só pode conter caracteres alfanuméricos e "-".
   * Localização: Selecione a localização onde serão criados os recursos.

Para mais informações, consulte [Implementar recursos a partir de modelo personalizado](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

Este exemplo pressupõe que guardou o `azuredeploy.json` modelo para um ficheiro nomeado no diretório atual:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace" -sku "basic"
```

Para mais informações, consulte [a implantação de recursos com modelos de Gestor de Recursos e o Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) e implante o modelo de Gestor de [Recursos privados com token SAS e Azure PowerShell](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

Este exemplo pressupõe que guardou o `azuredeploy.json` modelo para um ficheiro nomeado no diretório atual:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus sku=basic
```

Para mais informações, consulte [a implantação de recursos com modelos de Gestor de Recursos e o Azure CLI](../azure-resource-manager/templates/deploy-cli.md) e implante o modelo de Gestor de [Recursos privados com token SAS e Azure CLI](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="resource-provider-errors"></a>Erros do fornecedor de recursos

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Política de acesso ao cofre de chaves Azure e modelos de Gestor de Recursos Azure

Quando utiliza um modelo de Gestor de Recursos Azure para criar o espaço de trabalho e recursos associados (incluindo o Cofre chave Azure), várias vezes. Por exemplo, usar o modelo várias vezes com os mesmos parâmetros como parte de um gasoduto de integração e implantação contínua.

A maioria das operações de criação de recursos através de modelos são idempotentes, mas o Key Vault limpa as políticas de acesso cada vez que o modelo é usado. Limpar as políticas de acesso quebra o acesso ao Cofre chave para qualquer espaço de trabalho existente que o esteja a utilizar. Por exemplo, as funcionalidades Stop/Create dos Cadernos Azure VM podem falhar.  

Para evitar este problema, recomendamos uma das seguintes abordagens:

* Não desloque o modelo mais de uma vez para os mesmos parâmetros. Ou eliminar os recursos existentes antes de usar o modelo para recriá-los.

* Examine as políticas de acesso ao Cofre `accessPolicies` Chave e, em seguida, use estas políticas para definir a propriedade do modelo. Para visualizar as políticas de acesso, utilize o seguinte comando Azure CLI:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    Para obter mais `accessPolicies` informações sobre a utilização da secção do modelo, consulte a referência do [objeto AccessPolicyEntry](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry).

* Verifique se o recurso Key Vault já existe. Se o fizer, não o recrie através do modelo. Por exemplo, para utilizar o Cofre chave existente em vez de criar um novo, faça as seguintes alterações no modelo:

    * **Adicione** um parâmetro que aceite a identificação de um recurso chave vault existente:

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * **Remova** a secção que cria um recurso key vault:

        ```json
        {
          "type": "Microsoft.KeyVault/vaults",
          "apiVersion": "2018-02-14",
          "name": "[variables('keyVaultName')]",
          "location": "[parameters('location')]",
          "properties": {
            "tenantId": "[variables('tenantId')]",
            "sku": {
              "name": "standard",
              "family": "A"
            },
            "accessPolicies": [
            ]
          }
        },
        ```

    * **Retire** `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` a linha `dependsOn` da secção do espaço de trabalho. **Altere** `keyVault` também a `properties` entrada na secção do `keyVaultId` espaço de trabalho para fazer referência ao parâmetro:

        ```json
        {
          "type": "Microsoft.MachineLearningServices/workspaces",
          "apiVersion": "2019-11-01",
          "name": "[parameters('workspaceName')]",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
            "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
          ],
          "identity": {
            "type": "systemAssigned"
          },
          "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
          },
          "properties": {
            "friendlyName": "[parameters('workspaceName')]",
            "keyVault": "[parameters('keyVaultId')]",
            "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
            "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
          }
        }
        ```

    Após estas alterações, pode especificar a identificação do recurso Key Vault existente ao executar o modelo. O modelo irá então reutilizar `keyVault` o Cofre chave, definindo a propriedade do espaço de trabalho para o seu ID.

    Para obter a identificação do Cofre chave, pode fazer referência à saída do modelo original executar ou utilizar o Azure CLI. O seguinte comando é um exemplo de utilização do CLI Azure para obter o ID de recurso key vault:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    Este comando devolve um valor semelhante ao seguinte texto:

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

## <a name="next-steps"></a>Passos seguintes

* [Implementar recursos com modelos de Gestor](../azure-resource-manager/templates/deploy-rest.md)de Recursos e Gestor de Recursos REST API .
* [Criação e implantação de grupos de recursos Azure através do Estúdio Visual.](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)
