---
title: Criar e implementar especificações de modelo
description: Aprenda a criar uma especificação de modelo a partir do modelo ARM. Em seguida, implemente a especificação do modelo para um grupo de recursos na sua subscrição.
author: tfitzmac
ms.date: 12/14/2020
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: 1d3e7c6ee6d19d4d2cd3828d5abf95ccb5457e76
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97511353"
---
# <a name="quickstart-create-and-deploy-template-spec-preview"></a>Quickstart: Criar e implementar especificações de modelo (Pré-visualização)

Este quickstart mostra-lhe como embalar um modelo de gestor de recursos Azure (modelo ARM) numa [especificação de modelo](template-specs.md). Em seguida, você implanta a especificação do modelo. A especificação do seu modelo contém um modelo ARM que implementa uma conta de armazenamento.

## <a name="prerequisites"></a>Pré-requisitos

Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

> [!NOTE]
> As Especificações do Modelo estão atualmente em pré-visualização. Para usá-lo com a Azure PowerShell, tem de instalar [a versão 5.0.0 ou posterior](/powershell/azure/install-az-ps). Para usá-lo com Azure CLI, utilize [a versão 2.14.2 ou posterior](/cli/azure/install-azure-cli).

## <a name="create-template"></a>Criar o modelo

Você cria uma especificação de modelo a partir de um modelo local. Copie o modelo seguinte e guarde-o localmente para um ficheiro nomeado **azuredeploy.jsem**. Este quickstart assume que guardou para um caminho **c:\Templates\azuredeploy.js,** mas pode usar qualquer caminho.

:::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

## <a name="create-template-spec"></a>Criar especificação de modelo

A especificação do modelo é um tipo de recurso chamado `Microsoft.Resources/templateSpecs` . Para criar uma especificação de modelo, use PowerShell, Azure CLI, o portal ou um modelo ARM.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Crie um novo grupo de recursos para conter a especificação do modelo.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

1. Crie a especificação do modelo nesse grupo de recursos. Dê ao novo modelo especificar o nome **armazenamentoSpec**.

    ```azurepowershell
    New-AzTemplateSpec `
      -Name storageSpec `
      -Version "1.0" `
      -ResourceGroupName templateSpecRG `
      -Location westus2 `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. Crie um novo grupo de recursos para conter a especificação do modelo.

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Crie a especificação do modelo nesse grupo de recursos. Dê ao novo modelo especificar o nome **armazenamentoSpec**.

    ```azurecli
    az ts create \
      --name storageSpec \
      --version "1.0" \
      --resource-group templateSpecRG \
      --location "westus2" \
      --template-file "c:\Templates\azuredeploy.json"
    ```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure **as especificações do modelo.** Selecione **as especificações** do modelo das opções disponíveis.

    :::image type="content" source="./media/quickstart-create-template-specs/search-template-spec.png" alt-text="especificações de modelo de pesquisa":::

1. **Selecione o modelo de importação**.

    :::image type="content" source="./media/quickstart-create-template-specs/import-template.png" alt-text="modelo de importação":::

1. Selecione o ícone da pasta.

    :::image type="content" source="./media/quickstart-create-template-specs/open-folder.png" alt-text="pasta aberta":::

1. Navegue para o modelo local que guardou e selecione-o. Selecione **Abrir**.
1. Selecione **Import** (Importar).

    :::image type="content" source="./media/quickstart-create-template-specs/select-import.png" alt-text="selecionar opção de importação":::

1. Forneça os seguintes valores:

    - **Nome**: insira um nome para a especificação do modelo.  Por exemplo, **armazenamentoSpec**
    - **Subscrição**: selecione uma subscrição Azure utilizada para criar a especificação do modelo.
    - **Grupo de Recursos**: selecione **Criar novo** e, em seguida, introduza um novo nome de grupo de recursos.  Por exemplo, **modeloSpecRG**.
    - **Localização**: selecione uma localização para o grupo de recursos. Por exemplo,  **West US 2**.
    - **Versão**: introduza uma versão para a especificação do modelo. Use **1.0**.

1. Selecione **Rever + Criar**.
1. Selecione **Criar**.

# <a name="arm-template"></a>[Modelo do ARM](#tab/azure-resource-manager)

> [!NOTE]
> Em vez de utilizar um modelo ARM, recomendamos que utilize o PowerShell ou o CLI para criar a especificação do seu modelo. Essas ferramentas convertem automaticamente os modelos ligados a artefactos ligados ao seu modelo principal. Quando utilizar um modelo ARM para criar a especificação do modelo, deve adicionar manualmente os modelos ligados como artefactos, o que pode ser complicado.

1. Quando utiliza um modelo ARM para criar a especificação do modelo, o modelo está incorporado na definição de recursos. Há algumas alterações que precisa de fazer ao seu modelo local. Copie o modelo a seguir e guarde-o localmente à medida **queazuredeploy.js.**

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

