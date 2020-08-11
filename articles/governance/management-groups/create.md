---
title: Criar grupos de gestão para organizar recursos - Azure Governance
description: Saiba como criar grupos de gestão Azure para gerir múltiplos recursos usando o portal, Azure PowerShell e Azure CLI.
ms.date: 08/10/2020
ms.topic: conceptual
ms.openlocfilehash: 0739eafdf4cf41195b189a8496c331ab470bbec0
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056640"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Criar grupos de gestão para a organização e gestão de recursos

Os grupos de gestão são contentores que o ajudam a gerir o acesso, a política e o cumprimento em várias subscrições. Crie estes recipientes para construir uma hierarquia eficaz e eficiente que possa ser usada com [a Azure Policy](../policy/overview.md) e [os Controlos de Acesso Baseados em Funções Azure](../../role-based-access-control/overview.md). Para obter mais informações sobre grupos de gestão, consulte [Organizar os seus recursos com grupos de gestão Azure.](overview.md)

O primeiro grupo de gestão criado no diretório pode demorar até 15 minutos para ser concluído. Há processos que correm pela primeira vez para configurar o serviço de grupos de gestão dentro do Azure para o seu diretório. Recebe uma notificação quando o processo estiver concluído. Para mais informações, consulte [a configuração inicial dos grupos de gestão.](./overview.md#initial-setup-of-management-groups)

## <a name="create-a-management-group"></a>Criar um grupo de gestão

Qualquer utilizador Azure AD no arrendatário pode criar um grupo de gestão sem que o grupo de gestão escreva a permissão atribuída a esse utilizador. Este novo grupo de gestão será filho do Grupo de Gestão de Raízes e o criador receberá uma atribuição de função de "Proprietário". O serviço de grupo de gestão permite esta capacidade para que as atribuições de funções não sejam necessárias ao nível da raiz. Nenhum utilizadores tem acesso ao Grupo de Gestão de Raízes quando este é criado. Para evitar o obstáculo de encontrar os Azure AD Global Admins para começar a usar grupos de gestão, permitimos a criação dos grupos de gestão iniciais na raiz  
nível.

Pode criar o grupo de gestão utilizando o portal, um [modelo Azure Resource Manager,](../../azure-resource-manager/templates/deploy-to-tenant.md#create-management-group)PowerShell ou Azure CLI.

### <a name="create-in-portal"></a>Criar em portal

1. Inicie sessão no [portal Azure](https://portal.azure.com).

1. Selecione **Todos os serviços**  >  **Gestão + governação**.

1. Selecione **Grupos de Gestão**.

1. Selecione **+ Adicionar grupo de gestão**.

   :::image type="content" source="./media/main.png" alt-text="Página para trabalhar com grupos de gestão" border="false":::

1. Preencha o campo de identificação do grupo de gestão.

   - O **ID do Grupo de Gestão** é o identificador único do diretório que é usado para submeter comandos neste grupo de gestão. Este identificador não é editável após a criação, uma vez que é usado em todo o sistema Azure para identificar este grupo. O [grupo de gestão de raiz](overview.md#root-management-group-for-each-directory) é automaticamente criado com um ID que é o ID do Diretório Ativo Azure. Para todos os outros grupos de gestão, atribua uma identificação única.
   - O campo de nome de exibição é o nome que é apresentado dentro do portal Azure. Um nome de exibição separado é um campo opcional ao criar o grupo de gestão e pode ser alterado em qualquer  
     Hora.

   :::image type="content" source="./media/create_context_menu.png" alt-text="Opções de criação de um novo grupo de gestão" border="false":::

1. Selecione **Guardar**.

### <a name="create-in-powershell"></a>Criar em PowerShell

Para o PowerShell, utilize o [cmdlet New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) para criar um novo grupo de gestão.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

O **GroupName** é um identificador único que está a ser criado. Este ID é usado por outros comandos para referenciar este grupo e não pode ser alterado mais tarde.

Se pretender que o grupo de gestão apresente um nome diferente dentro do portal Azure, adicione o parâmetro **DisplayName.** Por exemplo, para criar um grupo de gestão com o Nome do Grupo de Contoso e o nome de exibição de "Grupo Contoso", utilize o seguinte cmdlet:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

Nos exemplos anteriores, o novo grupo de gestão é criado no âmbito do grupo de gestão de raiz. Para especificar um grupo de gestão diferente como o progenitor, utilize o parâmetro **ParentId.**

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>Criar em Azure CLI

Para o Azure CLI, utilize o [grupo de gestão de conta az criar](/cli/azure/account/management-group?view=azure-cli-latest#az-account-management-group-create) comando para criar um novo grupo de gestão.

```azurecli-interactive
az account management-group create --name Contoso
```

O **nome** é um identificador único que está a ser criado. Este ID é usado por outros comandos para referenciar este grupo e não pode ser alterado mais tarde.

Se quiser que o grupo de gestão apresente um nome diferente dentro do portal Azure, adicione o parâmetro **do nome de visualização.** Por exemplo, para criar um grupo de gestão com o Nome do Grupo de Contoso e o nome de exibição de "Grupo Contoso", utilize o seguinte comando:

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

Nos exemplos anteriores, o novo grupo de gestão é criado no âmbito do grupo de gestão de raiz. Para especificar um grupo de gestão diferente como o progenitor, use o parâmetro **principal** e forneça o nome do grupo-mãe.

```azurecli-interactive
az account management-group create --name ContosoSubGroup --parent Contoso
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os grupos de gestão, veja:

- [Criar grupos de gestão para organizar recursos do Azure](./create.md)
- [Como alterar, eliminar ou gerir os seus grupos de gestão](./manage.md)
- [Rever os grupos de gestão no Módulo de Recursos do Azure PowerShell](/powershell/module/az.resources#resources)
- [Rever os grupos de gestão na API REST](/rest/api/resources/managementgroups)
- [Rever os grupos de gestão na CLI do Azure](/cli/azure/account/management-group)
