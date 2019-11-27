---
title: Identificar recursos em não conformidade
description: Este guia orienta você pela correção de recursos que não são compatíveis com as políticas no Azure Policy.
ms.date: 09/09/2019
ms.topic: conceptual
ms.openlocfilehash: 8f1d263286a7504e7a8234ebd944bbbee69c5303
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74267365"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Remediar recursos não compatíveis com o Azure Policy

Os recursos que não são compatíveis com uma política **deployIfNotExists** ou **Modify** podem ser colocados em um estado compatível por meio da **correção**. A correção é realizada instruindo Azure Policy a executar o efeito **deployIfNotExists** ou as **operações** de marca da política atribuída em seus recursos existentes. Este artigo mostra as etapas necessárias para entender e realizar a correção com o Azure Policy.

## <a name="how-remediation-security-works"></a>Como funciona a segurança de remediação

Quando Azure Policy executa o modelo na definição de política **deployIfNotExists** , ele faz isso usando uma [identidade gerenciada](../../../active-directory/managed-identities-azure-resources/overview.md).
Azure Policy cria uma identidade gerenciada para cada atribuição, mas deve ter detalhes sobre quais funções devem ser concedidas à identidade gerenciada. Se a identidade gerida está em falta funções, este erro é apresentado durante a atribuição de política ou uma iniciativa. Ao usar o portal, Azure Policy concederá automaticamente à identidade gerenciada as funções listadas assim que a atribuição for iniciada.

