---
title: Dados pessoais
description: Saiba como gerir os dados pessoais associados às operações do Azure Resource Manager.
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: 2f616fa58c53a7f666978bd1db60d10a9266e342
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934211"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Gerir dados pessoais associados ao Azure Resource Manager

Para evitar expor informações sensíveis, elimine qualquer informação pessoal que possa ter fornecido em implementações, grupos de recursos ou tags. O Azure Resource Manager fornece operações que lhe permitem gerir os dados pessoais que pode ter fornecido em implementações, grupos de recursos ou tags.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="delete-personal-data-in-deployment-history"></a>Eliminar dados pessoais no histórico de implementação

Para implementações, o Gestor de Recursos mantém os valores dos parâmetros e as mensagens de estado no histórico de implantação. Estes valores persistem até eliminar a implantação da história. Para ver se forneceu dados pessoais nestes valores, liste as implementações. Se encontrar dados pessoais, elimine as implementações do histórico.

Para **listar implementações** na história, utilize:

* [Lista por Grupo de Recursos](/rest/api/resources/resources/deployments/listbyresourcegroup)
* [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment)
* [lista de grupos de implantação az](/cli/azure/deployment/group#az_deployment_group_list)

Para eliminar **as implementações** da história, utilize:

* [Eliminar](/rest/api/resources/resources/deployments/delete)
* [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/Remove-AzResourceGroupDeployment)
* [az grupo de implantação eliminar](/cli/azure/deployment/group#az_deployment_group_delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Eliminar dados pessoais em nomes de grupos de recursos

O nome do grupo de recursos persiste até eliminar o grupo de recursos. Para ver se forneceu dados pessoais nos nomes, liste os grupos de recursos. Se encontrar dados pessoais, [transloque os recursos](move-resource-group-and-subscription.md) para um novo grupo de recursos e elimine o grupo de recursos com dados pessoais no nome.

Para listar **grupos de recursos,** utilize:

* [Lista](/rest/api/resources/resources/resourcegroups/list)
* [Grupo Get-AzResource](/powershell/module/az.resources/Get-AzResourceGroup)
* [lista de grupos az](/cli/azure/group#az-group-list)

Para eliminar **grupos de recursos,** utilize:

* [Eliminar](/rest/api/resources/resources/resourcegroups/delete)
* [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>Eliminar dados pessoais em tags

Os nomes e valores das etiquetas persistem até que apague ou modifique a etiqueta. Para ver se forneceu dados pessoais nas etiquetas, liste as etiquetas. Se encontrar dados pessoais, elimine as etiquetas.

Para **listar tags,** utilize:

* [Lista](/rest/api/resources/resources/tags/list)
* [Get-AzTag](/powershell/module/az.resources/Get-AzTag)
* [lista de etiquetas az](/cli/azure/tag#az-tag-list)

Para eliminar **tags,** utilize:

* [Eliminar](/rest/api/resources/resources/tags/delete)
* [Remover-AzTag](/powershell/module/az.resources/Remove-AzTag)
* [az tag eliminar](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>Passos seguintes
* Para uma visão geral do Gestor de Recursos Azure, consulte o [Gestor de Recursos O que é Gestor de Recursos?](overview.md)