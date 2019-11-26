---
title: Sample - Apply tag and its default value
description: This sample policy definition appends a specified tag name and value defined in a parameter, if that tag is not provided.
ms.date: 01/26/2019
ms.topic: sample
ms.openlocfilehash: 33d0580d2f6c231c4cd7e73abdaab6cb14c363d9
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463670"
---
# <a name="sample---apply-tag-and-its-default-value"></a>Sample - Apply tag and its default value

Esta política acrescenta um nome de etiqueta e um valor especificado, se essa etiqueta não for fornecida. Especifica o nome de etiqueta e o valor a aplicar.

Pode implementar esta política de exemplo com:

- O [portal do Azure](#azure-portal)
- [O Azure PowerShell](#azure-powershell)
- [CLI do Azure](#azure-cli)
- [API REST](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Política de exemplo

### <a name="policy-definition"></a>Definição de política

A definição da política JSON composta, utilizada pela API REST, os botões"Implementar no Azure" e manualmente no portal.

[!code-json[main](../../../../policy-templates/samples/built-in-policy/apply-default-tag-value/azurepolicy.json "Apply tag and its default value")]

> [!NOTE]
> Se criar manualmente uma política no portal, utilize as opções **properties.parameters** e **properties.policyRule** acima. Junte as duas secções com chavetas `{}` para transformar num JSON válido.

### <a name="policy-rules"></a>Regras da política

O JSON que define as regras da política, utilizado pela CLI do Azure e o Azure PowerShell.

[!code-json[rule](../../../../policy-templates/samples/built-in-policy/apply-default-tag-value/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Parâmetros de política

O JSON que define os parâmetros da política, utilizado pela CLI do Azure e o Azure PowerShell.

[!code-json[parameters](../../../../policy-templates/samples/built-in-policy/apply-default-tag-value/azurepolicy.parameters.json "Policy parameters (JSON)")]

|Nome |Tipo |Campo |Descrição |
|---|---|---|---|
|tagName |String |etiquetas |Nome da etiqueta, como costCenter|
|tagValue |String |etiquetas |Valor da etiqueta, como headquarter|

Ao criar uma atribuição através do PowerShell ou da CLI do Azure, os valores do parâmetro podem ser transmitidos como JSON numa cadeia ou através de um ficheiro com o `-PolicyParameter` (PowerShell) ou a `--params` (CLI do Azure).
O PowerShell também suporta `-PolicyParameterObject`, o que exige passar ao cmdlet um Nome/Valor hashtable em que **Nome** é o nome do parâmetro e **Valor** é o valor único ou a matriz de valores a ser transmitida durante a atribuição.

No parâmetro deste exemplo, um _tagName_ de **costCenter** e um _tagValue_ de **headquarter** está definido.

```json
{
    "tagName": {
        "value": "costCenter"
    },
    "tagValue": {
        "value": "headquarter"
    }
}
```

## <a name="azure-portal"></a>Portal do Azure

[![Deploy the Policy sample to Azure](../media/deploy/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2Fbuilt-in-policy%2Fapply-default-tag-value%2Fazurepolicy.json)
[![Deploy the Policy sample to Azure Gov](../media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2Fbuilt-in-policy%2Fapply-default-tag-value%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Implementar com o Azure PowerShell

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'allowed-custom-images' -DisplayName 'Approved VM images' -description 'This policy governs the approved VM images' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the Policy Parameter (JSON format)
$policyparam = '{ "tagName": { "value": "costCenter" }, "tagValue": { "value": "headquarter" } }'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'apply-default-tag-value' -DisplayName 'Apply tag and its default value Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition -PolicyParameter $policyparam
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
definition=$(az policy definition create --name 'apply-default-tag-value' --display-name 'Apply tag and its default value' --description 'Applies a required tag and its default value if it is not specified by the user' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.parameters.json' --mode All)

# Set the scope to a resource group; may also be a resource, subscription, or management group
scope=$(az group show --name 'YourResourceGroup')

# Set the Policy Parameter (JSON format)
policyparam='{ "tagName": { "value": "costCenter" }, "tagValue": { "value": "headquarter" } }'

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'apply-default-tag-value' --display-name 'Apply tag and its default value Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r` --params "$policyparam")
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

Existem várias ferramentas que podem ser utilizadas para interagir com a API REST do Gestor de Recursos, como o [ARMClient](https://github.com/projectkudu/ARMClient) ou o PowerShell. Pode encontrar um exemplo de chamada da API REST a partir do PowerShell na secção **Aliases** da [Estrutura de definição da política](../concepts/definition-structure.md#aliases).

## <a name="rest-api"></a>API REST

### <a name="deploy-with-rest-api"></a>Implementar com API REST

- Crie a Definição de Política (âmbito da Subscrição). Utilize o JSON [definição de política](#policy-definition) para o Corpo do Pedido.

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/apply-default-tag-value?api-version=2016-12-01
  ```

- Criar Atribuição de Política (âmbito do Grupo de Recursos)

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/apply-default-tag-value-assignment?api-version=2017-06-01-preview
  ```

  Utilize o seguinte exemplo do JSON para o Corpo do Pedido:

  ```json
  {
      "properties": {
          "displayName": "Apply tag and its default value Assignment",
          "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/apply-default-tag-value",
          "parameters": {
              "tagName": {
                  "value": "costCenter"
              },
              "tagValue": {
                  "value": "headquarter"
              }
          }
      }
  }
  ```

### <a name="remove-with-rest-api"></a>Remover com a API REST

- Remover a Atribuição de Política

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/apply-default-tag-value-assignment?api-version=2017-06-01-preview
  ```

- Remover a Definição de Política

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/apply-default-tag-value?api-version=2016-12-01
  ```

### <a name="rest-api-explanation"></a>Explicação da API REST

| Serviço | Grupo | Operação | Notas |
|---|---|---|---|
| Gestão de Recursos | Definições de Política | [Criar](/rest/api/resources/policydefinitions/createorupdate) | Cria uma nova definição do Azure Policy numa subscrição. Em alternativa: [Criar no grupo de gestão](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) |
| Gestão de Recursos | Atribuições de Política | [Criar](/rest/api/resources/policyassignments/create) | Cria uma nova atribuição do Azure Policy. Neste exemplo, damos uma definição, mas também pode tomar iniciativa. |
| Gestão de Recursos | Atribuições de Política | [Eliminar](/rest/api/resources/policyassignments/delete) | Remove uma atribuição do Azure Policy existente. |
| Gestão de Recursos | Definições de Política | [Eliminar](/rest/api/resources/policydefinitions/delete) | Remove uma definição do Azure Policy existente. Em alternativa: [Eliminar no grupo de gestão](/rest/api/resources/policydefinitions/deleteatmanagementgroup) |

## <a name="next-steps"></a>Passos seguintes

- Reveja os [exemplos do Azure Policy](index.md) adicionais
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md)