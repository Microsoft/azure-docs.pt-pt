---
title: Como trabalhar com os seus grupos de gestão - Azure Governance
description: Aprenda a visualizar, manter, atualizar e eliminar a hierarquia do seu grupo de gestão.
ms.date: 04/15/2020
ms.topic: conceptual
ms.openlocfilehash: e3d92e0785e54c0999b8cc0f055b7de355a4c4f5
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747483"
---
# <a name="manage-your-resources-with-management-groups"></a>Gerencie os seus recursos com grupos de gestão

Se a sua organização tiver várias subscrições, poderá precisar de uma forma de gerir eficazmente o acesso, as políticas e a conformidade para essas subscrições. Os grupos de gestão do Azure fornecem um nível de âmbito acima das subscrições. Estes permitem-lhe organizar as subscrições em contentores chamados "grupos de gestão" e aplicar as suas condições de governação aos grupos de gestão. Todas as subscrições num grupo de gestão herdam automaticamente as condições aplicadas ao grupo de gestão.

Os grupos de gestão dão-lhe capacidades de gestão de nível empresarial em grande escala, independentemente do seu tipo de subscrição. Para saber mais sobre grupos de gestão, consulte Organizar os seus recursos com grupos de [gestão Azure.](./overview.md)

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

> [!IMPORTANT]
> Os tokens de utilizador do Azure Resource Manager e a cache do grupo de gestão duram 30 minutos antes de serem obrigados a refrescar-se. Depois de fazer qualquer ação como mover um grupo de gestão ou subscrição, pode levar até 30 minutos para mostrar. Para ver as atualizações mais cedo você precisa atualizar o seu token, refrescando o navegador, insindo e saindo, ou solicitando um novo token.  

## <a name="change-the-name-of-a-management-group"></a>Alterar o nome de um grupo de gestão

Pode alterar o nome do grupo de gestão utilizando o portal PowerShell ou o Azure CLI.

### <a name="change-the-name-in-the-portal"></a>Mude o nome no portal

