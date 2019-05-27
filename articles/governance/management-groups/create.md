---
title: Criar grupos de gestão para organizar recursos do Azure - governação do Azure
description: Saiba como criar grupos de gestão do Azure para gerir vários recursos com o portal, o Azure PowerShell e a CLI do Azure.
author: rthorn17
manager: rithorn
ms.service: azure-resource-manager
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: 2dd2a6e071533deef47a6482bfb9ed92953864ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66165917"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Criar grupos de gestão para a organização de recursos e gestão

Os grupos de gestão são contentores que o ajudam a gerir o acesso, política e conformidade em várias subscrições. Criar estes contentores para criar uma hierarquia, eficaz e eficiente, que pode ser utilizada com [do Azure Policy](../policy/overview.md) e [controlos de acesso com base do Azure função](../../role-based-access-control/overview.md). Para obter mais informações sobre grupos de gestão, consulte [organizar os recursos com grupos de gestão do Azure](overview.md).

O primeiro grupo de gestão criado no diretório pode demorar até 15 minutos a concluir. Existem processos que executam na primeira vez para configurar o serviço de grupos de gestão do Azure para o seu diretório. Receber uma notificação quando o processo estiver concluído.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="create-a-management-group"></a>Criar um grupo de gestão

Pode criar o grupo de gestão, utilizando o portal, o PowerShell ou a CLI do Azure. Atualmente, não é possível utilizar modelos do Resource Manager para criar grupos de gestão.

### <a name="create-in-portal"></a>Criar no portal

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **todos os serviços** > **grupos de gestão**.

1. Na página principal, selecione **grupo de gestão novos**.

   ![Página para trabalhar com grupos de gestão](./media/main.png)

1. Preencha o campo de ID do grupo de gestão.

   - O **ID do grupo de gestão** é o identificador exclusivo do diretório que é utilizado para submeter os comandos neste grupo de gestão. Este identificador não é editável após a criação pois ele é usado em todo o sistema do Azure para identificar este grupo. O [grupo de gestão de raiz](index.md#root-management-group-for-each-directory) são criados automaticamente com um ID que é o ID do Azure Active Directory. Para todos os outros grupos de gestão, atribuir um ID exclusivo.
   - O campo de nome de exibição é o nome que é apresentado no portal do Azure. Um nome de apresentação separada é um campo opcional ao criar a gestão de grupo e pode ser alterado em qualquer altura.  

   ![Painel de opções para criar um novo grupo de gestão](./media/create_context_menu.png)  

1. Selecione **Guardar**.

### <a name="create-in-powershell"></a>Criar no PowerShell

Para o PowerShell, utilize o [New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) cmdlet para criar um novo grupo de gestão.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

O **GroupName** é um identificador exclusivo que está sendo criado. Esse identificador é utilizado por outros comandos, para fazer referência a este grupo e não é possível alterar mais tarde.

Se pretender que o grupo de gestão para mostrar um nome diferente no portal do Azure, adicione a **DisplayName** parâmetro. Por exemplo, para criar um grupo de gestão com o GroupName da Contoso e o nome a apresentar do "Grupo de Contoso", utilize o seguinte cmdlet:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

Nos exemplos anteriores, o novo grupo de gestão é criado sob o grupo de gestão de raiz. Para especificar um grupo de gestão diferente como pai, utilize o **ParentId** parâmetro.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>Criar na CLI do Azure

Para a CLI do Azure, utilize o [criar grupo de gestão do conta az](/cli/azure/account/management-group?view=azure-cli-latest#az-account-management-group-create) comando para criar um novo grupo de gestão.

```azurecli-interactive
az account management-group create --name Contoso
```

O **nome** é um identificador exclusivo que está sendo criado. Esse identificador é utilizado por outros comandos, para fazer referência a este grupo e não é possível alterar mais tarde.

Se pretender que o grupo de gestão para mostrar um nome diferente no portal do Azure, adicione a **nome a apresentar** parâmetro. Por exemplo, para criar um grupo de gestão com o GroupName da Contoso e o nome a apresentar do "Grupo de Contoso", utilize o seguinte comando:

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

Nos exemplos anteriores, o novo grupo de gestão é criado sob o grupo de gestão de raiz. Para especificar um grupo de gestão diferente como pai, utilize o **principal** parâmetro e forneça o nome do grupo principal.

```azurecli-interactive
az account management-group create --name ContosoSubGroup --parent Contoso
```

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre os grupos de gestão, veja:

- [Criar grupos de gestão para organizar recursos do Azure](create.md)
- [Como alterar, eliminar ou gerir os seus grupos de gestão](manage.md)
- [Rever os grupos de gestão no Módulo de Recursos do Azure PowerShell](/powershell/module/az.resources#resources)
- [Rever os grupos de gestão na API REST](/rest/api/resources/managementgroups)
- [Rever os grupos de gestão na CLI do Azure](/cli/azure/account/management-group)