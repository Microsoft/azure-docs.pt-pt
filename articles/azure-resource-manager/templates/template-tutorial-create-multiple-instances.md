---
title: Criar várias instâncias de recurso
description: Saiba como criar um modelo do Azure Resource Manager para criar várias instâncias de recursos do Azure.
author: mumian
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 9ed14ce1af6421accccface1b66119057d1c5a30
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239301"
---
# <a name="tutorial-create-multiple-resource-instances-with-arm-templates"></a>Tutorial: Criar múltiplas instâncias de recursos com modelos ARM

Aprenda a iterar no seu modelo Azure Resource Manager (ARM) para criar várias instâncias de um recurso Azure. Neste tutorial, modifica um modelo para criar três instâncias de contas de armazenamento.

![Gestor de recursos azure cria diagrama de múltiplas instâncias](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances-diagram.png)

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Abrir um modelo de Início rápido
> * Editar o modelo
> * Implementar o modelo

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* Visual Studio Code com extensão Ferramentas do Resource Manager. Consulte [o Use Visual Studio Code para criar modelos ARM](use-vs-code-to-create-template.md).

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

[Os modelos Azure QuickStart](https://azure.microsoft.com/resources/templates/) são um repositório para modelos ARM. Em vez de criar um modelo do zero, pode encontrar um modelo de exemplo e personalizá-lo. O modelo utilizado neste início rápido chama-se [Criar uma conta de armazenamento padrão](https://azure.microsoft.com/resources/templates/101-storage-account-create/). O modelo define um recurso de conta de Armazenamento do Azure.

1. A partir do Código do Estúdio Visual, selecione **File**>**Open File**.
2. em **Nome de ficheiro**, cole o seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Selecione **Abrir** para abrir o ficheiro.
4. Existe um recurso “Microsoft.Storage/storageAccounts” definido no modelo. Compare o modelo à [referência do modelo](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts). É útil ter alguma compreensão básica do modelo antes de personalizá-lo.
5. Selecione **File**>**Save As** para guardar o ficheiro como **azuredeploy.json** para o seu computador local.

## <a name="edit-the-template"></a>Editar o modelo

O exemplo existente cria uma conta de armazenamento. Personaliza o modelo para criar três contas de armazenamento.

No Visual Studio Code, efetue as seguintes quatro alterações:

![O Azure Resource Manager cria várias instâncias](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

1. Adicione um elemento `copy` à definição do recurso de conta de armazenamento. No elemento de cópia, especifique o número de iterações e uma variável para este ciclo. O valor tem de ser um número inteiro positivo e não pode ser mais de 800.
2. A função `copyIndex()` devolve a iteração atual no ciclo. Utilize o índice como o prefixo do nome. `copyIndex()` é baseado em zero. Para deslocar o valor de índice, pode passar um valor na função copyIndex(). Por exemplo, *copyIndex(1)*.
3. Elimine o elemento **variables**, dado que já não é utilizado.
4. Elimine o elemento **outputs**. Já não é necessário.

O modelo completo assemelha-se a:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    }
  ]
}
```

Para obter mais informações sobre a criação de múltiplas instâncias, consulte [Implementar múltiplas instâncias de um recurso ou propriedade em modelos ARM](./copy-resources.md)

## <a name="deploy-the-template"></a>Implementar o modelo

Veja a secção [Implementar o modelo](quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) no guia de introdução do Visual Studio Code para o procedimento de implementação.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para listar as três contas de armazenamento, omita o parâmetro --name:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az storage account list --resource-group $resourceGroupName
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Get-AzStorageAccount -ResourceGroupName $resourceGroupName
```

---

Compare os nomes de contas de armazenamento com a definição de nome no modelo.

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. A partir do portal Azure, selecione **Grupo Recurso** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.  Verá um total de seis recursos no grupo de recursos.
4. **Selecione Eliminar** o grupo de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a criar várias instâncias de contas de armazenamento.  No próximo tutorial, vai desenvolver um modelo com vários recursos e vários tipos de recurso. Alguns dos recursos têm recursos dependentes.

> [!div class="nextstepaction"]
> [Criar recursos dependentes](./template-tutorial-create-templates-with-dependent-resources.md)
