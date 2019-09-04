---
title: Identificar recursos em não conformidade
description: Nesta explicação de procedimento orienta-o através de remediação de recursos que são incompatíveis para políticas no Azure Policy.
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 40658412f19c444cfa06f5663f567a78453c7e9a
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241144"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Remediar recursos não compatíveis com o Azure Policy

Recursos que são incompatíveis para um **deployIfNotExists** política pode ser colocada num Estado de conformidade por meio **remediação**. A correção é realizada instruindo Azure Policy a executar o efeito **deployIfNotExists** da política atribuída em seus recursos existentes. Este artigo mostra as etapas necessárias para entender e realizar a correção com o Azure Policy.

## <a name="how-remediation-security-works"></a>Como funciona a segurança de remediação

Quando Azure Policy executa o modelo na definição de política **deployIfNotExists** , ele faz isso usando uma [identidade gerenciada](../../../active-directory/managed-identities-azure-resources/overview.md).
Azure Policy cria uma identidade gerenciada para cada atribuição, mas deve ter detalhes sobre quais funções devem ser concedidas à identidade gerenciada. Se a identidade gerida está em falta funções, este erro é apresentado durante a atribuição de política ou uma iniciativa. Ao usar o portal, Azure Policy concederá automaticamente à identidade gerenciada as funções listadas assim que a atribuição for iniciada.

