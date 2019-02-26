---
title: Funções personalizadas para recursos do Azure | Documentos da Microsoft
description: Saiba como criar funções personalizadas com controlo de acesso baseado em funções (RBAC) para a gestão de acesso detalhado dos recursos do Azure.
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
ms.date: 02/22/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f795571de275453738d23e80885f4d9006ca3a20
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56804455"
---
# <a name="custom-roles-for-azure-resources"></a>Funções personalizadas para recursos do Azure

Se o [funções incorporadas para recursos do Azure](built-in-roles.md) não atenderem às necessidades específicas da sua organização, pode criar suas próprias funções personalizadas. Tal como funções incorporadas, pode atribuir funções personalizadas a utilizadores, grupos e principais de serviço na subscrição, grupo de recursos e os âmbitos de recursos. Funções personalizadas são armazenadas num diretório do Azure Active Directory (Azure AD) e podem ser partilhadas entre subscrições. Cada diretório pode ter até 2000 de funções personalizadas. Funções personalizadas podem ser criadas com o Azure PowerShell, CLI do Azure ou a API REST.

## <a name="custom-role-example"></a>Exemplo de função personalizada

A seguir mostra uma função personalizada aparência tal como apresentado no formato JSON. Esta função personalizada pode ser utilizada para monitorizar e reiniciar as máquinas virtuais.

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

Quando cria uma função personalizada, ele aparece no portal do Azure com um ícone de recurso de cor de laranja.

