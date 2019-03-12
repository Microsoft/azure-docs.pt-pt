---
title: Exemplo - cofres de chaves de auditoria para não existem pontos finais de rede virtual
description: Esta definição de política de exemplo audita cofres do Key Vault para detetar instâncias que tenham sem pontos finais de serviço de rede virtual.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/26/2019
ms.author: dacoulte
ms.openlocfilehash: 5c2bb1cfc4fcfe91c5860c229486f0f24b409797
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57540449"
---
# <a name="sample---key-vault-vaults-with-no-virtual-network-endpoints"></a>Exemplo - cofres do Key Vault com não existem pontos de extremidade de rede virtual

Esta política audita para os cofres de Cofre de chaves que não existem pontos finais de rede virtual. Utilize para impor os requisitos de segurança. Para obter mais informações, consulte [pontos finais de serviço de rede virtual no Cofre de chaves](../../../key-vault/key-vault-overview-vnet-service-endpoints.md)

Pode implementar esta política de exemplo com:

- O [portal do Azure](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [CLI do Azure](#azure-cli)
- [API REST](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Política de exemplo

### <a name="policy-definition"></a>Definição de política

A definição da política JSON composta, utilizada pela API REST, os botões"Implementar no Azure" e manualmente no portal.

[!code-json[full](../../../../policy-templates/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.json "KeyVault vnet rules")]

> [!NOTE]
> Se criar manualmente uma política no portal, utilize as opções **properties.parameters** e **properties.policyRule** acima. Junte as duas secções com chavetas `{}` para transformar num JSON válido.

### <a name="policy-rules"></a>Regras da política

O JSON que define as regras da política, utilizado pela CLI do Azure e o Azure PowerShell.

[!code-json[rule](../../../../policy-templates/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Parâmetros de política

Esta definição de política de exemplo não tem parâmetros definidos.

## <a name="azure-portal"></a>Portal do Azure

[![Implementar no Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FKeyVault%2Faudit-keyvault-vnet-rules%2Fazurepolicy.json)
[![Implementar no Azure Gov](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FKeyVault%2Faudit-keyvault-vnet-rules%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Implementar com o Azure PowerShell

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name "audit-keyvault-vnet-rules" -DisplayName "Audit if Key Vault has no virtual network rules" -description "Audits Key Vault vaults if they do not have virtual network service endpoints set up. More information on virtual network service endpoints in Key Vault is available here: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json' -Mode Indexed

# Set the scope to a resource group; may also be a subscription or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'audit-keyvault-vnet-rules-assignment' -DisplayName 'Audit Key Vault Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition
```

### <a name="remove-with-azure-powershell"></a>Remover com o Azure PowerShell

Execute o seguinte comando para remover a definição e a atribuição anterior:

```azurepowershell-interactive
# Remove the Policy Assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the Policy Definition
Remove-AzPolicyDefinition -Id $definition.ResourceId
```

### <a name="azure-powershell-explanation"></a>Explicação do Azure PowerShell

Os scripts de implementação e remoção utilizam os seguintes comandos. Cada comando na tabela seguinte liga à documentação específica do comando:

| Comando | Notas |
|---|---|
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-Azpolicydefinition) | Cria uma nova definição do Azure Policy. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-Azresourcegroup) | Obtém um grupo de recursos único. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) | Cria uma nova atribuição do Azure Policy. Neste exemplo, damos uma definição, mas também pode tomar iniciativa. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-Azpolicyassignment) | Remove uma atribuição do Azure Policy existente. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-Azpolicydefinition) | Remove uma definição do Azure Policy existente. |

## <a name="azure-cli"></a>CLI do Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

### <a name="deploy-with-azure-cli"></a>Implementar com o CLI do Azure

```azurecli-interactive
# Create the Policy Definition (Subscription scope)
definition=$(az policy definition create --name 'audit-keyvault-vnet-rules' --display-name 'Audit if Key Vault has no virtual network rules' --description 'Audits Key Vault vaults if they do not have virtual network service endpoints set up. More information on virtual network service endpoints in Key Vault is available here: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json' --mode Indexed)

# Set the scope to a resource group; may also be a subscription or management group
scope=$(az group show --name 'YourResourceGroup')

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'audit-keyvault-vnet-rules-assignment' --display-name 'Audit Key Vault Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r`)
```

### <a name="remove-with-azure-cli"></a>Remover com a CLI do Azure

Execute o seguinte comando para remover a definição e a atribuição anterior:

```azurecli-interactive
# Remove the Policy Assignment
az policy assignment delete --name `echo $assignment | jq '.name' -r`

# Remove the Policy Definition
az policy definition delete --name `echo $definition | jq '.name' -r`
```

### <a name="azure-cli-explanation"></a>Explicação da CLI do Azure

| Comando | Notas |
|---|---|
| [az policy definition create](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-create) | Cria uma nova definição do Azure Policy. |
| [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show) | Obtém um grupo de recursos único. |
| [az policy assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) | Cria uma nova atribuição do Azure Policy. Neste exemplo, damos uma definição, mas também pode tomar iniciativa. |
| [az policy assignment delete](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-delete) | Remove uma atribuição do Azure Policy existente. |
| [az policy definition delete](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-delete) | Remove uma definição do Azure Policy existente. |

## <a name="rest-api"></a>API REST

Existem várias ferramentas que podem ser utilizadas para interagir com a API REST do Gestor de Recursos, como o [ARMClient](https://github.com/projectkudu/ARMClient) ou o PowerShell.

### <a name="deploy-with-rest-api"></a>Implementar com API REST

- Crie a Definição de Política (âmbito da Subscrição). Utilize o JSON [definição de política](#policy-definition) para o Corpo do Pedido.

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules?api-version=2018-05-01
  ```

- Criar Atribuição de Política (âmbito do Grupo de Recursos)

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/audit-keyvault-vnet-rules-assignment?api-version=2018-05-01
  ```

  Utilize o seguinte exemplo do JSON para o Corpo do Pedido:

  ```json
  {
      "properties": {
          "displayName": "Audit Key Vault Assignment",
          "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules"
      }
  }
  ```

### <a name="remove-with-rest-api"></a>Remover com a API REST

- Remover a Atribuição de Política

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/audit-keyvault-vnet-rules-assignment?api-version=2018-05-01
  ```

- Remover a Definição de Política

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules?api-version=2018-05-01
  ```

### <a name="rest-api-explanation"></a>Explicação da API REST

| Serviço | Agrupar | Operação | Notas |
|---|---|---|---|
| Gestão de Recursos | Definições de Política | [Criar](/rest/api/resources/policydefinitions/createorupdate) | Cria uma nova definição do Azure Policy numa subscrição. Alternativa: [Criar grupo de gestão](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) |
| Gestão de Recursos | Atribuições de Política | [Criar](/rest/api/resources/policyassignments/create) | Cria uma nova atribuição do Azure Policy. Neste exemplo, damos uma definição, mas também pode tomar iniciativa. |
| Gestão de Recursos | Atribuições de Política | [Eliminar](/rest/api/resources/policyassignments/delete) | Remove uma atribuição do Azure Policy existente. |
| Gestão de Recursos | Definições de Política | [Eliminar](/rest/api/resources/policydefinitions/delete) | Remove uma definição do Azure Policy existente. Alternativa: [Eliminar grupo de gestão](/rest/api/resources/policydefinitions/deleteatmanagementgroup) |

## <a name="next-steps"></a>Passos Seguintes

- Reveja os [exemplos do Azure Policy](index.md) adicionais
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md)