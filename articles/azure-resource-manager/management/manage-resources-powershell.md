---
title: Gerir recursos - Azure PowerShell
description: Utilize o Azure PowerShell e o Azure Resource Manager para gerir os seus recursos. Mostra como implantar e eliminar recursos.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: d97390861200a0a7f3b04d951e0ca2a17796835d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91372506"
---
# <a name="manage-azure-resources-by-using-azure-powershell"></a>Gerir os recursos da Azure utilizando a Azure PowerShell

Saiba como usar o Azure PowerShell com [o Azure Resource Manager](overview.md) para gerir os seus recursos Azure. Para gerir grupos de recursos, consulte [os grupos de recursos Manage Azure utilizando a Azure PowerShell](manage-resource-groups-powershell.md).

Outros artigos sobre gestão de recursos:

- [Gerir os recursos do Azure utilizando o portal Azure](manage-resources-portal.md)
- [Gerir os recursos do Azure utilizando o Azure CLI](manage-resources-cli.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Mobilizar recursos para um grupo de recursos existente

Pode implementar os recursos do Azure diretamente utilizando o Azure PowerShell ou implementar um modelo de Gestor de Recursos para criar recursos Azure.

### <a name="deploy-a-resource"></a>Implementar um recurso

O seguinte script cria uma conta de armazenamento.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

# Create the storage account.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName "Standard_LRS"

# Retrieve the context.
$ctx = $storageAccount.Context
```

### <a name="deploy-a-template"></a>Implementar um modelo

O seguinte script cria uma implementação de um modelo Quickstart para criar uma conta de armazenamento. Para obter mais informações, consulte [Quickstart: Crie modelos de Gestor de Recursos Azure utilizando código de estúdio visual](../templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -Location $location
```

Para obter mais informações, consulte [implementar recursos com modelos de Gestor de Recursos e Azure PowerShell](../templates/deploy-powershell.md).

## <a name="deploy-a-resource-group-and-resources"></a>Implementar um grupo de recursos e recursos

Pode criar um grupo de recursos e mobilizar recursos para o grupo. Para obter mais informações, consulte [criar grupo de recursos e implementar recursos.](../templates/deploy-to-subscription.md#resource-groups)

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Mobilizar recursos para várias subscrições ou grupos de recursos

Normalmente, você implanta todos os recursos no seu modelo para um único grupo de recursos. No entanto, existem cenários em que pretende implementar um conjunto de recursos em conjunto, mas colocá-los em diferentes grupos de recursos ou subscrições. Para obter mais informações, consulte [recursos do Deploy Azure para várias subscrições ou grupos de recursos.](../templates/cross-scope-deployment.md)

## <a name="delete-resources"></a>Eliminar recursos

O seguinte script mostra como apagar uma conta de armazenamento.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Remove-AzStorageAccount -ResourceGroupName $resourceGroupName -AccountName $storageAccountName
```

Para obter mais informações sobre como o Azure Resource Manager ordena a supressão de recursos, consulte a eliminação do [grupo de recursos Azure Resource Manager](delete-resource-group.md).

## <a name="move-resources"></a>Mover recursos

O seguinte script mostra como remover uma conta de armazenamento de um grupo de recursos para outro grupo de recursos.

```azurepowershell-interactive
$srcResourceGroupName = Read-Host -Prompt "Enter the source Resource Group name"
$destResourceGroupName = Read-Host -Prompt "Enter the destination Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$storageAccount = Get-AzResource -ResourceGroupName $srcResourceGroupName -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $destResourceGroupName -ResourceId $storageAccount.ResourceId
```

Para obter mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Bloquear recursos

O bloqueio impede que outros utilizadores da sua organização apaguem ou modifiquem acidentalmente recursos críticos, tais como subscrição do Azure, grupo de recursos ou recursos. 

O seguinte script bloqueia uma conta de armazenamento para que a conta não possa ser eliminada.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

New-AzResourceLock -LockName LockStorage -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts 
```

O seguinte script obtém todas as fechaduras para uma conta de armazenamento:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts
```

O seguinte script elimina uma fechadura de uma conta de armazenamento:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$lockId = (Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts).LockId
Remove-AzResourceLock -LockId $lockId
```

Para obter mais informações, consulte [Bloquear recursos com o Azure Resource Manager](lock-resources.md).

## <a name="tag-resources"></a>Etiquetar recursos

A marcação ajuda a organizar logicamente o seu grupo de recursos e recursos. Para obter informações, consulte [a utilização de tags para organizar os seus recursos Azure.](tag-resources.md#powershell)

## <a name="manage-access-to-resources"></a>Gerir o acesso aos recursos

[O controlo de acesso baseado em funções (Azure RBAC)](../../role-based-access-control/overview.md) é a forma como gere o acesso aos recursos em Azure. Para obter mais informações, consulte [Adicionar ou remover atribuições de funções Azure utilizando a Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Passos seguintes

- Para saber o Gestor de Recursos Azure, consulte [a visão geral do Azure Resource Manager](overview.md).
- Para aprender a sintaxe do modelo do Gestor de Recursos, consulte [compreender a estrutura e a sintaxe dos modelos do Gestor de Recursos Azure](../templates/template-syntax.md).
- Para aprender a desenvolver modelos, consulte os [tutoriais passo a passo.](../index.yml)
- Para ver os esquemas de modelo do Gestor de Recursos Azure, consulte [a referência do modelo](/azure/templates/).
