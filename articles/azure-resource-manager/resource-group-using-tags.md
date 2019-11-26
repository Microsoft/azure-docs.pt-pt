---
title: Tag resources for logical organization
description: Shows how to apply tags to organize Azure resources for billing and managing.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: f3fca2030d33ba5a52d43924ff542801d435e4de
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484278"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>Utilizar etiquetas para organizar os recursos do Azure

[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

To apply tags to resources, the user must have write access to that resource type. To apply tags to all resource types, use the [Contributor](../role-based-access-control/built-in-roles.md#contributor) role. To apply tags to only one resource type, use the contributor role for that resource. For example, to apply tags to virtual machines, use the [Virtual Machine Contributor](../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="policies"></a>Políticas

You can use [Azure Policy](../governance/policy/overview.md) to enforce tagging rules and conventions. By creating a policy, you avoid the scenario of resources being deployed to your subscription that don't comply with the expected tags for your organization. Instead of manually applying tags or searching for resources that aren't compliant, you can create a policy that automatically applies the needed tags during deployment. Tags can also now be applied to existing resources with the new [Modify](../governance/policy/concepts/effects.md#modify) effect and a [remediation task](../governance/policy/how-to/remediate-resources.md). The following section shows example policies for tags.

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

To get *resources that have a specific tag name*, use:

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

To apply all tags from a resource group to its resources, and *not keep existing tags on the resources*, use the following script:

```azurepowershell-interactive
$groups = Get-AzResourceGroup
foreach ($g in $groups)
{
    Get-AzResource -ResourceGroupName $g.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

To apply all tags from a resource group to its resources, and *keep existing tags on resources that aren't duplicates*, use the following script:

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

Or, to see the existing tags for a *resource that has a specified name, type, and resource group*, use:

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

When looping through a collection of resources, you might want to show the resource by resource ID. A complete example is shown later in this article. Para ver as etiquetas existentes de um *recurso que tem um ID de recurso específico*, utilize:

```azurecli
az resource show --id <resource-id> --query tags
```

To get resource groups that have a specific tag, use `az group list`:

```azurecli
az group list --tag Dept=IT
```

To get all the resources that have a particular tag and value, use `az resource list`:

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

To add tags to a resource that already has tags, retrieve the existing tags, reformat that value, and reapply the existing and new tags:

```azurecli
jsonrtag=$(az resource show -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks" --query tags -o json)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

To apply all tags from a resource group to its resources, and *not keep existing tags on the resources*, use the following script:

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags -o json)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    az resource tag --tags $t --id $resid
  done
done
```

To apply all tags from a resource group to its resources, and *keep existing tags on resources*, use the following script:

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags -o json)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    jsonrtag=$(az resource show --id $resid --query tags -o json)
    rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
    az resource tag --tags $t$rt --id $resid
  done
done
```

## <a name="templates"></a>Modelos

To tag a resource during deployment, add the `tags` element to the resource you're deploying. Forneça o nome de etiqueta e o valor.

### <a name="apply-a-literal-value-to-the-tag-name"></a>Aplicar um valor literal ao nome da etiqueta

O exemplo seguinte mostra uma conta de armazenamento com duas etiquetas (`Dept` e `Environment`) que estão definidas com valores literais:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "Finance",
                "Environment": "Production"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

To set a tag to a datetime value, use the [utcNow function](resource-group-template-functions-string.md#utcnow).

### <a name="apply-an-object-to-the-tag-element"></a>Aplicar um objeto ao elemento da etiqueta

Pode definir um parâmetro de objeto que armazene várias etiquetas e aplicar esse objeto no elemento da etiqueta. Cada propriedade no objeto torna-se uma etiqueta separada para o recurso. O exemplo seguinte tem um parâmetro denominado `tagValues` que é aplicado no elemento da etiqueta.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "tagValues": {
            "type": "object",
            "defaultValue": {
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": "[parameters('tagValues')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-a-json-string-to-the-tag-name"></a>Aplicar uma cadeia JSON ao nome da etiqueta

Para armazenar muitos valores numa única etiqueta, aplique uma cadeia JSON que represente os valores. The entire JSON string is stored as one tag that can't exceed 256 characters. O exemplo seguinte tem uma única etiqueta com o nome `CostCenter` que contém vários valores de uma cadeia JSON:  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-from-resource-group"></a>Apply tags from resource group

To apply tags from a resource group to a resource, use the [resourceGroup](resource-group-template-functions-resource.md#resourcegroup) function. When getting the tag value, use the `tags.[tag-name]` syntax instead of the `tags.tag-name` syntax, because some characters aren't parsed correctly in the dot notation.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "[resourceGroup().tags['Dept']]",
                "Environment": "[resourceGroup().tags['Environment']]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

## <a name="portal"></a>Portal

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>API REST

The Azure portal and PowerShell both use the [Resource Manager REST API](https://docs.microsoft.com/rest/api/resources/) behind the scenes. If you need to integrate tagging into another environment, you can get tags by using **GET** on the resource ID and update the set of tags by using a **PATCH** call.

## <a name="tags-and-billing"></a>Tags and billing

You can use tags to group your billing data. For example, if you're running multiple VMs for different organizations, use the tags to group usage by cost center. You can also use tags to categorize costs by runtime environment, such as the billing usage for VMs running in the production environment.

You can retrieve information about tags through the [Azure Resource Usage and RateCard APIs](../billing/billing-usage-rate-card-overview.md) or the usage comma-separated values (CSV) file. You download the usage file from the [Azure Account Center](https://account.azure.com/Subscriptions) or Azure portal. For more information, see [Download or view your Azure billing invoice and daily usage data](../billing/billing-download-azure-invoice-daily-usage-date.md). When downloading the usage file from the Azure Account Center, select **Version 2**. For services that support tags with billing, the tags appear in the **Tags** column.

For REST API operations, see [Azure Billing REST API Reference](/rest/api/billing/).

## <a name="next-steps"></a>Passos seguintes

* Not all resource types support tags. To determine if you can apply a tag to a resource type, see [Tag support for Azure resources](tag-support.md).
* For an introduction to using the portal, see [Using the Azure portal to manage your Azure resources](manage-resource-groups-portal.md).  
