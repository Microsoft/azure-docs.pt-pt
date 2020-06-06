---
title: Quickstart - Criar um espaço de trabalho Azure Databricks pelo modelo Azure Resource Manager
description: Este quickstart mostra como usar o modelo Azure Resource Manager para criar um espaço de trabalho Azure Databricks.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc, subject-armqs
ms.date: 05/27/2020
ms.openlocfilehash: c8503aee5cefbe197fd63cd6406006a1cdf8afa9
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2020
ms.locfileid: "84463155"
---
# <a name="quickstart-create-an-azure-databricks-workspace-by-using-the-azure-resource-manager-template"></a>Quickstart: Criar um espaço de trabalho Azure Databricks utilizando o modelo Azure Resource Manager

Neste arranque rápido, você usa um modelo de Gestor de Recursos Azure para criar um espaço de trabalho Azure Databricks. Uma vez criado o espaço de trabalho, valida-se a implantação.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para completar este artigo, precisa:

* Tenha uma subscrição Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/)

## <a name="create-an-azure-databricks-workspace"></a>Criar uma área de trabalho do Azure Databricks

### <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste arranque rápido é de [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/101-databricks-workspace/).

:::code language="json" source="~/quickstart-templates/101-databricks-workspace/azuredeploy.json" range="1-53" highlight="33-46":::

O recurso Azure definido no modelo é [Microsoft.Databricks/workspaces](/azure/templates/microsoft.databricks/workspaces): criar um espaço de trabalho Azure Databricks.

### <a name="deploy-the-template"></a>Implementar o modelo

Nesta secção, vai criar uma área de trabalho do Azure Databricks com o modelo do Azure Resource Manager.

1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo. O modelo cria um espaço de trabalho Azure Databricks.

   [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-databricks-workspace%2Fazuredeploy.json)

2. Indique os valores necessários para criar a sua área de trabalho do Azure Databricks.

   ![Criar espaço de trabalho Azure Databricks usando um modelo de Gestor de Recursos Azure](./media/quickstart-create-databricks-workspace-resource-manager-template/create-databricks-workspace-using-resource-manager-template.png "Criar espaço de trabalho Azure Databricks usando um modelo de Gestor de Recursos Azure")

   Forneça os seguintes valores:

   |Propriedade  |Descrição  |
   |---------|---------|
   |**Subscrição**     | Na lista pendente, selecione a sua subscrição do Azure.        |
   |**Grupo de recursos**     | Especifique se quer criar um novo grupo de recursos ou utilizar um existente. Um grupo de recursos é um contentor que detém recursos relacionados para uma solução do Azure. Para obter mais informações, veja [Descrição geral do Grupo de Recursos do Azure](../azure-resource-manager/management/overview.md). |
   |**Localização**     | Selecione **E.U.A. Leste 2**. Para outras regiões disponíveis, veja [Serviços do Azure disponíveis por região](https://azure.microsoft.com/regions/services/).        |
   |**Nome da área de trabalho**     | Indique um nome para a sua área de trabalho do Databricks        |
   |**Nível de preços**     |  Escolha entre **Standard** ou **Premium**. Para obter mais informações sobre estes escalões, veja [Página de preços do Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

3. Selecione **Rever + Criar** e, em seguida, clique em **Criar**.

4. A criação da área de trabalho demora alguns minutos. Quando uma implantação do espaço de trabalho falha, o espaço de trabalho ainda é criado num estado falhado. Elimine o espaço de trabalho falhado e crie um novo espaço de trabalho que resolva os erros de implantação. Quando elimina o espaço de trabalho falhado, o grupo de recursos geridos e quaisquer recursos implantados com sucesso também são eliminados.

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Pode utilizar o portal Azure para verificar o espaço de trabalho do Azure Databricks ou utilizar o seguinte script Azure CLI ou Azure PowerShell para listar o recurso.

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
echo "Enter your Azure Databricks workspace name:" &&
read databricksWorkspaceName &&
echo "Enter the resource group where the Azure Databricks workspace exists:" &&
read resourcegroupName &&
az databricks workspace show -g $resourcegroupName -n $databricksWorkspaceName
```

### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your Azure Databricks workspace exists"
(Get-AzResource -ResourceType "Microsoft.Databricks/workspaces" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender continuar a ter tutoriais subsequentes, poderá querer deixar estes recursos no lugar. Quando já não for necessário, elimine o grupo de recursos, que elimina o espaço de trabalho Azure Databricks e os recursos geridos relacionados. Para eliminar o grupo de recursos utilizando a Azure CLI ou a Azure PowerShell:

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Próximos passos

Neste quickstart, criou um espaço de trabalho Azure Databricks usando um modelo de Gestor de Recursos Azure e validou a implementação. Avance para o artigo seguinte para saber como executar uma operação de ETL (extração, transformação e carregamento de dados) com o Azure Databricks.

> [!div class="nextstepaction"]
> [Extrair, transformar e carregar dados com o Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
