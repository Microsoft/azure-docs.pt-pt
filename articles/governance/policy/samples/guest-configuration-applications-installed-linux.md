---
title: Exemplo – auditar aplicativos ausentes em VMs Linux
description: Essa iniciativa de configuração de convidado de política de exemplo e as definições são auditadas se os aplicativos especificados não estiverem instalados dentro de máquinas virtuais do Linux.
author: DCtheGeek
ms.service: azure-policy
ms.topic: sample
ms.date: 05/02/2019
ms.author: dacoulte
ms.openlocfilehash: 5f4d4f4c1102c4409d891bb20b54788dc8ed40ee
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255740"
---
# <a name="sample---audit-if-specified-applications-arent-installed-inside-linux-vms"></a>Exemplo – auditar se os aplicativos especificados não estiverem instalados dentro de VMs do Linux

Essa iniciativa de configuração de convidado de política cria um evento de auditoria quando os aplicativos especificados não são instalados dentro de máquinas virtuais do Linux. A ID dessa iniciativa interna é `/providers/Microsoft.Authorization/policySetDefinitions/c937dcb4-4398-4b39-8d63-4a6be432252e`.

> [!IMPORTANT]
> Todas as iniciativas de configuração de convidado são compostas por definições de política de **auditoria** e **deployIfNotExists** . A atribuição de apenas uma das definições de política faz com que a configuração de convidado não funcione corretamente.

Você pode atribuir este exemplo usando:

