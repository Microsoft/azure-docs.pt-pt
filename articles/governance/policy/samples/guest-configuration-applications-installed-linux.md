---
title: Exemplo - auditoria se aplicações especificadas não estão instaladas em VMs do Linux
description: Esta iniciativa de configuração de convidado de política de exemplo e as definições de auditoria se as aplicações especificadas não são instaladas dentro de máquinas virtuais do Linux.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 03/18/2019
ms.author: dacoulte
ms.openlocfilehash: bc4c54fa157f5468bbc324ebdf03dd65a47488c9
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58190495"
---
# <a name="sample---audit-if-specified-applications-are-not-installed-inside-linux-vms"></a>Exemplo - auditoria se aplicações especificadas não estão instaladas em VMs do Linux

Esta iniciativa de configuração de convidado de política audita que a aplicação especificada está instalada no interior de máquinas virtuais do Linux. O ID dessa iniciativa incorporada é `/providers/Microsoft.Authorization/policySetDefinitions/c937dcb4-4398-4b39-8d63-4a6be432252e`.

> [!IMPORTANT]
> Todas as iniciativas de configuração de convidado são compostas por **auditar** e **deployIfNotExists** definições de política. Atribuir apenas uma das definições de política fazer com que a configuração de convidado para não funcionar corretamente.

É possível atribuir este exemplo ao utilizar:

- O [portal do Azure](#azure-portal)
- [Azure PowerShell](#azure-powershell)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="components-of-the-initiative"></a>Componentes da iniciativa

Isso [configuração do convidado](../concepts/guest-configuration.md) iniciativa é constituída pelas seguintes políticas:

- [auditar](#audit-definition) -se um aplicativo está instalado em VMs do Linux de auditoria
  - ID: `/providers/Microsoft.Authorization/policyDefinitions/fee5cb2b-9d9b-410e-afe3-2902d90d0004`
- [deployIfNotExists](#deployIfNotExists-definition) -extensão de VM de implementar para auditar que um aplicativo é instalado em VMs do Linux
  - ID: `/providers/Microsoft.Authorization/policyDefinitions/4d1c04de-2172-403f-901b-90608c35c721`

### <a name="initiative-definition"></a>Definição de iniciativa

A iniciativa é criada ao associar o **auditar** e **deployIfNotExists** definições em conjunto e o [parâmetros de iniciativa](#initiative-parameters). Este é o JSON da definição.

[!code-json[initiative-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/azurepolicyset.json "Initiative definition (JSON)")]

### <a name="initiative-parameters"></a>Parâmetros de iniciativa

| Nome | Tipo de | | Descrição | |---|---| | ---| | applicationName | Cadeia de caracteres | Nomes de aplicativo. Exemplo: "python", "powershell" ou uma lista separada por vírgulas como powershell "python". Uso \* correspondência de carateres universais, como "power\*". |

Ao criar uma atribuição através do PowerShell ou da CLI do Azure, os valores do parâmetro podem ser transmitidos como JSON numa cadeia ou através de um ficheiro com o `-PolicyParameter` (PowerShell) ou a `--params` (CLI do Azure).
O PowerShell também suporta `-PolicyParameterObject`, o que exige passar ao cmdlet um Nome/Valor hashtable em que **Nome** é o nome do parâmetro e **Valor** é o valor único ou a matriz de valores a ser transmitida durante a atribuição.

No parâmetro neste exemplo, a instalação de aplicativos _python_ e _powershell_ é auditada.

```json
{
    "applicationName": {
        "value": "python,powershell"
    }
}
```

Apenas os **deployIfNotExists** definição de política faz uso de parâmetros de iniciativa.

### <a name="audit-definition"></a>definição de auditoria

O JSON de definir as regras do **auditar** definição de política.

[!code-json[audit-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json "audit policy rules (JSON)")]

### <a name="deployifnotexists-definition"></a>definição de deployIfNotExists

O JSON de definir as regras do **deployIfNotExists** definição de política.

[!code-json[deployIfNotExists-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json "deployIfNotExists policy rules (JSON)")]

O **deployIfNotExists** definição de política define as imagens do Azure, a política foi validada em:

|Publicador |Oferta |SKU |
|-|-|-|
|OpenLogic |CentOS\* |Tudo, exceto a 6\* |
|RedHat |RHEL |Tudo, exceto a 6\* |
|RedHat |osa | Todos |
|credativ |Debian | Tudo, exceto a 7\* |
|SUSE |SLES\* |Tudo, exceto a 11\* |
|Canónico| UbuntuServer |Tudo, exceto a 12\* |
|microsoft-dsvm |linux-data-science-vm-ubuntu |Todos |
|microsoft-dsvm |azureml |Todos |
|cloudera |cloudera-centos-os |Tudo, exceto a 6\* |
|cloudera |cloudera-altus-centos-os |Todos |
|microsoft-ads |linux\* |Todos |
|microsoft-aks |Todos |Todos |
|AzureDatabricks |Todos |Todos |
|qubole-inc |Todos |Todos |
|datastax |Todos |Todos |
|Couchbase |Todos |Todos |
|scalegrid |Todos |Todos |
|Ponto de verificação |Todos |Todos |
|paloaltonetworks |Todos |Todos |

O **implantação** parte da regra passa a _installedApplication_ parâmetro para o agente de configuração de convidado na máquina virtual. Esta configuração permite que o agente realizar as validações e reportar a conformidade de volta através da **auditar** definição de política.

## <a name="azure-portal"></a>Portal do Azure

Depois do **auditar** e **deployIfNotExists** definições são criadas no portal, é recomendado para agrupá-los num [iniciativa](../concepts/definition-structure.md#initiatives) para atribuição.

### <a name="create-copy-of-audit-definition"></a>Criar uma cópia de definição de auditoria

[![Implementar no Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)
[![Implementar no Azure Gov](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)

Usando esses botões para implementar através do portal cria uma cópia dos **auditar** definição de política.
Sem o emparelhado **deployIfNotExists** definição de política, a configuração de convidado não funcionará corretamente.

### <a name="create-copy-of-deployifnotexists-definition"></a>Criar uma cópia de definição de deployIfNotExists

[![Implementar no Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)
[![Implementar no Azure Gov](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)

Usando esses botões para implementar através do portal cria uma cópia dos **deployIfNotExists** definição de política. Sem o emparelhado **auditar** definição de política, a configuração de convidado não funcionará corretamente.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Implementar com o Azure PowerShell

#### <a name="copy-and-assign-the-initiative"></a>Copie e atribuir iniciativa

Estes passos, criar uma cópia da iniciativa que inclui as diretivas internas para ambos **auditar** e **deployIfNotExists** e atribui a iniciativa a um grupo de recursos.

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

#### <a name="copy-and-assign-the-audit-definition"></a>Copie e atribuir a definição de auditoria

Estes passos, criar uma cópia do **auditar** definição e atribuí-lo a um grupo de recursos. Esta definição não funcionará corretamente sem o emparelhado **deployIfNotExists** definição também que está sendo atribuída.

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

#### <a name="copy-and-assign-the-deployifnotexists-definition"></a>Copie e atribuir a definição de deployIfNotExists

Estes passos, criar uma cópia do **deployIfNotExists** definição e atribuí-lo a um grupo de recursos.
Esta definição não funcionará corretamente sem o emparelhado **auditar** definição também que está sendo atribuída.

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
| [New-AzPolicySetDefinition](/powershell/module/az.resources/New-AzPolicySetDefinition) | Cria uma iniciativa de política do Azure. |
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-AzPolicyDefinition) | Cria uma definição de política do Azure. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup) | Obtém um grupo de recursos único. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-AzPolicyAssignment) | Cria uma nova atribuição de política do Azure para uma iniciativa ou definição. |
| [New-AzRoleAssignment](/powershell/module/az.resources/New-AzRoleAssignment) | Fornece uma atribuição de função existente para a entidade de segurança específica. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-AzPolicyAssignment) | Remove uma atribuição do Azure Policy existente. |
| [Remove-AzPolicySetDefinition](/powershell/module/az.resources/Remove-AzPolicySetDefinition) | Remove uma iniciativa. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-AzPolicyDefinition) | Remove uma definição. |

## <a name="next-steps"></a>Passos Seguintes

- Revisão adicional [exemplos do Azure Policy](index.md).
- Saiba mais sobre [configuração da política de convidado do Azure](../concepts/guest-configuration.md).
- Revisão [estrutura de definição de política do Azure](../concepts/definition-structure.md).