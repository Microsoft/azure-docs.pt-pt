---
title: Criar grupos de gerenciamento para organizar recursos do Azure-governança do Azure
description: Saiba como criar grupos de gerenciamento do Azure para gerenciar vários recursos usando o portal, Azure PowerShell e CLI do Azure.
author: rthorn17
manager: rithorn
ms.service: governance
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: 7b5ee5bfe4b7498bc13ff0c8582b49fed8478e0a
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71337789"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Criar grupos de gerenciamento para organização de recursos e gerenciamento

Os grupos de gerenciamento são contêineres que ajudam a gerenciar o acesso, a política e a conformidade em várias assinaturas. Crie esses contêineres para criar uma hierarquia eficaz e eficiente que pode ser usada com [Azure Policy](../policy/overview.md) e [controles de acesso baseados em função do Azure](../../role-based-access-control/overview.md). Para obter mais informações sobre grupos de gerenciamento, consulte [organizar seus recursos com grupos de gerenciamento do Azure](overview.md).

O primeiro grupo de gerenciamento criado no diretório pode levar até 15 minutos para ser concluído. Há processos que são executados na primeira vez para configurar o serviço de grupos de gerenciamento no Azure para seu diretório. Você receberá uma notificação quando o processo for concluído.

## <a name="create-a-management-group"></a>Criar um grupo de gestão

Você pode criar o grupo de gerenciamento usando o portal, o PowerShell ou o CLI do Azure. No momento, não é possível usar modelos do Resource Manager para criar grupos de gerenciamento.

### <a name="create-in-portal"></a>Criar no portal

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **todos os serviços** > **grupos de gerenciamento**.

1. Na página principal, selecione **novo grupo de gerenciamento**.

   ![Página para trabalhar com grupos de gerenciamento](./media/main.png)

1. Preencha o campo ID do grupo de gerenciamento.

   - A **ID do grupo de gerenciamento** é o identificador exclusivo do diretório que é usado para enviar comandos nesse grupo de gerenciamento. Esse identificador não é editável após a criação, pois é usado em todo o sistema do Azure para identificar esse grupo. O [grupo de gerenciamento raiz](overview.md#root-management-group-for-each-directory) é criado automaticamente com uma ID que é a id de Azure Active Directory. Para todos os outros grupos de gerenciamento, atribua uma ID exclusiva.
   - O campo nome de exibição é o nome que é exibido dentro do portal do Azure. Um nome de exibição separado é um campo opcional ao criar o grupo de gerenciamento e pode ser alterado a qualquer momento.  

   ![Painel de opções para criar um novo grupo de gerenciamento](./media/create_context_menu.png)  

1. Selecione **Guardar**.

### <a name="create-in-powershell"></a>Criar no PowerShell

Para o PowerShell, use o cmdlet [New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) para criar um novo grupo de gerenciamento.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**GroupName** é um identificador exclusivo que está sendo criado. Essa ID é usada por outros comandos para fazer referência a esse grupo e não pode ser alterada posteriormente.

Se você quiser que o grupo de gerenciamento mostre um nome diferente dentro do portal do Azure, adicione o parâmetro **DisplayName** . Por exemplo, para criar um grupo de gerenciamento com o GroupName de contoso e o nome de exibição de "grupo contoso", use o seguinte cmdlet:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

Nos exemplos anteriores, o novo grupo de gerenciamento é criado no grupo de gerenciamento raiz. Para especificar um grupo de gerenciamento diferente como pai, use o parâmetro **parentID** .

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>Criar no CLI do Azure

Para CLI do Azure, use o comando [AZ Account Management-Group Create](/cli/azure/account/management-group?view=azure-cli-latest#az-account-management-group-create) para criar um novo grupo de gerenciamento.

```azurecli-interactive
az account management-group create --name Contoso
```

O **nome** é um identificador exclusivo que está sendo criado. Essa ID é usada por outros comandos para fazer referência a esse grupo e não pode ser alterada posteriormente.

Se você quiser que o grupo de gerenciamento mostre um nome diferente dentro do portal do Azure, adicione o parâmetro **Display-Name** . Por exemplo, para criar um grupo de gerenciamento com o GroupName de contoso e o nome de exibição de "grupo contoso", use o seguinte comando:

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

Nos exemplos anteriores, o novo grupo de gerenciamento é criado no grupo de gerenciamento raiz. Para especificar um grupo de gerenciamento diferente como pai, use o parâmetro **pai** e forneça o nome do grupo pai.

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