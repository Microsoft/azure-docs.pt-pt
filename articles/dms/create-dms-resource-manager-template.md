---
title: Criar exemplo de DMS (modelo de Gestor de Recursos Azure)
description: Saiba como criar o Serviço de Migração de Bases de Dados utilizando o modelo do Gestor de Recursos Azure (modelo ARM).
author: MashaMSFT
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mathoma
ms.date: 06/29/2020
ms.service: dms
ms.openlocfilehash: a9c68bca4d50af734a0a2cd8a91c7e46d9b56ff1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94963220"
---
# <a name="quickstart-create-instance-of-azure-database-migration-service-using-arm-template"></a>Quickstart: Criar exemplo de Serviço de Migração de Bases de Dados Azure usando o modelo ARM

Utilize este modelo de Gestor de Recursos Azure (modelo ARM) para implementar uma instância do Serviço de Migração da Base de Dados Azure. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-database-migration-simple-deploy%2fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

O modelo Azure Database Migration Service ARM requer o seguinte: 

- A versão mais recente do [Azure CLI](/cli/azure/install-azure-cli) e/ou [PowerShell](/powershell/scripting/install/installing-powershell). 
- Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-azure-database-migration-simple-deploy/).

:::code language="json" source="~/quickstart-templates/101-azure-database-migration-simple-deploy/azuredeploy.json":::

Três recursos Azure são definidos no modelo: 

- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks): Cria a rede virtual. 
- [Microsoft.Network/virtualNetworks/subnets](/azure/templates/microsoft.network/virtualnetworks/subnets): Cria a sub-rede. 
- [Microsoft.DataMigration/services](/azure/templates/microsoft.datamigration/services): Implementa uma instância do Serviço de Migração de Bases de Dados Azure. 

Mais modelos de serviços de migração de bases de dados Azure podem ser encontrados na [galeria de modelos quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datamigration&pageNumber=1&sort=Popular).


## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo. O modelo cria uma instância do Serviço de Migração da Base de Dados Azure. 

   [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-database-migration-simple-deploy%2fazuredeploy.json)

2. Selecione ou introduza os seguintes valores.

    * **Assinatura**: Selecione uma subscrição Azure.
    * **Grupo de recursos**: Selecione um grupo de recursos existente a partir da queda ou selecione **Criar novos** para criar um novo grupo de recursos. 
    * **Região**: Local onde os recursos serão mobilizados.
    * **Nome do serviço**: Nome do novo serviço de migração.
    * **Localização**: A localização do grupo de recursos, deixe como padrão de `[resourceGroup().location]` .
    * **Nome Vnet**: Nome da nova rede virtual.
    * **Nome da sub-rede**: Nome da nova sub-rede associada à rede virtual.



3. Selecione **Rever + criar**. Após a aplicação do Serviço de Migração da Base de Dados Azure com sucesso, recebe uma notificação. 


O portal do Azure é utilizado para implementar o modelo. Além do portal Azure, também pode utilizar o Azure PowerShell, Azure CLI e REST API. Para aprender outros métodos de implementação, consulte [os modelos de implementação](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Pode utilizar o CLI Azure para verificar os recursos implantados. 


```azurecli-interactive
echo "Enter the resource group where your SQL Server VM exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```


## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine o grupo de recursos utilizando o Azure CLI ou o Azure PowerShell:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Passos seguintes

Para um tutorial passo a passo que o guia através do processo de criação de um modelo, consulte:

> [!div class="nextstepaction"]
> [ Tutorial: Crie e implemente o seu primeiro modelo ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Para outras formas de implantar o Serviço de Migração da Base de Dados Azure, consulte: 
- [Portal do Azure](quickstart-create-data-migration-service-portal.md)

Para saber mais, consulte [uma visão geral do Serviço de Migração da Base de Dados Azure](dms-overview.md)