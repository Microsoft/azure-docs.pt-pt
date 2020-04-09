---
title: Implementar a Política Azure para subscrições delegadas em escala
description: Saiba como a gestão de recursos delegados da Azure permite implementar uma definição de política e atribuição de políticas entre vários inquilinos.
ms.date: 11/8/2019
ms.topic: conceptual
ms.openlocfilehash: 9015351c3fc8f374c5ce85712907fa05249cde11
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984577"
---
# <a name="deploy-azure-policy-to-delegated-subscriptions-at-scale"></a>Implementar a Política Azure para subscrições delegadas em escala

Como prestador de serviços, pode ter embarcado em vários inquilinos de clientes para a gestão de recursos delegados da Azure. [O Azure Lighthouse](../overview.md) permite que os prestadores de serviços realizem operações em escala em vários inquilinos ao mesmo tempo, tornando as tarefas de gestão mais eficientes.

Este tópico mostra-lhe como usar a [Política Azure](../../governance/policy/index.yml) para implementar uma definição de política e atribuição de políticas entre vários inquilinos usando comandos PowerShell. Neste exemplo, a definição de política garante que as contas de armazenamento são asseguradas permitindo apenas o tráfego HTTPS.

## <a name="use-azure-resource-graph-to-query-across-customer-tenants"></a>Use o Gráfico de Recursos Azure para consultar os inquilinos dos clientes

Você pode usar [o Azure Resource Graph](../../governance/resource-graph/index.yml) para consultar todas as subscrições nos inquilinos de clientes que você gere. Neste exemplo, identificaremos quaisquer contas de armazenamento nestas subscrições que não necessitem atualmente de tráfego HTTPS.  

```powershell
$MspTenant = "insert your managing tenantId here"

$subs = Get-AzSubscription

$ManagedSubscriptions = Search-AzGraph -Query "ResourceContainers | where type == 'microsoft.resources/subscriptions' | where tenantId != '$($mspTenant)' | project name, subscriptionId, tenantId" -subscription $subs.subscriptionId

Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | project name, location, subscriptionId, tenantId, properties.supportsHttpsTrafficOnly" -subscription $ManagedSubscriptions.subscriptionId | convertto-json
```

## <a name="deploy-a-policy-across-multiple-customer-tenants"></a>Implementar uma política em vários inquilinos de clientes

O exemplo abaixo mostra como usar um modelo de Gestor de [Recursos Azure](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json) para implementar uma definição de política e atribuição de políticas em assinaturas delegadas em vários inquilinos de clientes. Esta definição de política requer que todas as contas de armazenamento utilizem o tráfego HTTPS, impedindo a criação de quaisquer novas contas de armazenamento que não cumpram e marcam as contas de armazenamento existentes sem a definição como incompatível.

```powershell
Write-Output "In total, there are $($ManagedSubscriptions.Count) delegated customer subscriptions to be managed"

foreach ($ManagedSub in $ManagedSubscriptions)
{
    Select-AzSubscription -SubscriptionId $ManagedSub.subscriptionId

    New-AzDeployment -Name mgmt `
                     -Location eastus `
                     -TemplateUri "https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json" `
                     -AsJob
}
```

## <a name="validate-the-policy-deployment"></a>Validar a implementação da política

Depois de ter implementado o modelo do Gestor de Recursos Do Azure, pode confirmar que a definição de política foi aplicada com sucesso ao tentar criar uma conta de armazenamento com o **EnableHttpsTrafficOnly** definido como **falso** numa das suas subscrições delegadas. Por causa da atribuição de apólices, você não deve ser capaz de criar esta conta de armazenamento.  

```powershell
New-AzStorageAccount -ResourceGroupName (New-AzResourceGroup -name policy-test -Location eastus -Force).ResourceGroupName `
                     -Name (get-random) `
                     -Location eastus `
                     -EnableHttpsTrafficOnly $false `
                     -SkuName Standard_LRS `
                     -Verbose                  
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar, remova a definição de política e a atribuição criadas pela implantação.

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

- Conheça a [Política Azure.](../../governance/policy/index.yml)
- Conheça [as experiências de gestão de inquilinos cruzados.](../concepts/cross-tenant-management-experience.md)
