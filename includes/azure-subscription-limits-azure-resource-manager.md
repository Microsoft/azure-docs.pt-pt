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
ms.openlocfilehash: b7db49d09d2292c08ce33ce86b1b7f427ef75fbc
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77618495"
---
| Recurso | Limite predefinido | Limite máximo |
| --- | --- | --- |
| Assinaturas por inquilino do Diretório Ativo Azure | Ilimitado. | Ilimitado. |
| [Coadministradores](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) por subscrição |Ilimitado. |Ilimitado. |
| [Grupos de recursos](../articles/azure-resource-manager/management/overview.md) por subscrição |980 |980 |
| Tamanho do pedido do Gestor de Recursos Azure API |4\.194.304 bytes. |4\.194.304 bytes. |
| Etiquetas por subscrição<sup>1</sup> |Ilimitado. |Ilimitado. |
| Cálculos de etiquetas únicos por subscrição<sup>1</sup> | 10,000 | 10,000 |
| [Implementações de nível de subscrição](../articles/azure-resource-manager/templates/deploy-to-subscription.md) por localização | 800<sup>2</sup> | 800 |

<sup>1</sup> Pode aplicar um número ilimitado de etiquetas por subscrição. O número de etiquetas por grupo de recursos ou recursos é limitado a 50. O Gestor de Recursos devolve uma [lista de nomes e valores únicos](/rest/api/resources/tags) na subscrição apenas quando o número de tags for de 10.000 ou menos. Ainda pode encontrar um recurso por etiqueta quando o número ultrapassa os 10.000.  

<sup>2</sup> Se atingir o limite de 800 implementações, elimine as implementações da história que já não são necessárias. Para eliminar as implementações de nível de subscrição, utilize a eliminação de [remove-azDeployment](/powershell/module/az.resources/Remove-AzDeployment) ou [a implementação az](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete).