- O [portal do Azure](#azure-portal)
- [O Azure PowerShell](#azure-powershell)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="components-of-the-initiative"></a>Componentes da iniciativa

Essa iniciativa de [configuração de convidado](../concepts/guest-configuration.md) é composta pelas seguintes políticas:

- [auditoria](#audit-definition) – auditoria quando os aplicativos não são instalados dentro de VMs do Linux
  - ID: `/providers/Microsoft.Authorization/policyDefinitions/fee5cb2b-9d9b-410e-afe3-2902d90d0004`
- [deployIfNotExists](#deployIfNotExists-definition) -implantar a extensão de VM para auditar quando os aplicativos não forem instalados dentro de VMs do Linux
  - ID: `/providers/Microsoft.Authorization/policyDefinitions/4d1c04de-2172-403f-901b-90608c35c721`

### <a name="initiative-definition"></a>Definição de iniciativa

A iniciativa é criada unindo-se as definições **Audit** e **deployIfNotExists** juntas e os [parâmetros de iniciativa](#initiative-parameters). Esse é o JSON da definição.

[!code-json[initiative-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/azurepolicyset.json "Initiative definition (JSON)")]

### <a name="initiative-parameters"></a>Parâmetros de iniciativa

|Nome |Tipo |Descrição |
|---|---|---|
|applicationName |String |Nomes de aplicativos. Exemplo: ' Python ', ' PowerShell ' ou uma lista separada por vírgulas, como ' Python, PowerShell '. Use \* para correspondência de curinga, como ' Power @ no__t-1 '. |

Ao criar uma atribuição através do PowerShell ou da CLI do Azure, os valores do parâmetro podem ser transmitidos como JSON numa cadeia ou através de um ficheiro com o `-PolicyParameter` (PowerShell) ou a `--params` (CLI do Azure).
O PowerShell também suporta `-PolicyParameterObject`, o que exige passar ao cmdlet um Nome/Valor hashtable em que **Nome** é o nome do parâmetro e **Valor** é o valor único ou a matriz de valores a ser transmitida durante a atribuição.

Neste parâmetro de exemplo, a instalação de aplicativos _Python_ e _PowerShell_ é auditada.

```json
{
    "applicationName": {
        "value": "python,powershell"
    }
}
```

Somente a definição de política **deployIfNotExists** usa os parâmetros de iniciativa.

### <a name="audit-definition"></a>definição de auditoria

O JSON que define as regras da definição de política de **auditoria** .

[!code-json[audit-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json "audit policy rules (JSON)")]

### <a name="deployifnotexists-definition"></a>definição de deployIfNotExists

O JSON que define as regras da definição de política **deployIfNotExists** .

[!code-json[deployIfNotExists-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json "deployIfNotExists policy rules (JSON)")]

A definição de política **deployIfNotExists** define as imagens do Azure nas quais a política foi validada:

|Publicador |Oferta |SKU |
|-|-|-|
|OpenLogic |CentOS @ no__t-0 |Todos, exceto 6 @ no__t-0 |
|RedHat |RHEL |Todos, exceto 6 @ no__t-0 |
|RedHat |OSA | Tudo |
|credativ |Debian | Todos, exceto 7 @ no__t-0 |
|SUSE |SLES @ no__t-0 |Todos, exceto 11 @ no__t-0 |
|Canonical| UbuntuServer |Todos, exceto 12 @ no__t-0 |
|Microsoft-dsvm |Linux-Data-Science-VM-Ubuntu |Tudo |
|Microsoft-dsvm |azureml |Tudo |
|Cloudera |cloudera-CentOS-os |Todos, exceto 6 @ no__t-0 |
|Cloudera |cloudera-Altus-CentOS-os |Tudo |
|Microsoft-anúncios |Linux @ no__t-0 |Tudo |
|Microsoft-AKs |Tudo |Tudo |
|AzureDatabricks |Tudo |Tudo |
|qubole-Inc. |Tudo |Tudo |
|datastax |Tudo |Tudo |
|couchbase |Tudo |Tudo |
|scalegrid |Tudo |Tudo |
|Verifica |Tudo |Tudo |
|paloaltonetworks |Tudo |Tudo |

A parte de **implantação** da regra passa o parâmetro _installedApplication_ para o agente de configuração convidado na máquina virtual. Essa configuração permite que o agente execute as validações e a conformidade do relatório de volta por meio da definição de política de **auditoria** .

## <a name="azure-portal"></a>Portal do Azure

Depois que as definições **Audit** e **deployIfNotExists** são criadas no portal, é recomendável agrupá-las em uma [iniciativa](../concepts/definition-structure.md#initiatives) para atribuição.

### <a name="create-copy-of-audit-definition"></a>Criar cópia da definição de auditoria

[![Deploy o exemplo de política para o azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)
[![Deploy o exemplo de política para o Azure gov](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)

O uso desses botões para implantar por meio do portal cria uma cópia da definição de política de **auditoria** .
Sem a definição de política **deployIfNotExists** emparelhada, a configuração de convidado não funcionará corretamente.

### <a name="create-copy-of-deployifnotexists-definition"></a>Criar cópia da definição de deployIfNotExists

[![Deploy o exemplo de política para o azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)
[![Deploy o exemplo de política para o Azure gov](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)

O uso desses botões para implantar por meio do portal cria uma cópia da definição de política **deployIfNotExists** . Sem a definição de política de **auditoria** emparelhada, a configuração de convidado não funcionará corretamente.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Implementar com o Azure PowerShell

#### <a name="copy-and-assign-the-initiative"></a>Copiar e atribuir a iniciativa

Essas etapas criam uma cópia da iniciativa que inclui as políticas internas para **Audit** e **deployIfNotExists** e atribui a iniciativa a um grupo de recursos.

```azurepowershell-interactive
# Create the policy initiative (Subscription scope)
$initDef = New-AzPolicySetDefinition -Name 'guestconfig-installed-application-linux' -DisplayName 'GuestConfig - Audit that an application is installed inside Linux VMs' -description 'This initiative will both deploy the policy requirements and audit that the specified application is installed inside Linux virtual machines.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/azurepolicyset.definitions.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/azurepolicyset.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the initiative parameter (JSON format)
$initParam = '{ "applicationName": { "value": "python,powershell" } }'

# Create the initiative assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-assignment' -DisplayName 'GuestConfig - Python and PowerShell apps on Linux' -Scope $scope.ResourceID -PolicySetDefinition $initDef -PolicyParameter $initParam -AssignIdentity -Location 'westus2'

# Get the system-assigned managed identity created by the assignment with -AssignIdentity
$saIdentity = $assignment.Identity.principalId

# Give the system-assigned managed identity the 'Contributor' role on the scope (needed by deployIfNotExists)
$roleAssignment = New-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'
```

Execute o seguinte comando para remover a definição e a atribuição anterior:

```azurepowershell-interactive
# Remove the initiative assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the 'Contributor' role from the system-assigned managed identity
Remove-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'

# Remove the initiative definition
Remove-AzPolicySetDefinition -Id $initDef
```

#### <a name="copy-and-assign-the-audit-definition"></a>Copiar e atribuir a definição de auditoria

Essas etapas criam uma cópia da definição de **auditoria** e a atribuem a um grupo de recursos. Essa definição não funcionará corretamente sem a definição de **deployIfNotExists** emparelhada que também está sendo atribuída.

```azurepowershell-interactive
# Create the policy definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'guestconfig-installed-application-linux-audit' -DisplayName 'GuestConfig - Audit that an application is installed inside Linux VMs' -description 'This policy audits that the specified application is installed inside Linux virtual machines. This policy should only be used along with its corresponding deploy policy in an initiative/policy set.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Create the policy assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-audit-assignment' -DisplayName 'GuestConfig - Python and PowerShell apps on Linux' -Scope $scope.ResourceID -PolicyDefinition $definition
```

Execute o seguinte comando para remover a definição e a atribuição anterior:

```azurepowershell-interactive
# Remove the policy definition
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the policy definition
Remove-AzPolicyDefinition -Id $definition
```

#### <a name="copy-and-assign-the-deployifnotexists-definition"></a>Copiar e atribuir a definição de deployIfNotExists

Essas etapas criam uma cópia da definição de **deployIfNotExists** e a atribuem a um grupo de recursos.
Essa definição não funcionará corretamente sem que a definição de **auditoria** emparelhada também esteja sendo atribuída.

```azurepowershell-interactive
# Create the policy definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'guestconfig-installed-application-linux-deployIfNotExists' -DisplayName 'GuestConfig - Deploy VM extension to audit that an application is installed inside Linux VMs' -description 'Include this rule to deploy the VM extension for Microsoft Guest Configuration, the VM extension for Microsoft Azure Managed Service Identity, and the content required to audit that an application is installed inside Linux virtual machines. This policy should only be used along with its corresponding audit policy in an initiative/policy set.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the definition parameter (JSON format)
$policyParam  = '{ "applicationName": { "value": "python,powershell" } }'

# Create the policy assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-deployIfNotExists-assignment' -DisplayName 'GuestConfig - Deploy VM extension to audit that Python and PowerShell are installed inside Linux VMs' -Scope $scope.ResourceID -PolicyDefinition $definition -PolicyParameter $policyParam -AssignIdentity -Location 'westus2'

# Get the system-assigned managed identity created by the assignment with -AssignIdentity
$saIdentity = $assignment.Identity.principalId

# Give the system-assigned managed identity the 'Contributor' role on the scope (needed by deployIfNotExists)
$roleAssignment = New-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'
```

Execute o seguinte comando para remover a definição e a atribuição anterior:

```azurepowershell-interactive
# Remove the policy assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the 'Contributor' role from the system-assigned managed identity
Remove-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'

# Remove the policy definition
Remove-AzPolicyDefinition -Id $definition
```

### <a name="azure-powershell-explanation"></a>Explicação do Azure PowerShell

Os scripts de implementação e remoção utilizam os seguintes comandos. Cada comando na tabela seguinte liga à documentação específica do comando:

| Comando | Notas |
|---|---|
| [New-AzPolicySetDefinition](/powershell/module/az.resources/New-AzPolicySetDefinition) | Cria uma iniciativa de Azure Policy. |
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-AzPolicyDefinition) | Cria uma definição de Azure Policy. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup) | Obtém um grupo de recursos único. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-AzPolicyAssignment) | Cria uma nova atribuição de Azure Policy para uma iniciativa ou definição. |
| [New-AzRoleAssignment](/powershell/module/az.resources/New-AzRoleAssignment) | Fornece uma atribuição de função existente para a entidade específica. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-AzPolicyAssignment) | Remove uma atribuição do Azure Policy existente. |
| [Remove-AzPolicySetDefinition](/powershell/module/az.resources/Remove-AzPolicySetDefinition) | Remove uma iniciativa. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-AzPolicyDefinition) | Remove uma definição. |

## <a name="next-steps"></a>Passos seguintes

- Examine os [exemplos de Azure Policy](index.md)adicionais.
- Saiba mais sobre [Azure Policy configuração de convidado](../concepts/guest-configuration.md).
- Examine a [estrutura de definição de Azure Policy](../concepts/definition-structure.md).
