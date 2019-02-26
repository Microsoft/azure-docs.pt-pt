---
title: Gerir grupos do Azure Resource Manager com CLI do Azure | Documentos da Microsoft
description: Utilize a CLI do Azure para gerir os seus grupos do Azure Resource Manager.
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
ms.openlocfilehash: c9e6cc68cbd629642849d323d4271722f3232aa3
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56825207"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Gerir grupos de recursos do Azure Resource Manager com CLI do Azure

Saiba como utilizar a CLI do Azure com [do Azure Resource Manager](resource-group-overview.md) para gerir os seus grupos de recursos do Azure. Para gerir recursos do Azure, veja [recursos de gerir o Azure com CLI do Azure](./manage-resources-cli.md).

Outros artigos sobre a gestão de grupos de recursos:

- [Gerir grupos de recursos do Azure com o portal do Azure](./manage-resources-portal.md)
- [Gerir grupos de recursos do Azure com o Azure PowerShell](./manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>O que é um grupo de recursos

Um grupo de recursos é um contentor que mantém recursos relacionados para uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que pretende gerir como um grupo. Decida como pretende atribuir recursos a grupos de recursos com base no que é mais adequado para a sua organização. Em geral, adicione recursos que partilham o mesmo ciclo de vida no mesmo grupo de recursos para que possa facilmente implantar, atualizar e eliminá-los como um grupo.

O grupo de recursos armazena metadados sobre os recursos. Por conseguinte, quando especifica uma localização para o grupo de recursos, está a especificar onde esses metadados estão armazenados. Por motivos de conformidade, poderá ter de certificar que os dados estão armazenados numa determinada região.

O grupo de recursos armazena metadados sobre os recursos. Quando especificar uma localização para o grupo de recursos, especifica onde esses metadados estão armazenados.

## <a name="create-resource-groups"></a>Criar grupos de recursos

O seguinte script CLI cria um grupo de recursos e, em seguida, mostra o grupo de recursos.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location $location
```

## <a name="list-resource-groups"></a>Listar grupos de recursos

O script da CLI seguinte lista os grupos de recursos na sua subscrição.

```azurecli-interactive
az group list
```

Para obter um grupo de recursos:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Eliminar grupos de recursos

O script da CLI seguinte elimina um grupo de recursos:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

Para obter mais informações sobre como o Azure Resource Manager ordena a eliminação de recursos, consulte [eliminação do grupo de recursos do Azure Resource Manager](./resource-group-delete.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Implementar recursos no grupo de recursos existente

Ver [implementar recursos no grupo de recursos existente](./manage-resources-cli.md#deploy-resources-to-an-existing-resource-group).

## <a name="deploy-a-resource-group-and-resources"></a>Implementar um grupo de recursos e recursos

Pode criar um grupo de recursos e implementar recursos no grupo utilizando um modelo do Resource Manager. Para obter mais informações, consulte [criar o grupo de recursos e implementar recursos](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## <a name="move-to-another-resource-group-or-subscription"></a>Mover para outro grupo de recursos ou subscrição

Pode mover os recursos no grupo para outro grupo de recursos. Para obter mais informações, consulte [mover recursos](./manage-resources-cli.md#move-resources).

## <a name="lock-resource-groups"></a>Grupos de recursos de bloqueio

Bloqueio evita que outros utilizadores na sua organização acidentalmente eliminem ou modifiquem recursos críticos, como o recurso, grupo de recursos ou subscrição do Azure. 

O seguinte script bloqueia um grupo de recursos para que não é possível eliminar o grupo de recursos.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName  
```

O script seguinte obtém todos os bloqueios para um grupo de recursos:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock list --resource-group $resourceGroupName  
```

O script seguinte elimina um bloqueio:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the lock name:" &&
read lockName &&
az lock delete --name $lockName --resource-group $resourceGroupName
```

Para obter mais informações, consulte [Bloquear recursos com o Azure Resource Manager](resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Grupos de recursos de etiqueta

Pode aplicar etiquetas a grupos de recursos e recursos para organizar logicamente os recursos. Para obter informações, consulte [utilizar etiquetas para organizar os recursos do Azure](./resource-group-using-tags.md#azure-cli).

## <a name="export-resource-groups-to-templates"></a>Grupos de recursos de exportação para modelos

Depois de configurar o seu grupo de recursos com êxito, poderá ver o modelo do Resource Manager para o grupo de recursos. Exportar o modelo oferece duas vantagens:

- Automatize implementações futuras da solução porque o modelo contém toda a infraestrutura completa.
- Aprenda a sintaxe do modelo ao procurar em JavaScript Object Notation (JSON) que representa a sua solução.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName  
```

O script apresenta o modelo na consola.  Copie o JSON e guarde como um ficheiro.

Para obter mais informações, consulte [grupo de recursos de exportação](./manage-resource-groups-portal.md#export-resource-groups-to-templates).

## <a name="manage-access-to-resource-groups"></a>Gerir o acesso aos grupos de recursos

[O controlo de acesso baseado em funções (RBAC)](../role-based-access-control/overview.md) é a forma de gerir o acesso a recursos no Azure. Para obter mais informações, consulte [gerir o acesso com RBAC e a CLI do Azure](../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Passos Seguintes

- Para obter o Azure Resource Manager, veja [descrição geral do Azure Resource Manager](./resource-group-overview.md).
- Para obter a sintaxe do modelo do Resource Manager, veja [compreender a estrutura e a sintaxe de modelos Azure Resource Manager](./resource-group-authoring-templates.md).
- Para saber como desenvolver modelos, veja a [tutoriais passo a passo](/azure/azure-resource-manager/).
- Para ver os esquemas de modelo do Azure Resource Manager, consulte [referência de modelo](/azure/templates/).