![Ícone de função personalizada](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Passos para criar uma função personalizada

1. Decidir como pretende criar a função personalizada

    Pode criar funções personalizadas usando [do Azure PowerShell](custom-roles-powershell.md), [CLI do Azure](custom-roles-cli.md), ou o [REST API](custom-roles-rest.md).

1. Determinar as permissões que necessárias

    Quando cria uma função personalizada, precisa saber o recurso de operações de fornecedor que estão disponíveis para definir as suas permissões. Para ver a lista de operações, consulte a [operações de fornecedor de recursos do Azure Resource Manager](resource-provider-operations.md). Adicionará as operações para o `Actions` ou `NotActions` propriedades do [definição de função](role-definitions.md). Se tiver de operações de dados, irá adicioná-los para o `DataActions` ou `NotDataActions` propriedades.

1. Criar a função personalizada

    Normalmente, começar com uma função incorporada existente e, em seguida, modificá-lo para as suas necessidades. Em seguida, utilizar o [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) ou [criação da definição de função de az](/cli/azure/role/definition#az-role-definition-create) comandos para criar a função personalizada. Para criar uma função personalizada, tem de ter o `Microsoft.Authorization/roleDefinitions/write` permissão em todos os `AssignableScopes`, tal como [proprietário](built-in-roles.md#owner) ou [administrador de acesso de utilizador](built-in-roles.md#user-access-administrator).

1. Testar a função personalizada

    Assim que tiver a função personalizada, precisa testá-lo para verificar se funciona conforme o esperado. Se precisar de fazer ajustes mais tarde, pode atualizar a função personalizada.

Para obter um tutorial passo a passo sobre como criar uma função personalizada, consulte [Tutorial: Criar uma função personalizada com o Azure PowerShell](tutorial-custom-role-powershell.md) ou [Tutorial: Criar uma função personalizada com a CLI do Azure](tutorial-custom-role-cli.md).

## <a name="custom-role-properties"></a>Propriedades de função personalizada

Uma função personalizada tem as seguintes propriedades.

| Propriedade | Necessário | Tipo | Descrição |
| --- | --- | --- | --- |
| `Name` | Sim | String | O nome a apresentar da função personalizada. Enquanto uma definição de função é um recurso de nível de assinatura, uma definição de função pode ser utilizada em várias subscrições que partilham o mesmo diretório do Azure AD. Este nome a apresentar tem de ser exclusivo no âmbito do diretório do Azure AD. Pode incluir letras, números, espaços e carateres especiais. Número máximo de carateres é 128. |
| `Id` | Sim | String | O ID exclusivo da função personalizada. Para o Azure PowerShell e CLI do Azure, este ID é gerado automaticamente quando cria uma nova função. |
| `IsCustom` | Sim | String | Indica se se trata de uma função personalizada. Definido como `true` para funções personalizadas. |
| `Description` | Sim | String | A descrição da função personalizada. Pode incluir letras, números, espaços e carateres especiais. Número máximo de carateres é de 1024. |
| `Actions` | Sim | String[] | Uma matriz de cadeias de caracteres que especifica as operações de gestão que permite que a função a ser executada. Para obter mais informações, consulte [ações](role-definitions.md#actions). |
| `NotActions` | Não | String[] | Uma matriz de cadeias de caracteres que especifica as operações de gestão que são excluídas da permitidos `Actions`. Para obter mais informações, consulte [NotActions](role-definitions.md#notactions). |
| `DataActions` | Não | String[] | Uma matriz de cadeias de caracteres que especifica as operações de dados que permite que a função a ser executada aos seus dados dentro desse objeto. Para obter mais informações, consulte [DataActions (pré-visualização)](role-definitions.md#dataactions-preview). |
| `NotDataActions` | Não | String[] | Uma matriz de cadeias de caracteres que especifica as operações de dados que são excluídas da permitidos `DataActions`. Para obter mais informações, consulte [NotDataActions (pré-visualização)](role-definitions.md#notdataactions-preview). |
| `AssignableScopes` | Sim | String[] | Uma matriz de cadeias de caracteres que especifica os âmbitos que a função personalizada está disponível para atribuição. Atualmente, não é possível definir o âmbito de raiz (`"/"`) ou um âmbito de grupo de gestão. Para obter mais informações, consulte [AssignableScopes](role-definitions.md#assignablescopes) e [organizar os recursos com grupos de gestão do Azure](../governance/management-groups/index.md#custom-rbac-role-definition-and-assignment). |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Quem pode criar, eliminar, atualizar ou ver uma função personalizada

Tal como funções incorporadas, o `AssignableScopes` propriedade especifica os âmbitos que a função está disponível para atribuição. O `AssignableScopes` propriedade para uma função personalizada também controla quem pode criar, eliminar, atualizar ou ver a função personalizada.

| Tarefa | Operação | Descrição |
| --- | --- | --- |
| Criar/eliminar uma função personalizada | `Microsoft.Authorization/ roleDefinition/write` | Os utilizadores que recebem esta operação em todos os `AssignableScopes` da função personalizada pode criar (ou eliminar) funções personalizadas para utilização nesses âmbitos. Por exemplo, [proprietários](built-in-roles.md#owner) e [administradores do acesso de utilizador](built-in-roles.md#user-access-administrator) de subscrições, grupos de recursos e recursos. |
| Atualizar uma função personalizada | `Microsoft.Authorization/ roleDefinition/write` | Os utilizadores que recebem esta operação em todos os `AssignableScopes` da função personalizada, pode atualizar funções personalizadas nesses âmbitos. Por exemplo, [proprietários](built-in-roles.md#owner) e [administradores do acesso de utilizador](built-in-roles.md#user-access-administrator) de subscrições, grupos de recursos e recursos. |
| Ver uma função personalizada | `Microsoft.Authorization/ roleDefinition/read` | Os utilizadores que são concedidos esta operação com um âmbito podem ver as funções personalizadas que estão disponíveis para atribuição nesse âmbito. Todas as funções incorporadas que funções personalizadas estar disponível para atribuição. |

## <a name="next-steps"></a>Passos Seguintes
- [Criar funções personalizadas para recursos do Azure com o Azure PowerShell](custom-roles-powershell.md)
- [Criar funções personalizadas para recursos do Azure com a CLI do Azure](custom-roles-cli.md)
- [Compreender as definições de funções para recursos do Azure](role-definitions.md)
- [Resolver problemas relacionados com o RBAC para recursos do Azure](troubleshooting.md)
