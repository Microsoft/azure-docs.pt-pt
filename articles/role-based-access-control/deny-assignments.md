---
title: Compreender Azure negar atribuições - Azure RBAC
description: Saiba mais sobre o Azure negar atribuições no controlo de acesso baseado em papel Azure (Azure RBAC).
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
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/03/2020
ms.locfileid: "82733778"
---
# <a name="understand-azure-deny-assignments"></a>Entenda Azure negar atribuições

Semelhante a uma atribuição de funções, uma *atribuição de negação* atribui um conjunto de ações de negação a um utilizador, grupo ou diretor de serviço num determinado âmbito para negar o acesso. As atribuições de negação bloqueiam os utilizadores de realizarem ações específicas de recursos Do Azure, mesmo que uma atribuição de funções lhes conceda acesso.

Este artigo descreve como as missões de negação são definidas.

## <a name="how-deny-assignments-are-created"></a>Como é que as missões de negação são criadas

As missões de negação são criadas e geridas pela Azure para proteger recursos. As aplicações geridas pelo Azure Blueprints e azure utilizam missões de negação para proteger recursos geridos pelo sistema. As aplicações geridas pelo Azure Blueprints e Azure são a única forma de negar atribuições. Não podes criar diretamente as tuas próprias missões de negação. Para obter mais informações sobre como as plantas usam as missões de negação para bloquear recursos, consulte compreender o bloqueio de [recursos em Plantas Azure](../governance/blueprints/concepts/resource-locking.md).

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
> | `DenyAssignmentName` | Sim | String | O nome da exibição da missão de negação. Os nomes devem ser únicos para um dado âmbito. |
> | `Description` | No | String | A descrição da atribuição de negação. |
> | `Permissions.Actions` | Pelo menos uma Ação ou uma Ação de Dados | Corda[] | Uma série de cordas que especificam as operações de gestão a que a missão de negação bloqueia o acesso. |
> | `Permissions.NotActions` | No | Corda[] | Um conjunto de cordas que especificam as operações de gestão para excluir da atribuição de negação. |
> | `Permissions.DataActions` | Pelo menos uma Ação ou uma Ação de Dados | Corda[] | Um conjunto de cordas que especificam as operações de dados a que a atribuição de negação bloqueia o acesso. |
> | `Permissions.NotDataActions` | No | Corda[] | Um conjunto de cordas que especificam as operações de dados para excluir da atribuição de negação. |
> | `Scope` | No | String | Uma cadeia que especifica o âmbito a que a atribuição de negação se aplica. |
> | `DoNotApplyToChildScopes` | No | Booleano | Especifica se a atribuição de negação se aplica aos âmbitos infantis. O valor predefinido é falso. |
> | `Principals[i].Id` | Sim | Corda[] | Uma série de IDs principais de objetos Azure AD (utilizador, grupo, diretor de serviço ou identidade gerida) a que se aplica a atribuição de negação. Coloque-o num `00000000-0000-0000-0000-000000000000` GUID vazio para representar todos os principais. |
> | `Principals[i].Type` | No | Corda[] | Uma série de tipos de objetos representados pelos `SystemDefined` Diretores[i].Id. Definido para representar todos os principais. |
> | `ExcludePrincipals[i].Id` | No | Corda[] | Uma série de IDs principais de objetos Azure AD (utilizador, grupo, diretor de serviço ou identidade gerida) às quais a atribuição de negação não se aplica. |
> | `ExcludePrincipals[i].Type` | No | Corda[] | Uma série de tipos de objetos representados por ExcluiPrincipais[i].Id. |
> | `IsSystemProtected` | No | Booleano | Especifica se esta atribuição de negação foi criada pela Azure e não pode ser editada ou eliminada. Atualmente, todas as missões de negação estão protegidas pelo sistema. |

## <a name="the-all-principals-principal"></a>O diretor de Todos os Principais

Para apoiar a negação de atribuições, foi introduzido um diretor definido pelo sistema chamado *All Principais.* Este principal representa todos os utilizadores, grupos, diretores de serviços e identidades geridas num diretório Azure AD. Se o ID principal `00000000-0000-0000-0000-000000000000` for um GUIA `SystemDefined`zero e o tipo principal for, o principal representa todos os principais. Na saída Azure PowerShell, todos os principais parecem ser os seguintes:

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

Todos os principais `ExcludePrincipals` podem ser combinados para negar todos os principais, exceto alguns utilizadores. Todos os Principais têm os seguintes constrangimentos:

- Só pode ser `Principals` utilizado e `ExcludePrincipals`não pode ser utilizado em .
- `Principals[i].Type`deve ser `SystemDefined`definido para .

## <a name="next-steps"></a>Passos seguintes

* [Tutorial: Proteja novos recursos com fechaduras de recursos da Azure Blueprints](../governance/blueprints/tutorials/protect-new-resources.md)
* [List Azure nega atribuições usando o portal Azure](deny-assignments-portal.md)