![Identidade gerida - função em falta](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> Se um recurso modificado por **deployIfNotExists** ou **Modify** estiver fora do escopo da atribuição de política ou o modelo acessar propriedades em recursos fora do escopo da atribuição de política, a identidade gerenciada da atribuição deverá ser [concedida ao acesso manualmente](#manually-configure-the-managed-identity) ou a implantação de correção falhará.

## <a name="configure-policy-definition"></a>Configurar a definição de política

A primeira etapa é definir as funções que **deployIfNotExists** e **Modificar** precisam na definição de política para implantar com êxito o conteúdo do modelo incluído. Na propriedade **Details** , adicione uma propriedade **roleDefinitionIds** . Esta propriedade é uma matriz de cadeias de caracteres que correspondam a funções no seu ambiente. Para obter um exemplo completo, consulte o [exemplo de deployIfNotExists](../concepts/effects.md#deployifnotexists-example) ou os exemplos de [Modificar](../concepts/effects.md#modify-examples).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

A propriedade **roleDefinitionIds** usa o identificador de recurso completo e não assume a **roleName** curta da função. Para obter o ID para a função de "Contribuinte" no seu ambiente, utilize o seguinte código:

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

Para criar uma identidade gerenciada durante a atribuição da política, o **local** deve ser definido e **AssignIdentity** usado. O exemplo a seguir obtém a definição da política interna implantar a **Transparent Data Encryption do banco de dados SQL**, define o grupo de recursos de destino e, em seguida, cria a atribuição.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

A variável `$assignment` agora contém a ID da entidade de segurança gerenciada junto com os valores padrão retornados ao criar uma atribuição de política. Ele pode ser acessado por meio de `$assignment.Identity.PrincipalId`.

### <a name="grant-defined-roles-with-powershell"></a>Concessão definido funções com o PowerShell

Podem ser concedida as funções necessárias, a nova identidade gerida tem de concluir a replicação através do Azure Active Directory. Após a conclusão da replicação, o exemplo a seguir itera a definição de política em `$policyDef` para o **roleDefinitionIds** e usa [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) para conceder à nova identidade gerenciada as funções.

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

Há duas maneiras de conceder à identidade gerenciada de uma atribuição as funções definidas usando o portal, usando o **controle de acesso (iam)** ou editando a atribuição de política ou iniciativa e clicando em **salvar**.

Para adicionar uma função a identidade gerida da atribuição, siga estes passos:

1. Inicie o serviço Azure Policy no portal do Azure ao clicar em **Todos os serviços** e, em seguida, ao pesquisar e selecionar **Policy**.

1. Selecione **Atribuições** no lado esquerdo da página Azure Policy.

1. Localize a atribuição de que tem uma identidade gerida e clique no nome.

1. Localize a propriedade **ID de atribuição** na página Editar. O ID de atribuição será algo como:

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   O nome da identidade gerenciada é a última parte da ID de recurso de atribuição, que é `2802056bfc094dfb95d4d7a5` neste exemplo. Copie esta parte do ID de recurso de atribuição.

1. Navegue para o recurso ou recursos contentor principal (grupo de recursos, subscrição, grupo de gestão) que tem a definição de função adicionada manualmente.

1. Clique no link **controle de acesso (iam)** na página recursos e clique em **+ Adicionar atribuição de função** na parte superior da página controle de acesso.

1. Selecione a função apropriada que corresponde a um **roleDefinitionIds** da definição de política.
   Deixe **atribuir acesso para** definir como o padrão de ' usuário, grupo ou aplicativo do Azure AD. Na caixa **selecionar** , Cole ou digite a parte da ID de recurso de atribuição localizada anteriormente. Quando a pesquisa for concluída, clique no objeto com o mesmo nome para selecionar ID e clique em **salvar**.

## <a name="create-a-remediation-task"></a>Criar uma tarefa de remediação

### <a name="create-a-remediation-task-through-portal"></a>Criar uma tarefa de correção por meio do portal

Durante a avaliação, a atribuição de política com efeitos **deployIfNotExists** ou **Modify** determina se há recursos sem conformidade. Quando são encontrados recursos sem conformidade, os detalhes são fornecidos na página **correção** . Junto com a lista de políticas que têm recursos sem conformidade há a opção de disparar uma **tarefa de correção**. Essa opção é o que cria uma implantação do modelo **deployIfNotExists** ou das operações **Modify** .

Para criar uma **tarefa de correção**, siga estas etapas:

1. Inicie o serviço Azure Policy no portal do Azure ao clicar em **Todos os serviços** e, em seguida, ao pesquisar e selecionar **Policy**.

   ![Pesquisar política em todos os serviços](../media/remediate-resources/search-policy.png)

1. Selecione **correção** no lado esquerdo da página Azure Policy.

   ![Selecionar correção na página de política](../media/remediate-resources/select-remediation.png)

1. Todos os **deployIfNotExists** e **Modificar** atribuições de política com recursos sem conformidade estão incluídos nas **políticas para corrigir a** guia e a tabela de dados. Clique numa política com recursos que são incompatíveis. A página **nova tarefa de correção** é aberta.

   > [!NOTE]
   > Uma maneira alternativa de abrir a página **tarefa de correção** é localizar e clicar na política na página **conformidade** e clicar no botão **criar tarefa de correção** .

1. Na página **nova tarefa de correção** , filtre os recursos para corrigir usando as reticências do **escopo** para selecionar recursos filho de onde a política é atribuída (incluindo os objetos de recurso individuais). Além disso, use a lista suspensa **locais** para filtrar ainda mais os recursos. Apenas os recursos listados na tabela serão remediados.

   ![Corrigir-selecione os recursos a serem corrigidos](../media/remediate-resources/select-resources.png)

1. Inicie a tarefa de correção depois que os recursos tiverem sido filtrados clicando em **corrigir**. A página conformidade da política será aberta na guia **tarefas de correção** para mostrar o estado do progresso das tarefas.

   ![Corrigir-progresso das tarefas de correção](../media/remediate-resources/task-progress.png)

1. Clique na **tarefa de correção** na página conformidade da política para obter detalhes sobre o progresso. A filtragem utilizado para a tarefa é apresentada juntamente com uma lista dos recursos a ser corrigida.

1. Na página **tarefa de correção** , clique com o botão direito do mouse em um recurso para exibir a implantação da tarefa de correção ou o recurso. No final da linha, clique em **eventos relacionados** para ver detalhes como uma mensagem de erro.

   ![Remediar - menu de contexto de tarefa de recursos](../media/remediate-resources/resource-task-context-menu.png)

Os recursos implantados por meio de uma **tarefa de correção** são adicionados à guia **recursos implantados** na página conformidade da política.

### <a name="create-a-remediation-task-through-azure-cli"></a>Criar uma tarefa de correção por meio do CLI do Azure

Para criar uma **tarefa de correção** com CLI do Azure, use os comandos `az policy remediation`. Substitua `{subscriptionId}` pela sua ID de assinatura e `{myAssignmentId}` com sua ID de atribuição de política **deployIfNotExists** ou **Modify** .

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Para outros comandos de correção e exemplos, consulte os comandos [AZ Policy remediation](/cli/azure/policy/remediation) .

### <a name="create-a-remediation-task-through-azure-powershell"></a>Criar uma tarefa de correção por meio do Azure PowerShell

Para criar uma **tarefa de correção** com Azure PowerShell, use os comandos `Start-AzPolicyRemediation`. Substitua `{subscriptionId}` pela sua ID de assinatura e `{myAssignmentId}` com sua ID de atribuição de política **deployIfNotExists** ou **Modify** .

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Para obter outros cmdlets de correção e exemplos, consulte o módulo [AZ. PolicyInsights](/powershell/module/az.policyinsights/#policy_insights) .

## <a name="next-steps"></a>Passos seguintes

- Examine exemplos em [exemplos de Azure Policy](../samples/index.md).
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).
- Entenda como [criar políticas programaticamente](programmatically-create.md).
- Saiba como [obter dados de conformidade](get-compliance-data.md).
- Examine o que é um grupo de gerenciamento e [Organize seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).
