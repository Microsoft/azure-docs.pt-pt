---
title: Marcar recursos para a organização lógica
description: Mostra como aplicar marcas para organizar recursos do Azure para cobrança e gerenciamento.
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 0b2fff801b01afce0907bf86887fb110478377c1
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2020
ms.locfileid: "75665167"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>Utilizar etiquetas para organizar os recursos do Azure

Você aplica marcas aos recursos do Azure para organizá-los logicamente em uma taxonomia. Cada marca consiste em um par de nome e valor. Por exemplo, pode aplicar o nome "Ambiente" e o valor "Produção" em todos os recursos na produção.

Depois de aplicar etiquetas, pode obter todos os recursos na sua subscrição com este nome e este valor da etiqueta. As etiquetas permitem-lhe obter recursos relacionados de grupos de recursos diferentes. Esta abordagem é útil quando precisa de organizar os recursos de gestão ou de faturação.

Sua taxonomia deve considerar uma estratégia de marcação de metadados de autoatendimento, além de uma estratégia de marcação automática para reduzir a carga sobre os usuários e aumentar a precisão.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="limitations"></a>Limitações

As seguintes limitações aplicam-se às etiquetas:

* Nem todos os tipos de recurso dão suporte a marcas. Para determinar se você pode aplicar uma marca a um tipo de recurso, consulte [suporte a marcas para recursos do Azure](tag-support.md).
* Cada recurso ou grupo de recursos pode ter um máximo de 50 pares de nome/valor de marca. Se você precisar aplicar mais marcas do que o número máximo permitido, use uma cadeia de caracteres JSON para o valor da marca. A cadeia JSON pode conter muitos valores que são aplicados a um nome de etiqueta individual. Um grupo de recursos pode conter muitos recursos que têm um número de 50 pares de nome/valor de marca.
* O nome de etiqueta está limitado a 512 caracteres e o valor a 256. Nas contas de armazenamento, o nome da etiqueta está limitado a 128 caracteres e o valor a 256.
* As VMs generalizadas não dão suporte a marcas.
* As etiquetas aplicadas ao grupo de recursos não são herdadas pelos recursos nesse grupo de recursos.
* As marcas não podem ser aplicadas a recursos clássicos, como serviços de nuvem.
* Os nomes de marca não podem conter estes caracteres: `<`, `>`, `%`, `&`, `\`, `?`, `/`

   > [!NOTE]
   > Atualmente, as zonas DNS do Azure e os serviços do Gerenciador de tráfego também não permitem o uso de espaços na marca. 

## <a name="required-access"></a>Acesso necessário

Para aplicar marcas aos recursos, o usuário deve ter acesso de gravação a esse tipo de recurso. Para aplicar marcas a todos os tipos de recursos, use a função [colaborador](../../role-based-access-control/built-in-roles.md#contributor) . Para aplicar marcas a apenas um tipo de recurso, use a função de colaborador para esse recurso. Por exemplo, para aplicar marcas a máquinas virtuais, use o [colaborador da máquina virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

## <a name="policies"></a>Políticas

Você pode usar [Azure Policy](../../governance/policy/overview.md) para impor regras e convenções de marcação. Ao criar uma política, você evita o cenário dos recursos que estão sendo implantados em sua assinatura que não estão em conformidade com as marcas esperadas para sua organização. Em vez de aplicar marcas manualmente ou procurar recursos que não são compatíveis, você pode criar uma política que aplica automaticamente as marcas necessárias durante a implantação. Agora, as marcas também podem ser aplicadas a recursos existentes com o novo efeito [Modificar](../../governance/policy/concepts/effects.md#modify) e uma [tarefa de correção](../../governance/policy/how-to/remediate-resources.md). A seção a seguir mostra as políticas de exemplo para marcas.

[!INCLUDE [Tag policies](../../../includes/azure-policy-samples-general-tags.md)]

## <a name="powershell"></a>PowerShell

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

Para ver as marcas existentes de um *recurso que tem um nome e um grupo de recursos especificados*, use:

```azurepowershell-interactive
(Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

Ou, se você tiver a ID de recurso para um recurso, poderá passar essa ID de recurso para obter as marcas.

```azurepowershell-interactive
(Get-AzResource -ResourceId /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>).Tags
```

Para obter os *grupos de recursos que têm um nome e valor de marca específicos*, use:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "Dept"="Finance" }).ResourceGroupName
```

Para obter *recursos que têm um nome e valor de marca específicos*, use:

```azurepowershell-interactive
(Get-AzResource -Tag @{ "Dept"="Finance"}).Name
```

Para obter *recursos que têm um nome de marca específico*, use:

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

Sempre que aplicar etiquetas a um recurso ou grupo de recursos, as etiquetas existentes nesse recurso ou grupo de recursos são substituídas. Por conseguinte, tem de utilizar uma abordagem diferente, que tenha em conta se o recurso ou grupo de recursos tem etiquetas existentes.

Para adicionar etiquetas a um *grupo de recursos que não tenha etiquetas*, utilize:

```azurepowershell-interactive
Set-AzResourceGroup -Name examplegroup -Tag @{ "Dept"="IT"; "Environment"="Test" }
```

Para adicionar etiquetas a um *grupo de recursos que tenha etiquetas existentes*, obtenha-as, adicione a etiqueta nova e volte a aplicar as etiquetas:

```azurepowershell-interactive
$tags = (Get-AzResourceGroup -Name examplegroup).Tags
$tags.Add("Status", "Approved")
Set-AzResourceGroup -Tag $tags -Name examplegroup
```

Para adicionar etiquetas a um *recurso que não tenha etiquetas*, utilize:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzResource -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $resource.ResourceId -Force
```

