---
title: Entenda negar atribuições para recursos Azure
description: Saiba mais sobre a negação de atribuições no controlo de acesso baseado em papéis (RBAC) para recursos Azure.
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 7ab811635ca50c3a28ecd8bdf6d0f18fad4c384f
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137379"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>Entenda negar atribuições para recursos Azure

Semelhante a uma atribuição de funções, uma *atribuição de negação* atribui um conjunto de ações de negação a um utilizador, grupo ou diretor de serviço num determinado âmbito para negar o acesso. As atribuições de negação bloqueiam os utilizadores de realizarem ações específicas de recursos Do Azure, mesmo que uma atribuição de funções lhes conceda acesso.

Este artigo descreve como as missões de negação são definidas.

## <a name="how-deny-assignments-are-created"></a>Como é que as missões de negação são criadas

As missões de negação são criadas e geridas pela Azure para proteger recursos. As aplicações geridas pelo Azure Blueprints e azure utilizam missões de negação para proteger recursos geridos pelo sistema. As aplicações geridas pelo Azure Blueprints e Azure são a única forma de negar atribuições. Não podes criar diretamente as tuas próprias missões de negação.  Para mais informações, consulte Proteja novos recursos com fechaduras de [recursos da Azure Blueprints](../governance/blueprints/tutorials/protect-new-resources.md).

> [!NOTE]
> Não podes criar diretamente as tuas próprias missões de negação.

## <a name="compare-role-assignments-and-deny-assignments"></a>Compare atribuições de papéis e negue atribuições

As atribuições de negação seguem um padrão semelhante ao de atribuições de papéis, mas também têm algumas diferenças.

| Capacidade | Atribuição de função | Negar a atribuição |
| --- | --- | --- |
| Conceder acesso | :heavy_check_mark: |  |
| Negar o acesso |  | :heavy_check_mark: |
| Pode ser criado diretamente | :heavy_check_mark: |  |
| Aplicar num âmbito | :heavy_check_mark: | :heavy_check_mark: |
| Excluir os principais |  | :heavy_check_mark: |
| Impedir a herança aos âmbitos infantis |  | :heavy_check_mark: |
| Aplicar-se a atribuições clássicas de [administrador de subscrição](rbac-and-directory-admin-roles.md) |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>Negar propriedades de atribuição

 Uma atribuição de negação tem as seguintes propriedades:

> [!div class="mx-tableFixed"]
> | Propriedade | Necessário | Tipo | Descrição |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Sim | Cadeia | O nome da exibição da missão de negação. Os nomes devem ser únicos para um dado âmbito. |
> | `Description` | Não | Cadeia | A descrição da atribuição de negação. |
> | `Permissions.Actions` | Pelo menos uma Ação ou uma Ação de Dados | Corda[] | Uma série de cordas que especificam as operações de gestão a que a missão de negação bloqueia o acesso. |
> | `Permissions.NotActions` | Não | Corda[] | Um conjunto de cordas que especificam as operações de gestão para excluir da atribuição de negação. |
> | `Permissions.DataActions` | Pelo menos uma Ação ou uma Ação de Dados | Corda[] | Um conjunto de cordas que especificam as operações de dados a que a atribuição de negação bloqueia o acesso. |
> | `Permissions.NotDataActions` | Não | Corda[] | Um conjunto de cordas que especificam as operações de dados para excluir da atribuição de negação. |
> | `Scope` | Não | Cadeia | Uma cadeia que especifica o âmbito a que a atribuição de negação se aplica. |
> | `DoNotApplyToChildScopes` | Não | Booleano | Especifica se a atribuição de negação se aplica aos âmbitos infantis. O valor predefinido é falso. |
> | `Principals[i].Id` | Sim | Corda[] | Uma série de IDs principais de objetos Azure AD (utilizador, grupo, diretor de serviço ou identidade gerida) a que se aplica a atribuição de negação. Definir para um `00000000-0000-0000-0000-000000000000` GUIA vazio para representar todos os principais. |
> | `Principals[i].Type` | Não | Corda[] | Uma série de tipos de objetos representados pelos Diretores[i].Id. set para `SystemDefined` para representar todos os principais. |
> | `ExcludePrincipals[i].Id` | Não | Corda[] | Uma série de IDs principais de objetos Azure AD (utilizador, grupo, diretor de serviço ou identidade gerida) às quais a atribuição de negação não se aplica. |
> | `ExcludePrincipals[i].Type` | Não | Corda[] | Uma série de tipos de objetos representados por ExcluiPrincipais[i].Id. |
> | `IsSystemProtected` | Não | Booleano | Especifica se esta atribuição de negação foi criada pela Azure e não pode ser editada ou eliminada. Atualmente, todas as missões de negação estão protegidas pelo sistema. |

## <a name="the-all-principals-principal"></a>O diretor de Todos os Principais

Para apoiar a negação de atribuições, foi introduzido um diretor definido pelo sistema chamado *All Principais.* Este principal representa todos os utilizadores, grupos, diretores de serviços e identidades geridas num diretório Azure AD. Se o ID principal for um `00000000-0000-0000-0000-000000000000` GUIA zero e o tipo principal for `SystemDefined`, o principal representa todos os principais. Na saída Azure PowerShell, todos os principais parecem ser os seguintes:

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

Todos os Principais podem ser combinados com `ExcludePrincipals` negar todos os principais, exceto alguns utilizadores. Todos os Principais têm os seguintes constrangimentos:

- Só pode ser utilizado em `Principals` e não pode ser utilizado em `ExcludePrincipals`.
- `Principals[i].Type` deve ser definido para `SystemDefined`.

## <a name="next-steps"></a>Passos seguintes

* [Lista nega atribuições para recursos Azure utilizando o portal Azure](deny-assignments-portal.md)
* [Compreender definições de papéis para os recursos do Azure](role-definitions.md)
