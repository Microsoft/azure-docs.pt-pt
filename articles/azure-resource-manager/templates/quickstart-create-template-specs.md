---
title: Criar e implementar especificações de modelo
description: Aprenda a criar uma especificação de modelo a partir do modelo ARM. Em seguida, implemente a especificação do modelo para um grupo de recursos na sua subscrição.
author: tfitzmac
ms.date: 11/10/2020
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: 96ef5796aac1d0ee02e6bbafc40b7d3bcdea4e2f
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506367"
---
# <a name="quickstart-create-and-deploy-template-spec-preview"></a>Quickstart: Criar e implementar especificações de modelo (Pré-visualização)

Este quickstart mostra-lhe como embalar um modelo de gestor de recursos Azure (modelo ARM) numa [especificação de modelo](template-specs.md) e, em seguida, implementar essa especificação do modelo. A especificação do seu modelo contém um modelo ARM que implementa uma conta de armazenamento.

## <a name="prerequisites"></a>Pré-requisitos

Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

> [!NOTE]
> As Especificações do Modelo estão atualmente em pré-visualização. Para usá-lo, tem de [se inscrever na lista de espera.](https://aka.ms/templateSpecOnboarding)
>
> Depois de aprovado na lista de espera, receberá instruções para instalar o módulo PowerShell de pré-visualização e o módulo CLI de pré-visualização.

## <a name="create-template-spec"></a>Criar especificação de modelo

A especificação do modelo é um tipo de recurso chamado **Microsoft.Resources/templateSpecs**. Para criar a especificação do seu modelo, pode utilizar a Azure PowerShell, Azure CLI ou um modelo ARM. Em todas as opções, você precisa de um modelo ARM que é embalado dentro da especificação do modelo.

Com PowerShell e CLI, o modelo ARM é passado como parâmetro para o comando. Com o modelo ARM, o modelo ARM para embalar dentro da especificação do modelo está incorporado na definição de especificação do modelo.

Estas opções são mostradas abaixo.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Quando criar uma especificação de modelo com PowerShell, pode passar num modelo local. Copie o modelo seguinte e guarde-o localmente para um ficheiro nomeado **azuredeploy.jsem**. Este quickstart assume que guardou para um caminho **c:\Templates\azuredeploy.js,** mas pode usar qualquer caminho.

    :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

1. Crie um novo grupo de recursos para conter a especificação do modelo.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

1. Em seguida, crie a especificação do modelo nesse grupo de recursos. Você dá ao novo modelo especificar o nome **armazenamentoSpec**.

    ```azurepowershell
    New-AzTemplateSpec `
      -Name storageSpec `
      -Version "1.0" `
      -ResourceGroupName templateSpecRG `
      -Location westus2 `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. Quando você criar uma especificação de modelo com CLI, você pode passar em um modelo local. Copie o modelo seguinte e guarde-o localmente para um ficheiro nomeado **azuredeploy.jsem**. Este quickstart assume que guardou para um caminho **c:\Templates\azuredeploy.js,** mas pode usar qualquer caminho.

    :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

1. Crie um novo grupo de recursos para conter a especificação do modelo.

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Em seguida, crie a especificação do modelo nesse grupo de recursos. Você dá ao novo modelo especificar o nome **armazenamentoSpec**.

    ```azurecli
    az ts create \
      --name storageSpec \
      --version "1.0" \
      --resource-group templateSpecRG \
      --location "westus2" \
      --template-file "c:\Templates\azuredeploy.json"
    ```

# <a name="arm-template"></a>[Modelo ARM](#tab/azure-resource-manager)

1. Quando utiliza um modelo ARM para criar a especificação do modelo, o modelo está incorporado na definição de recursos. Copie o modelo a seguir e guarde-o localmente à medida **queazuredeploy.js.** Este quickstart assume que guardou para um caminho **c:\Templates\azuredeploy.js,** mas pode usar qualquer caminho.

    > [!NOTE]
    > No modelo incorporado, todas as [expressões do modelo](template-expressions.md) devem ser escapadas com um segundo suporte esquerdo. Use `"[[` em vez de `"[` . As matrizes JSON ainda usam um único suporte esquerdo.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/templateSpecs",
          "apiVersion": "2019-06-01-preview",
          "name": "storageSpec",
          "location": "westus2",
          "properties": {
            "displayName": "Storage template spec"
          },
          "tags": {},
          "resources": [
            {
              "type": "versions",
              "apiVersion": "2019-06-01-preview",
              "name": "1.0",
              "location": "westus2",
              "dependsOn": [ "storageSpec" ],
              "properties": {
                "template": {
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
                      "defaultValue": "[[resourceGroup().location]",
                      "metadata": {
                        "description": "Location for all resources."
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat('store', uniquestring(resourceGroup().id))]"
                  },
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[[variables('storageAccountName')]",
                      "location": "[[parameters('location')]",
                      "sku": {
                        "name": "[[parameters('storageAccountType')]"
                      },
                      "kind": "StorageV2",
                      "properties": {}
                    }
                  ],
                  "outputs": {
                    "storageAccountName": {
                      "type": "string",
                      "value": "[[variables('storageAccountName')]"
                    }
                  }
                }
              },
              "tags": {}
            }
          ]
        }
      ],
      "outputs": {}
    }
    ```

1. Utilize o Azure CLI ou o PowerShell para criar um novo grupo de recursos.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Implemente o seu modelo com Azure CLI ou PowerShell.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName templateSpecRG `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

    ```azurecli
    az deployment group create \
      --name templateSpecRG \
      --template-file "c:\Templates\azuredeploy.json"
    ```

