---
title: Gerir recursos - Azure CLI
description: Utilize o Azure CLI e o Azure Resource Manager para gerir os seus recursos. Mostra como implantar e eliminar recursos.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: 077ebdb4dd33249923064a4f5ed20c5641a82e26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97695892"
---
# <a name="manage-azure-resources-by-using-azure-cli"></a>Gerir os recursos do Azure utilizando o Azure CLI

Saiba como utilizar o Azure CLI com [o Azure Resource Manager](overview.md) para gerir os seus recursos Azure. Para gerir grupos de recursos, consulte [os grupos de recursos Manage Azure utilizando o Azure CLI](manage-resource-groups-cli.md).

Outros artigos sobre gestão de recursos:

- [Gerir os recursos do Azure utilizando o portal Azure](manage-resources-portal.md)
- [Gerir os recursos da Azure utilizando a Azure PowerShell](manage-resources-powershell.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Mobilizar recursos para um grupo de recursos existente

Pode implementar os recursos do Azure diretamente utilizando o CLI do Azure ou implementar um modelo de Gestor de Recursos para criar recursos Azure.

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

O seguinte script cria uma implementação de um modelo Quickstart para criar uma conta de armazenamento. Para obter mais informações, consulte [Quickstart: Crie modelos ARM com Código de Estúdio Visual](../templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az deployment group create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

Para obter mais informações, consulte [implementar recursos com modelos de Gestor de Recursos e Azure CLI](../templates/deploy-cli.md).

## <a name="deploy-a-resource-group-and-resources"></a>Implementar um grupo de recursos e recursos

Pode criar um grupo de recursos e mobilizar recursos para o grupo. Para obter mais informações, consulte [criar grupo de recursos e implementar recursos.](../templates/deploy-to-subscription.md#resource-groups)

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Mobilizar recursos para várias subscrições ou grupos de recursos

Normalmente, você implanta todos os recursos no seu modelo para um único grupo de recursos. No entanto, existem cenários em que pretende implementar um conjunto de recursos em conjunto, mas colocá-los em diferentes grupos de recursos ou subscrições. Para obter mais informações, consulte [recursos do Deploy Azure para várias subscrições ou grupos de recursos.](../templates/deploy-to-resource-group.md)

## <a name="delete-resources"></a>Eliminar recursos

O seguinte script mostra como apagar uma conta de armazenamento.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --resource-group $resourceGroupName --name $storageAccountName 
```

Para obter mais informações sobre como o Azure Resource Manager ordena a supressão de recursos, consulte a eliminação do [grupo de recursos Azure Resource Manager](delete-resource-group.md).

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

O bloqueio impede que outros utilizadores da sua organização apaguem ou modifiquem acidentalmente recursos críticos, tais como subscrição do Azure, grupo de recursos ou recursos. 

O seguinte script bloqueia uma conta de armazenamento para que a conta não possa ser eliminada.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock create --name LockSite --lock-type CanNotDelete --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts 
```

O seguinte script obtém todas as fechaduras para uma conta de armazenamento:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock list --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --parent ""
```

O seguinte script elimina uma fechadura de uma conta de armazenamento:

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

A marcação ajuda a organizar logicamente o seu grupo de recursos e recursos. Para obter informações, consulte [a utilização de tags para organizar os seus recursos Azure.](tag-resources.md#azure-cli)

## <a name="manage-access-to-resources"></a>Gerir o acesso aos recursos

[O controlo de acesso baseado em funções (Azure RBAC)](../../role-based-access-control/overview.md) é a forma como gere o acesso aos recursos em Azure. Para obter mais informações, consulte [Adicionar ou remover atribuições de funções Azure utilizando O Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Passos seguintes

- Para saber o Gestor de Recursos Azure, consulte [a visão geral do Azure Resource Manager](overview.md).
- Para aprender a sintaxe do modelo do Gestor de Recursos, consulte [compreender a estrutura e a sintaxe dos modelos do Gestor de Recursos Azure](../templates/template-syntax.md).
- Para aprender a desenvolver modelos, consulte os [tutoriais passo a passo.](../index.yml)
- Para ver os esquemas de modelo do Gestor de Recursos Azure, consulte [a referência do modelo](/azure/templates/).