1. Inicie sessão no [portal Azure.](https://portal.azure.com)

1. Selecione **todos os grupos**de gestão de  >  **serviços**.

1. Selecione o grupo de gestão que gostaria de mudar o nome.

1. Selecione **detalhes**.

1. Selecione a opção **de grupo De nome** no topo da página.

   :::image type="content" source="./media/detail_action_small.png" alt-text="Renomear a opção do Grupo na página do grupo de gestão" border="false":::

1. Quando o menu abrir, introduza o novo nome que gostaria de ter exibido.

   :::image type="content" source="./media/rename_context.png" alt-text="Renomear painel do Grupo para renomear grupo de gestão" border="false":::

1. Selecione **Guardar**.

### <a name="change-the-name-in-powershell"></a>Mude o nome em PowerShell

Para atualizar o nome do ecrã utilize o **Update-AzManagementGroup**. Por exemplo, para alterar um nome de exibição de grupos de gestão de "Contoso IT" para "Grupo Contoso", executa o seguinte comando:

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>Alterar o nome em Azure CLI

Para o Azure CLI, utilize o comando de atualização.

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>Eliminar um grupo de gestão

Para eliminar um grupo de gestão, devem ser cumpridos os seguintes requisitos:

1. Não existem grupos de gestão infantil ou subscrições no âmbito do grupo de gestão.

   - Para mover um grupo de subscrição ou gestão para outro grupo de gestão, consulte [grupos de gestão em movimento e subscrições na hierarquia.](#moving-management-groups-and-subscriptions)

1. Precisa de permissões de escrita no grupo de gestão ("Proprietário", "Contribuinte" ou "Colaborador do Grupo de Gestão"). Para ver que permissões tem, selecione o grupo de gestão e, em seguida, **selecione IAM**. Para saber mais sobre os Papéis RBAC, consulte  
   [Gerir acessos e permissões com RBAC](../../role-based-access-control/overview.md).

### <a name="delete-in-the-portal"></a>Apagar no portal

1. Inicie sessão no [portal Azure.](https://portal.azure.com)

1. Selecione **todos os grupos**de gestão de  >  **serviços**.

1. Selecione o grupo de gestão que gostaria de eliminar.

1. Selecione **detalhes**.

1. **Selecione Excluir**

   :::image type="content" source="./media/delete.png" alt-text="Eliminar a opção de grupo" border="false":::

   > [!TIP]
   > Se o ícone estiver desativado, o seletor do rato sobre o ícone mostra-lhe a razão.

1. Há uma janela que se abre confirmando que quer apagar o grupo de gestão.

   :::image type="content" source="./media/delete_confirm.png" alt-text="Eliminar a janela de confirmação do grupo" border="false":::

1. Selecione **Sim**.

### <a name="delete-in-powershell"></a>Eliminar no PowerShell

Utilize o comando **Remove-AzManagementGroup** dentro da PowerShell para eliminar grupos de gestão.

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>Eliminação na CLI do Azure

Com o Azure CLI, utilize o grupo de gestão de conta az de comando.

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>Ver grupos de gestão

Pode ver qualquer grupo de gestão em que tenha um papel RBAC direto ou herdado.  

### <a name="view-in-the-portal"></a>Ver no portal

1. Inicie sessão no [portal Azure.](https://portal.azure.com)

1. Selecione **todos os grupos**de gestão de  >  **serviços**.

1. A página da hierarquia do grupo de gestão vai carregar. Esta página é onde você pode explorar todos os grupos de gestão e subscrições a que você tem acesso. A seleção do nome de grupo leva-o a um nível na hierarquia. A navegação funciona da mesma forma que um explorador de ficheiros.

1. Para ver os detalhes do grupo de gestão, selecione o link **(detalhes)** ao lado do título do grupo de gestão. Se este link não estiver disponível, não tem permissão para ver o grupo de gestão.

   :::image type="content" source="./media/main.png" alt-text="Principal" border="false":::

### <a name="view-in-powershell"></a>Vista em PowerShell

Usa o comando Get-AzManagementGroup para recuperar todos os grupos. Consulte os módulos [Az.Resources](/powershell/module/az.resources/Get-AzManagementGroup) para obter a lista completa dos comandos do grupo de gestão GET PowerShell.  

```azurepowershell-interactive
Get-AzManagementGroup
```

Para obter informações de um único grupo de gestão, utilize o parâmetro -GroupName

```azurepowershell-interactive
Get-AzManagementGroup -GroupName 'Contoso'
```

Para devolver um grupo de gestão específico e todos os níveis da hierarquia sob ele, use **-Expandir** e **-Recurse** parâmetros.  

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

### <a name="view-in-azure-cli"></a>Vista em Azure CLI

Usa o comando da lista para recuperar todos os grupos.  

```azurecli-interactive
az account management-group list
```

Para obter informações de um único grupo de gestão, use o comando do show

```azurecli-interactive
az account management-group show --name 'Contoso'
```

Para devolver um grupo de gestão específico e todos os níveis da hierarquia sob ele, use **-Expandir** e **-Recurse** parâmetros.

```azurecli-interactive
az account management-group show --name 'Contoso' -e -r
```

## <a name="moving-management-groups-and-subscriptions"></a>Grupos de gestão em movimento e subscrições   

Uma das razões para criar um grupo de gestão é agregar subscrições em conjunto. Apenas grupos de gestão e subscrições podem ser constituídos filhos de outro grupo de gestão. Uma subscrição que se move para um grupo de gestão herda todo o acesso e políticas dos utilizadores do grupo de gestão de pais

Ao mover um grupo de gestão ou subscrição para ser filho de outro grupo de gestão, três regras precisam de ser avaliadas como verdadeiras.

Se estás a fazer a ação, precisas de: 

- O grupo de gestão escreve e role Assignment write permissões no grupo de subscrição ou gestão infantil.
  - Exemplo de papel incorporado **Proprietário**
- O grupo de gestão escreve o acesso ao grupo de gestão dos pais-alvo.
  - Exemplo de papel incorporado: **Proprietário,** **Colaborador**, Colaborador do Grupo de **Gestão**
- O grupo de gestão escreve o acesso ao grupo de gestão parental existente.
  - Exemplo de papel incorporado: **Proprietário,** **Colaborador**, Colaborador do Grupo de **Gestão**

**Exceção**: Se o alvo ou o grupo de gestão dos pais existente sou o grupo de gestão root, os requisitos de permissões não se aplicam. Uma vez que o grupo de gestão root é o local de aterragem padrão para todos os novos grupos de gestão e subscrições, você não precisa de permissões nele para mover um item.

Se o papel do Proprietário na subscrição for herdado do grupo de gestão atual, os seus alvos de movimento são limitados. Só pode mover a subscrição para outro grupo de gestão onde tem o papel de Proprietário. Não pode movê-lo para um grupo de gestão onde é um contribuinte porque perderia a propriedade da subscrição. Se você é diretamente atribuído ao papel de Proprietário para a subscrição (não herdada do grupo de gestão), você é capaz de movê-lo para qualquer grupo de gestão onde você é um contribuinte.

Para ver que permissões tem no portal Azure, selecione o grupo de gestão e, em seguida, **selecione IAM**. Para saber mais sobre as Funções RBAC, consulte [Gerir acessoe permissões com RBAC](../../role-based-access-control/overview.md).

## <a name="move-subscriptions"></a>Mover subscrições 

### <a name="add-an-existing-subscription-to-a-management-group-in-the-portal"></a>Adicione uma Subscrição existente a um grupo de gestão no portal

1. Inicie sessão no [portal Azure.](https://portal.azure.com)

1. Selecione **todos os grupos**de gestão de  >  **serviços**.

1. Selecione o grupo de gestão que planeia ser o pai.

1. No topo da página, selecione **Adicionar subscrição**.

1. Selecione a subscrição na lista com o ID correto.

   :::image type="content" source="./media/add_context_sub.png" alt-text="Subscrições disponíveis para adicionar a um grupo de gestão" border="false":::

1. Selecione "Guardar".

### <a name="remove-a-subscription-from-a-management-group-in-the-portal"></a>Remover uma subscrição de um grupo de gestão no portal

1. Inicie sessão no [portal Azure.](https://portal.azure.com)

1. Selecione **todos os grupos**de gestão de  >  **serviços**.

1. Selecione o grupo de gestão que está a planear que é o pai atual.  

1. Selecione a elipse no final da linha para a subscrição na lista que pretende mover.

   :::image type="content" source="./media/move_small.png" alt-text="Mover opção em um grupo de gestão" border="false":::

1. Selecione **Mover**.

1. No menu que abre, selecione o grupo de **gestão dos pais.**

   :::image type="content" source="./media/move_small_context.png" alt-text="Mova o painel para mudar o grupo dos pais" border="false":::

1. Selecione **Guardar**.

### <a name="move-subscriptions-in-powershell"></a>Mover subscrições no PowerShell

Para mover uma subscrição no PowerShell, utiliza o comando New-AzManagementGroupSubscription.  

```azurepowershell-interactive
New-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

Para remover a ligação entre e a subscrição e o grupo de gestão utilizar o comando remove-AzManagementGroupSubscription.

```azurepowershell-interactive
Remove-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>Mover subscrições em Azure CLI

Para mover uma subscrição em CLI, utilize o comando de adição.

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

Para remover a subscrição do grupo de gestão, utilize o comando de remoção de subscrição.  

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

## <a name="move-management-groups"></a>Mover grupos de gestão 

### <a name="move-management-groups-in-the-portal"></a>Mover grupos de gestão no portal

1. Inicie sessão no [portal Azure.](https://portal.azure.com)

1. Selecione **todos os grupos**de gestão de  >  **serviços**.

1. Selecione o grupo de gestão que planeia ser o pai.

1. No topo da página, selecione **Adicionar grupo de gestão**.

1. No menu que abre, selecione se deseja um novo ou utilize um grupo de gestão existente.

   - A seleção de novos irá criar um novo grupo de gestão.
   - A seleção de um existente irá apresentar-lhe uma queda de todos os grupos de gestão que pode mover para este grupo de gestão.  

   :::image type="content" source="./media/add_context_MG.png" alt-text="Mover um grupo de gestão para um grupo novo ou existente" border="false":::

1. Selecione **Guardar**.

### <a name="move-management-groups-in-powershell"></a>Mover grupos de gestão em PowerShell

Utilize o comando Update-AzManagementGroup no PowerShell para mover um grupo de gestão sob um grupo diferente.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName ContosoIT
Update-AzManagementGroup -GroupName 'Contoso' -ParentId $parentGroup.id
```  

### <a name="move-management-groups-in-azure-cli"></a>Grupos de gestão de movimentos no Azure CLI

Utilize o comando de atualização para mover um grupo de gestão com o Azure CLI.

```azurecli-interactive
az account management-group update --name 'Contoso' --parent ContosoIT
```

## <a name="audit-management-groups-using-activity-logs"></a>Auditar os grupos de gestão que utilizam registos de atividades

Os grupos de gestão são suportados no [Registo de Atividades do Azure](../../azure-monitor/platform/platform-logs-overview.md). Você pode consultar todos os eventos que acontecem a um grupo de gestão no mesmo local central que outros recursos Azure. Por exemplo, pode ver todas as alterações de Atribuições de Funções ou de Atribuição de Política feitas a um grupo de gestão específico.

:::image type="content" source="./media/al-mg.png" alt-text="Registos de Atividadecom grupos de gestão" border="false":::

Quando quiser consultar Grupos de Gestão fora do portal do Azure, o âmbito de destino dos grupos de gestão é semelhante a **"/ providers/Microsoft.Management/managementGroups/{yourMgID}"**.

## <a name="referencing-management-groups-from-other-resource-providers"></a>Referenciação de grupos de gestão de outros Fornecedores de Recursos

Ao referir grupos de gestão de outras ações do Fornecedor de Recursos, utilize o seguinte caminho como âmbito. Este caminho é utilizado ao utilizar PowerShell, Azure CLI e REST APIs.  

`/providers/Microsoft.Management/managementGroups/{yourMgID}`

Um exemplo de utilização deste caminho é a atribuição de uma nova atribuição de funções a um grupo de gestão na PowerShell:

```azurepowershell-interactive
New-AzRoleAssignment -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

O mesmo caminho de âmbito é utilizado na recuperação de uma definição de política num grupo de gestão.

```http
GET https://management.azure.com/providers/Microsoft.Management/managementgroups/MyManagementGroup/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming?api-version=2019-09-01
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os grupos de gestão, veja:

- [Criar grupos de gestão para organizar recursos do Azure](./create.md)
- [Como alterar, eliminar ou gerir os seus grupos de gestão](./manage.md)
- [Rever os grupos de gestão no Módulo de Recursos do Azure PowerShell](/powershell/module/az.resources#resources)
- [Rever os grupos de gestão na API REST](/rest/api/resources/managementgroups)
- [Rever os grupos de gestão na CLI do Azure](/cli/azure/account/management-group)
