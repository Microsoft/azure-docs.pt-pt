---
title: Dados pessoais
description: Saiba como gerenciar dados pessoais associados a operações de Azure Resource Manager.
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: 22cfc1b6096980f3d10db404a1c4e02f2de355d2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75485263"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Gerenciar dados pessoais associados ao Azure Resource Manager

Para evitar a exposição de informações confidenciais, exclua todas as informações pessoais que você tenha fornecido em implantações, grupos de recursos ou marcas. O Azure Resource Manager fornece operações que permitem gerenciar dados pessoais que você pode ter fornecido em implantações, grupos de recursos ou marcas.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="delete-personal-data-in-deployment-history"></a>Excluir dados pessoais no histórico de implantação

Para implantações, o Resource Manager retém valores de parâmetro e mensagens de status no histórico de implantação. Esses valores persistem até que você exclua a implantação do histórico. Para ver se você forneceu dados pessoais nesses valores, liste as implantações. Se você encontrar dados pessoais, exclua as implantações do histórico.

Para listar as **implantações** no histórico, use:

* [Listar por grupo de recursos](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment)
* [az group deployment list](/cli/azure/group/deployment#az-group-deployment-list)

Para excluir **implantações** do histórico, use:

* [Eliminar](/rest/api/resources/deployments/delete)
* [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/Remove-AzResourceGroupDeployment)
* [excluir implantação de grupo AZ](/cli/azure/group/deployment#az-group-deployment-delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Excluir dados pessoais em nomes de grupos de recursos

O nome do grupo de recursos persiste até que você exclua o grupo de recursos. Para ver se você forneceu dados pessoais nos nomes, liste os grupos de recursos. Se você encontrar dados pessoais, [mova os recursos](move-resource-group-and-subscription.md) para um novo grupo de recursos e exclua o grupo de recursos com dados pessoais no nome.

Para listar **grupos de recursos**, use:

* [Lista](/rest/api/resources/resourcegroups/list)
* [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup)
* [az group list](/cli/azure/group#az-group-list)

Para excluir **grupos de recursos**, use:

* [Eliminar](/rest/api/resources/resourcegroups/delete)
* [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>Excluir dados pessoais em marcas

Os nomes e os valores das marcas permanecem até que você exclua ou modifique a marca. Para ver se você forneceu dados pessoais nas marcas, liste as marcas. Se você encontrar dados pessoais, exclua as marcas.

Para listar **marcas**, use:

* [Lista](/rest/api/resources/tags/list)
* [Get-AzTag](/powershell/module/az.resources/Get-AzTag)
* [az tag list](/cli/azure/tag#az-tag-list)

Para excluir **marcas**, use:

* [Eliminar](/rest/api/resources/tags/delete)
* [Remove-AzTag](/powershell/module/az.resources/Remove-AzTag)
* [az tag delete](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>Passos seguintes
* Para obter uma visão geral do Azure Resource Manager, consulte o [que é o Gerenciador de recursos?](overview.md)