---
title: Identificar recursos em não conformidade
description: Este guia acompanha-o através da reparação de recursos que não estão em conformidade com as políticas da Política Azure.
ms.date: 10/05/2020
ms.topic: how-to
ms.openlocfilehash: 76d2e57c1b5df965c81c88506ff2c2f70b2cb1f8
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876333"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Remediar recursos não conformes com a Política Azure

Os recursos que não sejam compatíveis com uma **política de implantaçãoIfNotExists** ou **modificar** políticas podem ser colocados num estado compatível através da **remediação**. A reparação é realizada instruindo a Azure Policy a executar o efeito **deployIfNotExists** ou as **operações modificadas** da política atribuída nos seus recursos existentes, quer essa atribuição seja para um grupo de gestão, uma subscrição, um grupo de recursos ou um recurso individual. Este artigo mostra os passos necessários para compreender e realizar a remediação com a Política Azure.

## <a name="how-remediation-security-works"></a>Como funciona a segurança da reparação

Quando a Política Azure executa o modelo na definição de política **doIfNotExists,** fá-lo utilizando uma [identidade gerida](../../../active-directory/managed-identities-azure-resources/overview.md).
A Azure Policy cria uma identidade gerida para cada atribuição, mas deve ter detalhes sobre quais as funções a conceder a identidade gerida. Se faltar a identidade gerida, é apresentado um erro durante a atribuição da apólice ou de uma iniciativa. Ao utilizar o portal, a Azure Policy concede automaticamente à identidade gerida as funções listadas assim que a atribuição começa. Ao utilizar o SDK, as funções devem ser concedidas manualmente à identidade gerida. A _localização_ da identidade gerida não afeta o seu funcionamento com a Política Azure.

:::image type="content" source="../media/remediate-resources/missing-role.png" alt-text="Screenshot de uma política de implementaçãoIfNotExists que está faltando uma permissão definida sobre a identidade gerida." border="false":::

