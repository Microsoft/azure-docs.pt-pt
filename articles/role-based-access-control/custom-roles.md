---
title: Papéis personalizados para os recursos do Azure / Microsoft Docs
description: Aprenda a criar papéis personalizados com controlo de acesso baseado em papéis (RBAC) para gestão de acesso de grãos finos de recursos Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8c5db13b343783a86dc04b84e09746bc4406186b
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77660704"
---
# <a name="custom-roles-for-azure-resources"></a>Papéis personalizados para recursos Azure

Se as [funções incorporadas para os recursos Azure](built-in-roles.md) não atenderem às necessidades específicas da sua organização, pode criar os seus próprios papéis personalizados. Tal como as funções incorporadas, pode atribuir funções personalizadas a utilizadores, grupos e diretores de serviços em subscrições, grupos de recursos e âmbitos de recursos.

As funções personalizadas podem ser partilhadas entre subscrições que confiam no mesmo diretório Azure AD. Há um limite de **5.000** funções personalizadas por diretório. (Para nuvens especializadas, como o Governo Azure, a Alemanha Azure e a Azure China 21Vianet, o limite é de 2.000 papéis personalizados.) As funções personalizadas podem ser criadas utilizando o portal Azure (Preview), Azure PowerShell, Azure CLI ou a REST API.

## <a name="custom-role-example"></a>Exemplo de exemplo de papel personalizado