---

## <a name="deploy-template-spec"></a>Implementar especificação de modelo

Agora pode implementar a especificação do modelo. Implementar a especificação do modelo é como implementar o modelo que contém, exceto que você passa no ID de recurso da especificação do modelo. Utiliza os mesmos comandos de implantação e, se necessário, passa em valores de parâmetro para a especificação do modelo.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Crie um grupo de recursos para conter a nova conta de armazenamento.

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

1. Obtenha o ID de recurso da especificação do modelo.

    ```azurepowershell
    $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "1.0").Versions.Id
    ```

1. Implemente a especificação do modelo.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG
    ```

1. Você fornece parâmetros exatamente como você faria para um modelo ARM. Reimplante a especificação do modelo com um parâmetro para o tipo de conta de armazenamento.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG `
      -storageAccountType Standard_GRS
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. Crie um grupo de recursos para conter a nova conta de armazenamento.

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. Obtenha o ID de recurso da especificação do modelo.

    ```azurecli
    id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")
    ```

    > [!NOTE]
    > Há um problema conhecido em obter um ID de especificação de modelo e atribuí-lo a uma variável no Windows PowerShell.

1. Implemente a especificação do modelo.

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id
    ```

1. Você fornece parâmetros exatamente como você faria para um modelo ARM. Reimplante a especificação do modelo com um parâmetro para o tipo de conta de armazenamento.

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters storageAccountType='Standard_GRS'
    ```

# <a name="arm-template"></a>[Modelo ARM](#tab/azure-resource-manager)

1. Copie o modelo seguinte e guarde-o localmente para um ficheiro nomeado **storage.jsem**.

    ```json
       {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-06-01",
          "name": "demo",
          "properties": {
            "templateLink": {
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
            },
            "parameters": {
            },
            "mode": "Incremental"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. Utilize o Azure CLI ou o PowerShell para criar um novo grupo de recursos para a conta de armazenamento.

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. Implemente o seu modelo com Azure CLI ou PowerShell.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName storageRG `
      -TemplateFile "c:\Templates\storage.json"
    ```

    ```azurecli
    az deployment group create \
      --name storageRG \
      --template-file "c:\Templates\storage.json"
    ```

---

## <a name="grant-access"></a>Conceder acesso

Se quiser permitir que outros utilizadores da sua organização implementem o seu espectro de modelos, tem de lhes conceder acesso de leitura. Pode atribuir a função Reader a um grupo AD Azure para o grupo de recursos que contém especificações de modelo que pretende partilhar. Para obter mais informações, consulte [Tutorial: Conceder a um grupo acesso aos recursos da Azure utilizando a Azure PowerShell](../../role-based-access-control/tutorial-role-assignments-group-powershell.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Para limpar o recurso que implementou neste arranque rápido, elimine ambos os grupos de recursos que criou.

1. No portal do Azure, selecione Grupo de recursos no menu à esquerda.

1. Introduza o nome do grupo de recursos (modeloSpecRG e storageRG) no campo Filtro por nome.

1. Selecione o nome do grupo de recursos.

1. Selecione Eliminar grupo de recursos no menu superior.

## <a name="next-steps"></a>Passos seguintes

Para aprender sobre a criação de uma especificação de modelo que inclui modelos ligados, consulte [Criar uma especificação de modelo de um modelo ligado.](template-specs-create-linked.md)