Para implementar uma especificação de modelo, use os mesmos comandos de implementação que utilizaria para implementar um modelo. Passe o ID do recurso da especificação do modelo para implementar.

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
    id=$(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")
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

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecione a especificação do modelo que criou.

    :::image type="content" source="./media/quickstart-create-template-specs/select-template-spec.png" alt-text="selecionar especificações de modelo":::

1. Selecione **Implementar**.

    :::image type="content" source="./media/quickstart-create-template-specs/deploy-template-spec.png" alt-text="implementar especificações de modelo":::

1. Forneça os seguintes valores:

    - **Subscrição**: selecione uma subscrição Azure para criar o recurso.
    - **Grupo de recursos**: selecione **Criar novo** e, em seguida, insira **o storageRG**.
    - **Tipo de conta de armazenamento**: selecione **Standard_GRS**.

1. Selecione **Rever + criar**.
1. Selecione **Criar**.

# <a name="arm-template"></a>[Modelo do ARM](#tab/azure-resource-manager)

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

## <a name="update-template"></a>Modelo de atualização

Suponhamos que identificou uma alteração que quer fazer ao modelo na especificação do seu modelo. O modelo a seguir é semelhante ao seu modelo anterior, exceto que adiciona um prefixo para o nome da conta de armazenamento. Copie o modelo a seguir e atualize o seu azuredeploy.jsno ficheiro.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/templatespecs/azuredeploy2.json":::

## <a name="update-template-spec-version"></a>Versão de especificação do modelo de atualização

Em vez de criar uma nova especificação de modelo para o modelo revisto, adicione uma nova versão nomeada `2.0` à especificação de modelo existente.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Crie uma nova versão para a especificação do modelo.

   ```powershell
   New-AzTemplateSpec `
     -Name storageSpec `
     -Version "2.0" `
     -ResourceGroupName templateSpecRG `
     -Location westus2 `
     -TemplateFile "c:\Templates\azuredeploy.json"
   ```

1. Para implementar a nova versão, obtenha o ID de recurso para a `2.0` versão.

   ```powershell
   $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "2.0").Versions.Id
   ```

1. Implemente esta versão. Forneça um prefixo para o nome da conta de armazenamento.

   ```powershell
   New-AzResourceGroupDeployment `
     -TemplateSpecId $id `
     -ResourceGroupName storageRG `
     -namePrefix "demoaccount"
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. Crie uma nova versão para a especificação do modelo.

   ```azurecli
   az ts create \
     --name storageSpec \
     --version "2.0" \
     --resource-group templateSpecRG \
     --location "westus2" \
     --template-file "c:\Templates\azuredeploy.json"
   ```

1. Para implementar a nova versão, obtenha o ID de recurso para a `2.0` versão.

   ```azurecli
   id=$(az ts show --name storageSpec --resource-group templateSpecRG --version "2.0" --query "id")
   ```

1. Implemente esta versão. Forneça um prefixo para o nome da conta de armazenamento.

   ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters namePrefix='demoaccount'
    ```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Na especificação do seu modelo, selecione **Criar nova versão.**

   :::image type="content" source="./media/quickstart-create-template-specs/select-versions.png" alt-text="criar nova versão":::

1. Nomeie a nova versão `2.0` e adicione opcionalmente notas. Selecione **o modelo de edição**.

   :::image type="content" source="./media/quickstart-create-template-specs/add-version-name.png" alt-text="nomear nova versão":::

1. Substitua o conteúdo do modelo pelo seu modelo atualizado. Selecione **Review + Guardar**.
1. Selecione **Guardar alterações**.

1. Para implementar a nova versão, selecione **Versões**

   :::image type="content" source="./media/quickstart-create-template-specs/see-versions.png" alt-text="versões de lista":::

1. Para a versão que pretende implementar, selecione os três pontos e **implemente**.

   :::image type="content" source="./media/quickstart-create-template-specs/deploy-version.png" alt-text="selecionar versão para implementar":::

1. Preencha os campos como fez ao implementar a versão anterior.
1. Selecione **Rever + criar**.
1. Selecione **Criar**.

# <a name="arm-template"></a>[Modelo do ARM](#tab/azure-resource-manager)

1. Mais uma vez, você deve fazer algumas alterações no seu modelo local para fazê-lo funcionar com especificações de modelo. Copie o modelo seguinte e guarde-o localmente à medida que azuredeploy.js.

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
              "name": "2.0",
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
                    },
                    "namePrefix": {
                      "type": "string",
                      "maxLength": 11,
                      "defaultValue": "store",
                      "metadata": {
                        "description": "Prefix for storage account name"
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat(parameters('namePrefix'), uniquestring(resourceGroup().id))]"
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

1. Para adicionar a nova versão à especificação do seu modelo, implemente o seu modelo com Azure CLI ou PowerShell.

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
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '2.0')]"
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

## <a name="clean-up-resources"></a>Limpar os recursos

Para limpar o recurso que implementou neste arranque rápido, elimine ambos os grupos de recursos que criou.

1. No portal do Azure, selecione Grupo de recursos no menu à esquerda.

1. Introduza o nome do grupo de recursos (modeloSpecRG e storageRG) no campo Filtro por nome.

1. Selecione o nome do grupo de recursos.

1. Selecione Eliminar grupo de recursos no menu superior.

## <a name="next-steps"></a>Passos seguintes

Para aprender sobre a criação de uma especificação de modelo que inclui modelos ligados, consulte [Criar uma especificação de modelo de um modelo ligado.](template-specs-create-linked.md)
