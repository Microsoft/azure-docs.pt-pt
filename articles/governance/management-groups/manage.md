---
title: Como alterar, eliminar ou gerir os seus grupos de gestão no Azure - governação do Azure
description: Saiba como ver, manter, atualizar e eliminar a hierarquia de grupo de gestão.
author: rthorn17
ms.service: azure-resource-manager
ms.date: 02/20/2019
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: 51e9d44a95a3896767caf4b3f04d17c2933e8599
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56990549"
---
# <a name="manage-your-resources-with-management-groups"></a>Gerir os recursos com grupos de gestão

Se a sua organização tiver várias subscrições, poderá precisar de uma forma de gerir eficazmente o acesso, as políticas e a conformidade para essas subscrições. Os grupos de gestão do Azure fornecem um nível de âmbito acima das subscrições. Estes permitem-lhe organizar as subscrições em contentores chamados "grupos de gestão" e aplicar as suas condições de governação aos grupos de gestão. Todas as subscrições num grupo de gestão herdam automaticamente as condições aplicadas ao grupo de gestão.

Os grupos de gestão dão-lhe capacidades de gestão de nível empresarial em grande escala, independentemente do seu tipo de subscrição.  Para saber mais sobre os grupos de gestão, veja [organizar os recursos com grupos de gestão do Azure](overview.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="change-the-name-of-a-management-group"></a>Alterar o nome de um grupo de gestão

Pode alterar o nome do grupo de gestão, utilizando o portal, o PowerShell ou a CLI do Azure.

### <a name="change-the-name-in-the-portal"></a>Altere o nome no portal

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **todos os serviços** > **grupos de gestão**.

1. Selecione o grupo de gestão que pretende mudar o nome.

1. Selecione o **mudança de nome de grupo** opção na parte superior da página.

   ![Opção de mudança de nome de grupo](./media/detail_action_small.png)

1. Quando abre o menu, introduza o novo nome que gostaria de ter apresentado.

   ![Painel de mudança de nome de grupo](./media/rename_context.png)

1. Selecione **Guardar**.

### <a name="change-the-name-in-powershell"></a>Alterar o nome no PowerShell

Para atualizar o uso de nome de exibição **AzManagementGroup atualização**. Por exemplo, para alterar um gerenciamento de grupos de nome a apresentar de "Contoso TI" para "Grupo de Contoso", execute o seguinte comando:

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>Altere o nome na CLI do Azure

Para a CLI do Azure, utilize o comando de atualização.

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>Eliminar um grupo de gestão

Para eliminar um grupo de gestão, devem ser cumpridos os seguintes requisitos:

1. Não há grupos de gestão subordinado ou subscrições no grupo de gestão.

   - Para mover uma subscrição para fora de um grupo de gestão, consulte [mover a subscrição para outro grupo de gestão](#Move-subscriptions-in-the-hierarchy).

   - Para mover um grupo de gestão para outro grupo de gestão, consulte [mover grupos de gestão na hierarquia de](#Move-management-groups-in-the-hierarchy).

1. Tem permissões de escrita no grupo de gestão ("Proprietário", "Contribuinte" ou "Contribuinte do grupo de gestão"). Para ver quais as permissões que tem, selecione o grupo de gestão e, em seguida, selecione **IAM**. Para saber mais sobre as funções do RBAC, veja [gerir o acesso e permissões com RBAC](../../role-based-access-control/overview.md).  

### <a name="delete-in-the-portal"></a>Eliminar no portal

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **todos os serviços** > **grupos de gestão**.

1. Selecione o grupo de gestão que pretende eliminar.

1. Selecione **eliminar**

    > [!TIP]
    > Se o ícone estiver desabilitado, passar o rato seu Seletor de mouse sobre o ícone mostra-lhe o motivo.

   ![Eliminar a opção de grupo](./media/delete.png)

1. Há uma janela que abre-se de confirmar que pretende eliminar o grupo de gestão.

   ![Eliminar a janela de confirmação de grupo](./media/delete_confirm.png)

1. Selecione **Sim**.

### <a name="delete-in-powershell"></a>Eliminar no PowerShell

Utilize o **Remove-AzManagementGroup** comando do PowerShell para eliminar grupos de gestão.

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>Eliminação na CLI do Azure

Com a CLI do Azure, utilize a eliminação de grupo de gestão de conta do comando az.

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>Ver grupos de gestão

Pode ver qualquer grupo de gestão que tem uma função RBAC direta ou herdada no.  

### <a name="view-in-the-portal"></a>Ver no portal

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **todos os serviços** > **grupos de gestão**.

1. A página de hierarquia de grupo de gestão será carregado. Esta página é onde pode explorar todos os grupos de gestão e subscrições que tem acesso. Selecionar o nome do grupo, leva-o para um nível na hierarquia. A navegação funciona da mesma forma um Explorador de ficheiros.

1. Para ver os detalhes do grupo de gestão, selecione o **(detalhes)** ligação ao lado do título do grupo de gestão. Se esta ligação não estiver disponível, não tem permissões para ver esse grupo de gestão.

   ![Principal](./media/main.png)

### <a name="view-in-powershell"></a>Modo de exibição no PowerShell

Utilize o comando Get-AzManagementGroup para recuperar todos os grupos.  Ver [Az.Resources](/powershell/module/az.resources/Get-AzManagementGroup) módulos para a lista completa de gestão de grupo de comandos do Powershell de obter.  

```azurepowershell-interactive
Get-AzManagementGroup
```

Para informações de um grupo de gestão único, utilize o parâmetro - GroupName

```azurepowershell-interactive
Get-AzManagementGroup -GroupName 'Contoso'
```

Para devolver um grupo de gestão específico e todos os níveis da hierarquia abaixo do mesmo, utilize **-expandir** e **-Recurse** parâmetros.  

```azurepowershell-interactive
PS C:\> $response = Get-AzManagementGroup -GroupName TestGroupParent -Expand -Recurse
PS C:\> $response

Id                : /providers/Microsoft.Management/managementGroups/TestGroupParent
Type              : /providers/Microsoft.Management/managementGroups
Name              : TestGroupParent
TenantId          : 00000000-0000-0000-0000-000000000000
DisplayName       : TestGroupParent
UpdatedTime       : 2/1/2018 11:15:46 AM
UpdatedBy         : 00000000-0000-0000-0000-000000000000
ParentId          : /providers/Microsoft.Management/managementGroups/00000000-0000-0000-0000-000000000000
ParentName        : 00000000-0000-0000-0000-000000000000
ParentDisplayName : 00000000-0000-0000-0000-000000000000
Children          : {TestGroup1DisplayName, TestGroup2DisplayName}

PS C:\> $response.Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestGroup1
Name        : TestGroup1
DisplayName : TestGroup1DisplayName
Children    : {TestRecurseChild}

PS C:\> $response.Children[0].Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestRecurseChild
Name        : TestRecurseChild
DisplayName : TestRecurseChild
Children    :
```

### <a name="view-in-azure-cli"></a>Modo de exibição na CLI do Azure

Utilize o comando de lista para obter todos os grupos.  

```azurecli-interactive
az account management-group list
```

Para informações de um grupo de gestão único, use o comando show

```azurecli-interactive
az account management-group show --name 'Contoso'
```

Para devolver um grupo de gestão específico e todos os níveis da hierarquia abaixo do mesmo, utilize **-expandir** e **-Recurse** parâmetros.

```azurecli-interactive
az account management-group show --name 'Contoso' -e -r
```

## <a name="move-subscriptions-in-the-hierarchy"></a>Mover subscrições na hierarquia

Uma das razões para criar um grupo de gestão é agrupar subscrições. Apenas grupos de gestão e as subscrições podem ser feitas filhos do outro grupo de gestão. Uma subscrição que se move para um grupo de gestão herda todas as políticas de acesso de utilizador e do grupo de gestão principal.

Para mover a subscrição, todas as permissões RBAC seguintes tem de ser verdadeiras:

- Função de "Proprietário" na subscrição filho.
- Função "Proprietário", "Contribuinte" ou "Contribuinte do grupo de gestão" no group.* de gestão do destino principal
- Função "Proprietário", "Contribuinte" ou "Contribuinte do grupo de gestão" no group.* de gestão principal existente

*: A menos que o grupo de gestão principal existente ou o destino é o grupo de gestão de raiz. Uma vez que o grupo de gestão de raiz é a predefinição de destino iguais para todos os novos grupos de gestão e subscrições, os utilizadores não precisam de permissões no mesmo para mover um item.

Para ver quais as permissões que tem no portal do Azure, selecione a gestão de grupo e, em seguida, selecione **IAM**. Para saber mais sobre as funções do RBAC, veja [gerir o acesso e permissões com RBAC](../../role-based-access-control/overview.md).

### <a name="move-subscriptions-in-the-portal"></a>Mover subscrições no portal

#### <a name="add-an-existing-subscription-to-a-management-group"></a>Adicionar uma subscrição existente para um grupo de gestão

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **todos os serviços** > **grupos de gestão**.

1. Selecione o grupo de gestão que pretende ser o elemento principal.

1. Na parte superior da página, selecione **adicionar subscrição**.

1. Selecione a subscrição na lista com o ID correto.

   ![Crianças](./media/add_context_sub.png)

1. Selecione "Guardar".

#### <a name="remove-a-subscription-from-a-management-group"></a>Remover uma assinatura de um grupo de gestão

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **todos os serviços** > **grupos de gestão**.

1. Selecione o grupo de gestão estiver a planear isto é o principal atual.  

1. Selecione as reticências no final da linha para a subscrição na lista que pretende mover.

   ![Opção de movimentação](./media/move_small.png)

1. Selecione **mover**.

1. No menu que se abre, selecione o **grupo de gestão principal**.

   ![Mover o painel](./media/move_small_context.png)

1. Selecione **Guardar**.

### <a name="move-subscriptions-in-powershell"></a>Mover subscrições no PowerShell

Para mover uma subscrição no PowerShell, utilize o comando New-AzManagementGroupSubscription.  

```azurepowershell-interactive
New-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

Para remover a associação entre e subscrição e o grupo de gestão, utilize o comando de Remove-AzManagementGroupSubscription.

```azurepowershell-interactive
Remove-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>Mover subscrições na CLI do Azure

Para mover uma subscrição na CLI, utilize o comando add.

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

Para remover a subscrição do grupo de gestão, utilize o comando de remoção de subscrição.  

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

## <a name="move-management-groups-in-the-hierarchy"></a>Mover grupos de gestão na hierarquia  

Ao mover um grupo de gestão principal, a hierarquia de grupo seja também movido.

### <a name="move-management-groups-in-the-portal"></a>Mover grupos de gestão no portal

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **todos os serviços** > **grupos de gestão**.

1. Selecione o grupo de gestão que pretende ser o elemento principal.

1. Na parte superior da página, selecione **adicionar grupo de gestão**.

1. No menu que se abre, selecione se quiser que um novo ou utilizar um grupo de gestão existente.

   - Selecionar novo irá criar um novo grupo de gestão.
   - Selecionar um existente irá apresentar-lhe com um menu suspenso de todos os grupos de gestão que pode mover a este grupo de gestão.  

   ![mover](./media/add_context_MG.png)

1. Selecione **Guardar**.

### <a name="move-management-groups-in-powershell"></a>Mover grupos de gestão no PowerShell

Utilize o comando de atualização AzManagementGroup no PowerShell para mover um grupo de gestão num grupo diferente.

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'Contoso' -ParentId '/providers/Microsoft.Management/managementGroups/ContosoIT'
```  

### <a name="move-management-groups-in-azure-cli"></a>Mover grupos de gestão na CLI do Azure

Utilize o comando de atualização para mover um grupo de gestão com a CLI do Azure.

```azurecli-interactive
az account management-group update --name 'Contoso' --parent-id '/providers/Microsoft.Management/managementGroups/ContosoIT'
```

## <a name="audit-management-groups-using-activity-logs"></a>Auditar os grupos de gestão que utilizam registos de atividades

Os grupos de gestão são suportados dentro [registo de atividades do Azure](../../azure-monitor/platform/activity-logs-overview.md). Pode consultar todos os eventos que ocorrem a um grupo de gestão na mesma localização central como outros recursos do Azure.  Por exemplo, pode ver todas as atribuições de funções ou atribuição de política as alterações efetuadas a um grupo de gestão específico.

![Registos de atividades com grupos de gestão](media/al-mg.png)

Ao ver a consulta em grupos de gestão fora do portal do Azure, o âmbito de destino para grupos de gestão é semelhante **"/ providers/Microsoft.Management/managementGroups/{yourMgID}"**.

## <a name="referencing-management-groups-from-other-resource-providers"></a>Grupos de gestão de referenciação de outros fornecedores de recursos

Ao fazer referência a grupos de gestão de ações de outros fornecedor de recursos, utilize o seguinte caminho como o âmbito. Este caminho é utilizado ao utilizar o PowerShell, CLI do Azure e REST APIs.  

>"/providers/Microsoft.Management/managementGroups/{yourMgID}"

Um exemplo de uso esse caminho é quando atribui uma nova atribuição de função a um grupo de gestão do PowerShell

```powershell-interactive
New-AzRoleAssignment -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

O mesmo caminho de âmbito é utilizado quando uma definição de política num grupo de gestão a obter.

```http
GET https://management.azure.com/providers/Microsoft.Management/managementgroups/MyManagementGroup/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming?api-version=2018-05-01
```

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre os grupos de gestão, veja:

- [Criar grupos de gestão para organizar recursos do Azure](create.md)
- [Como alterar, eliminar ou gerir os seus grupos de gestão](manage.md)
- [Rever os grupos de gestão no Módulo de Recursos do Azure PowerShell](/powershell/module/az.resources#resources)
- [Rever os grupos de gestão na API REST](/rest/api/resources/managementgroups)
- [Rever os grupos de gestão na CLI do Azure](/cli/azure/account/management-group)