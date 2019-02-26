---
title: Gerir recursos do Azure com o Azure PowerShell | Documentos da Microsoft
description: Utilize o Azure PowerShell e Azure Resource Manager para gerir os recursos.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 241b820122fe1c82b9a68829db87635745c051d9
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56825158"
---
# <a name="manage-azure-resources-by-using-azure-powershell"></a>Gerir recursos do Azure com o Azure PowerShell

Saiba como utilizar o Azure PowerShell com [do Azure Resource Manager](resource-group-overview.md) para gerir os recursos do Azure. Para gerir grupos de recursos, veja [grupos de recursos de gerir o Azure com o Azure PowerShell](./manage-resource-groups-powershell.md).

Outros artigos sobre a gestão de recursos:

- [Gerir recursos do Azure com o portal do Azure](./manage-resources-portal.md)
- [Gerir recursos do Azure com o Azure PowerShell](./manage-resources-powershell.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Implementar recursos no grupo de recursos existente

Pode implementar recursos do Azure diretamente com o Azure PowerShell ou implementar um modelo do Resource Manager para criar recursos do Azure.

### <a name="deploy-a-resource"></a>Implementar um recurso

O script seguinte cria uma conta de armazenamento.

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

O seguinte script cria implementar um modelo de início rápido para criar uma conta de armazenamento. Para obter mais informações, consulte [início rápido: Criar modelos Azure Resource Manager com o Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -Location $location
```

Para obter mais informações, consulte [implementar recursos com modelos do Resource Manager e o Azure PowerShell](./resource-group-template-deploy.md).

## <a name="deploy-a-resource-group-and-resources"></a>Implementar um grupo de recursos e recursos

Pode criar um grupo de recursos e implementar recursos no grupo. Para obter mais informações, consulte [criar o grupo de recursos e implementar recursos](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Implementar recursos em várias subscrições ou grupos de recursos

Normalmente, implementa todos os recursos no seu modelo para um grupo de recursos. No entanto, existem cenários onde pretende implementar um conjunto de recursos em conjunto, mas colocá-los em diferentes grupos de recursos ou subscrições. Para obter mais informações, consulte [recursos do Azure implementar a várias subscrições ou grupos de recursos](./resource-manager-cross-resource-group-deployment.md).

## <a name="delete-resources"></a>Eliminar recursos

O script seguinte mostra como eliminar uma conta de armazenamento.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Remove-AzStorageAccount -ResourceGroupName $resourceGroupName -AccountName $storageAccountName
```

Para obter mais informações sobre como o Azure Resource Manager ordena a eliminação de recursos, consulte [eliminação do grupo de recursos do Azure Resource Manager](./resource-group-delete.md).

## <a name="move-resources"></a>Mover recursos

O script seguinte mostra como remover uma conta de armazenamento de um grupo de recursos para outro grupo de recursos.

```azurepowershell-interactive
$srcResourceGroupName = Read-Host -Prompt "Enter the source Resource Group name"
$destResourceGroupName = Read-Host -Prompt "Enter the destination Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$storageAccount = Get-AzResource -ResourceGroupName $srcResourceGroupName -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $destResourceGroupName -ResourceId $storageAccount.ResourceId
```

Para seguir um tutorial, veja [Tutorial: Mover recursos do Azure para outro grupo de recursos ou subscrição](./resource-manager-tutorial-move-resources.md). 

Para obter mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição](resource-group-move-resources.md).

## <a name="lock-resources"></a>Bloquear recursos

Bloqueio evita que outros utilizadores na sua organização acidentalmente eliminem ou modifiquem recursos críticos, como o recurso, grupo de recursos ou subscrição do Azure. 

O seguinte script bloqueia uma conta de armazenamento, pelo que não é possível eliminar a conta.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

New-AzResourceLock -LockName LockStorage -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts 
```

O script seguinte obtém todos os bloqueios para uma conta de armazenamento:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts
```

O script seguinte elimina um bloqueio de uma conta de armazenamento:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$lockId = (Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts).LockId
Remove-AzResourceLock -LockId $lockId
```

Para obter mais informações, consulte [Bloquear recursos com o Azure Resource Manager](resource-group-lock-resources.md).

## <a name="tag-resources"></a>Etiquetar recursos

Ajuda a organizar logicamente o seu grupo de recursos e recursos de marcação. Para obter informações, consulte [utilizar etiquetas para organizar os recursos do Azure](./resource-group-using-tags.md#powershell).

## <a name="manage-access-to-resources"></a>Gerir o acesso aos recursos

[O controlo de acesso baseado em funções (RBAC)](../role-based-access-control/overview.md) é a forma de gerir o acesso a recursos no Azure. Para obter mais informações, consulte [gerir o acesso com RBAC e o Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Passos Seguintes

- Para obter o Azure Resource Manager, veja [descrição geral do Azure Resource Manager](./resource-group-overview.md).
- Para obter a sintaxe do modelo do Resource Manager, veja [compreender a estrutura e a sintaxe de modelos Azure Resource Manager](./resource-group-authoring-templates.md).
- Para saber como desenvolver modelos, veja a [tutoriais passo a passo](/azure/azure-resource-manager/).
- Para ver os esquemas de modelo do Azure Resource Manager, consulte [referência de modelo](/azure/templates/).