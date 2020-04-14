---
title: Criar grupos de gestão para organizar recursos - Azure Governance
description: Aprenda a criar grupos de gestão Azure para gerir múltiplos recursos usando o portal, Azure PowerShell e Azure CLI.
ms.date: 12/18/2019
ms.topic: conceptual
ms.openlocfilehash: a06679432d795b5b7854af8dc66b468841978e9c
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273193"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Criar grupos de gestão para a organização e gestão de recursos

Os grupos de gestão são recipientes que o ajudam a gerir o acesso, a política e o cumprimento em várias subscrições. Crie estes recipientes para construir uma hierarquia eficaz e eficiente que possa ser usada com [a Política Azure](../policy/overview.md) e controlos de [acesso baseados em funções azure.](../../role-based-access-control/overview.md) Para obter mais informações sobre grupos de gestão, consulte Organizar os seus recursos com grupos de [gestão Azure.](overview.md)

O primeiro grupo de gestão criado no diretório pode demorar até 15 minutos a ser concluído. Existem processos que funcionam pela primeira vez para criar o serviço de grupos de gestão dentro do Azure para o seu diretório. Recebe uma notificação quando o processo estiver concluído. Para obter mais informações, consulte a [configuração inicial dos grupos de gestão](./overview.md#initial-setup-of-management-groups). 

## <a name="create-a-management-group"></a>Criar um grupo de gestão

Qualquer utilizador da AD Azure no inquilino pode criar um grupo de gestão sem que o grupo de gestão escreva permissão atribuída a esse utilizador.  Este novo grupo de gestão será filho do Grupo de Gestão de Raízes e o criador terá uma função de "Proprietário". O serviço de grupo de gestão permite esta capacidade para que as atribuições de funções não sejam necessárias ao nível da raiz. Nenhum utilizador tem acesso ao Grupo de Gestão de Raiz quando é criado.  Para evitar o obstáculo de encontrar os Administradores Globais da AD Azure para começar a usar grupos de gestão, permitimos a criação dos grupos de gestão inicial ao nível das raízes.      

Pode criar o grupo de gestão utilizando o portal, um modelo de Gestor de [Recursos,](../../azure-resource-manager/templates/deploy-to-tenant.md#create-management-group)PowerShell ou Azure CLI.

### <a name="create-in-portal"></a>Criar no portal

1. Inicie sessão no [portal Azure.](https://portal.azure.com)

1. Selecione **Todos os serviços** > **Gestão + governação.**

1. Selecione Gestão de **Custos + Faturação**

1. Na página de Gestão de Custos + Faturação - Grupos de Gestão, selecione **Grupos de Gestão**

1. Selecione **+ Adicionar grupo de gestão**.

   ![Página para trabalhar com grupos de gestão](./media/main.png)

1. Preencha o campo de ID do grupo de gestão.

   - O Id do **Grupo de Gestão** é o identificador único de diretório que é usado para enviar comandos neste grupo de gestão. Este identificador não é editável após a criação, uma vez que é usado em todo o sistema Azure para identificar este grupo. O grupo de [gestão de raiz](overview.md#root-management-group-for-each-directory) é automaticamente criado com um ID que é o ID do Diretório Ativo Azure. Para todos os outros grupos de gestão, atribua um ID único.
   - O campo de nome do visor é o nome que é exibido dentro do portal Azure. Um nome de exibição separado é um campo opcional ao criar o grupo de gestão e pode ser alterado a qualquer momento.  

   ![Painel de opções para criar um novo grupo de gestão](./media/create_context_menu.png)  

1. Selecione **Guardar**.

### <a name="create-in-powershell"></a>Criar em PowerShell

Para a PowerShell, utilize o [cmdlet New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) para criar um novo grupo de gestão.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

O **Nome de Grupo** é um identificador único que está a ser criado. Este ID é usado por outros comandos para fazer referência a este grupo e não pode ser alterado mais tarde.

Se pretender que o grupo de gestão mostre um nome diferente dentro do portal Azure, adicione o parâmetro **DisplayName.** Por exemplo, para criar um grupo de gestão com o Nome de Grupo de Contoso e o nome de exibição do "Grupo Contoso", utilize o seguinte cmdlet:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

Nos exemplos anteriores, o novo grupo de gestão é criado no âmbito do grupo de gestão de raízes. Para especificar um grupo de gestão diferente como o progenitor, utilize o parâmetro **ParentId.**

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>Criar em Azure CLI

Para o Azure CLI, utilize o [grupo de gestão de conta az criar](/cli/azure/account/management-group?view=azure-cli-latest#az-account-management-group-create) um comando para criar um novo grupo de gestão.

```azurecli-interactive
az account management-group create --name Contoso
```

O **nome** é um identificador único sendo criado. Este ID é usado por outros comandos para fazer referência a este grupo e não pode ser alterado mais tarde.

Se quiser que o grupo de gestão mostre um nome diferente dentro do portal Azure, adicione o parâmetro **de nome de exibição.** Por exemplo, para criar um grupo de gestão com o Nome de Grupo de Contoso e o nome de exibição do "Grupo Contoso", utilize o seguinte comando:

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

Nos exemplos anteriores, o novo grupo de gestão é criado no âmbito do grupo de gestão de raízes. Para especificar um grupo de gestão diferente como o progenitor, utilize o **parâmetro-mãe** e forneça o nome do grupo-mãe.

```azurecli-interactive
az account management-group create --name ContosoSubGroup --parent Contoso
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os grupos de gestão, veja:

- [Criar grupos de gestão para organizar recursos do Azure](create.md)
- [Como alterar, eliminar ou gerir os seus grupos de gestão](manage.md)
- [Rever os grupos de gestão no Módulo de Recursos do Azure PowerShell](/powershell/module/az.resources#resources)
- [Rever os grupos de gestão na API REST](/rest/api/resources/managementgroups)
- [Rever os grupos de gestão na CLI do Azure](/cli/azure/account/management-group)
