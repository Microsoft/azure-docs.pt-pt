---
title: incluir ficheiro
description: incluir ficheiro
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: cdcf6215973755444da9e513761de7ac71e479d4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98738850"
---
| Recurso | Limite |
| --- | --- |
| Grupos de gestão por inquilino Azure AD | 10,000 |
| Assinaturas por grupo de gestão | Ilimitado. |
| Níveis de hierarquia do grupo de gestão | Nível de raiz mais 6 níveis<sup>1</sup> |
| Grupo de gestão direta de pais por grupo de gestão | Um |
| [Implementações de nível de grupo de gestão](../articles/azure-resource-manager/templates/deploy-to-management-group.md) por local | 800<sup>2</sup> |

<sup>1</sup> Os 6 níveis não incluem o nível de subscrição.

<sup>2</sup> Se atingir o limite de 800 implementações, elimine as implementações da história que já não são necessárias. Para eliminar as implementações de nível de grupo de gestão, utilize [remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) ou [az deployment mg delete](/cli/azure/deployment/mg#az-deployment-mg-delete).
