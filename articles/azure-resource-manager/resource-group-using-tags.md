---
title: Etiquetar recursos do Azure para a organização lógica | Documentos da Microsoft
description: Mostra como aplicar etiquetas para organizar os recursos do Azure para faturação e gestão.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 003a78e5-2ff8-4685-93b4-e94d6fb8ed5b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: AzurePortal
ms.devlang: na
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: tomfitz
ms.openlocfilehash: 87b6dcb007eaea140b9655c248fdfb356cbc9b5f
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56817325"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>Utilizar etiquetas para organizar os recursos do Azure

[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

Para aplicar etiquetas a recursos, o utilizador tem de ter acesso de escrita para esse tipo de recurso. Para aplicar etiquetas a todos os tipos de recursos, utilize o [contribuinte](../role-based-access-control/built-in-roles.md#contributor) função. Para aplicar as etiquetas para o tipo de recurso apenas uma, utilize a função de Contribuidor para esse recurso. Por exemplo, para aplicar etiquetas a máquinas virtuais, utilize o [contribuinte de Máquina Virtual](../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="policies"></a>Políticas

Pode usar [do Azure Policy](../governance/policy/overview.md) para impor a etiquetagem regras e as convenções. Ao criar uma política, evite o cenário de recursos ser implementado na sua subscrição que não estejam em conformidade com as etiquetas esperadas para a sua organização. Em vez de aplicar etiquetas manualmente ou procurar recursos que não estão em conformidade, pode criar uma política que aplica automaticamente as etiquetas necessárias durante a implementação. A secção seguinte mostra algumas políticas para as etiquetas.

[!INCLUDE [Tag policies](../../includes/azure-policy-samples-general-tags.md)]

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para ver as etiquetas existentes de um *grupo de recursos*, utilize:

```azurepowershell-interactive
(Get-AzResourceGroup -Name examplegroup).Tags
```

Este script devolve o formato seguinte:

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

Para ver as etiquetas existentes de um *recurso que tem um ID de recurso específico*, utilize:

```azurepowershell-interactive
(Get-AzResource -ResourceId /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>).Tags
```

Ou para ver as etiquetas existentes de um *recurso que tem um nome e grupo de recurso específicos*, utilize:

```azurepowershell-interactive
(Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

Para obter *grupos de recursos com uma etiqueta específica*, utilize:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ Dept="Finance" }).ResourceGroupName
```

Para obter *recursos com uma etiqueta específica*, utilize:

```azurepowershell-interactive
(Get-AzResource -Tag @{ Dept="Finance"}).Name
```

Para obter *recursos que tenham um nome de etiqueta específica*, utilize:

```azurepowershell-interactive
(Get-AzResource -TagName Dept).Name
```

Sempre que aplicar etiquetas a um recurso ou grupo de recursos, as etiquetas existentes nesse recurso ou grupo de recursos são substituídas. Por conseguinte, tem de utilizar uma abordagem diferente, que tenha em conta se o recurso ou grupo de recursos tem etiquetas existentes.

Para adicionar etiquetas a um *grupo de recursos que não tenha etiquetas*, utilize:

```azurepowershell-interactive
Set-AzResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

Para adicionar etiquetas a um *grupo de recursos que tenha etiquetas existentes*, obtenha-as, adicione a etiqueta nova e volte a aplicar as etiquetas:

```azurepowershell-interactive
$tags = (Get-AzResourceGroup -Name examplegroup).Tags
$tags.Add("Status", "Approved")
Set-AzResourceGroup -Tag $tags -Name examplegroup
```

Para adicionar etiquetas a um *recurso que não tenha etiquetas*, utilize:

```azurepowershell-interactive
$r = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId $r.ResourceId -Force
```

Para adicionar etiquetas a um *recurso que tenha etiquetas*, utilize:

```azurepowershell-interactive
$r = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
$r.Tags.Add("Status", "Approved")
Set-AzResource -Tag $r.Tags -ResourceId $r.ResourceId -Force
```

Para aplicar todas as etiquetas de um grupo de recursos para todos os respetivos recursos, *sem reter etiquetas existentes nos recursos*, utilize o script seguinte:

```azurepowershell-interactive
$groups = Get-AzResourceGroup
foreach ($g in $groups)
{
    Get-AzResource -ResourceGroupName $g.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

Para aplicar todas as etiquetas de um grupo de recursos para todos os respetivos recursos, *retendo etiquetas existentes nos recursos que não são duplicados*, utilize o script seguinte:

```azurepowershell-interactive
$group = Get-AzResourceGroup "examplegroup"
if ($null -ne $group.Tags) {
    $resources = Get-AzResource -ResourceGroupName $group.ResourceGroupName
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzResource -ResourceId $r.ResourceId).Tags
        if ($resourcetags)
        {
            foreach ($key in $group.Tags.Keys)
            {
                if (-not($resourcetags.ContainsKey($key)))
                {
                    $resourcetags.Add($key, $group.Tags[$key])
                }
            }
            Set-AzResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
        else
        {
            Set-AzResource -Tag $group.Tags -ResourceId $r.ResourceId -Force
        }
    }
}
```

Para remover todas as etiquetas, transmita uma tabela hash vazia:

```azurepowershell-interactive
Set-AzResourceGroup -Tag @{} -Name examplegroup
```

## <a name="azure-cli"></a>CLI do Azure

Para ver as etiquetas existentes de um *grupo de recursos*, utilize:

```azurecli
az group show -n examplegroup --query tags
```

Este script devolve o formato seguinte:

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

Ou, para ver as etiquetas existentes de um *recursos que tem um grupo de nome, o tipo e o recurso especificado*, utilize:

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

Ao fazer o looping através de uma coleção de recursos, pode querer mostrar o ID de recurso por recurso. Um exemplo completo é mostrado neste artigo. Para ver as etiquetas existentes de um *recurso que tem um ID de recurso específico*, utilize:

```azurecli
az resource show --id <resource-id> --query tags
```

Para obter os grupos de recursos que tenham uma etiqueta específica, utilize `az group list`:

```azurecli
az group list --tag Dept=IT
```

Para obter todos os recursos que tenham uma etiqueta específica e um valor, utilize `az resource list`:

```azurecli
az resource list --tag Dept=Finance
```

Sempre que aplicar etiquetas a um recurso ou grupo de recursos, as etiquetas existentes nesse recurso ou grupo de recursos são substituídas. Por conseguinte, tem de utilizar uma abordagem diferente, que tenha em conta se o recurso ou grupo de recursos tem etiquetas existentes.

Para adicionar etiquetas a um *grupo de recursos que não tenha etiquetas*, utilize:

```azurecli
az group update -n examplegroup --set tags.Environment=Test tags.Dept=IT
```

Para adicionar etiquetas a um *recurso que não tenha etiquetas*, utilize:

```azurecli
az resource tag --tags Dept=IT Environment=Test -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Para adicionar etiquetas a um recurso que já tem etiquetas, obter as etiquetas existentes, reformatar esse valor e volte a aplicar as etiquetas existentes e novas:

```azurecli
jsonrtag=$(az resource show -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Para aplicar todas as etiquetas de um grupo de recursos para todos os respetivos recursos, *sem reter etiquetas existentes nos recursos*, utilize o script seguinte:

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    az resource tag --tags $t --id $resid
  done
done
```

Para aplicar todas as etiquetas de um grupo de recursos para seus próprios recursos, e *reter etiquetas existentes nos recursos*, utilize o seguinte script:

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    jsonrtag=$(az resource show --id $resid --query tags)
    rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
    az resource tag --tags $t$rt --id $resid
  done
done
```

## <a name="templates"></a>Modelos

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="portal"></a>Portal

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>API REST

O portal do Azure e o PowerShell utilizam o [a API de REST do Resource Manager](https://docs.microsoft.com/rest/api/resources/) em segundo plano. Se precisar de integrar a marcação para outro ambiente, pode obter etiquetas usando **obter** no ID de recurso e atualizar o conjunto de etiquetas utilizando um **aplicar o PATCH** chamar.

## <a name="tags-and-billing"></a>As etiquetas e faturação

Pode utilizar etiquetas para agrupar os dados de faturas. Por exemplo, se estiver a executar várias VMs para organizações diferentes, use as marcas para utilização do grupo pelo centro de custos. Também pode utilizar etiquetas para categorizar os custos ao ambiente de tempo de execução, como a utilização de faturação para VMs em execução no ambiente de produção.

Pode obter informações sobre etiquetas através do [utilização de recursos do Azure e RateCard APIs](../billing/billing-usage-rate-card-overview.md) ou o ficheiro de valores separados por vírgulas (CSV) de utilização. Transferir o ficheiro de utilização a partir do [Centro de contas do Azure](https://account.azure.com/Subscriptions) ou o portal do Azure. Para obter mais informações, consulte [baixe ou exiba a nota fiscal e diário de dados de utilização de faturação do Azure](../billing/billing-download-azure-invoice-daily-usage-date.md). Ao transferir o ficheiro de utilização a partir do Centro de contas do Azure, selecione **versão 2**. Para os serviços que suportam etiquetas com a faturação, as etiquetas aparecem na **etiquetas** coluna.

Para operações de REST API, consulte [referência de API de REST de faturação do Azure](/rest/api/billing/).

## <a name="next-steps"></a>Passos Seguintes

* Nem todos os tipos de recursos suportam etiquetas. Para determinar se pode aplicar uma etiqueta a um tipo de recurso, veja [marca o suporte para recursos do Azure](tag-support.md).
* Para uma introdução ao utilizar o portal, consulte [no portal do Azure para gerir os recursos do Azure](manage-resource-groups-portal.md).  