Você pode ter mais de um recurso com o mesmo nome em um grupo de recursos. Nesse caso, você pode definir cada recurso com os seguintes comandos:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Set-AzResource -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Force }
```

Para adicionar etiquetas a um *recurso que tenha etiquetas*, utilize:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
$resource.Tags.Add("Status", "Approved")
Set-AzResource -Tag $resource.Tags -ResourceId $resource.ResourceId -Force
```

Para aplicar todas as marcas de um grupo de recursos a seus recursos e *não manter as marcas existentes nos recursos*, use o seguinte script:

```azurepowershell-interactive
$group = Get-AzResourceGroup -Name examplegroup
Get-AzResource -ResourceGroupName $group.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag $group.Tags -Force }
```

Para aplicar todas as marcas de um grupo de recursos a seus recursos e *manter as marcas existentes nos recursos que não são duplicatas*, use o seguinte script:

```azurepowershell-interactive
$group = Get-AzResourceGroup -Name examplegroup
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

```azurecli-interactive
az group show -n examplegroup --query tags
```

Este script devolve o formato seguinte:

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

Ou, para ver as marcas existentes de um *recurso que tem um nome, tipo e grupo de recursos especificados*, use:

```azurecli-interactive
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

Ao executar um loop em uma coleção de recursos, talvez você queira mostrar o recurso por ID de recurso. Um exemplo completo é mostrado posteriormente neste artigo. Para ver as etiquetas existentes de um *recurso que tem um ID de recurso específico*, utilize:

```azurecli-interactive
az resource show --id <resource-id> --query tags
```

Para obter os grupos de recursos que têm uma marca específica, use `az group list`:

```azurecli-interactive
az group list --tag Dept=IT
```

Para obter todos os recursos que têm uma marca e um valor específicos, use `az resource list`:

```azurecli-interactive
az resource list --tag Dept=Finance
```

Ao adicionar marcas a um grupo de recursos ou recurso, você pode substituir as marcas existentes ou acrescentar novas marcas a marcas existentes.

Para substituir as marcas existentes em um grupo de recursos, use:

```azurecli-interactive
az group update -n examplegroup --tags 'Environment=Test' 'Dept=IT'
```

Para acrescentar uma marca às marcas existentes em um grupo de recursos, use:

```azurecli-interactive
az group update -n examplegroup --set tags.'Status'='Approved'
```

Para substituir as marcas em um recurso, use:

