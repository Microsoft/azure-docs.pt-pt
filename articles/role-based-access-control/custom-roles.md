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
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9454962e210781559f2fdceb1c36f499c4ae8ff7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062176"
---
# <a name="custom-roles-for-azure-resources"></a>Custom roles for Azure resources (Funções personalizadas para recursos do Azure)

> [!IMPORTANT]
> A adição de `AssignableScopes` um grupo de gestão está atualmente em pré-visualização.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

Se as [funções incorporadas para os recursos Azure](built-in-roles.md) não atenderem às necessidades específicas da sua organização, pode criar os seus próprios papéis personalizados. Tal como as funções incorporadas, pode atribuir funções personalizadas a utilizadores, grupos e diretores de serviços em âmbitos de grupos de gestão, subscrição e grupo de recursos.

As funções personalizadas podem ser partilhadas entre subscrições que confiam no mesmo diretório Azure AD. Há um limite de **5.000** funções personalizadas por diretório. (Para a Azure Germany e a Azure China 21Vianet, o limite é de 2.000 funções personalizadas.) As funções personalizadas podem ser criadas utilizando o portal Azure (Preview), Azure PowerShell, Azure CLI ou a REST API.

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
    "/providers/Microsoft.Management/managementGroups/{groupId1}"
  ]
}
```

Quando se cria um papel personalizado, aparece no portal Azure com um ícone de recurso laranja.

![Ícone de papel personalizado](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Passos para criar um papel personalizado

1. Decida como pretende criar o papel personalizado

    Pode criar funções personalizadas utilizando [o portal Azure](custom-roles-portal.md) (Preview), [O Azure PowerShell,](custom-roles-powershell.md) [o Azure CLI](custom-roles-cli.md)ou o [REST API](custom-roles-rest.md).

1. Determine as permissões que precisa

    Quando cria uma função personalizada, precisa de conhecer as operações do fornecedor de recursos que estão disponíveis para definir as suas permissões. Para visualizar a lista de operações, consulte as operações do fornecedor de recursos do Gestor de [Recursos do Azure.](resource-provider-operations.md) Você adicionará as `Actions` operações às propriedades ou `NotActions` propriedades da definição de [papel.](role-definitions.md) Se tiver operações de dados, `DataActions` irá `NotDataActions` adicioná-las às propriedades ou propriedades.

1. Criar o papel personalizado

    Normalmente, começa-se com um papel incorporado existente e depois modifica-o para as suas necessidades. Em seguida, você usa a [definição](/cli/azure/role/definition#az-role-definition-create) de papel [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) ou az criar comandos para criar o papel personalizado. Para criar uma função personalizada, `Microsoft.Authorization/roleDefinitions/write` deve `AssignableScopes`ter a permissão em todos, como [Proprietário](built-in-roles.md#owner) ou Administrador de Acesso ao [Utilizador.](built-in-roles.md#user-access-administrator)

1. Testar o papel personalizado

    Uma vez que tenha o seu papel personalizado, tem que testá-lo para verificar se funciona como espera. Se precisar de fazer ajustes mais tarde, pode atualizar a função personalizada.

Para um tutorial passo a passo sobre como criar um papel personalizado, consulte [Tutorial: Crie uma função personalizada usando Azure PowerShell](tutorial-custom-role-powershell.md) ou [Tutorial: Crie uma função personalizada usando o Azure CLI](tutorial-custom-role-cli.md).

## <a name="custom-role-properties"></a>Propriedades de papel personalizado

Um papel personalizado tem as seguintes propriedades.

| Propriedade | Necessário | Tipo | Descrição |
| --- | --- | --- | --- |
| `Name` | Sim | Cadeia | O nome de exibição do papel personalizado. Embora uma definição de função seja um grupo de gestão ou um recurso de nível de subscrição, uma definição de função pode ser usada em múltiplas subscrições que partilham o mesmo diretório Azure AD. Este nome de exibição deve ser único no âmbito do diretório Azure AD. Pode incluir letras, números, espaços e personagens especiais. O número máximo de caracteres é de 128. |
| `Id` | Sim | Cadeia | A identificação única do papel personalizado. Para o Azure PowerShell e o Azure CLI, este ID é gerado automaticamente quando cria uma nova função. |
| `IsCustom` | Sim | Cadeia | Indica se este é um papel personalizado. Pronto `true` para papéis personalizados. |
| `Description` | Sim | Cadeia | A descrição do papel personalizado. Pode incluir letras, números, espaços e personagens especiais. O número máximo de caracteres é 1024. |
| `Actions` | Sim | Corda[] | Uma série de cordas que especifica as operações de gestão que a função permite ser executadas. Para mais informações, consulte [Ações](role-definitions.md#actions). |
| `NotActions` | Não | Corda[] | Um conjunto de cordas que especifica as operações de `Actions`gestão que estão excluídas das permitidas . Para mais informações, consulte [NotActions](role-definitions.md#notactions). |
| `DataActions` | Não | Corda[] | Um conjunto de cordas que especifica as operações de dados que a função permite ser executada aos seus dados dentro desse objeto. Se criar um papel `DataActions`personalizado com , esse papel não pode ser atribuído no âmbito do grupo de gestão. Para mais informações, consulte [DataActions](role-definitions.md#dataactions). |
| `NotDataActions` | Não | Corda[] | Um conjunto de cordas que especifica as operações de `DataActions`dados que estão excluídas das permitidas . Para mais informações, consulte [NotDataActions](role-definitions.md#notdataactions). |
| `AssignableScopes` | Sim | Corda[] | Uma variedade de cordas que especifica os âmbitos que a função personalizada está disponível para atribuição. Só se pode definir `AssignableScopes` um grupo de gestão com um papel personalizado. A adição de `AssignableScopes` um grupo de gestão está atualmente em pré-visualização. Para mais informações, consulte [Os Scopes Atribuídos](role-definitions.md#assignablescopes). |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Quem pode criar, eliminar, atualizar ou ver um papel personalizado

Tal como as funções `AssignableScopes` incorporadas, a propriedade especifica os âmbitos que o papel está disponível para atribuição. A `AssignableScopes` propriedade para uma função personalizada também controla quem pode criar, excluir, atualizar ou ver a função personalizada.

| Tarefa | Operação | Descrição |
| --- | --- | --- |
| Criar/eliminar uma função personalizada | `Microsoft.Authorization/ roleDefinitions/write` | Os utilizadores a quem seja `AssignableScopes` concedida esta operação em toda a função personalizada podem criar (ou excluir) funções personalizadas para utilização nesses âmbitos. Por exemplo, [Proprietários](built-in-roles.md#owner) e Administradores de Acesso ao [Utilizador](built-in-roles.md#user-access-administrator) de grupos de gestão, subscrições e grupos de recursos. |
| Atualizar uma função personalizada | `Microsoft.Authorization/ roleDefinitions/write` | Os utilizadores que recebem esta `AssignableScopes` operação em toda a função personalizada podem atualizar funções personalizadas nesses âmbitos. Por exemplo, [Proprietários](built-in-roles.md#owner) e Administradores de Acesso ao [Utilizador](built-in-roles.md#user-access-administrator) de grupos de gestão, subscrições e grupos de recursos. |
| Ver um papel personalizado | `Microsoft.Authorization/ roleDefinitions/read` | Os utilizadores a quem seja concedida esta operação num âmbito podem visualizar as funções personalizadas que estão disponíveis para atribuição nesse âmbito. Todas as funções incorporadas permitem que as funções personalizadas estejam disponíveis para atribuição. |

## <a name="custom-role-limits"></a>Limites de função personalizados

A lista seguinte descreve os limites para funções personalizadas.

- Cada diretório pode ter até **5000** papéis personalizados.
- A Azure Germany e a Azure China 21Vianet podem ter até 2000 funções personalizadas para cada diretório.
- Não é `AssignableScopes` possível definir`"/"`o âmbito da raiz ( ).
- Só se pode definir `AssignableScopes` um grupo de gestão com um papel personalizado. A adição de `AssignableScopes` um grupo de gestão está atualmente em pré-visualização.
- As funções personalizadas não `DataActions` podem ser atribuídas no âmbito do grupo de gestão.
- O Gestor de Recursos Azure não valida a existência do grupo de gestão no âmbito atribuível da definição de funções.

Para obter mais informações sobre papéis personalizados e grupos de gestão, consulte Organizar os seus recursos com grupos de [gestão Azure.](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment)

## <a name="next-steps"></a>Passos seguintes
- [Criar ou atualizar funções personalizadas do Azure utilizando o portal Azure (Pré-visualização)](custom-roles-portal.md)
- [Compreender definições de papéis para os recursos do Azure](role-definitions.md)
- [RBAC de resolução de problemas para recursos Azure](troubleshooting.md)
