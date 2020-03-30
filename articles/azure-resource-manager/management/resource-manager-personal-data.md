---
title: Dados pessoais
description: Saiba como gerir os dados pessoais associados às operações do Gestor de Recursos do Azure.
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: 22cfc1b6096980f3d10db404a1c4e02f2de355d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485263"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Gerir dados pessoais associados ao Gestor de Recursos Azure

Para evitar expor informações sensíveis, elimine quaisquer informações pessoais que possa ter fornecido em implementações, grupos de recursos ou tags. O Azure Resource Manager fornece operações que lhe permitem gerir dados pessoais que possa ter fornecido em implementações, grupos de recursos ou tags.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="delete-personal-data-in-deployment-history"></a>Eliminar dados pessoais no histórico de implementação

Para implementações, o Gestor de Recursos mantém valores de parâmetros e mensagens de estado no histórico de implementação. Estes valores persistem até eliminar a implantação da história. Para ver se forneceu dados pessoais nestes valores, enumere as implementações. Se encontrar dados pessoais, elimine as implementações do histórico.

Para listar **as implementações** na história, utilize:

* [Lista por Grupo de Recursos](/rest/api/resources/deployments/listbyresourcegroup)
* [Implantação do Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroupDeployment)
* [lista de implantação do grupo AZ](/cli/azure/group/deployment#az-group-deployment-list)

Para eliminar **as implementações** do histórico, utilize:

* [Eliminar](/rest/api/resources/deployments/delete)
* [Remoção-AzResourceGroupDeployment](/powershell/module/az.resources/Remove-AzResourceGroupDeployment)
* [az grupo eliminar](/cli/azure/group/deployment#az-group-deployment-delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Eliminar dados pessoais em nomes de grupos de recursos

O nome do grupo de recursos persiste até eliminar o grupo de recursos. Para ver se forneceu dados pessoais nos nomes, enumere os grupos de recursos. Se encontrar dados pessoais, [desloque os recursos](move-resource-group-and-subscription.md) para um novo grupo de recursos e elimine o grupo de recursos com dados pessoais no nome.

Para listar grupos de **recursos,** utilize:

* [Lista](/rest/api/resources/resourcegroups/list)
* [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup)
* [lista de grupo az](/cli/azure/group#az-group-list)

Para eliminar grupos de **recursos,** utilize:

* [Eliminar](/rest/api/resources/resourcegroups/delete)
* [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>Eliminar dados pessoais em tags

As etiquetas de nomes e valores persistem até que apague ou modifique a etiqueta. Para ver se forneceu dados pessoais nas etiquetas, enumere as etiquetas. Se encontrar dados pessoais, elimine as etiquetas.

Para listar **etiquetas,** utilize:

* [Lista](/rest/api/resources/tags/list)
* [Get-AzTag](/powershell/module/az.resources/Get-AzTag)
* [az lista de tag](/cli/azure/tag#az-tag-list)

Para eliminar **etiquetas,** utilize:

* [Eliminar](/rest/api/resources/tags/delete)
* [Remover-AzTag](/powershell/module/az.resources/Remove-AzTag)
* [az tag excluir](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>Passos seguintes
* Para uma visão geral do Gestor de Recursos Azure, consulte o [What is Resource Manager?](overview.md)