```azurecli-interactive
az resource tag --tags 'Dept=IT' 'Environment=Test' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Para acrescentar uma marca às marcas existentes em um recurso, use:

```azurecli-interactive
az resource update --set tags.'Status'='Approved' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Para aplicar todas as marcas de um grupo de recursos a seus recursos e *não manter as marcas existentes nos recursos*, use o seguinte script:

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '"{},' | sed 's/: /=/g')
resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  az resource tag --tags $tags --id $id
done
```

Para aplicar todas as marcas de um grupo de recursos a seus recursos e *manter as marcas existentes nos recursos*, use o seguinte script:

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '"{},' | sed 's/: /=/g')

resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  resourcejsontags=$(az resource show --id $id --query tags -o json)
  resourcetags=$(echo $resourcejsontags | tr -d '"{},' | sed 's/: /=/g')
  az resource tag --tags $tags$resourcetags --id $id
done
```

Se os nomes ou valores de marcação incluírem espaços, você deverá executar algumas etapas adicionais. O exemplo a seguir aplica todas as marcas de um grupo de recursos a seus recursos quando as marcas podem conter espaços.

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '{}"' | sed 's/: /=/g' | sed "s/\"/'/g" | sed 's/, /,/g' | sed 's/ *$//g' | sed 's/^ *//g')
origIFS=$IFS
IFS=','
read -a tagarr <<< "$tags"
resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  az resource tag --tags "${tagarr[@]}" --id $id
done
IFS=$origIFS
```

## <a name="templates"></a>Modelos

Para marcar um recurso durante a implantação, adicione o elemento `tags` ao recurso que você está implantando. Forneça o nome de etiqueta e o valor.

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

Para definir uma marca para um valor DateTime, use a [função UtcNow](../templates/template-functions-string.md#utcnow).

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

Para armazenar muitos valores numa única etiqueta, aplique uma cadeia JSON que represente os valores. A cadeia de caracteres JSON inteira é armazenada como uma marca que não pode exceder 256 caracteres. O exemplo seguinte tem uma única etiqueta com o nome `CostCenter` que contém vários valores de uma cadeia JSON:  

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

### <a name="apply-tags-from-resource-group"></a>Aplicar marcas do grupo de recursos

Para aplicar marcas de um grupo de recursos a um recurso, use a função [resourcegroup](../templates/template-functions-resource.md#resourcegroup) . Ao obter o valor da marca, use a sintaxe `tags.[tag-name]` em vez da sintaxe `tags.tag-name`, porque alguns caracteres não são analisados corretamente na notação de ponto.

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

[!INCLUDE [resource-manager-tag-resource](../../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>API REST

O portal do Azure e o PowerShell usam a [API REST do Gerenciador de recursos](/rest/api/resources/) em segundo plano. Se você precisar integrar a marcação em outro ambiente, poderá obter marcas usando **Get** na ID do recurso e atualizar o conjunto de marcas usando uma chamada de **patch** .

## <a name="tags-and-billing"></a>Marcas e cobrança

Pode utilizar etiquetas para agrupar os seus dados de faturação. Por exemplo, se estiver a executar múltiplas VMs para organizações diferentes, utilize as etiquetas para agrupar a utilização por centro de custos. Também pode utilizar etiquetas para categorizar os custos por ambiente de runtime, tal como a utilização de faturação das VMs executadas no ambiente de produção.

Você pode recuperar informações sobre marcas por meio de [APIs de RateCard e uso de recursos do Azure](../../billing/billing-usage-rate-card-overview.md) ou o arquivo CSV (valores separados por vírgula) de uso. Você baixa o arquivo de uso do [centro de contas do Azure](https://account.azure.com/Subscriptions) ou portal do Azure. Para obter mais informações, consulte [baixar ou exibir sua fatura de cobrança do Azure e dados de uso diário](../../billing/billing-download-azure-invoice-daily-usage-date.md). Ao baixar o arquivo de uso do Centro de Contas do Azure, selecione **versão 2**. Para serviços que dão suporte a marcas com cobrança, as marcas aparecem na coluna **marcas** .

Para operações da API REST, consulte [referência da API REST de cobrança do Azure](/rest/api/billing/).

## <a name="next-steps"></a>Passos seguintes

* Nem todos os tipos de recurso dão suporte a marcas. Para determinar se você pode aplicar uma marca a um tipo de recurso, consulte [suporte a marcas para recursos do Azure](tag-support.md).
* Para obter uma introdução ao uso do portal, consulte [usando o portal do Azure para gerenciar seus recursos do Azure](manage-resource-groups-portal.md).  
