---
title: incluir ficheiro
description: incluir ficheiro
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 02/10/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 32d39493e526a4b95369e2998d3f9ade9f2964dc
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133732"
---
| Recurso | Limite predefinido | Limite máximo |
| --- | --- | --- |
| [Grupos de recursos](../articles/azure-resource-manager/management/overview.md) por subscrição |980 |980 |
| Tamanho da solicitação da API Azure Resource Manager |4\.194.304 bytes. |4\.194.304 bytes. |
| Etiquetas por subscrição<sup>1</sup> |Ilimitado. |Ilimitado. |
| Cálculos de etiquetas únicos por subscrição<sup>1</sup> | 10,000 | 10,000 |
| [Implementações de nível de subscrição](../articles/azure-resource-manager/templates/deploy-to-subscription.md) por localização | 800<sup>2</sup> | 800 |
| Assinaturas por inquilino do Diretório Ativo Azure | Ilimitado. | Ilimitado. |
| [Coadministradores](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) por subscrição |Ilimitado. |Ilimitado. |

<sup>1</sup> Pode aplicar um número ilimitado de etiquetas por subscrição. O número de marcas por recurso ou grupo de recursos é limitado a 50. O Gestor de Recursos devolve uma [lista de nomes e valores únicos](/rest/api/resources/tags) na subscrição apenas quando o número de tags for de 10.000 ou menos. Você ainda pode encontrar um recurso por marca quando o número excede 10.000.  

<sup>2</sup> Se atingir o limite de 800 implementações, elimine as implementações da história que já não são necessárias. Para eliminar as implementações de nível de subscrição, utilize a eliminação de [remove-azDeployment](/powershell/module/az.resources/Remove-AzDeployment) ou [a implementação az](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete).
