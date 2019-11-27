---
title: Entender as atribuições de negação dos recursos do Azure | Microsoft Docs
description: Saiba mais sobre as atribuições de negação no RBAC (controle de acesso baseado em função) para recursos do Azure.
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
ms.openlocfilehash: 2c663b587d2e9ee278fc774c2841899b060ccbcf
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74479358"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>Entender as atribuições de negação para recursos do Azure

Semelhante a uma atribuição de função, uma *atribuição de negação* anexa um conjunto de ações Deny a um usuário, grupo ou entidade de serviço em um determinado escopo com a finalidade de negar o acesso. As atribuições Deny bloqueiam os usuários de executarem ações específicas de recursos do Azure, mesmo que uma atribuição de função conceda a eles acesso.

Este artigo descreve como as atribuições de negação são definidas.

## <a name="how-deny-assignments-are-created"></a>Como as atribuições de negação são criadas

As atribuições de negação são criadas e gerenciadas pelo Azure para proteger os recursos. Os planos gráficos do Azure e os aplicativos gerenciados do Azure usam atribuições de negação para proteger os recursos gerenciados pelo sistema. Os planos gráficos do Azure e os aplicativos gerenciados do Azure são a única maneira de que as atribuições de negação podem ser criadas. Você não pode criar suas próprias atribuições de negação diretamente.  Para obter mais informações, consulte [proteger novos recursos com bloqueios de recursos de plantas do Azure](../governance/blueprints/tutorials/protect-new-resources.md).

> [!NOTE]
> Você não pode criar suas próprias atribuições de negação diretamente.

## <a name="compare-role-assignments-and-deny-assignments"></a>Comparar atribuições de função e atribuições de negação

As atribuições de negação seguem um padrão semelhante como atribuições de função, mas também têm algumas diferenças.

| Capacidade | Atribuição de função | Atribuição de negação |
| --- | --- | --- |
| Conceder acesso | :heavy_check_mark: |  |
| Negar o acesso |  | :heavy_check_mark: |
| Pode ser criado diretamente | :heavy_check_mark: |  |
| Aplicar em um escopo | :heavy_check_mark: | :heavy_check_mark: |
| Excluir entidades |  | :heavy_check_mark: |
| Impedir a herança para escopos filho |  | :heavy_check_mark: |
| Aplicar a atribuições de [administrador de assinatura clássica](rbac-and-directory-admin-roles.md) |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>Propriedades de atribuição de negação

 Uma atribuição de negação tem as seguintes propriedades:

> [!div class="mx-tableFixed"]
> | Propriedade | Necessário | Tipo | Descrição |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Sim | Cadeia | O nome de exibição da atribuição de negação. Os nomes devem ser exclusivos para um determinado escopo. |
> | `Description` | Não | Cadeia | A descrição da atribuição de negação. |
> | `Permissions.Actions` | Pelo menos uma ação ou um dataactions | Cadeia de caracteres [] | Uma matriz de cadeias de caracteres que especificam as operações de gerenciamento para as quais a atribuição de negação bloqueia o acesso. |
> | `Permissions.NotActions` | Não | Cadeia de caracteres [] | Uma matriz de cadeias de caracteres que especifica as operações de gerenciamento a serem excluídas da atribuição de negação. |
> | `Permissions.DataActions` | Pelo menos uma ação ou um dataactions | Cadeia de caracteres [] | Uma matriz de cadeias de caracteres que especificam as operações de dados para as quais a atribuição de negação bloqueia o acesso. |
> | `Permissions.NotDataActions` | Não | Cadeia de caracteres [] | Uma matriz de cadeias de caracteres que especificam as operações de dados a serem excluídas da atribuição de negação. |
> | `Scope` | Não | Cadeia | Uma cadeia de caracteres que especifica o escopo ao qual a atribuição de negação se aplica. |
> | `DoNotApplyToChildScopes` | Não | Booleano | Especifica se a atribuição de negação se aplica a escopos filho. O valor padrão é false. |
> | `Principals[i].Id` | Sim | Cadeia de caracteres [] | Uma matriz de IDs de objeto de entidade do Azure AD (usuário, grupo, entidade de serviço ou identidade gerenciada) à qual a atribuição de negação se aplica. Defina como um GUID vazio `00000000-0000-0000-0000-000000000000` para representar todas as entidades de segurança. |
> | `Principals[i].Type` | Não | Cadeia de caracteres [] | Uma matriz de tipos de objeto representada por entidades de segurança [i]. ID. Defina como `SystemDefined` para representar todas as entidades de segurança. |
> | `ExcludePrincipals[i].Id` | Não | Cadeia de caracteres [] | Uma matriz de IDs de objeto da entidade de segurança do Azure AD (usuário, grupo, entidade de serviço ou identidade gerenciada) à qual a atribuição de negação não se aplica. |
> | `ExcludePrincipals[i].Type` | Não | Cadeia de caracteres [] | Uma matriz de tipos de objeto representada por ExcludePrincipals [i]. ID. |
> | `IsSystemProtected` | Não | Booleano | Especifica se esta atribuição de negação foi criada pelo Azure e não pode ser editada ou excluída. Atualmente, todas as atribuições de negação são protegidas pelo sistema. |

## <a name="the-all-principals-principal"></a>A entidade de segurança todos os principais

Para dar suporte a atribuições de negação, uma entidade definida pelo sistema denominada *todas as entidades de segurança* foi introduzida. Essa entidade representa todos os usuários, grupos, entidades de serviço e identidades gerenciadas em um diretório do Azure AD. Se a ID da entidade de segurança for um GUID zero `00000000-0000-0000-0000-000000000000` e o tipo de entidade de segurança for `SystemDefined`, a entidade de segurança representará todas as entidades de segurança. Na saída Azure PowerShell, todas as entidades de segurança são parecidas com as seguintes:

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

Todas as entidades de segurança podem ser combinadas com `ExcludePrincipals` para negar todas as entidades de segurança, exceto alguns usuários. Todas as entidades de segurança têm as seguintes restrições:

- Pode ser usado somente em `Principals` e não pode ser usado em `ExcludePrincipals`.
- `Principals[i].Type` deve ser definido como `SystemDefined`.

## <a name="next-steps"></a>Passos seguintes

* [Listar atribuições de negação para recursos do Azure usando o portal do Azure](deny-assignments-portal.md)
* [Entender as definições de função dos recursos do Azure](role-definitions.md)
