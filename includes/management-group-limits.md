---
title: ficheiro de inclusão
description: ficheiro de inclusão
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 204ca121045d1bd53fd507acc7ea9691bfeac12e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88748804"
---
| Recurso | Limite |
| --- | --- |
| Grupos de gestão por inquilino Azure AD | 10,000 |
| Assinaturas por grupo de gestão | Ilimitado. |
| Níveis de hierarquia do grupo de gestão | Nível de raiz mais 6 níveis<sup>1</sup> |
| Grupo de gestão direta de pais por grupo de gestão | Um |
| [Implementações de nível de grupo de gestão](../articles/azure-resource-manager/templates/deploy-to-management-group.md) por local | 800<sup>2</sup> |

<sup>1</sup> Os 6 níveis não incluem o nível de subscrição.

<sup>2</sup> Se atingir o limite de 800 implementações, elimine as implementações da história que já não são necessárias. Para eliminar as implementações de nível de grupo de gestão, utilize [remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) ou [az deployment mg delete](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-delete).
