---
title: Implementar a Política Azure para delegar subscrições em escala
description: Saiba como o Azure Lighthouse permite implementar uma definição de política e atribuição de políticas em vários inquilinos.
ms.date: 08/12/2020
ms.topic: how-to
ms.openlocfilehash: 27d32a68c1a3806e514533efbae581aa97bc6d0c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88167288"
---
# <a name="deploy-azure-policy-to-delegated-subscriptions-at-scale"></a>Implementar a Política Azure para delegar subscrições em escala

Como prestador de serviços, pode ter a bordo vários inquilinos de clientes para o [Farol de Azure.](../overview.md) O Azure Lighthouse permite que os prestadores de serviços realizem operações em escala em vários inquilinos ao mesmo tempo, tornando as tarefas de gestão mais eficientes.

Este tópico mostra-lhe como usar a [Política Azure](../../governance/policy/index.yml) para implementar uma definição de política e atribuição de políticas em vários inquilinos usando comandos PowerShell. Neste exemplo, a definição de política garante que as contas de armazenamento são asseguradas permitindo apenas o tráfego HTTPS.

> [!TIP]
> Embora nos refiramos a prestadores de serviços e clientes neste tópico, [as empresas que gerem vários inquilinos](../concepts/enterprise.md) podem usar os mesmos processos.

## <a name="use-azure-resource-graph-to-query-across-customer-tenants"></a>Use o Gráfico de Recursos Azure para consultar os inquilinos do cliente

Você pode usar [o Azure Resource Graph](../../governance/resource-graph/index.yml) para consultar todas as subscrições nos inquilinos do cliente que você gere. Neste exemplo, identificaremos quaisquer contas de armazenamento nestas subscrições que não necessitem atualmente de tráfego HTTPS.  

```powershell
$MspTenant = "insert your managing tenantId here"

$subs = Get-AzSubscription

$ManagedSubscriptions = Search-AzGraph -Query "ResourceContainers | where type == 'microsoft.resources/subscriptions' | where tenantId != '$($mspTenant)' | project name, subscriptionId, tenantId" -subscription $subs.subscriptionId

Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | project name, location, subscriptionId, tenantId, properties.supportsHttpsTrafficOnly" -subscription $ManagedSubscriptions.subscriptionId | convertto-json
```

## <a name="deploy-a-policy-across-multiple-customer-tenants"></a>Implementar uma política em vários inquilinos de clientes

O exemplo abaixo mostra como usar um [modelo de Gestor de Recursos Azure](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json) para implementar uma definição de política e atribuição de políticas em assinaturas delegadas em vários inquilinos de clientes. Esta definição de política requer que todas as contas de armazenamento utilizem o tráfego HTTPS, impedindo a criação de novas contas de armazenamento que não cumpram e marquem as contas de armazenamento existentes sem a configuração como não conforme.

```powershell
Write-Output "In total, there are $($ManagedSubscriptions.Count) delegated customer subscriptions to be managed"

foreach ($ManagedSub in $ManagedSubscriptions)
{
    Select-AzSubscription -SubscriptionId $ManagedSub.subscriptionId

    New-AzSubscriptionDeployment -Name mgmt `
                     -Location eastus `
                     -TemplateUri "https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json" `
                     -AsJob
}
```

## <a name="validate-the-policy-deployment"></a>Validar a implementação da política

Depois de ter implementado o modelo Azure Resource Manager, pode confirmar que a definição de política foi aplicada com sucesso ao tentar criar uma conta de armazenamento com **EnableHttpsTrafficOnly** definida como **falsa** numa das suas subscrições delegadas. Por causa da atribuição de políticas, não poderá criar esta conta de armazenamento.  

```powershell
New-AzStorageAccount -ResourceGroupName (New-AzResourceGroup -name policy-test -Location eastus -Force).ResourceGroupName `
                     -Name (get-random) `
                     -Location eastus `
                     -EnableHttpsTrafficOnly $false `
                     -SkuName Standard_LRS `
                     -Verbose                  
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar, remova a definição de política e a atribuição criada pela implementação.

```powershell
foreach ($ManagedSub in $ManagedSubscriptions)
{
    select-azsubscription -subscriptionId $ManagedSub.subscriptionId

    Remove-AzSubscriptionDeployment -Name mgmt -AsJob

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

- Saiba mais sobre [a Política Azure.](../../governance/policy/index.yml)
- Conheça as [experiências de gestão de inquilinos cruzados.](../concepts/cross-tenant-management-experience.md)
