---
title: Gerir recursos - Azure CLI
description: Utilize o Azure CLI e o Azure Resource Manager para gerir os seus recursos. Mostra como implantar e apagar recursos.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: f00c1725201b0e49f80fec64e5d69b375ec7a233
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75485536"
---
# <a name="manage-azure-resources-by-using-azure-cli"></a>Gerir os recursos azure utilizando o Azure CLI

Aprenda a utilizar o Azure CLI com o [Azure Resource Manager](overview.md) para gerir os seus recursos Azure. Para gerir grupos de recursos, consulte manage os grupos de [recursos Do Manage Azure utilizando o Azure CLI](manage-resource-groups-cli.md).

Outros artigos sobre gestão de recursos:

- [Gerir os recursos do Azure utilizando o portal Azure](manage-resources-portal.md)
- [Gerir os recursos azure utilizando o Azure PowerShell](manage-resources-powershell.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Mobilizar recursos para um grupo de recursos existente

Pode implantar os recursos do Azure diretamente utilizando o Azure CLI, ou implementar um modelo de Gestor de Recursos para criar recursos Azure.

### <a name="deploy-a-resource"></a>Implementar um recurso

O seguinte script cria uma conta de armazenamento.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account create --resource-group $resourceGroupName --name $storageAccountName --location $location --sku Standard_LRS --kind StorageV2 &&
az storage account show --resource-group $resourceGroupName --name $storageAccountName 
```

### <a name="deploy-a-template"></a>Implementar um modelo

O seguinte script cria implementar um modelo Quickstart para criar uma conta de armazenamento. Para mais informações, consulte [Quickstart: Crie modelos de Gestor de Recursos Azure utilizando o Código do Estúdio Visual](../templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group deployment create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

Para mais informações, consulte [A implantação de recursos com modelos de Gestor de Recursos e ClI Azure](../templates/deploy-cli.md).

## <a name="deploy-a-resource-group-and-resources"></a>Implementar um grupo de recursos e recursos

Pode criar um grupo de recursos e implantar recursos para o grupo. Para mais informações, consulte [Criar grupo de recursos e implementar recursos.](../templates/deploy-to-subscription.md#resource-group-and-resources)

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Implementar recursos para múltiplas subscrições ou grupos de recursos

Normalmente, você implementa todos os recursos do seu modelo para um único grupo de recursos. No entanto, existem cenários em que pretende utilizar um conjunto de recursos, mas colocá-los em diferentes grupos de recursos ou subscrições. Para mais informações, consulte a Implantação de [recursos azure para várias subscrições ou grupos de recursos.](../templates/cross-resource-group-deployment.md)

## <a name="delete-resources"></a>Eliminar recursos

O seguinte script mostra como eliminar uma conta de armazenamento.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --resource-group $resourceGroupName --name $storageAccountName 
```

Para obter mais informações sobre como o Gestor de Recursos azure ordena a eliminação de recursos, consulte a eliminação do grupo de recursos do Gestor de [Recursos do Azure.](delete-resource-group.md)

## <a name="move-resources"></a>Mover recursos

O seguinte script mostra como remover uma conta de armazenamento de um grupo de recursos para outro grupo de recursos.

```azurecli-interactive
echo "Enter the source Resource Group name:" &&
read srcResourceGroupName &&
echo "Enter the destination Resource Group name:" &&
read destResourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
storageAccount=$(az resource show --resource-group $srcResourceGroupName --name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --query id --output tsv) &&
az resource move --destination-group $destResourceGroupName --ids $storageAccount
```

Para obter mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Bloquear recursos

O bloqueio impede que outros utilizadores da sua organização apagam ou modifiquem acidentalmente recursos críticos, tais como subscrição azure, grupo de recursos ou recursos. 

O seguinte script bloqueia uma conta de armazenamento para que a conta não possa ser eliminada.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock create --name LockSite --lock-type CanNotDelete --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts 
```

O seguinte script recebe todas as fechaduras para uma conta de armazenamento:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock list --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --parent ""
```

O seguinte script elimina um bloqueio de uma conta de armazenamento:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
lockId=$(az lock show --name LockSite --resource-group $resourceGroupName --resource-type Microsoft.Storage/storageAccounts --resource-name $storageAccountName --output tsv --query id)&&
az lock delete --ids $lockId
```

Para obter mais informações, consulte [Bloquear recursos com o Azure Resource Manager](lock-resources.md).

## <a name="tag-resources"></a>Etiquetar recursos

A marcação ajuda a organizar o seu grupo de recursos e recursos logicamente. Para obter informações, consulte [Utilizar tags para organizar os seus recursos Azure.](tag-resources.md#azure-cli)

## <a name="manage-access-to-resources"></a>Gerir o acesso aos recursos

[O controlo de acesso baseado em funções (RBAC)](../../role-based-access-control/overview.md) é a forma como gere o acesso aos recursos em Azure. Para mais informações, consulte [Gerir o acesso utilizando rBAC e Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Passos seguintes

- Para aprender O Gestor de Recursos Azure, consulte a [visão geral do Gestor de Recursos do Azure.](overview.md)
- Para aprender a sintaxe do modelo do Gestor de Recursos, consulte [Compreender a estrutura e a sintaxe dos modelos do Gestor](../templates/template-syntax.md)de Recursos Azure .
- Para aprender a desenvolver modelos, consulte os [tutoriais passo a passo.](/azure/azure-resource-manager/)
- Para ver os esquemas de modelo do Gestor de Recursos Azure, consulte a referência do [modelo](/azure/templates/).