![Identidade gerida - função em falta](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> Se um recurso modificado por **deployIfNotExists** está fora do âmbito de atribuição de política ou o modelo de propriedades de acessos em recursos fora do âmbito de atribuição de política, a identidade gerida da atribuição tem de ser [manualmente concedido acesso](#manually-configure-the-managed-identity) ou a implementação de atualização irá falhar.

## <a name="configure-policy-definition"></a>Configurar a definição de política

A primeira etapa é definir as funções que **deployIfNotExists** precisa na definição de política para implementar o conteúdo do seu modelo incluído com êxito. Sob o **detalhes** propriedade, adicionar um **roleDefinitionIds** propriedade. Esta propriedade é uma matriz de cadeias de caracteres que correspondam a funções no seu ambiente. Para obter um exemplo completo, consulte a [deployIfNotExists exemplo](../concepts/effects.md#deployifnotexists-example).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

**roleDefinitionIds** utiliza o identificador de recurso completo e não tira o curto **roleName** da função. Para obter o ID para a função de "Contribuinte" no seu ambiente, utilize o seguinte código:

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>Configurar manualmente a identidade gerida

Ao criar uma atribuição usando o portal, o Azure Policy gera a identidade gerenciada e concede a ela as funções definidas em **roleDefinitionIds**. Nas seguintes condições, passos para criar a identidade gerida e atribua-lhe permissões devem ser feitos manualmente:

- Ao utilizar o SDK (por exemplo, o Azure PowerShell)
- Quando um recurso fora do âmbito de atribuição é modificado pelo modelo
- Quando um recurso fora do âmbito de atribuição é lido pelo modelo

> [!NOTE]
> Azure PowerShell e .NET são os SDKs únicos que suportam atualmente esta capacidade.

### <a name="create-managed-identity-with-powershell"></a>Criar a identidade gerida com o PowerShell

Para criar uma identidade gerida durante a atribuição da política **localização** tem de ser definido e **AssignIdentity** utilizado. O exemplo seguinte obtém a definição da política incorporada **encriptação de dados transparente de implementar o SQL DB**, define o grupo de recursos de destino e, em seguida, cria a atribuição.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

O `$assignment` variável agora contém o ID de principal de a identidade gerida, juntamente com os valores padrão retornado ao criar uma atribuição de política. Pode ser acedido através de `$assignment.Identity.PrincipalId`.

### <a name="grant-defined-roles-with-powershell"></a>Concessão definido funções com o PowerShell

Podem ser concedida as funções necessárias, a nova identidade gerida tem de concluir a replicação através do Azure Active Directory. Após a conclusão da replicação, o exemplo a seguir itera a definição de `$policyDef` política em para o **roleDefinitionIds** e usa [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) para conceder à nova identidade gerenciada as funções.

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

### <a name="grant-defined-roles-through-portal"></a>Concessão definido funções através do portal

Existem duas formas de conceder as funções definidas com o portal, utilizando a identidade gerida de uma atribuição **controlo de acesso (IAM)** ou a atribuição de política ou iniciativa de edição e clicando em **guardar**.

Para adicionar uma função a identidade gerida da atribuição, siga estes passos:

1. Inicie o serviço Azure Policy no portal do Azure ao clicar em **Todos os serviços** e, em seguida, ao pesquisar e selecionar **Policy**.

1. Selecione **Atribuições** no lado esquerdo da página Azure Policy.

1. Localize a atribuição de que tem uma identidade gerida e clique no nome.

1. Encontrar o **ID de atribuição** propriedade na página de edição. O ID de atribuição será algo como:

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   O nome da identidade gerida é a última parte do ID de recurso de atribuição, o que é `2802056bfc094dfb95d4d7a5` neste exemplo. Copie esta parte do ID de recurso de atribuição.

1. Navegue para o recurso ou recursos contentor principal (grupo de recursos, subscrição, grupo de gestão) que tem a definição de função adicionada manualmente.

1. Clique nas **controlo de acesso (IAM)** ligar na página de recursos e clique em **+ adicionar atribuição de função** na parte superior da página de controle de acesso.

1. Selecione a função adequada que corresponda a um **roleDefinitionIds** da definição de política.
   Deixe **atribuir acesso a** predefinido de 'Utilizador do Azure AD, grupo ou aplicação'. Na **selecione** caixa, cole ou escreva a parte do ID do recurso de atribuição localizado anteriormente. Uma vez concluída a pesquisa, clique com o objeto com o mesmo nome para selecionar o ID e clique em **guardar**.

## <a name="create-a-remediation-task"></a>Criar uma tarefa de remediação

### <a name="create-a-remediation-task-through-portal"></a>Criar uma tarefa de correção por meio do portal

Durante a avaliação, a atribuição de política com **deployIfNotExists** efeito determina se existem recursos incompatíveis. Quando são encontrados recursos não compatíveis, os detalhes fornecidos no **remediação** página. Juntamente com a lista de políticas que tenham recursos em não conformidade é a opção para acionar uma **tarefas de remediação**. Esta opção é o que cria uma implementação a partir da **deployIfNotExists** modelo.

Para criar uma **tarefas de remediação**, siga estes passos:

1. Inicie o serviço Azure Policy no portal do Azure ao clicar em **Todos os serviços** e, em seguida, ao pesquisar e selecionar **Policy**.

   ![Pesquisar política em todos os serviços](../media/remediate-resources/search-policy.png)

1. Selecione **remediação** no lado esquerdo da página política do Azure.

   ![Selecionar correção na página de política](../media/remediate-resources/select-remediation.png)

1. Todos os **deployIfNotExists** atribuições de política com recursos não compatíveis estão incluídas no **políticas para remediar** separador e os dados de tabela. Clique numa política com recursos que são incompatíveis. O **nova tarefa de remediação** é aberta a página.

   > [!NOTE]
   > Uma forma alternativa para abrir o **tarefas de remediação** página é a localização e clique na política da **conformidade** página, em seguida, clique no **criar tarefa de remediação** botão.

1. Na **nova tarefa de remediação** página, filtrar os recursos necessários para remediar utilizando o **âmbito** reticências e escolha os recursos subordinados de onde a política é atribuída (incluindo para baixo para o recurso individual objetos). Além disso, utilizar o **localizações** pendente para filtrar ainda mais os recursos. Apenas os recursos listados na tabela serão remediados.

   ![Corrigir-selecione os recursos a serem corrigidos](../media/remediate-resources/select-resources.png)

1. Iniciar a tarefa de remediação assim que os recursos foram filtrados clicando **Remedeie**. A página de política de conformidade será aberta para o **tarefas de atualização** separador para mostrar o estado do progresso de tarefas.

   ![Corrigir-progresso das tarefas de correção](../media/remediate-resources/task-progress.png)

1. Clique nas **tarefas de remediação** da página de conformidade da política para obter detalhes sobre o progresso. A filtragem utilizado para a tarefa é apresentada juntamente com uma lista dos recursos a ser corrigida.

1. Partir do **tarefas de remediação** página, clique com o botão direito num recurso para ver a implementação da tarefa de atualização ou o recurso. No final da linha, clique em **eventos relacionados com** para ver os detalhes, como uma mensagem de erro.

   ![Remediar - menu de contexto de tarefa de recursos](../media/remediate-resources/resource-task-context-menu.png)

Recursos implementados através de um **tarefas de remediação** são adicionados à **recursos implementados** separador na página de conformidade de política.

### <a name="create-a-remediation-task-through-azure-cli"></a>Criar uma tarefa de correção por meio do CLI do Azure

Para criar uma **tarefa de correção** com CLI do Azure, use `az policy remediation` os comandos. Substitua `{subscriptionId}` pela sua ID de assinatura `{myAssignmentId}` e pela sua ID de atribuição de política **deployIfNotExists** .

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Para outros comandos de correção e exemplos, consulte os comandos [AZ Policy](/cli/azure/policy/remediation) remediation.

### <a name="create-a-remediation-task-through-azure-powershell"></a>Criar uma tarefa de correção por meio do Azure PowerShell

Para criar uma **tarefa de correção** com Azure PowerShell, use `Start-AzPolicyRemediation` os comandos. Substitua `{subscriptionId}` pela sua ID de assinatura `{myAssignmentId}` e pela sua ID de atribuição de política **deployIfNotExists** .

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Para obter outros cmdlets de correção e exemplos, consulte o módulo [AZ. PolicyInsights](/powershell/module/az.policyinsights/#policy_insights) .

## <a name="next-steps"></a>Passos Seguintes

- Examine exemplos em [exemplos de Azure Policy](../samples/index.md).
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).
- Entenda como [criar políticas](programmatically-create.md)programaticamente.
- Saiba como [obter dados de conformidade](getting-compliance-data.md).
- Examine o que é um grupo de gerenciamento e [Organize seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).
