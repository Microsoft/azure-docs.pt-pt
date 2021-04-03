---
title: Funções personalizadas Azure - Azure RBAC
description: Saiba como criar funções personalizadas Azure com o controlo de acesso baseado em funções Azure (Azure RBAC) para uma gestão de acesso de grãos finos dos recursos Azure.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 12/15/2020
ms.author: rolyon
ms.openlocfilehash: 79aaeee942a6d46243ee1c72d5904484b8698ebe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97617328"
---
# <a name="azure-custom-roles"></a>Funções personalizadas do Azure

> [!IMPORTANT]
> A adição de um grupo de gestão `AssignableScopes` está atualmente em pré-visualização.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se os [papéis incorporados do Azure](built-in-roles.md) não corresponderem às necessidades específicas da sua organização, pode criar os seus próprios papéis personalizados. Tal como as funções incorporadas, pode atribuir funções personalizadas aos utilizadores, grupos e diretores de serviços nos âmbitos do grupo de gestão, subscrição e grupo de recursos.

As funções personalizadas podem ser partilhadas entre subscrições que confiam no mesmo diretório AD Azure. Há um limite de **5.000** funções personalizadas por diretório. (Para a Azure Alemanha e Azure China 21Vianet, o limite é de 2.000 papéis personalizados.) As funções personalizadas podem ser criadas utilizando o portal Azure PowerShell, Azure CLI ou a API REST.

## <a name="steps-to-create-a-custom-role"></a>Passos para criar um papel personalizado

Aqui estão os passos básicos para criar um papel personalizado.

