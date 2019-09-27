---
title: Funções personalizadas para recursos do Azure | Microsoft Docs
description: Saiba como criar funções personalizadas com o RBAC (controle de acesso baseado em função) para o gerenciamento de acesso refinado de recursos do Azure.
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
ms.date: 06/07/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fbea0567ec125ce12acd8f757b32df723876fe09
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338577"
---
# <a name="custom-roles-for-azure-resources"></a>Funções personalizadas para recursos do Azure

Se as [funções internas para os recursos do Azure](built-in-roles.md) não atenderem às necessidades específicas de sua organização, você poderá criar suas próprias funções personalizadas. Assim como as funções internas, você pode atribuir funções personalizadas a usuários, grupos e entidades de serviço na assinatura, grupo de recursos e escopos de recursos.

As funções personalizadas são armazenadas em um diretório Azure Active Directory (Azure AD) e podem ser compartilhadas entre assinaturas. Cada diretório pode ter até **5000** funções personalizadas. (para clouds especializadas, como o Azure Government, o Azure Alemanha e o Azure China 21Vianet, o limite é de 2000 funções personalizadas). As funções personalizadas podem ser criadas usando Azure PowerShell, CLI do Azure ou a API REST.

## <a name="custom-role-example"></a>Exemplo de função personalizada

O exemplo a seguir mostra a aparência de uma função personalizada, conforme exibido no formato JSON. Essa função personalizada pode ser usada para monitorar e reiniciar máquinas virtuais.

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

Quando você cria uma função personalizada, ela aparece na portal do Azure com um ícone de recurso laranja.

