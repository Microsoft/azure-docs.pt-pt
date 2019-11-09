---
title: Implantar Azure Policy em assinaturas delegadas em escala
description: Saiba como o gerenciamento de recursos delegado do Azure permite implantar uma definição de política e uma atribuição de política em vários locatários.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 11/8/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 537802358af4dbca4f9e24699fc8455c1c6ef01b
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73893768"
---
# <a name="deploy-azure-policy-to-delegated-subscriptions-at-scale"></a>Implantar Azure Policy em assinaturas delegadas em escala

Como um provedor de serviços, você pode ter integrado vários locatários de clientes para o gerenciamento de recursos delegado do Azure. O Azure Lighthouse permite que os provedores de serviços executem operações em escala em vários locatários de uma vez, tornando as tarefas de gerenciamento mais eficientes.

Este tópico mostra como usar [Azure Policy](https://docs.microsoft.com/azure/governance/policy/) para implantar uma definição de política e uma atribuição de política em vários locatários usando comandos do PowerShell. Neste exemplo, a definição de política garante que as contas de armazenamento sejam protegidas, permitindo apenas o tráfego HTTPS.

## <a name="use-azure-resource-graph-to-query-across-customer-tenants"></a>Usar o grafo de recursos do Azure para consultar em locatários do cliente

Você pode usar o [grafo de recursos do Azure](https://docs.microsoft.com/azure/governance/resource-graph/) para consultar em todas as assinaturas nos locatários do cliente que você gerencia. Neste exemplo, identificaremos todas as contas de armazenamento nessas assinaturas que atualmente não exigem tráfego HTTPS.  

```powershell
$MspTenant = "insert your managing tenantId here"

$subs = Get-AzSubscription

$ManagedSubscriptions = Search-AzGraph -Query "ResourceContainers | where type == 'microsoft.resources/subscriptions' | where tenantId != '$($mspTenant)' | project name, subscriptionId, tenantId" -subscription $subs.subscriptionId

Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | project name, location, subscriptionId, tenantId, properties.supportsHttpsTrafficOnly" -subscription $ManagedSubscriptions.subscriptionId | convertto-json
```

## <a name="deploy-a-policy-across-multiple-customer-tenants"></a>Implantar uma política em vários locatários do cliente

O exemplo a seguir mostra como usar um [modelo de Azure Resource Manager](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/policy-enforce-https-storage/enforceHttpsStorage.json) para implantar uma definição de política e uma atribuição de política em assinaturas delegadas em vários locatários de cliente. Essa definição de política requer que todas as contas de armazenamento usem o tráfego HTTPS, impedindo a criação de novas contas de armazenamento que não estejam em conformidade e marcando as contas de armazenamento existentes sem a configuração como sem conformidade.

```powershell
Write-Output "In total, there are $($ManagedSubscriptions.Count) delegated customer subscriptions to be managed"

foreach ($ManagedSub in $ManagedSubscriptions)
{
    Select-AzSubscription -SubscriptionId $ManagedSub.subscriptionId

    New-AzDeployment -Name mgmt `
                     -Location eastus `
                     -TemplateUri "https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/policy-enforce-https-storage/enforceHttpsStorage.json" `
                     -AsJob
}
```

## <a name="validate-the-policy-deployment"></a>Validar a implantação da política

Depois de implantar o modelo de Azure Resource Manager, você pode confirmar que a definição de política foi aplicada com êxito ao tentar criar uma conta de armazenamento com **EnableHttpsTrafficOnly** definido como **false** em uma de suas assinaturas delegadas. Devido à atribuição de política, você não poderá criar essa conta de armazenamento.  

```powershell
New-AzStorageAccount -ResourceGroupName (New-AzResourceGroup -name policy-test -Location eastus -Force).ResourceGroupName `
                     -Name (get-random) `
                     -Location eastus `
                     -EnableHttpsTrafficOnly $false `
                     -SkuName Standard_LRS `
                     -Verbose                  
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando tiver terminado, remova a definição de política e a atribuição criada pela implantação.

```powershell
foreach ($ManagedSub in $ManagedSubscriptions)
{
    select-azsubscription -subscriptionId $ManagedSub.subscriptionId

    Remove-AzDeployment -Name mgmt -AsJob

    $Assignment = Get-AzPolicyAssignment | where-object {$_.Name -like "enforce-https-storage-assignment"}

    if ([string]::IsNullOrEmpty($Assignment))
    {
        Write-Output "Nothing to clean up - we're done"
    }
    else
    {

    Remove-AzPolicyAssignment -Name 'enforce-https-storage-assignment' -Scope "/subscriptions/$($ManagedSub.subscriptionId)" -Verbose

    Write-Output "Deployment has been deleted - we're done"
    }
}
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [Azure Policy](https://docs.microsoft.com/azure/governance/policy/).
- Saiba mais sobre as [experiências de gerenciamento entre locatários](../concepts/cross-tenant-management-experience.md).
