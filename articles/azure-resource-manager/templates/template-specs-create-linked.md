---
title: Crie uma especificação de modelo com modelos ligados
description: Aprenda a criar uma especificação de modelo com modelos ligados.
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 038fb3e6bbb6086535b83a69de7a3fe49a8e60bb
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518894"
---
# <a name="tutorial-create-a-template-spec-with-linked-templates-preview"></a>Tutorial: Criar uma especificação de modelo com modelos ligados (Pré-visualização)

Aprenda a criar uma [especificação de modelo](template-specs.md) com um modelo principal e um [modelo ligado.](linked-templates.md#linked-template) Utiliza especificações de modelo para partilhar modelos ARM com outros utilizadores na sua organização. Este artigo mostra-lhe como criar uma especificação de modelo para embalar um modelo principal e seus modelos ligados usando a `relativePath` propriedade do recurso de [implantação](/azure/templates/microsoft.resources/deployments).

## <a name="prerequisites"></a>Pré-requisitos

Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

> [!NOTE]
> As Especificações do Modelo estão atualmente em pré-visualização. Para usá-lo com a Azure PowerShell, tem de instalar [a versão 5.0.0 ou posterior](/powershell/azure/install-az-ps). Para usá-lo com Azure CLI, utilize [a versão 2.14.2 ou posterior](/cli/azure/install-azure-cli).

## <a name="create-linked-templates"></a>Criar modelos ligados

Crie o modelo principal e o modelo ligado.

Para ligar um modelo, adicione um [recurso de implementações](/azure/templates/microsoft.resources/deployments) ao seu modelo principal. Na `templateLink` propriedade, especifique o caminho relativo do modelo ligado de acordo com o caminho do modelo principal.

O modelo ligado é chamado **linkedTemplate.jsem**, e é armazenado em uma sub-página chamada **artefactos** no caminho onde o modelo principal é armazenado.  Pode utilizar um dos seguintes valores para o ParentePath:

- `./artifacts/linkedTemplate.json`
- `/artifacts/linkedTemplate.json`
- `artifacts/linkedTemplate.json`

A `relativePath` propriedade é sempre relativa ao ficheiro de modelo onde é `relativePath` declarado, por isso, se houver outra linkedTemplate2.jssobre o que é chamado a partir de linkedTemplate.jse linkedTemplate2.jsestá armazenado na mesma subpagadora de artefactos, o parentePath especificado em linkedTemplate.jsé apenas `linkedTemplate2.json` .

1. Crie o modelo principal com o seguinte JSON. Guarde o modelo principal à medida **queazuredeploy.jspara** o computador local. Este tutorial assume que guardou para um caminho **c:\Templates\linkedTS\azuredeploy.js,** mas pode usar qualquer caminho.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "westus2",
          "metadata":{
            "description": "Specify the location for the resources."
          }
        },
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "metadata":{
            "description": "Specify the storage account type."
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
              "relativePath": "artifacts/linkedTemplate.json"
            },
            "parameters": {
              "storageAccountType": {
                "value": "[parameters('storageAccountType')]"
              }
            }
          }
        }
      ]
    }
    ```

    > [!NOTE]
    > A apiversão `Microsoft.Resources/deployments` deve ser 2020-06-01 ou mais tarde.

1. Crie um diretório chamado **artefactos** na pasta onde o modelo principal é guardado.
1. Crie o modelo ligado com o seguinte JSON:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "allowedValues": [
            "Standard_LRS",
            "Standard_GRS",
            "Standard_ZRS",
            "Premium_LRS"
          ],
          "metadata": {
            "description": "Storage Account type"
          }
        },
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        }
      },
      "variables": {
        "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "apiVersion": "2019-04-01",
          "name": "[variables('storageAccountName')]",
          "location": "[parameters('location')]",
          "sku": {
            "name": "[parameters('storageAccountType')]"
          },
          "kind": "StorageV2",
          "properties": {}
        }
      ],
      "outputs": {
        "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
        }
      }
    }
    ```

1. Guarde o gabarito à medida **quelinkedTemplate.jsna** pasta de **artefactos.**

## <a name="create-template-spec"></a>Criar especificação de modelo

As especificações dos modelos são armazenadas em grupos de recursos.  Crie um grupo de recursos e, em seguida, crie uma especificação de modelo com o seguinte script. O nome de especificação do modelo é **webSpec.**

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name templateSpecRG `
  -Location westus2

New-AzTemplateSpec `
  -Name webSpec `
  -Version "1.0.0.0" `
  -ResourceGroupName templateSpecRG `
  -Location westus2 `
  -TemplateFile "c:\Templates\linkedTS\azuredeploy.json"
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az group create \
  --name templateSpecRG \
  --location westus2

az ts create \
  --name webSpec \
  --version "1.0.0.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "c:\Templates\linkedTS\azuredeploy.json"
```

---

Quando terminar, pode ver a especificação do modelo a partir do portal Azure ou utilizando o seguinte cmdlet:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Get-AzTemplateSpec -ResourceGroupName templatespecRG -Name webSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts show --name webSpec --resource-group templateSpecRG --version "1.0.0.0"
```

---

## <a name="deploy-template-spec"></a>Implementar especificação de modelo

Agora pode implementar a especificação do modelo. Implementar a especificação do modelo é como implementar o modelo que contém, exceto que você passa no ID de recurso da especificação do modelo. Utiliza os mesmos comandos de implantação e, se necessário, passa em valores de parâmetro para a especificação do modelo.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

$id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name webSpec -Version "1.0.0.0").Versions.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName webRG
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az group create \
  --name webRG \
  --location westus2

id = $(az template-specs show --name webSpec --resource-group templateSpecRG --version "1.0.0.0" --query "id")

az deployment group create \
  --resource-group webRG \
  --template-spec $id
```

> [!NOTE]
> Há um problema conhecido em obter um ID de especificação de modelo e atribuí-lo a uma variável no Windows PowerShell.

---

## <a name="next-steps"></a>Passos seguintes

Para aprender sobre a implementação de uma especificação de modelo como um modelo ligado, consulte [Tutorial: Implemente uma especificação de modelo como um modelo ligado](template-specs-deploy-linked-template.md).
