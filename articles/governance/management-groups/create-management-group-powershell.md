---
title: 'Quickstart: Criar um grupo de gestão com a Azure PowerShell'
description: Neste arranque rápido, você usa a Azure PowerShell para criar um grupo de gestão para organizar os seus recursos numa hierarquia de recursos.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 216cfeb6e1389793afcfd27d8785a5f912db2c97
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99592539"
---
# <a name="quickstart-create-a-management-group-with-azure-powershell"></a>Quickstart: Criar um grupo de gestão com a Azure PowerShell

Os grupos de gestão são contentores que o ajudam a gerir o acesso, a política e o cumprimento em várias subscrições. Crie estes recipientes para construir uma hierarquia eficaz e eficiente que possa ser usada com [a Azure Policy](../policy/overview.md) e [os Controlos de Acesso Baseados em Funções Azure](../../role-based-access-control/overview.md). Para obter mais informações sobre grupos de gestão, consulte [Organizar os seus recursos com grupos de gestão Azure.](overview.md)

O primeiro grupo de gestão criado no diretório pode demorar até 15 minutos para ser concluído. Há processos que correm pela primeira vez para configurar o serviço de grupos de gestão dentro do Azure para o seu diretório. Recebe uma notificação quando o processo estiver concluído. Para mais informações, consulte [a configuração inicial dos grupos de gestão.](./overview.md#initial-setup-of-management-groups)

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

- Antes de começar, certifique-se de que a versão mais recente do Azure PowerShell está instalada. Consulte [o módulo Azure PowerShell](/powershell/azure/install-az-ps) para obter informações detalhadas.

- Qualquer utilizador Azure AD no arrendatário pode criar um grupo de gestão sem que o grupo de gestão escreva a permissão atribuída a esse utilizador se a [proteção da hierarquia](./how-to/protect-resource-hierarchy.md#setting---require-authorization) não estiver ativada. Este novo grupo de gestão torna-se filho do Grupo de Gestão de Raízes ou do [grupo de gestão padrão](./how-to/protect-resource-hierarchy.md#setting---default-management-group) e o criador recebe uma atribuição de função de "Proprietário". O serviço de grupo de gestão permite esta capacidade para que as atribuições de funções não sejam necessárias ao nível da raiz. Nenhum utilizadores tem acesso ao Grupo de Gestão de Raízes quando este é criado. Para evitar o obstáculo de encontrar os Azure AD Global Admins para começar a usar grupos de gestão, permitimos a criação dos grupos de gestão iniciais ao nível da raiz.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-azure-powershell"></a>Criar em Azure PowerShell

Para o PowerShell, utilize o [cmdlet New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) para criar um novo grupo de gestão. Neste exemplo, o grupo de gestão **GroupName** é _o Contoso._

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

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover o grupo de gestão acima criado, utilize o cmdlet [Remove-AzManagementGroup:](/powershell/module/az.resources/remove-azmanagementgroup)

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou um grupo de gestão para organizar a sua hierarquia de recursos. O grupo de gestão pode realizar subscrições ou outros grupos de gestão.

Para saber mais sobre grupos de gestão e como gerir a sua hierarquia de recursos, continue a:

> [!div class="nextstepaction"]
> [Gerir os seus recursos com grupos de gestão](./manage.md)