---
title: Compreenda Azure negar atribuições - Azure RBAC
description: Saiba mais sobre a Azure negar atribuições no controlo de acesso baseado em funções Azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/26/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: a5f17f009caa9306631debf511f2c890f8f2a450
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82733778"
---
# <a name="understand-azure-deny-assignments"></a>Compreenda que Azure nega atribuições

Semelhante a uma atribuição de funções, uma *atribuição de negação* anexa um conjunto de ações de negação a um utilizador, grupo ou principal de serviço em um determinado âmbito para o propósito de negar o acesso. Negar que as atribuições bloqueiam os utilizadores de realizarem ações específicas de recursos Azure, mesmo que uma atribuição de papel lhes conceda acesso.

Este artigo descreve como as atribuições de negação são definidas.

## <a name="how-deny-assignments-are-created"></a>Como as atribuições de negação são criadas

As missões de negação são criadas e geridas pela Azure para proteger os recursos. As aplicações geridas pela Azure Blueprints e Azure utilizam atribuições de negação para proteger recursos geridos pelo sistema. As aplicações geridas Azure Blueprints e Azure são a única forma de negar atribuições. Não podes criar diretamente as tuas próprias missões de negação. Para obter mais informações sobre como a Blueprints usa atribuições de negação para bloquear recursos, consulte [o bloqueio de recursos em Azure Blueprints](../governance/blueprints/concepts/resource-locking.md).

> [!NOTE]
> Não podes criar diretamente as tuas próprias missões de negação.

## <a name="compare-role-assignments-and-deny-assignments"></a>Compare atribuições de funções e negue atribuições

Negar atribuições seguem um padrão semelhante ao das atribuições de funções, mas também têm algumas diferenças.

| Funcionalidade | Atribuição de função | Negar atribuição |
| --- | --- | --- |
| Conceder acesso | :heavy_check_mark: |  |
| Negar o acesso |  | :heavy_check_mark: |
| Pode ser criado diretamente | :heavy_check_mark: |  |
| Aplicar num âmbito | :heavy_check_mark: | :heavy_check_mark: |
| Excluir os principais |  | :heavy_check_mark: |
| Prevenir a herança para os âmbitos infantis |  | :heavy_check_mark: |
| Aplicar-se a atribuições [clássicas de administrador de subscrição](rbac-and-directory-admin-roles.md) |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>Negar propriedades de atribuição

 Uma atribuição de negação tem as seguintes propriedades:

> [!div class="mx-tableFixed"]
> | Propriedade | Necessário | Tipo | Descrição |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Sim | String | O nome de exibição da missão de negação. Os nomes devem ser únicos para um determinado âmbito. |
> | `Description` | Não | String | A descrição da missão de negação. |
> | `Permissions.Actions` | Pelo menos uma ação ou uma DataActions | Corda[] | Uma série de cadeias que especificam as operações de gestão às quais a missão de negação bloqueia o acesso. |
> | `Permissions.NotActions` | Não | Corda[] | Uma série de cadeias que especificam as operações de gestão para excluir da atribuição de negação. |
> | `Permissions.DataActions` | Pelo menos uma ação ou uma DataActions | Corda[] | Uma série de cadeias que especificam as operações de dados às quais a atribuição de negação bloqueia o acesso. |
> | `Permissions.NotDataActions` | Não | Corda[] | Uma série de cadeias que especificam as operações de dados para excluir da atribuição de negação. |
> | `Scope` | Não | String | Uma cadeia que especifica o âmbito a que a atribuição de negação se aplica. |
> | `DoNotApplyToChildScopes` | Não | Booleano | Especifica se a atribuição de negação se aplica aos âmbitos infantis. O valor predefinido é falso. |
> | `Principals[i].Id` | Sim | Corda[] | Uma matriz de IDs de objeto principal Azure AD (utilizador, grupo, principal de serviço ou identidade gerida) a que se aplica a atribuição de negação. Definir para um GUID vazio `00000000-0000-0000-0000-000000000000` para representar todos os principais. |
> | `Principals[i].Type` | Não | Corda[] | Uma série de tipos de objetos representados pelos principais[i].Id. Definido `SystemDefined` para representar todos os principais. |
> | `ExcludePrincipals[i].Id` | Não | Corda[] | Uma série de IDs de objeto principal Azure AD (utilizador, grupo, principal de serviço ou identidade gerida) aos quais a atribuição de negação não se aplica. |
> | `ExcludePrincipals[i].Type` | Não | Corda[] | Uma série de tipos de objetos representados por ExcludePrincipals[i].Id. |
> | `IsSystemProtected` | Não | Booleano | Especifica se esta atribuição de negação foi criada pela Azure e não pode ser editada ou eliminada. Atualmente, todas as missões de negação estão protegidas pelo sistema. |

## <a name="the-all-principals-principal"></a>O diretor de todos os diretores

Para apoiar as missões de negação, foi introduzido um diretor definido pelo sistema chamado *Todos os Principais.* Este principal representa todos os utilizadores, grupos, diretores de serviços e identidades geridas num diretório AD Azure. Se o ID principal for um GUID zero `00000000-0000-0000-0000-000000000000` e o tipo principal `SystemDefined` for, o principal representa todos os principais. Na saída Azure PowerShell, todos os principais parecem ser os seguintes:

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

Todos os diretores podem ser combinados `ExcludePrincipals` para negar todos os principais, exceto alguns utilizadores. Todos os diretores têm os seguintes constrangimentos:

- Pode ser utilizado apenas `Principals` dentro e não pode ser usado em `ExcludePrincipals` .
- `Principals[i].Type` deve ser definido para `SystemDefined` .

## <a name="next-steps"></a>Passos seguintes

* [Tutorial: Proteja novos recursos com bloqueios de recursos da Azure Blueprints](../governance/blueprints/tutorials/protect-new-resources.md)
* [Lista Azure nega atribuições usando o portal Azure](deny-assignments-portal.md)
