---
title: Como alterar, excluir ou gerenciar seus grupos de gerenciamento-governança do Azure
description: Saiba como exibir, manter, atualizar e excluir sua hierarquia de grupo de gerenciamento.
author: rthorn17
ms.service: governance
ms.date: 05/22/2019
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: 7fd9a8e00072bfb8d3a0e57fb552822588111019
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241185"
---
# <a name="manage-your-resources-with-management-groups"></a>Gerenciar seus recursos com grupos de gerenciamento

Se a sua organização tiver várias subscrições, poderá precisar de uma forma de gerir eficazmente o acesso, as políticas e a conformidade para essas subscrições. Os grupos de gestão do Azure fornecem um nível de âmbito acima das subscrições. Estes permitem-lhe organizar as subscrições em contentores chamados "grupos de gestão" e aplicar as suas condições de governação aos grupos de gestão. Todas as subscrições num grupo de gestão herdam automaticamente as condições aplicadas ao grupo de gestão.

Os grupos de gestão dão-lhe capacidades de gestão de nível empresarial em grande escala, independentemente do seu tipo de subscrição.  Para saber mais sobre grupos de gerenciamento, confira [organizar seus recursos com grupos de gerenciamento do Azure](overview.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-name-of-a-management-group"></a>Alterar o nome de um grupo de gerenciamento

Você pode alterar o nome do grupo de gerenciamento usando o portal, o PowerShell ou o CLI do Azure.

### <a name="change-the-name-in-the-portal"></a>Alterar o nome no portal

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **todos os serviços** > **grupos de gerenciamento**.

1. Selecione o grupo de gerenciamento que deseja renomear.

1. Selecione **detalhes**.

1. Selecione a opção renomear **grupo** na parte superior da página.

   ![Opção Renomear Grupo na página grupo de gerenciamento](./media/detail_action_small.png)

1. Quando o menu for aberto, insira o novo nome que você gostaria de exibir.

   ![Renomear painel de grupo para renomear grupo de gerenciamento](./media/rename_context.png)

1. Selecione **Guardar**.

### <a name="change-the-name-in-powershell"></a>Alterar o nome no PowerShell

Para atualizar o nome de exibição **, use Update-AzManagementGroup**. Por exemplo, para alterar o nome de exibição de grupos de gerenciamento de "contoso IT" para "contoso Group", execute o seguinte comando:

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>Altere o nome em CLI do Azure

Para CLI do Azure, use o comando Update.

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>Excluir um grupo de gerenciamento

Para excluir um grupo de gerenciamento, os seguintes requisitos devem ser atendidos:

1. Não há grupos de gerenciamento filho ou assinaturas no grupo de gerenciamento.

   - Para mover uma assinatura para fora de um grupo de gerenciamento, consulte [mover a assinatura para outro grupo de gerenciamento](#move-subscriptions-in-the-hierarchy).

   - Para mover um grupo de gerenciamento para outro grupo de gerenciamento, consulte [mover grupos de gerenciamento na hierarquia](#move-management-groups-in-the-hierarchy).

1. Você tem permissões de gravação no grupo de gerenciamento ("proprietário", "colaborador" ou "colaborador do grupo de gerenciamento"). Para ver quais permissões você tem, selecione o grupo de gerenciamento e, em seguida, selecione **iam**. Para saber mais sobre as funções de RBAC, consulte [gerenciar acesso e permissões com o RBAC](../../role-based-access-control/overview.md).  

### <a name="delete-in-the-portal"></a>Excluir no portal

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **todos os serviços** > **grupos de gerenciamento**.

1. Selecione o grupo de gerenciamento que você deseja excluir.

1. Selecione **detalhes**.

1. Selecione **excluir**

    > [!TIP]
    > Se o ícone estiver desabilitado, focalizar o seletor do mouse sobre o ícone mostrará o motivo.

   ![Opção Excluir grupo](./media/delete.png)

1. Há uma janela que abre a confirmação de que você deseja excluir o grupo de gerenciamento.

   ![Janela excluir confirmação do grupo](./media/delete_confirm.png)

1. Selecione **Sim**.

### <a name="delete-in-powershell"></a>Excluir no PowerShell

Use o comando **Remove-AzManagementGroup** no PowerShell para excluir grupos de gerenciamento.

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>Eliminação na CLI do Azure

Com CLI do Azure, use o comando AZ Account Management-Group Delete.

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>Exibir grupos de gerenciamento

Você pode exibir qualquer grupo de gerenciamento no qual tenha uma função de RBAC direta ou herdada.  

### <a name="view-in-the-portal"></a>Exibir no portal

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **todos os serviços** > **grupos de gerenciamento**.

1. A página hierarquia do grupo de gerenciamento será carregada. Esta página é onde você pode explorar todos os grupos de gerenciamento e assinaturas aos quais você tem acesso. Selecionar o nome do grupo o leva um nível para baixo na hierarquia. A navegação funciona da mesma forma que um explorador de arquivos.

1. Para ver os detalhes do grupo de gerenciamento, selecione o link **(detalhes)** ao lado do título do grupo de gerenciamento. Se esse link não estiver disponível, você não terá permissões para exibir esse grupo de gerenciamento.

   ![Principal](./media/main.png)

### <a name="view-in-powershell"></a>Exibir no PowerShell

Use o comando Get-AzManagementGroup para recuperar todos os grupos.  Consulte módulos [AZ.](/powershell/module/az.resources/Get-AzManagementGroup) Resources para obter a lista completa do grupo de gerenciamento obter comandos do PowerShell.  

```azurepowershell-interactive
Get-AzManagementGroup
```

Para obter informações de um único grupo de gerenciamento, use o parâmetro-GroupName

```azurepowershell-interactive
Get-AzManagementGroup -GroupName 'Contoso'
```

Para retornar um grupo de gerenciamento específico e todos os níveis da hierarquia sob ele, use os parâmetros **-Expand** e **-recurse** .  

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

### <a name="view-in-azure-cli"></a>Exibir no CLI do Azure

Você usa o comando list para recuperar todos os grupos.  

```azurecli-interactive
az account management-group list
```

Para obter informações de um único grupo de gerenciamento, use o comando show

```azurecli-interactive
az account management-group show --name 'Contoso'
```

Para retornar um grupo de gerenciamento específico e todos os níveis da hierarquia sob ele, use os parâmetros **-Expand** e **-recurse** .

```azurecli-interactive
az account management-group show --name 'Contoso' -e -r
```

## <a name="move-subscriptions-in-the-hierarchy"></a>Mover assinaturas na hierarquia

Um motivo para criar um grupo de gerenciamento é agrupar assinaturas. Somente grupos de gerenciamento e assinaturas podem se tornar filhos de outro grupo de gerenciamento. Uma assinatura que se move para um grupo de gerenciamento herda todo o acesso do usuário e as políticas do grupo de gerenciamento pai.

Para mover a assinatura, todas as seguintes permissões RBAC precisam ser verdadeiras:

- Função de "proprietário" na assinatura filho.
- Função "proprietário", "colaborador" ou "colaborador do grupo de gerenciamento" no grupo de gerenciamento pai de destino.
- Função "proprietário", "colaborador" ou "colaborador do grupo de gerenciamento" no grupo de gerenciamento pai existente.

Se o destino ou o grupo de gerenciamento pai existente for o grupo de gerenciamento raiz, os requisitos de permissões não se aplicarão. Como o grupo de gerenciamento raiz é o ponto de aterrissagem padrão para todos os novos grupos de gerenciamento e assinaturas, você não precisa de permissões para mover um item.

Se a função de proprietário na assinatura for herdada do grupo de gerenciamento atual, seus destinos de movimentação serão limitados. Você só pode mover a assinatura para outro grupo de gerenciamento no qual você tem a função de proprietário. Você não pode movê-lo para um grupo de gerenciamento em que você é um colaborador, pois você perderia a propriedade da assinatura. Se você estiver diretamente atribuído à função de proprietário da assinatura (não herdada do grupo de gerenciamento), poderá movê-la para qualquer grupo de gerenciamento no qual você seja um colaborador.

Para ver quais permissões você tem no portal do Azure, selecione o grupo de gerenciamento e, em seguida, selecione **iam**. Para saber mais sobre as funções de RBAC, consulte [gerenciar acesso e permissões com o RBAC](../../role-based-access-control/overview.md).

### <a name="move-subscriptions-in-the-portal"></a>Mover assinaturas no portal

#### <a name="add-an-existing-subscription-to-a-management-group"></a>Adicionar uma assinatura existente a um grupo de gerenciamento

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **todos os serviços** > **grupos de gerenciamento**.

1. Selecione o grupo de gerenciamento que você pretende que seja o pai.

1. Na parte superior da página, selecione **Adicionar assinatura**.

1. Selecione a assinatura na lista com a ID correta.

   ![Assinaturas disponíveis para adicionar a um grupo de gerenciamento](./media/add_context_sub.png)

1. Selecione "salvar".

#### <a name="remove-a-subscription-from-a-management-group"></a>Remover uma assinatura de um grupo de gerenciamento

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **todos os serviços** > **grupos de gerenciamento**.

1. Selecione o grupo de gerenciamento que você está planejando que seja o pai atual.  

1. Selecione a elipse no final da linha da assinatura na lista que você deseja mover.

   ![Mover opção em um grupo de gerenciamento](./media/move_small.png)

1. Selecione **mover**.

1. No menu que é aberto, selecione o **grupo de gerenciamento pai**.

   ![Mover painel para alterar grupo pai](./media/move_small_context.png)

1. Selecione **Guardar**.

### <a name="move-subscriptions-in-powershell"></a>Mover assinaturas no PowerShell

Para mover uma assinatura no PowerShell, use o comando New-AzManagementGroupSubscription.  

```azurepowershell-interactive
New-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

Para remover o link entre o e a assinatura e o grupo de gerenciamento, use o comando Remove-AzManagementGroupSubscription.

```azurepowershell-interactive
Remove-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>Mover assinaturas no CLI do Azure

Para mover uma assinatura na CLI, use o comando adicionar.

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

Para remover a assinatura do grupo de gerenciamento, use o comando Subscription remove.  

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

## <a name="move-management-groups-in-the-hierarchy"></a>Mover grupos de gerenciamento na hierarquia  

Quando você move um grupo de gerenciamento pai, a hierarquia sob esse grupo é movida com ele. Para obter o acesso necessário para mover grupos de gerenciamento, consulte [acesso ao grupo de gerenciamento](index.md#management-group-access).

### <a name="move-management-groups-in-the-portal"></a>Mover grupos de gerenciamento no portal

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **todos os serviços** > **grupos de gerenciamento**.

1. Selecione o grupo de gerenciamento que você pretende que seja o pai.

1. Na parte superior da página, selecione **Adicionar grupo de gerenciamento**.

1. No menu que é aberto, selecione se você deseja um novo ou usar um grupo de gerenciamento existente.

   - A seleção de novo criará um novo grupo de gerenciamento.
   - A seleção de um existente apresentará uma lista suspensa de todos os grupos de gerenciamento que você pode mover para esse grupo de gerenciamento.  

   ![Mover um grupo de gerenciamento para um grupo novo ou existente](./media/add_context_MG.png)

1. Selecione **Guardar**.

### <a name="move-management-groups-in-powershell"></a>Mover grupos de gerenciamento no PowerShell

Use o comando Update-AzManagementGroup no PowerShell para mover um grupo de gerenciamento em um grupo diferente.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName ContosoIT
Update-AzManagementGroup -GroupName 'Contoso' -ParentId $parentGroup.id
```  

### <a name="move-management-groups-in-azure-cli"></a>Mover grupos de gerenciamento no CLI do Azure

Use o comando atualizar para mover um grupo de gerenciamento com CLI do Azure.

```azurecli-interactive
az account management-group update --name 'Contoso' --parent ContosoIT
```

## <a name="audit-management-groups-using-activity-logs"></a>Auditar os grupos de gestão que utilizam registos de atividades

Os grupos de gestão são suportados no [Registo de Atividades do Azure](../../azure-monitor/platform/activity-logs-overview.md). Você pode consultar todos os eventos que acontecem com um grupo de gerenciamento no mesmo local central que outros recursos do Azure.  Por exemplo, pode ver todas as alterações de Atribuições de Funções ou de Atribuição de Política feitas a um grupo de gestão específico.

![Logs de atividade com grupos de gerenciamento](media/al-mg.png)

Quando quiser consultar Grupos de Gestão fora do portal do Azure, o âmbito de destino dos grupos de gestão é semelhante a **"/ providers/Microsoft.Management/managementGroups/{yourMgID}"** .

## <a name="referencing-management-groups-from-other-resource-providers"></a>Referenciando grupos de gerenciamento de outros provedores de recursos

Ao referenciar grupos de gerenciamento de outras ações do provedor de recursos, use o caminho a seguir como o escopo. Esse caminho é usado ao usar o PowerShell, CLI do Azure e APIs REST.  

>"/providers/Microsoft.Management/managementGroups/{yourMgID}"

Um exemplo de como usar esse caminho é ao atribuir uma nova atribuição de função a um grupo de gerenciamento no PowerShell

```azurepowershell-interactive
New-AzRoleAssignment -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

O mesmo caminho de escopo é usado ao recuperar uma definição de política em um grupo de gerenciamento.

```http
GET https://management.azure.com/providers/Microsoft.Management/managementgroups/MyManagementGroup/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming?api-version=2018-05-01
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os grupos de gestão, veja:

- [Criar grupos de gestão para organizar recursos do Azure](create.md)
- [Como alterar, eliminar ou gerir os seus grupos de gestão](manage.md)
- [Rever os grupos de gestão no Módulo de Recursos do Azure PowerShell](/powershell/module/az.resources#resources)
- [Rever os grupos de gestão na API REST](/rest/api/resources/managementgroups)
- [Rever os grupos de gestão na CLI do Azure](/cli/azure/account/management-group)