O seguinte mostra como um papel personalizado parece como exibido no formato JSON. Esta função personalizada pode ser usada para monitorizar e reiniciar máquinas virtuais.

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "88888888-8888-8888-8888-888888888888",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/subscriptions/{subscriptionId3}"
  ]
}
```

Quando se cria um papel personalizado, aparece no portal Azure com um ícone de recurso laranja.

![Ícone de papel personalizado](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Passos para criar um papel personalizado

1. Decida como pretende criar o papel personalizado

    Pode criar funções personalizadas utilizando [o portal Azure](custom-roles-portal.md) (Preview), [O Azure PowerShell,](custom-roles-powershell.md) [o Azure CLI](custom-roles-cli.md)ou o [REST API](custom-roles-rest.md).

1. Determine as permissões que precisa

    Quando cria uma função personalizada, precisa de conhecer as operações do fornecedor de recursos que estão disponíveis para definir as suas permissões. Para visualizar a lista de operações, consulte as operações do fornecedor de recursos do Gestor de [Recursos do Azure.](resource-provider-operations.md) Irá adicionar as operações às propriedades `Actions` ou `NotActions` da definição de [funções.](role-definitions.md) Se tiver operações de dados, irá adicioná-las às propriedades `DataActions` ou `NotDataActions`.

1. Criar o papel personalizado

    Normalmente, começa-se com um papel incorporado existente e depois modifica-o para as suas necessidades. Em seguida, você usa a [definição](/cli/azure/role/definition#az-role-definition-create) de papel [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) ou az criar comandos para criar o papel personalizado. Para criar uma função personalizada, deve ter a permissão `Microsoft.Authorization/roleDefinitions/write` em todas as `AssignableScopes`, como [Proprietário](built-in-roles.md#owner) ou Administrador de [Acesso ao Utilizador](built-in-roles.md#user-access-administrator).

1. Testar o papel personalizado

    Uma vez que tenha o seu papel personalizado, tem que testá-lo para verificar se funciona como espera. Se precisar de fazer ajustes mais tarde, pode atualizar a função personalizada.

Para um tutorial passo a passo sobre como criar um papel personalizado, consulte [Tutorial: Crie uma função personalizada usando Azure PowerShell](tutorial-custom-role-powershell.md) ou [Tutorial: Crie uma função personalizada usando o Azure CLI](tutorial-custom-role-cli.md).

## <a name="custom-role-properties"></a>Propriedades de papel personalizado

Um papel personalizado tem as seguintes propriedades.

| Propriedade | Necessário | Tipo | Descrição |
| --- | --- | --- | --- |
| `Name` | Sim | String | O nome de exibição do papel personalizado. Embora uma definição de função seja um recurso de nível de subscrição, uma definição de função pode ser usada em múltiplas subscrições que partilham o mesmo diretório Azure AD. Este nome de exibição deve ser único no âmbito do diretório Azure AD. Pode incluir letras, números, espaços e personagens especiais. O número máximo de caracteres é de 128. |
| `Id` | Sim | String | A identificação única do papel personalizado. Para o Azure PowerShell e o Azure CLI, este ID é gerado automaticamente quando cria uma nova função. |
| `IsCustom` | Sim | String | Indica se este é um papel personalizado. Prepara-te para `true` para papéis personalizados. |
| `Description` | Sim | String | A descrição do papel personalizado. Pode incluir letras, números, espaços e personagens especiais. O número máximo de caracteres é 1024. |
| `Actions` | Sim | Corda[] | Uma série de cordas que especifica as operações de gestão que a função permite ser executadas. Para mais informações, consulte [Ações](role-definitions.md#actions). |
| `NotActions` | Não | Corda[] | Um conjunto de cordas que especifica as operações de gestão que estão excluídas da `Actions`permitida . Para mais informações, consulte [NotActions](role-definitions.md#notactions). |
| `DataActions` | Não | Corda[] | Um conjunto de cordas que especifica as operações de dados que a função permite ser executada aos seus dados dentro desse objeto. Para mais informações, consulte [DataActions](role-definitions.md#dataactions). |
| `NotDataActions` | Não | Corda[] | Um conjunto de cordas que especifica as operações de dados que estão excluídas da `DataActions`permitida . Para mais informações, consulte [NotDataActions](role-definitions.md#notdataactions). |
| `AssignableScopes` | Sim | Corda[] | Uma variedade de cordas que especifica os âmbitos que a função personalizada está disponível para atribuição. Para funções personalizadas, não é possível definir `AssignableScopes` ao âmbito raiz (`"/"`) ou a um âmbito de grupo de gestão. Para mais informações, consulte [Assalcências Atribuídas](role-definitions.md#assignablescopes) e Organize os seus recursos com grupos de [gestão Azure.](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment) |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Quem pode criar, eliminar, atualizar ou ver um papel personalizado

Tal como as funções incorporadas, a propriedade `AssignableScopes` especifica os âmbitos que o papel está disponível para atribuição. A propriedade `AssignableScopes` para uma função personalizada também controla quem pode criar, excluir, atualizar ou ver a função personalizada.

| Tarefa | Operação | Descrição |
| --- | --- | --- |
| Criar/eliminar uma função personalizada | `Microsoft.Authorization/ roleDefinitions/write` | Os utilizadores a quem seja concedida esta operação em todas as `AssignableScopes` da função personalizada podem criar (ou excluir) funções personalizadas para utilização nesses âmbitos. Por exemplo, [Proprietários](built-in-roles.md#owner) e Administradores de [Acesso ao Utilizador](built-in-roles.md#user-access-administrator) de subscrições, grupos de recursos e recursos. |
| Atualizar uma função personalizada | `Microsoft.Authorization/ roleDefinitions/write` | Os utilizadores que recebem esta operação em todas as `AssignableScopes` da função personalizada podem atualizar funções personalizadas nesses âmbitos. Por exemplo, [Proprietários](built-in-roles.md#owner) e Administradores de [Acesso ao Utilizador](built-in-roles.md#user-access-administrator) de subscrições, grupos de recursos e recursos. |
| Ver um papel personalizado | `Microsoft.Authorization/ roleDefinitions/read` | Os utilizadores a quem seja concedida esta operação num âmbito podem visualizar as funções personalizadas que estão disponíveis para atribuição nesse âmbito. Todas as funções incorporadas permitem que as funções personalizadas estejam disponíveis para atribuição. |

## <a name="next-steps"></a>Passos seguintes
- [Criar ou atualizar funções personalizadas do Azure utilizando o portal Azure (Pré-visualização)](custom-roles-portal.md)
- [Compreender definições de papéis para os recursos do Azure](role-definitions.md)
- [RBAC de resolução de problemas para recursos Azure](troubleshooting.md)
