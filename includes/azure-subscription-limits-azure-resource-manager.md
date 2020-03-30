---
title: incluir ficheiro
description: incluir ficheiro
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 94fd7e692be31ba247e3342246d3940ed08ef9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334981"
---
| Recurso | Limite |
| --- | --- |
| Assinaturas por inquilino do Diretório Ativo Azure | Ilimitado. |
| [Coadministradores](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) por subscrição |Ilimitado. |
| [Grupos de recursos](../articles/azure-resource-manager/management/overview.md) por subscrição |980 |
| Tamanho do pedido do Gestor de Recursos Azure API |4.194.304 bytes. |
| Etiquetas por subscrição<sup>1</sup> |50 |
| Cálculos de etiquetas únicos por subscrição<sup>1</sup> | 10,000 |
| [Implementações de nível de subscrição](../articles/azure-resource-manager/templates/deploy-to-subscription.md) por localização | 800<sup>2</sup> |

<sup>1</sup> Pode aplicar até 50 tags diretamente a uma subscrição. No entanto, a subscrição pode conter um número ilimitado de tags que são aplicadas a grupos de recursos e recursos dentro da subscrição. O número de etiquetas por grupo de recursos ou recursos é limitado a 50. O Gestor de Recursos devolve uma [lista de nomes e valores únicos](/rest/api/resources/tags) na subscrição apenas quando o número de tags for de 10.000 ou menos. Ainda pode encontrar um recurso por etiqueta quando o número ultrapassa os 10.000.  

<sup>2</sup> Se atingir o limite de 800 implementações, elimine as implementações da história que já não são necessárias. Para eliminar as implementações de nível de subscrição, utilize [a eliminação de remove-azDeployment](/powershell/module/az.resources/Remove-AzDeployment) ou [az implementação sub eliminar](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-delete).