> [!IMPORTANT]
> Nos seguintes cenários, a identidade gerida da atribuição deve ser [autorizada manualmente](#manually-configure-the-managed-identity) a ter acesso ou a implementação de reparação falhará:
>
> - Se a atribuição for criada através da SDK
> - Se um recurso modificado por **implementarIfNotExists** ou **modificar** está fora do âmbito da atribuição de políticas
> - Se o modelo acessa propriedades em recursos fora do âmbito da atribuição de políticas

## <a name="configure-policy-definition"></a>Configurar definição de política

O primeiro passo é definir as funções que **implementam osIfNotExists** e **modificam** as necessidades na definição de política para implementar com sucesso o conteúdo do seu modelo incluído. Sob a propriedade **details,** adicione uma propriedade **RoleDefinitionIds.** Esta propriedade é uma variedade de cordas que combinam com papéis no seu ambiente. Para um exemplo completo, consulte o [exemplo de implementarIfNotExists](../concepts/effects.md#deployifnotexists-example) ou os [exemplos de modificação](../concepts/effects.md#modify-examples).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

A **propriedade RoleDefinitionIds** usa o identificador de recursos completo e não assume o **papel curtoName** do papel. Para obter o ID para o papel de 'Contribuinte' no seu ambiente, utilize o seguinte código:

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>Configurar manualmente a identidade gerida

Ao criar uma atribuição utilizando o portal, a Política Azure gera a identidade gerida e concede-lhe as funções definidas no **papelDefinitionIds**. Nas seguintes condições, as medidas para criar a identidade gerida e atribuir-lhe permissões devem ser feitas manualmente:

- Ao utilizar o SDK (como a Azure PowerShell)
- Quando um recurso fora do âmbito de atribuição é modificado pelo modelo
- Quando um recurso fora do âmbito de atribuição é lido pelo modelo

### <a name="create-managed-identity-with-powershell"></a>Criar identidade gerida com PowerShell

Para criar uma identidade gerida durante a atribuição da apólice, **a Localização** deve ser definida e **a Entidade Designadadida** utilizada. O exemplo a seguir obtém a definição da política incorporada **Implementar encriptação de dados transparentes SQL DB,** define o grupo de recursos-alvo e, em seguida, cria a atribuição.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

A `$assignment` variável agora contém o ID principal da identidade gerida juntamente com os valores padrão devolvidos ao criar uma atribuição de política. Pode ser acedido através `$assignment.Identity.PrincipalId` de.

### <a name="grant-defined-roles-with-powershell"></a>Conceder funções definidas com PowerShell

A nova identidade gerida deve completar a replicação através do Azure Ative Directory antes de poder ser concedidas as funções necessárias. Uma vez concluída a replicação, o exemplo a seguir imisita a definição de política `$policyDef` para o **papelDefinitionIds** e usa [a Assinatura New-AzRole](/powershell/module/az.resources/new-azroleassignment) para conceder à nova identidade gerida as funções.

```azurepowershell-interactive
# Use the $policyDef to get to the roleDefinitionIds array
$roleDefinitionIds = $policyDef.Properties.policyRule.then.details.roleDefinitionIds

if ($roleDefinitionIds.Count -gt 0)
{
    $roleDefinitionIds | ForEach-Object {
        $roleDefId = $_.Split("/") | Select-Object -Last 1
        New-AzRoleAssignment -Scope $resourceGroup.ResourceId -ObjectId $assignment.Identity.PrincipalId -RoleDefinitionId $roleDefId
    }
}
```

### <a name="grant-defined-roles-through-portal"></a>Conceder funções definidas através do portal

Existem duas formas de conceder à identidade gerida de uma atribuição as funções definidas utilizando o portal, utilizando o **controlo de acesso (IAM)** ou editando a atribuição de política ou iniciativa e selecionando **o Save.**

Para adicionar um papel à identidade gerida da atribuição, siga estes passos:

1. Lançar o serviço Azure Policy no portal Azure selecionando **todos os serviços,** procurando e selecionando **a Política**.

1. Selecione **Atribuições** no lado esquerdo da página Azure Policy.

1. Localize a atribuição que tem uma identidade gerida e selecione o nome.

1. Encontre a propriedade **de ID de atribuição** na página de edição. A id de atribuição será algo como:

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   O nome da identidade gerida é a última parte do ID do recurso de atribuição, que está `2802056bfc094dfb95d4d7a5` neste exemplo. Copie esta parte do ID do recurso de atribuição.

1. Navegue para o recurso ou para o contentor-mãe de recursos (grupo de recursos, subscrição, grupo de gestão) que necessita da definição de função adicionada manualmente.

1. Selecione o link **access control (IAM)** na página de recursos e, em seguida, selecione + Adicionar a atribuição de **funções** no topo da página de controlo de acesso.

1. Selecione o papel adequado que corresponda a um **papelDefinitionIds** da definição de política.
   Deixar **Atribuir acesso ao** conjunto ao padrão de 'Utilizador, grupo ou aplicação Azure AD'. Na caixa **Select,** cole ou digite a parte do ID do recurso de atribuição localizado anteriormente. Assim que a pesquisa estiver concluída, selecione o objeto com o mesmo nome para selecionar ID e selecione **Guardar**.

## <a name="create-a-remediation-task"></a>Criar uma tarefa de remediação

### <a name="create-a-remediation-task-through-portal"></a>Criar uma tarefa de remediação através do portal

Durante a avaliação, a atribuição de políticas com **os implementosIfNotExists** ou **modificar** efeitos determina se existem recursos não conformes. Quando os recursos não conformes são encontrados, os detalhes são fornecidos na página **de Remediação.** Juntamente com a lista de políticas que têm recursos não conformes é a opção de desencadear uma **tarefa de remediação**. Esta opção é o que cria uma implementação a partir do modelo **de implementaçãoIfNotExists** ou as operações **de modificação.**

Para criar uma **tarefa de remediação,** siga estes passos:

1. Lançar o serviço Azure Policy no portal Azure selecionando **todos os serviços,** procurando e selecionando **a Política**.

   :::image type="content" source="../media/remediate-resources/search-policy.png" alt-text="Screenshot de uma política de implementaçãoIfNotExists que está faltando uma permissão definida sobre a identidade gerida." border="false":::

1. Selecione **Remediação** no lado esquerdo da página Política Azure.

   :::image type="content" source="../media/remediate-resources/select-remediation.png" alt-text="Screenshot de uma política de implementaçãoIfNotExists que está faltando uma permissão definida sobre a identidade gerida." border="false":::

1. Todos os **implantadosIfNotExists** e **modificam** atribuições de políticas com recursos não conformes estão incluídos nas **Políticas para remediar** o separador e a tabela de dados. Selecione uma política com recursos que não sejam compatíveis. Abre a nova página **de tarefas de remediação.**

   > [!NOTE]
   > Uma forma alternativa de abrir a página **de tarefas de remediação** é encontrar e selecionar a política a partir da página **'Conformidade'** e, em seguida, selecionar o botão **Tarefa de Remediação.**

1. Na nova página de **tarefas de remediação,** filtrar os recursos para remediar utilizando as elipses **scope** para escolher recursos infantis de onde a política é atribuída (incluindo até os objetos de recursos individuais). Além disso, utilize as **localizações** para filtrar ainda mais os recursos. Apenas os recursos listados na tabela serão remediados.

   :::image type="content" source="../media/remediate-resources/select-resources.png" alt-text="Screenshot de uma política de implementaçãoIfNotExists que está faltando uma permissão definida sobre a identidade gerida." border="false":::

1. Inicie a tarefa de remediação uma vez filtrados os recursos selecionando **Remediate**. A página de conformidade da política abre-se ao **separador tarefas de Remediação** para mostrar o estado das tarefas a progredir. As implementações criadas pela tarefa de reparação começam imediatamente.

   :::image type="content" source="../media/remediate-resources/task-progress.png" alt-text="Screenshot de uma política de implementaçãoIfNotExists que está faltando uma permissão definida sobre a identidade gerida." border="false":::

1. Selecione sobre a tarefa de **remediação** na página de conformidade da política para obter detalhes sobre o progresso. A filtragem utilizada para a tarefa é mostrada juntamente com uma lista dos recursos que estão a ser remediados.

1. A partir da página **de tarefas de Remediação,** clique com o botão direito num recurso para visualizar a implementação da tarefa de remediação ou o recurso. No final da linha, selecione em **eventos relacionados** para ver detalhes como uma mensagem de erro.

   :::image type="content" source="../media/remediate-resources/resource-task-context-menu.png" alt-text="Screenshot de uma política de implementaçãoIfNotExists que está faltando uma permissão definida sobre a identidade gerida." border="false":::

Os recursos utilizados através de uma **tarefa de reparação** são adicionados ao separador **Recursos Implantados** na página de conformidade da política.

### <a name="create-a-remediation-task-through-azure-cli"></a>Criar uma tarefa de remediação através do Azure CLI

Para criar uma **tarefa de reparação** com o Azure CLI, utilize os `az policy remediation` comandos. `{subscriptionId}`Substitua-se pelo seu ID de subscrição e `{myAssignmentId}` pelo seu **ID de implementaçãoIfNotExists** ou **modifique** o ID de atribuição de políticas.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Para outros comandos e exemplos de reparação, consulte os comandos [de remediação da política az.](/cli/azure/policy/remediation)

### <a name="create-a-remediation-task-through-azure-powershell"></a>Criar uma tarefa de remediação através do Azure PowerShell

Para criar uma **tarefa de remediação** com a Azure PowerShell, utilize os `Start-AzPolicyRemediation` comandos. `{subscriptionId}`Substitua-se pelo seu ID de subscrição e `{myAssignmentId}` pelo seu **ID de implementaçãoIfNotExists** ou **modifique** o ID de atribuição de políticas.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Para outros cmdlets e exemplos de remediação, consulte o módulo [Az.PolicyInsights.](/powershell/module/az.policyinsights/#policy_insights)

### <a name="create-a-remediation-task-during-policy-assignment-in-the-azure-portal"></a>Criar uma tarefa de remediação durante a atribuição de políticas no portal Azure

Uma forma simplificada de criar uma tarefa de reparação é fazê-lo a partir do portal Azure durante a atribuição de políticas. Se a definição de política a atribuir for um **implementadorIfNotExists** ou um efeito **Modificar,** o assistente no **separador Remediação** oferece uma opção _de tarefa de remediação._ Se esta opção for selecionada, é criada uma tarefa de reparação ao mesmo tempo que a atribuição de políticas.

## <a name="next-steps"></a>Passos seguintes

- Rever exemplos nas [amostras da Azure Policy](../samples/index.md).
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).
- Entenda como [criar políticas programáticas.](programmatically-create.md)
- Saiba como [obter dados de conformidade.](get-compliance-data.md)
- Reveja o que é um grupo de gestão com [Organizar os seus recursos com grupos de gestão Azure.](../../management-groups/overview.md)
