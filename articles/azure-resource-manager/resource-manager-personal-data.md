---
title: Os dados pessoais do Azure Resource Manager | Documentos da Microsoft
description: Saiba como gerir dados pessoais associados a operações do Azure Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/14/2018
ms.author: tomfitz
ms.openlocfilehash: db4e1b8705b879fd5716763869462bafdf1f905c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66128459"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Gerir dados pessoais associados com o Azure Resource Manager

Para evitar expor informações confidenciais, elimine quaisquer informações pessoais, que pode ter fornecido em implementações, grupos de recursos ou etiquetas. O Azure Resource Manager proporciona operações que lhe permitem gerir dados pessoais que tenha fornecido em implementações, grupos de recursos ou etiquetas.

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="delete-personal-data-in-deployment-history"></a>Eliminar os dados pessoais no histórico de implementação

Para implementações, o Resource Manager mantém valores de parâmetros e as mensagens de estado no histórico de implementação. Estes valores persistirem até que elimine a implementação do histórico. Para ver se forneceu os dados pessoais nestes valores, liste as implementações. Se localizar dados pessoais, elimine as implementações do histórico.

A lista **implementações** na história, utilize:

* [Lista por grupo de recursos](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment)
* [az group deployment list](/cli/azure/group/deployment#az-group-deployment-list)

Para eliminar **implementações** a partir do histórico, utilize:

* [Eliminar](/rest/api/resources/deployments/delete)
* [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/Remove-AzResourceGroupDeployment)
* [az group deployment delete](/cli/azure/group/deployment#az-group-deployment-delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Eliminar dados pessoais em nomes de grupo de recursos

O nome do grupo de recursos persiste até que elimine o grupo de recursos. Para ver se forneceu os dados pessoais nos nomes, liste os grupos de recursos. Se encontrar os dados pessoais [mover os recursos](resource-group-move-resources.md) para um novo grupo de recursos e a eliminar o grupo de recursos com os dados pessoais no nome.

A lista **grupos de recursos**, utilize:

* [Lista](/rest/api/resources/resourcegroups/list)
* [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup)
* [az group list](/cli/azure/group#az-group-list)

Para eliminar **grupos de recursos**, utilize:

* [Eliminar](/rest/api/resources/resourcegroups/delete)
* [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>Eliminar dados pessoais em etiquetas

Nomes de etiquetas e valores persistirem até que elimine ou modifique a marca. Para ver se forneceu os dados pessoais nas etiquetas, liste as etiquetas. Se localizar dados pessoais, elimine as etiquetas.

A lista **etiquetas**, utilize:

* [Lista](/rest/api/resources/tags/list)
* [Get-AzTag](/powershell/module/az.resources/Get-AzTag)
* [az tag list](/cli/azure/tag#az-tag-list)

Para eliminar **etiquetas**, utilize:

* [Eliminar](/rest/api/resources/tags/delete)
* [Remove-AzTag](/powershell/module/az.resources/Remove-AzTag)
* [az tag delete](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>Passos Seguintes
* Para obter uma descrição geral do Azure Resource Manager, consulte o [o que é o Resource Manager?](resource-group-overview.md)