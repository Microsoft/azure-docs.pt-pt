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
ms.reviewer: ''
ms.date: 06/22/2020
ms.openlocfilehash: badeb850e8d0346347a994f053fb0f64fd01240a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91283348"
---
# <a name="quickstart-create-an-azure-sql-managed-instance-using-an-arm-template"></a>Quickstart: Criar uma instância gerida Azure SQL usando um modelo ARM

Este quickstart foca-se no processo de implantação de um modelo de Gestor de Recursos Azure (modelo ARM) para criar um Azure SQL Managed Instance e vNet. [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) é uma base de dados de nuvem inteligente, totalmente gerida e escalável, com quase 100% de paridade de funcionalidades com o motor de base de dados SQL Server.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sqlmi-new-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).

:::code language="json" source="~/quickstart-templates/101-sqlmi-new-vnet/azuredeploy.json":::

Estes recursos são definidos no modelo:

- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.Network/networkSecurityGroups)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.Network/routeTables)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.Network/virtualNetworks)
- [**Microsoft.Sql/managedinstances**](/azure/templates/microsoft.sql/managedinstances)

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

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

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

---

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Visite o [portal Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) e verifique se a instância gerida está no seu grupo de recursos selecionado. Uma vez que a criação de um caso gerido pode demorar algum tempo, pode ser necessário verificar o link **Implementações** na página geral do seu grupo **de** recursos.

- Para um arranque rápido que mostre como ligar-se a SQL Managed Instance a partir de uma máquina virtual Azure, consulte [configurar uma ligação virtual Azure](connect-vm-instance-configure.md).
- Para obter um arranque rápido que mostre como ligar-se à SQL Managed Instance a partir de um computador cliente no local utilizando uma ligação ponto-a-local, consulte [configurar uma ligação ponto-a-local](point-to-site-p2s-configure.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Mantenha a instância gerida se quiser ir aos [próximos passos,](#next-steps)mas elimine a instância gerida e os recursos relacionados após completar quaisquer tutoriais adicionais. Depois de eliminar uma instância gerida, consulte [Eliminar uma sub-rede depois de eliminar uma instância gerida](virtual-cluster-delete.md).


Para eliminar o grupo de recursos:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configure um Azure VM para ligar à Azure SQL Gerenciada Instância](connect-vm-instance-configure.md)
