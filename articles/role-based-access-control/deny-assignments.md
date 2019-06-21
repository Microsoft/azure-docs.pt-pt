---
title: Compreender negar atribuições para recursos do Azure | Documentos da Microsoft
description: Saiba mais sobre negar atribuições no controlo de acesso baseado em funções (RBAC) para recursos do Azure.
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
ms.date: 06/13/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: f15d6fd81337aa4a859539e86f37a516848c9370
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165987"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>Compreender negar atribuições para recursos do Azure

Semelhante a uma atribuição de função, um *negar atribuição* anexa um conjunto de ações de negação para um utilizador, grupo ou principal de serviço num determinado âmbito para efeitos de negar o acesso. Negar atribuições impedir que os utilizadores de executarem ações de recursos do Azure específica, mesmo se uma atribuição de função lhes concede acesso.

Este artigo descreve como negar atribuições são definidas.

## <a name="how-deny-assignments-are-created"></a>Como negar atribuições são criadas

Negar atribuições são criadas e geridas pelo Azure para proteger os recursos. Por exemplo, planos de gráficos do Azure e Azure aplicações geridas por utilização negar atribuições para proteger os recursos gerenciados pelo sistema. Para obter mais informações, consulte [proteger os recursos novos com bloqueios de recursos do Azure esquemas](../governance/blueprints/tutorials/protect-new-resources.md).

## <a name="compare-role-assignments-and-deny-assignments"></a>Comparar as atribuições de funções e as atribuições de negação

Negar atribuições seguem um padrão semelhante como atribuições de funções, mas também tem algumas diferenças.

| Funcionalidade | Atribuição de função | Atribuição de negação |
| --- | --- | --- |
| Conceder acesso | :heavy_check_mark: |  |
| Negar o acesso |  | :heavy_check_mark: |
| Podem ser criados diretamente | :heavy_check_mark: |  |
| Aplicam-se a um âmbito | :heavy_check_mark: | :heavy_check_mark: |
| Excluir entidades de segurança |  | :heavy_check_mark: |
| Impedir a herança para âmbitos subordinados |  | :heavy_check_mark: |
| Aplicar a [administrador de subscrição clássica](rbac-and-directory-admin-roles.md) atribuições |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>Propriedades de atribuição de negação

 Uma atribuição de negações tem as seguintes propriedades:

> [!div class="mx-tableFixed"]
> | Propriedade | Necessário | Tipo | Descrição |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Sim | String | O nome a apresentar da atribuição de negação. Os nomes têm de ser exclusivos para um determinado âmbito. |
> | `Description` | Não | String | A descrição da atribuição de negação. |
> | `Permissions.Actions` | Ações, pelo menos, um ou um DataActions | String[] | Uma matriz de cadeias de caracteres que especificam as operações de gestão aos quais a atribuição de negar bloqueia o acesso. |
> | `Permissions.NotActions` | Não | String[] | Uma matriz de cadeias de caracteres que especificam as operações de gestão para excluir da atribuição de negação. |
> | `Permissions.DataActions` | Ações, pelo menos, um ou um DataActions | String[] | Uma matriz de cadeias de caracteres que especificam as operações de dados aos quais a atribuição de negar bloqueia o acesso. |
> | `Permissions.NotDataActions` | Não | String[] | Uma matriz de cadeias de caracteres que especificam as operações de dados para impedir que a atribuição de negação. |
> | `Scope` | Não | String | Uma cadeia de caracteres que especifica o âmbito que se aplica a atribuição de negação. |
> | `DoNotApplyToChildScopes` | Não | Boolean | Especifica se a atribuição de negar aplica-se a âmbitos subordinados. Valor predefinido é false. |
> | `Principals[i].Id` | Sim | String[] | Uma matriz de IDs (utilizador, grupo, principal de serviço ou identidade gerida) aos quais se aplica a atribuição de negação de objeto de entidade do Azure AD. Definido como um GUID vazio `00000000-0000-0000-0000-000000000000` para representar todos os principais. |
> | `Principals[i].Type` | Não | String[] | Uma matriz de tipos de objeto representado pelo Direi principais [i]. Definido como `SystemDefined` para representar todos os principais. |
> | `ExcludePrincipals[i].Id` | Não | String[] | Uma matriz de IDs (utilizador, grupo, principal de serviço ou identidade gerida) para o qual não se aplica a atribuição de negação de objeto de entidade do Azure AD. |
> | `ExcludePrincipals[i].Type` | Não | String[] | Uma matriz de tipos de objeto representado pelo Direi ExcludePrincipals [i]. |
> | `IsSystemProtected` | Não | Boolean | Especifica se negar atribuição foi criada pelo Azure e não pode ser editada ou eliminada. Atualmente, todos negar atribuições são protegido do sistema. |

## <a name="the-all-principals-principal"></a>O principal de todos os principais

Para suporte negar atribuições, um principal de definidos pelo sistema de mensagens em fila com o nome *todos os principais* foi introduzido. Este principal de representa todos os utilizadores, grupos, os principais de serviço e identidades geridas num diretório do Azure AD. Se o ID de principal é um zero GUID `00000000-0000-0000-0000-000000000000` e o tipo de principal é `SystemDefined`, o principal representa todas as entidades. Na saída do Azure PowerShell, todos os principais é semelhante ao seguinte:

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

Todas as entidades podem ser combinadas com `ExcludePrincipals` para negar a todos os principais, exceto alguns usuários. Todos os principais tem as seguintes restrições:

- Pode ser utilizada apenas num `Principals` e não é possível utilizar `ExcludePrincipals`.
- `Principals[i].Type` tem de ser definido como `SystemDefined`.

## <a name="next-steps"></a>Passos Seguintes

* [Lista negar atribuições para recursos do Azure no portal do Azure](deny-assignments-portal.md)
* [Compreender as definições de funções para recursos do Azure](role-definitions.md)
