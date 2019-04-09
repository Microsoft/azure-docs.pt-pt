---
title: Criar grupos de gestão para organizar recursos do Azure - governação do Azure
description: Saiba como criar grupos de gestão do Azure para gerir vários recursos com o portal, o Azure PowerShell e a CLI do Azure.
author: rthorn17
manager: rithorn
ms.service: azure-resource-manager
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2019
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: 928cb790bd97270870618534a73316bba5eeb070
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057443"
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

No PowerShell, utilize o cmdlet New-AzManagementGroup:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

O **GroupName** é um identificador exclusivo que está sendo criado. Esse identificador é utilizado por outros comandos, para fazer referência a este grupo e não é possível alterar mais tarde.

Se quisesse o grupo de gestão para mostrar um nome diferente no portal do Azure, adicionaria as **DisplayName** parâmetro com a cadeia de caracteres. Por exemplo, se quisesse criar um grupo de gestão com o GroupName da Contoso e o nome a apresentar do "Grupo de Contoso", usaria o seguinte cmdlet:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group' -ParentId '/providers/Microsoft.Management/managementGroups/ContosoTenant'
```

Utilize o **ParentId** parâmetro para que este grupo de gestão criadas numa gestão diferentes.

### <a name="create-in-azure-cli"></a>Criar na CLI do Azure

Na CLI do Azure, utiliza o az comando de criação do grupo de gestão de conta.

```azurecli-interactive
az account management-group create --name 'Contoso'
```

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre os grupos de gestão, veja:

- [Criar grupos de gestão para organizar recursos do Azure](create.md)
- [Como alterar, eliminar ou gerir os seus grupos de gestão](manage.md)
- [Reveja os grupos de gestão no módulo de recursos do Azure PowerShell](/powershell/module/az.resources#resources)
- [Reveja os grupos de gestão na REST API](/rest/api/resources/managementgroups)
- [Reveja os grupos de gestão na CLI do Azure](/cli/azure/account/management-group)