1. Determine as permissões necessárias.

    Quando cria uma função personalizada, precisa de conhecer as operações que estão disponíveis para definir as suas permissões. Normalmente, começa-se com um papel incorporado existente e depois modifica-se para as suas necessidades. Irá adicionar as operações à `Actions` ou `NotActions` propriedades da definição de [função.](role-definitions.md) Se tiver operações de dados, irá adicioná-las às `DataActions` `NotDataActions` propriedades ou propriedades.

    Para mais informações, consulte a secção seguinte [Como determinar as permissões de que necessita](#how-to-determine-the-permissions-you-need).

1. Decida como quer criar o papel personalizado.

    Pode criar funções personalizadas utilizando [o portal Azure](custom-roles-portal.md), [Azure PowerShell,](custom-roles-powershell.md) [Azure CLI](custom-roles-cli.md)ou a [API REST](custom-roles-rest.md).

1. Crie o papel personalizado.

    A maneira mais fácil é usar o portal Azure. Para obter etapas sobre como criar uma função personalizada utilizando o portal Azure, consulte [criar ou atualizar funções personalizadas Azure utilizando o portal Azure](custom-roles-portal.md).

1. Teste o papel personalizado.

    Uma vez que tenha o seu papel personalizado, você tem que testá-lo para verificar se funciona como você espera. Se precisar de fazer ajustes mais tarde, pode atualizar a função personalizada.

## <a name="how-to-determine-the-permissions-you-need"></a>Como determinar as permissões de que precisa

O Azure tem milhares de permissões que pode potencialmente incluir no seu papel personalizado. Aqui estão alguns métodos que podem ajudá-lo a determinar as permissões que pretende adicionar à sua função personalizada:

- Veja os [papéis incorporados existentes.](built-in-roles.md)

    Pode querer modificar um papel existente ou combinar permissões usadas em várias funções.

- Enuse os serviços Azure a que pretende conceder acesso.

- Determine os [fornecedores de recursos que mapeiam para os serviços Azure](../azure-resource-manager/management/azure-services-resource-providers.md).

    Os serviços Azure expõem as suas funcionalidades e permissões através [de fornecedores de recursos.](../azure-resource-manager/management/overview.md) Por exemplo, o fornecedor de recursos Microsoft.Compute fornece recursos de máquinas virtuais e o fornecedor de recursos Microsoft.Billing fornece recursos de subscrição e faturação. Conhecer os fornecedores de recursos pode ajudá-lo a reduzir e determinar as permissões necessárias para o seu papel personalizado.

    Quando cria uma função personalizada utilizando o portal Azure, também pode determinar os fornecedores de recursos procurando palavras-chave. Esta funcionalidade de pesquisa é descrita no [Criar ou atualizar funções personalizadas Azure utilizando o portal Azure.](custom-roles-portal.md#step-4-permissions)

    ![Adicione o painel de permissões com o fornecedor de recursos](./media/custom-roles-portal/add-permissions-provider.png)

- Procure as [permissões disponíveis](resource-provider-operations.md) para encontrar permissões que pretende incluir.

    Quando criar uma função personalizada utilizando o portal Azure, pode procurar permissões através de palavras-chave. Por exemplo, pode procurar por *máquinas virtuais* ou permissões *de faturação.* Também pode descarregar todas as permissões como ficheiro CSV e, em seguida, pesquisar neste ficheiro. Esta funcionalidade de pesquisa é descrita no [Criar ou atualizar funções personalizadas Azure utilizando o portal Azure.](custom-roles-portal.md#step-4-permissions)

    ![Adicionar lista de permissões](./media/custom-roles-portal/add-permissions-list.png)

## <a name="custom-role-example"></a>Exemplo de função personalizada

O seguinte mostra como é um papel personalizado como exibido usando a Azure PowerShell no formato JSON. Esta função personalizada pode ser usada para monitorizar e reiniciar máquinas virtuais.

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

O seguinte mostra o mesmo papel personalizado que o Azure CLI.

```json
[
  {
    "assignableScopes": [
      "/subscriptions/{subscriptionId1}",
      "/subscriptions/{subscriptionId2}",
      "/providers/Microsoft.Management/managementGroups/{groupId1}"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/88888888-8888-8888-8888-888888888888",
    "name": "88888888-8888-8888-8888-888888888888",
    "permissions": [
      {
        "actions": [
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
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Quando cria um papel personalizado, aparece no portal Azure com um ícone de recurso laranja.

![Ícone de função personalizada](./media/custom-roles/roles-custom-role-icon.png)

## <a name="custom-role-properties"></a>Propriedades de função personalizadas

A tabela a seguir descreve o que significam as propriedades de funções personalizadas.

| Propriedade | Necessário | Tipo | Descrição |
| --- | --- | --- | --- |
| `Name`</br>`roleName` | Sim | String | O nome de exibição do papel personalizado. Embora uma definição de papel seja um grupo de gestão ou recurso de nível de subscrição, uma definição de papel pode ser usada em várias subscrições que partilham o mesmo diretório AD Azure. Este nome de exibição deve ser único no âmbito do diretório AZure AD. Pode incluir letras, números, espaços e caracteres especiais. O número máximo de caracteres é de 128. |
| `Id`</br>`name` | Sim | String | A identificação única do papel personalizado. Para a Azure PowerShell e Azure CLI, este ID é gerado automaticamente quando cria um novo papel. |
| `IsCustom`</br>`roleType` | Sim | String | Indica se este é um papel personalizado. Definir para `true` ou `CustomRole` para papéis personalizados. Definir para `false` ou `BuiltInRole` para papéis embutidos. |
| `Description`</br>`description` | Sim | String | A descrição do papel personalizado. Pode incluir letras, números, espaços e caracteres especiais. O número máximo de caracteres é de 1024. |
| `Actions`</br>`actions` | Yes | Corda[] | Um conjunto de cordas que especifica as operações de gestão que o papel permite ser executado. Para mais informações, consulte [Ações.](role-definitions.md#actions) |
| `NotActions`</br>`notActions` | No | Corda[] | Um conjunto de cordas que especifica as operações de gestão que são excluídas do permitido `Actions` . Para mais informações, consulte [NotActions](role-definitions.md#notactions). |
| `DataActions`</br>`dataActions` | No | Corda[] | Um conjunto de cordas que especifica as operações de dados que a função permite ser realizada aos seus dados dentro desse objeto. Se criar uma função personalizada `DataActions` com, essa função não pode ser atribuída no âmbito do grupo de gestão. Para obter mais informações, consulte [DataActions](role-definitions.md#dataactions). |
| `NotDataActions`</br>`notDataActions` | No | Corda[] | Um conjunto de cordas que especifica as operações de dados que são excluídas do permitido `DataActions` . Para obter mais informações, consulte [NotDataActions](role-definitions.md#notdataactions). |
| `AssignableScopes`</br>`assignableScopes` | Yes | Corda[] | Uma variedade de cordas que especifica os âmbitos que o papel personalizado está disponível para atribuição. Só é possível definir um grupo de gestão num `AssignableScopes` papel personalizado. A adição de um grupo de gestão `AssignableScopes` está atualmente em pré-visualização. Para mais informações, consulte [OsScopes Atribuíveis](role-definitions.md#assignablescopes). |

## <a name="wildcard-permissions"></a>Permissões wildcard

`Actions`, `NotActions` e `DataActions` suporte `NotDataActions` wildcards para definir `*` permissões. Um wildcard `*` () estende uma permissão a tudo o que corresponde à cadeia de ação que fornece. Por exemplo, suponha que queria adicionar todas as permissões relacionadas com a Azure Cost Management e exportações. Pode adicionar todas estas cordas de ação:

```
Microsoft.CostManagement/exports/action
Microsoft.CostManagement/exports/read
Microsoft.CostManagement/exports/write
Microsoft.CostManagement/exports/delete
Microsoft.CostManagement/exports/run/action
```

Em vez de adicionar todas estas cordas, poderia adicionar uma corda wildcard. Por exemplo, a seguinte corda wildcard é equivalente às cinco cordas anteriores. Isto incluiria igualmente eventuais futuras autorizações de exportação que possam ser adicionadas.

```
Microsoft.CostManagement/exports/*
```

Também pode ter vários wildcards numa corda. Por exemplo, o seguinte fio representa todas as permissões de consulta para a Gestão de Custos.

```
Microsoft.CostManagement/*/query/*
```

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Quem pode criar, eliminar, atualizar ou ver uma função personalizada

Tal como as funções `AssignableScopes` incorporadas, a propriedade especifica os âmbitos de aplicação que o papel está disponível para atribuição. A `AssignableScopes` propriedade para um papel personalizado também controla quem pode criar, eliminar, atualizar ou ver o papel personalizado.

| Tarefa | Operação | Description |
| --- | --- | --- |
| Criar/eliminar uma função personalizada | `Microsoft.Authorization/ roleDefinitions/write` | Os utilizadores que recebem esta operação em toda `AssignableScopes` a função personalizada podem criar (ou eliminar) funções personalizadas para utilização nesses âmbitos. Por exemplo, [Proprietários](built-in-roles.md#owner) e Administradores de Acesso ao Utilizador de [grupos](built-in-roles.md#user-access-administrator) de gestão, subscrições e grupos de recursos. |
| Atualizar uma função personalizada | `Microsoft.Authorization/ roleDefinitions/write` | Os utilizadores que recebem esta operação em toda `AssignableScopes` a função personalizada podem atualizar funções personalizadas nesses âmbitos. Por exemplo, [Proprietários](built-in-roles.md#owner) e Administradores de Acesso ao Utilizador de [grupos](built-in-roles.md#user-access-administrator) de gestão, subscrições e grupos de recursos. |
| Ver um papel personalizado | `Microsoft.Authorization/ roleDefinitions/read` | Os utilizadores que recebem esta operação num âmbito podem visualizar as funções personalizadas que estão disponíveis para atribuição nesse âmbito. Todas as funções incorporadas permitem que as funções personalizadas estejam disponíveis para atribuição. |

## <a name="custom-role-limits"></a>Limites de função personalizados

A lista que se segue descreve os limites para funções personalizadas.

- Cada diretório pode ter até **5000** funções personalizadas.
- AZure Germany e Azure China 21Vianet podem ter até 2000 funções personalizadas para cada diretório.
- Não é possível definir `AssignableScopes` o âmbito da raiz `"/"` ().
- Não é possível utilizar wildcards `*` em `AssignableScopes` . Esta restrição wildcard ajuda a garantir que um utilizador não pode potencialmente obter acesso a um âmbito atualizando a definição de função.
- Só é possível definir um grupo de gestão num `AssignableScopes` papel personalizado. A adição de um grupo de gestão `AssignableScopes` está atualmente em pré-visualização.
- As funções personalizadas `DataActions` com as quais não podem ser atribuídas no âmbito do grupo de gestão.
- O Gestor de Recursos Azure não valida a existência do grupo de gestão no âmbito atribuível da definição de função.

Para obter mais informações sobre funções personalizadas e grupos de gestão, consulte [Organizar os seus recursos com grupos de gestão Azure.](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment)

## <a name="input-and-output-formats"></a>Formatos de entrada e saída

Para criar uma função personalizada utilizando a linha de comando, normalmente utiliza o JSON para especificar as propriedades que pretende para o papel personalizado. Dependendo das ferramentas que utiliza, os formatos de entrada e saída serão ligeiramente diferentes. Esta secção lista os formatos de entrada e saída dependendo da ferramenta.

### <a name="azure-powershell"></a>Azure PowerShell

Para criar uma função personalizada utilizando a Azure PowerShell, tem de fornecer a seguinte entrada.

```json
{
  "Name": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Para atualizar uma função personalizada utilizando o Azure PowerShell, tem de fornecer a seguinte entrada. Note que a `Id` propriedade foi adicionada. 

```json
{
  "Name": "",
  "Id": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

O seguinte mostra um exemplo da saída quando lista uma função personalizada utilizando o Azure PowerShell e o comando [ConvertTo-Json.](/powershell/module/microsoft.powershell.utility/convertto-json) 

```json
{
  "Name": "",
  "Id": "",
  "IsCustom": true,
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

### <a name="azure-cli"></a>CLI do Azure

Para criar ou atualizar uma função personalizada utilizando o Azure CLI, tem de fornecer a seguinte entrada. Este formato é o mesmo quando cria uma função personalizada utilizando o Azure PowerShell.

```json
{
  "Name": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

O seguinte mostra um exemplo da saída quando lista uma função personalizada utilizando o Azure CLI.

```json
[
  {
    "assignableScopes": [],
    "description": "",
    "id": "",
    "name": "",
    "permissions": [
      {
        "actions": [],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="rest-api"></a>API REST

Para criar ou atualizar uma função personalizada utilizando a API REST, tem de fornecer a seguinte entrada. Este formato é o mesmo formato que é gerado quando cria uma função personalizada utilizando o portal Azure.

```json
{
  "properties": {
    "roleName": "",
    "description": "",
    "assignableScopes": [],
    "permissions": [
      {
        "actions": [],
        "notActions": [],
        "dataActions": [],
        "notDataActions": []
      }
    ]
  }
}
```

O seguinte mostra um exemplo da saída quando lista uma função personalizada utilizando a API REST.

```json
{
    "properties": {
        "roleName": "",
        "type": "CustomRole",
        "description": "",
        "assignableScopes": [],
        "permissions": [
            {
                "actions": [],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ],
        "createdOn": "",
        "updatedOn": "",
        "createdBy": "",
        "updatedBy": ""
    },
    "id": "",
    "type": "Microsoft.Authorization/roleDefinitions",
    "name": ""
}
```

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Criar um papel personalizado Azure usando Azure PowerShell](tutorial-custom-role-powershell.md)
- [Tutorial: Criar um papel personalizado Azure usando O Azure CLI](tutorial-custom-role-cli.md)
- [Compreender definições de função Azure](role-definitions.md)
- [Resolução de problemas Azure RBAC](troubleshooting.md)
