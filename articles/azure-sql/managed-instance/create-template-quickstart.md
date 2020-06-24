---
title: 'Gestor de Recursos Azure: Criar uma instância gerida Azure SQL'
description: Aprenda a criar um Azure SQL Managed Instance utilizando um modelo de Gestor de Recursos Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: subject-armqs
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 06/22/2020
ms.openlocfilehash: 01c6c37d31d41f88b370face372555536724adde
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85256185"
---
# <a name="quickstart-create-an-azure-sql-managed-instance-using-an-azure-resource-manager-template"></a>Quickstart: Criar uma instância gerida Azure SQL usando um modelo de Gestor de Recursos Azure

Este quickstart foca-se no processo de implementação de um modelo de Gestor de Recursos para criar um Azure SQL Managed Instance e vNet.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

Nenhum.

## <a name="create-an-azure-sql-managed-instance"></a>Criar uma Instância Gerida do SQL do Azure

[Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) é uma base de dados de nuvem inteligente, totalmente gerida e escalável, com quase 100% de paridade de funcionalidades com o motor de base de dados SQL Server.

### <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).

:::code language="json" source="~/quickstart-templates/101-sqlmi-new-vnet/azuredeploy.json":::

Estes recursos são definidos no modelo:

- [**Microsoft.Sql/managedinstances**](/azure/templates/microsoft.sql/managedinstances)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.Network/virtualNetworks)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.Network/routeTables)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.Network/networkSecurityGroups)



Mais amostras de modelo podem ser encontradas em [modelos Azure Quickstart.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>Implementar o modelo

Selecione **Experimente-o** a partir do seguinte bloco de código PowerShell para abrir a Azure Cloud Shell.

> [!IMPORTANT]
> Implementar um caso gerido é uma operação de longa duração. A implantação da primeira instância na sub-rede normalmente demora muito mais tempo do que a implantação numa sub-rede com instâncias geridas existentes. Para tempos médios de provisionamento, consulte [as operações de gestão de instância gerida sql](sql-managed-instance-paas-overview.md#management-operations).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="the-azure-cli"></a>[O Azure CLI](#tab/azure-cli)

```azurecli-interactive
read -p "Enter a project name that is used for generating resource names:" projectName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

* * *

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Visite o [portal Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) e verifique se a instância gerida está no seu grupo de recursos selecionado. Uma vez que a criação de um caso gerido pode demorar algum tempo, pode ser necessário verificar o link **Implementações** na página geral do seu grupo **de** recursos.

- Para um arranque rápido que mostre como ligar-se a SQL Managed Instance a partir de uma máquina virtual Azure, consulte [configurar uma ligação virtual Azure](connect-vm-instance-configure.md).
- Para obter um arranque rápido que mostre como ligar-se à SQL Managed Instance a partir de um computador cliente no local utilizando uma ligação ponto-a-local, consulte [configurar uma ligação ponto-a-local](point-to-site-p2s-configure.md).

## <a name="clean-up-resources"></a>Limpar recursos

Mantenha a instância gerida se quiser ir aos [próximos passos,](#next-steps)mas elimine a instância gerida e os recursos relacionados após completar quaisquer tutoriais adicionais. Depois de eliminar uma instância gerida, consulte [Eliminar uma sub-rede depois de eliminar uma instância gerida](virtual-cluster-delete.md).


Para eliminar o grupo de recursos:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

# <a name="the-azure-cli"></a>[O Azure CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

* * *

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configure um Azure VM para ligar à Azure SQL Gerenciada Instância](connect-vm-instance-configure.md)
