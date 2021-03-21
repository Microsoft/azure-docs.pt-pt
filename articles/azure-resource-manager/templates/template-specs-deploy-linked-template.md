---
title: Implementar uma especificação de modelo como um modelo ligado
description: Aprenda a implementar uma especificação de modelo existente numa implementação ligada.
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: b30a99a469ec010dcc2e128bbeb446b493631608
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96518877"
---
# <a name="tutorial-deploy-a-template-spec-as-a-linked-template-preview"></a>Tutorial: Implementar uma especificação de modelo como um modelo ligado (Pré-visualização)

Aprenda a implementar uma [especificação](template-specs.md) de modelo existente utilizando uma [implementação ligada](linked-templates.md#linked-template). Utiliza especificações de modelo para partilhar modelos ARM com outros utilizadores na sua organização. Depois de ter criado uma especificação de modelo, pode implementar a especificação do modelo utilizando o Azure PowerShell ou o Azure CLI. Também pode implementar a especificação do modelo como parte da sua solução utilizando um modelo ligado.

## <a name="prerequisites"></a>Pré-requisitos

Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

> [!NOTE]
> As Especificações do Modelo estão atualmente em pré-visualização. Para usá-lo com a Azure PowerShell, tem de instalar [a versão 5.0.0 ou posterior](/powershell/azure/install-az-ps). Para usá-lo com Azure CLI, utilize [a versão 2.14.2 ou posterior](/cli/azure/install-azure-cli).

## <a name="create-a-template-spec"></a>Criar uma especificação de modelo

Siga [o Quickstart: Crie e implemente a especificação do modelo](quickstart-create-template-specs.md) para criar uma especificação de modelo para a implementação de uma conta de armazenamento. Você precisa do nome do grupo de recursos da especificação do modelo, nome de especificação do modelo e versão de especificação do modelo na secção seguinte.

## <a name="create-the-main-template"></a>Crie o modelo principal

Para implementar uma especificação de modelo num modelo ARM, adicione um [recurso de implementações](/azure/templates/microsoft.resources/deployments) ao seu modelo principal. Na `templateLink` propriedade, especifique o ID de recurso de uma especificação de modelo. Criar um modelo com o seguinte JSON chamado **azuredeploy.jsem**. Este tutorial assume que guardou para um caminho **c:\Templates\deployTS\azuredeploy.js,** mas pode usar qualquer caminho.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "tsResourceGroup":{
      "type": "string",
      "metadata": {
        "Description": "Specifies the resource group name of the template spec."
      }
    },
    "tsName": {
      "type": "string",
      "metadata": {
        "Description": "Specifies the name of the template spec."
      }
    },
    "tsVersion": {
      "type": "string",
      "defaultValue": "1.0.0.0",
      "metadata": {
        "Description": "Specifies the version the template spec."
      }
    },
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "metadata": {
        "Description": "Specifies the storage account type required by the template spec."
      }
    }
  },
  "variables": {
    "appServicePlanName": "[concat('plan', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2016-09-01",
      "name": "[variables('appServicePlanName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "B1",
        "tier": "Basic",
        "size": "B1",
        "family": "B",
        "capacity": 1
      },
      "kind": "linux",
      "properties": {
        "perSiteScaling": false,
        "reserved": true,
        "targetWorkerCount": 0,
        "targetWorkerSizeId": 0
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "createStorage",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
        },
        "parameters": {
          "storageAccountType": {
            "value": "[parameters('storageAccountType')]"
          }
        }
      }
    }
  ],
  "outputs": {
    "templateSpecId": {
      "type": "string",
      "value": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
    }
  }
}
```

O ID de especificação do modelo é gerado utilizando a [`resourceID()`](template-functions-resource.md#resourceid) função. O argumento do grupo de recursos na função resourceID() é opcional se o modeloSpec estiver no mesmo grupo de recursos da implementação atual.  Também pode passar diretamente no ID de recurso como parâmetro. Para obter a ID, use:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$id = (Get-AzTemplateSpec -ResourceGroupName $resourceGroupName -Name $templateSpecName -Version $templateSpecVersion).Versions.Id
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli-interactive
id = $(az ts show --name $templateSpecName --resource-group $resourceGroupName --version $templateSpecVersion --query "id")
```

> [!NOTE]
> Há um problema conhecido em obter um ID de especificação de modelo e atribuí-lo a uma variável no Windows PowerShell.

---

A sintaxe para passar parâmetros para a especificação do modelo é:

```json
"parameters": {
  "storageAccountType": {
    "value": "[parameters('storageAccountType')]"
  }
}
```

> [!NOTE]
> A apiversão `Microsoft.Resources/deployments` deve ser 2020-06-01 ou mais tarde.

## <a name="deploy-the-template"></a>Implementar o modelo

Quando implementa o modelo ligado, implementa tanto a aplicação web como a conta de armazenamento. A implementação é o mesmo que a implantação de outros modelos ARM.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

New-AzResourceGroupDeployment `
  -ResourceGroupName webRG `
  -TemplateFile "c:\Templates\deployTS\azuredeploy.json" `
  -tsResourceGroup templateSpecRg `
  -tsName storageSpec `
  -tsVersion 1.0
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az group create \
  --name webRG \
  --location westus2

az deployment group create \
  --resource-group webRG \
  --template-file "c:\Templates\deployTS\azuredeploy.json" \
  --parameters tsResourceGroup=templateSpecRG tsName=storageSpec tsVersion=1.0
```

---

## <a name="next-steps"></a>Passos seguintes

Para aprender sobre a criação de uma especificação de modelo que inclui modelos ligados, consulte [Criar uma especificação de modelo de um modelo ligado.](template-specs-create-linked.md)
