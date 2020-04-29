---
title: incluir ficheiro
description: incluir ficheiro
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 9b2bf8763d175bca028be1fcceda921e87eb5b23
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335113"
---
| Recurso | Limite |
| --- | --- |
| Grupos de gestão por diretório | 10,000 |
| Assinaturas por grupo de gestão | Ilimitado. |
| Níveis de hierarquia do grupo de gestão | Nível raiz mais 6 níveis<sup>1</sup> |
| Grupo de gestão de pais diretos por grupo de gestão | Um |
| [Implementações de nível de grupo](../articles/azure-resource-manager/templates/deploy-to-management-group.md) de gestão por localização | 800<sup>2</sup> |

<sup>1</sup> Os 6 níveis não incluem o nível de subscrição.

<sup>2</sup> Se atingir o limite de 800 implementações, elimine as implementações da história que já não são necessárias. Para eliminar as implementações de nível de grupo de gestão, utilize [a remoção de remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) ou [az deployment mg eliminar](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-delete).
