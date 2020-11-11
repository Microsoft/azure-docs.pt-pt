---
title: Criar políticas programaticamente
description: Este artigo acompanha-o através de políticas programáticas para a Política Azure com Azure CLI, Azure PowerShell e REST API.
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: 2bf2b1864331fd785ecdd70be4af79be01f1e5e0
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491805"
---
# <a name="programmatically-create-policies"></a>Criar políticas programaticamente

Este artigo acompanha-o programáticamente criando e gerindo políticas. As definições de Política Azure impõem diferentes regras e efeitos sobre os seus recursos. A aplicação garante que os recursos se mantêm conformes com os seus padrões corporativos e acordos de nível de serviço.

Para obter informações sobre a conformidade, consulte [obter dados de conformidade.](get-compliance-data.md)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que os seguintes pré-requisitos são cumpridos:

1. Se ainda não o fez, instale o [ARMClient](https://github.com/projectkudu/ARMClient). É uma ferramenta que envia pedidos de HTTP para APIs baseadas no Azure Resource Manager.

1. Atualize o módulo Azure PowerShell para a versão mais recente. Consulte [o módulo Azure PowerShell](/powershell/azure/install-az-ps) para obter informações detalhadas. Para obter mais informações sobre a versão mais recente, consulte [a Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

1. Registe o fornecedor de recursos Azure Policy Insights utilizando o Azure PowerShell para validar que a sua subscrição funciona com o fornecedor de recursos. Para registar um fornecedor de recursos, tem de ter permissão para executar a operação de ação de registo para o fornecedor de recursos. Esta operação está incluída nas funções de Contribuinte e Proprietário. Execute o seguinte comando para registar o fornecedor de recursos:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
   ```

   Para obter mais informações sobre o registo e visualização de fornecedores de recursos, consulte [Fornecedores e Tipos de Recursos.](../../../azure-resource-manager/management/resource-providers-and-types.md)

1. Se ainda não o fez, instale o Azure CLI. Pode obter a versão mais recente no [Install Azure CLI no Windows](/cli/azure/install-azure-cli-windows).

## <a name="create-and-assign-a-policy-definition"></a>Criar e atribuir uma definição de política

O primeiro passo para uma melhor visibilidade dos seus recursos é criar e atribuir políticas sobre os seus recursos. O próximo passo é aprender a criar e atribuir uma política programática. O exemplo das contas de armazenamento de políticas abertas a todas as redes públicas utilizando pedidos powerShell, Azure CLI e HTTP.

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Criar e atribuir uma definição de política com o PowerShell

1. Utilize o seguinte corte JSON para criar um ficheiro JSON com o nome AuditStorageAccounts.jsligado.

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   Para obter mais informações sobre a autoria de uma definição de política, consulte [a Estrutura de Definição de Políticas do Azure.](../concepts/definition-structure.md)

1. Executar o seguinte comando para criar uma definição de política utilizando o AuditStorageAccounts.jsno ficheiro.

   ```azurepowershell-interactive
   New-AzPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
   ```

   O comando cria uma definição de política chamada _Contas de Armazenamento de Auditoria Abertas a Redes Públicas._
   Para obter mais informações sobre outros parâmetros que possa utilizar, consulte [New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition).

   Quando chamado sem parâmetros de localização, `New-AzPolicyDefinition` o predefinição em guardar a definição de política na subscrição selecionada do contexto das sessões. Para guardar a definição para um local diferente, utilize os seguintes parâmetros:

   - **SubscriptionId** - Guarde para uma subscrição diferente. Requer um valor _GUID._
   - **ManagementGroupName** - Guarde para um grupo de gestão. Requer um valor _de corda._

1. Depois de criar a sua definição de política, pode criar uma atribuição de política executando os seguintes comandos:

   ```azurepowershell-interactive
   $rg = Get-AzResourceGroup -Name 'ContosoRG'
   $Policy = Get-AzPolicyDefinition -Name 'AuditStorageAccounts'
   New-AzPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
   ```

   Substitua _a ContosoRG_ pelo nome do seu grupo de recursos pretendido.

   O parâmetro **Scope** sobre `New-AzPolicyAssignment` trabalhos com grupo de gestão, subscrição, grupo de recursos ou um único recurso. O parâmetro usa um caminho de recursos completo, que a propriedade **ResourceId** em `Get-AzResourceGroup` devoluções. O padrão de **Âmbito** para cada recipiente é o seguinte. `{rName}`Substitua, , e pelo seu nome de `{rgName}` `{subId}` `{mgName}` recurso, nome de grupo de recursos, ID de subscrição e nome de grupo de gestão, respectivamente.
   `{rType}` seria substituído pelo tipo de **recurso** do recurso, como por exemplo para `Microsoft.Compute/virtualMachines` um VM.

   - Recurso - `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Grupo de recursos - `/subscriptions/{subId}/resourceGroups/{rgName}`
   - Assinatura - `/subscriptions/{subId}`
   - Grupo de gestão - `/providers/Microsoft.Management/managementGroups/{mgName}`

Para obter mais informações sobre a gestão das políticas de recursos utilizando o módulo PowerShell do Gestor de Recursos, consulte [Az.Resources](/powershell/module/az.resources/#policies).

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Criar e atribuir uma definição de política usando ARMClient

Utilize o seguinte procedimento para criar uma definição de política.

1. Copie o seguinte corte JSON para criar um ficheiro JSON. Vai ligar para o ficheiro no próximo passo.

   ```json
   "properties": {
       "displayName": "Audit Storage Accounts Open to Public Networks",
       "policyType": "Custom",
       "mode": "Indexed",
       "description": "This policy ensures that storage accounts with exposure to Public Networks are audited.",
       "parameters": {},
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Storage/storageAccounts"
                   },
                   {
                       "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                       "equals": "Allow"
                   }
               ]
           },
           "then": {
               "effect": "audit"
           }
       }
   }
   ```

1. Crie a definição de política utilizando uma das seguintes chamadas:

   ```console
   # For defining a policy in a subscription
   armclient PUT "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2019-09-01" @<path to policy definition JSON file>

   # For defining a policy in a management group
   armclient PUT "/providers/Microsoft.Management/managementgroups/{managementGroupId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2019-09-01" @<path to policy definition JSON file>
   ```

   Substitua o {subscriçãoId} anteriormente pelo ID da sua subscrição ou {managementGroupId} pelo ID do seu grupo de [gestão](../../management-groups/overview.md).

   Para obter mais informações sobre a estrutura da consulta, consulte [Definições de Política do Azure – Criar ou Atualizar](/rest/api/resources/policydefinitions/createorupdate) e [Definições de Política – Criar ou Atualizar Grupo de Gestão](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup)

Utilize o seguinte procedimento para criar uma atribuição de política e atribuir a definição de política ao nível do grupo de recursos.

1. Copie o seguinte corte JSON para criar um ficheiro de atribuição de políticas JSON. Substitua a informação de exemplo em &lt; &gt; símbolos com os seus próprios valores.

   ```json
   {
       "properties": {
           "description": "This policy assignment makes sure that storage accounts with exposure to Public Networks are audited.",
           "displayName": "Audit Storage Accounts Open to Public Networks Assignment",
           "parameters": {},
           "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks",
           "scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>"
       }
   }
   ```

1. Crie a atribuição de política utilizando a seguinte chamada:

   ```console
   armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2019-09-01" @<path to Assignment JSON file>
   ```

   Substitua a informação de exemplo em &lt; &gt; símbolos com os seus próprios valores.

   Para obter mais informações sobre a realização de chamadas HTTP para a API REST, consulte [Azure REST API Resources](/rest/api/resources/).

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Criar e atribuir uma definição de política com o Azure CLI

Para criar uma definição de política, utilize o seguinte procedimento:

1. Copie o seguinte corte JSON para criar um ficheiro de atribuição de políticas JSON.

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   Para obter mais informações sobre a autoria de uma definição de política, consulte [a Estrutura de Definição de Políticas do Azure.](../concepts/definition-structure.md)

1. Executar o seguinte comando para criar uma definição de política:

   ```azurecli-interactive
   az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
   ```

   O comando cria uma definição de política chamada _Contas de Armazenamento de Auditoria Abertas a Redes Públicas._
   Para obter mais informações sobre outros parâmetros que possa utilizar, consulte [a definição de política az create](/cli/azure/policy/definition#az-policy-definition-create).

   Quando chamado sem parâmetros de localização, `az policy definition creation` o predefinição em guardar a definição de política na subscrição selecionada do contexto das sessões. Para guardar a definição para um local diferente, utilize os seguintes parâmetros:

   - **subscrição** - Guarde para uma subscrição diferente. Requer um valor _GUID_ para o ID de subscrição ou um valor _de cadeia_ para o nome de subscrição.
   - **grupo de gestão** - Salvo para um grupo de gestão. Requer um valor _de corda._

1. Utilize o seguinte comando para criar uma atribuição de política. Substitua a informação de exemplo em &lt; &gt; símbolos com os seus próprios valores.

   ```azurecli-interactive
   az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
   ```

   O parâmetro **de âmbito** em `az policy assignment create` funcionamento com grupo de gestão, subscrição, grupo de recursos ou um único recurso. O parâmetro usa um caminho de recursos completo. O padrão de **mira** para cada recipiente é o seguinte. `{rName}`Substitua, , e pelo seu nome de `{rgName}` `{subId}` `{mgName}` recurso, nome de grupo de recursos, ID de subscrição e nome de grupo de gestão, respectivamente. `{rType}` seria substituído pelo tipo de **recurso** do recurso, como por exemplo para `Microsoft.Compute/virtualMachines` um VM.

   - Recurso - `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Grupo de recursos - `/subscriptions/{subID}/resourceGroups/{rgName}`
   - Assinatura - `/subscriptions/{subID}`
   - Grupo de gestão - `/providers/Microsoft.Management/managementGroups/{mgName}`

Pode obter o ID de Definição de Política Azure utilizando o PowerShell com o seguinte comando:

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

O ID de definição de política para a definição de política que criou deve assemelhar-se ao seguinte exemplo:

```output
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Para obter mais informações sobre como gerir as políticas de recursos com o Azure [CLI, consulte as Políticas de Recursos do CLI Azure.](/cli/azure/policy)

## <a name="next-steps"></a>Passos seguintes

Reveja os seguintes artigos para obter mais informações sobre os comandos e consultas neste artigo.

- [Recursos AZURE REST API](/rest/api/resources/)
- [Módulos Azure PowerShell](/powershell/module/az.resources/#policies)
- [Comandos políticos do Azure CLI](/cli/azure/policy)
- [Azure Policy Insights fornecedor de recursos REST API referência](/rest/api/policy-insights)
- [Organizar os recursos com os grupos de gestão do Azure](../../management-groups/overview.md).