![Ícone de função personalizada](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Etapas para criar uma função personalizada

1. Decidir como você deseja criar a função personalizada

    Você pode criar funções personalizadas usando [Azure PowerShell](custom-roles-powershell.md), [CLI do Azure](custom-roles-cli.md)ou a [API REST](custom-roles-rest.md).

1. Determinar as permissões necessárias

    Ao criar uma função personalizada, você precisa saber as operações do provedor de recursos que estão disponíveis para definir suas permissões. Para exibir a lista de operações, consulte o [Azure Resource Manager operações do provedor de recursos](resource-provider-operations.md). Você adicionará as operações às propriedades `Actions` ou `NotActions` da definição de [função](role-definitions.md). Se houver operações de dados, você as adicionará às propriedades `DataActions` ou `NotDataActions`.

1. Criar a função personalizada

    Normalmente, você começa com uma função interna existente e a modifica para suas necessidades. Em seguida, use os comandos Create [-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) ou [AZ role Definition](/cli/azure/role/definition#az-role-definition-create) para criar a função personalizada. Para criar uma função personalizada, você deve ter a permissão `Microsoft.Authorization/roleDefinitions/write` em todos os `AssignableScopes`, como o [proprietário](built-in-roles.md#owner) ou [administrador de acesso do usuário](built-in-roles.md#user-access-administrator).

1. Testar a função personalizada

    Depois de ter sua função personalizada, você precisa testá-la para verificar se ela funciona conforme o esperado. Se precisar fazer ajustes mais tarde, você poderá atualizar a função personalizada.

Para obter um tutorial passo a passo sobre como criar uma função personalizada, consulte [Tutorial: Crie uma função personalizada usando Azure PowerShell @ no__t-0 ou [Tutorial: Crie uma função personalizada usando CLI do Azure @ no__t-0.

## <a name="custom-role-properties"></a>Propriedades da função personalizada

Uma função personalizada tem as propriedades a seguir.

| Propriedade | Requerido | Tipo | Descrição |
| --- | --- | --- | --- |
| `Name` | Sim | Cadeia | O nome de exibição da função personalizada. Embora uma definição de função seja um recurso de nível de assinatura, uma definição de função pode ser usada em várias assinaturas que compartilham o mesmo diretório do AD do Azure. Esse nome de exibição deve ser exclusivo no escopo do diretório do Azure AD. Pode incluir letras, números, espaços e caracteres especiais. O número máximo de caracteres é 128. |
| `Id` | Sim | Cadeia | A ID exclusiva da função personalizada. Para Azure PowerShell e CLI do Azure, essa ID é gerada automaticamente quando você cria uma nova função. |
| `IsCustom` | Sim | Cadeia | Indica se esta é uma função personalizada. Defina como `true` para funções personalizadas. |
| `Description` | Sim | Cadeia | A descrição da função personalizada. Pode incluir letras, números, espaços e caracteres especiais. O número máximo de caracteres é 1024. |
| `Actions` | Sim | Cadeia de caracteres [] | Uma matriz de cadeias de caracteres que especifica as operações de gerenciamento que a função permite executar. Para obter mais informações, consulte [ações](role-definitions.md#actions). |
| `NotActions` | Não | Cadeia de caracteres [] | Uma matriz de cadeias de caracteres que especifica as operações de gerenciamento que são excluídas do `Actions` permitido. Para obter mais informações, consulte não- [ações](role-definitions.md#notactions). |
| `DataActions` | Não | Cadeia de caracteres [] | Uma matriz de cadeias de caracteres que especifica as operações de dados que a função permite que sejam executadas em seus dados dentro desse objeto. Para obter mais informações, consulte [Dataactions](role-definitions.md#dataactions). |
| `NotDataActions` | Não | Cadeia de caracteres [] | Uma matriz de cadeias de caracteres que especifica as operações de dados que são excluídas do `DataActions` permitido. Para obter mais informações, consulte [NotDataActions](role-definitions.md#notdataactions). |
| `AssignableScopes` | Sim | Cadeia de caracteres [] | Uma matriz de cadeias de caracteres que especifica os escopos que a função personalizada está disponível para atribuição. Para funções personalizadas, atualmente, você não pode definir `AssignableScopes` para o escopo raiz (`"/"`) ou um escopo de grupo de gerenciamento. Para obter mais informações, consulte [AssignableScopes](role-definitions.md#assignablescopes) e [Organize seus recursos com grupos de gerenciamento do Azure](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment). |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Quem pode criar, excluir, atualizar ou exibir uma função personalizada

Assim como as funções internas, a propriedade `AssignableScopes` especifica os escopos que a função está disponível para atribuição. A propriedade `AssignableScopes` para uma função personalizada também controla quem pode criar, excluir, atualizar ou exibir a função personalizada.

| Tarefa | Operação | Descrição |
| --- | --- | --- |
| Criar/excluir uma função personalizada | `Microsoft.Authorization/ roleDefinitions/write` | Os usuários que recebem essa operação em todos os `AssignableScopes` da função personalizada podem criar (ou excluir) funções personalizadas para uso nesses escopos. Por exemplo, [proprietários](built-in-roles.md#owner) e [Administradores de acesso de usuário](built-in-roles.md#user-access-administrator) de assinaturas, grupos de recursos e recursos. |
| Atualizar uma função personalizada | `Microsoft.Authorization/ roleDefinitions/write` | Os usuários que recebem essa operação em todos os `AssignableScopes` da função personalizada podem atualizar funções personalizadas nesses escopos. Por exemplo, [proprietários](built-in-roles.md#owner) e [Administradores de acesso de usuário](built-in-roles.md#user-access-administrator) de assinaturas, grupos de recursos e recursos. |
| Exibir uma função personalizada | `Microsoft.Authorization/ roleDefinitions/read` | Os usuários que recebem essa operação em um escopo podem exibir as funções personalizadas que estão disponíveis para atribuição nesse escopo. Todas as funções internas permitem que as funções personalizadas estejam disponíveis para atribuição. |

## <a name="next-steps"></a>Passos seguintes
- [Create custom roles for Azure resources using Azure PowerShell](custom-roles-powershell.md) (Utilizar o Azure PowerShell para criar funções personalizadas para recursos do Azure)
- [Create custom roles for Azure resources using Azure CLI](custom-roles-cli.md) (Utilizar a CLI do Azure para criar funções personalizadas para recursos do Azure)
- [Entender as definições de função dos recursos do Azure](role-definitions.md)
- [Solucionar problemas de RBAC para recursos do Azure](troubleshooting.md)
