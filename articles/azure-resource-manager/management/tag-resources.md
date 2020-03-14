---
title: Recursos de etiqueta para organização lógica
description: Mostra como aplicar tags para organizar recursos Azure para faturação e gestão.
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: c7f8d8672e205fa677bff33c8ed173c1105b26c6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274505"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>Use tags para organizar os seus recursos Azure

Aplica etiquetas aos seus recursos Azure para logicamente organizá-las numa taxonomia. Cada etiqueta consiste num nome e num par de valor. Por exemplo, pode aplicar o nome "Ambiente" e o valor "Produção" em todos os recursos na produção.

Depois de aplicar etiquetas, pode obter todos os recursos na sua subscrição com este nome e este valor da etiqueta. As etiquetas permitem-lhe recuperar recursos relacionados de diferentes grupos de recursos. Esta abordagem é útil quando precisa de organizar os recursos de gestão ou de faturação.

A sua taxonomia deve considerar uma estratégia de marcação de metadados de autosserviço, além de uma estratégia de marcação automática para reduzir os encargos para os utilizadores e aumentar a precisão.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="limitations"></a>Limitações

As seguintes limitações aplicam-se às etiquetas:

* Nem todos os tipos de recursos suportam tags. Para determinar se pode aplicar uma etiqueta a um tipo de recurso, consulte o [suporte da Tag para os recursos Azure](tag-support.md).
* Cada grupo de recursos ou recursos pode ter um máximo de 50 pares de nome/valor de 50 tags. Se precisar aplicar mais etiquetas do que o número máximo permitido, utilize uma corda JSON para o valor da etiqueta. A cadeia JSON pode conter muitos valores que são aplicados a um nome de etiqueta individual. Um grupo de recursos pode conter muitos recursos que cada um tem 50 pares de nome/valor de etiquetas.
* O nome de etiqueta está limitado a 512 caracteres e o valor a 256. Nas contas de armazenamento, o nome da etiqueta está limitado a 128 caracteres e o valor a 256.
* VMs generalizados não suportam etiquetas.
* As etiquetas aplicadas ao grupo de recursos não são herdadas pelos recursos nesse grupo de recursos.
* As etiquetas não podem ser aplicadas a recursos clássicos, como os Serviços cloud.
* Os nomes de etiquetas não podem conter estes caracteres: `<`, `>`, `%`, `&`, `\`, `?`, `/`

   > [!NOTE]
   > Atualmente, as zonas DeDNs azure e os serviços de Traffic Manger também não permitem a utilização de espaços na etiqueta. 

## <a name="required-access"></a>Acesso obrigatório

Para aplicar etiquetas aos recursos, o utilizador deve ter acesso a esse tipo de recursos. Para aplicar etiquetas a todos os tipos de recursos, utilize a função [Contributiva.](../../role-based-access-control/built-in-roles.md#contributor) Para aplicar etiquetas a apenas um tipo de recurso, utilize a função de contribuinte para esse recurso. Por exemplo, para aplicar etiquetas em máquinas virtuais, utilize o Colaborador da [Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

## <a name="policies"></a>Políticas

Pode usar a [Política Azure](../../governance/policy/overview.md) para impor regras e convenções de marcação. Ao criar uma política, evita o cenário de recursos que estão a ser implantados na sua subscrição que não cumprem as etiquetas esperadas para a sua organização. Em vez de aplicar manualmente tags ou procurar recursos que não sejam compatíveis, pode criar uma política que aplique automaticamente as etiquetas necessárias durante a implementação. As etiquetas também podem agora ser aplicadas aos recursos existentes com o novo efeito [Modificar](../../governance/policy/concepts/effects.md#modify) e uma tarefa de [reparação.](../../governance/policy/how-to/remediate-resources.md) A secção seguinte mostra políticas de exemplo para tags.

[!INCLUDE [Tag policies](../../../includes/azure-policy-samples-policies-tags.md)]

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

Para ver as etiquetas existentes para um recurso que tenha um nome e um grupo de *recursos especificados,* utilize:

```azurepowershell-interactive
(Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

Ou, se tiver o ID de recurso para um recurso, pode passar essa identificação de recursos para obter as etiquetas.

```azurepowershell-interactive
(Get-AzResource -ResourceId /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>).Tags
```

Para obter grupos de *recursos que tenham um nome e valor específicos,* use:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "Dept"="Finance" }).ResourceGroupName
```

Para obter *recursos que tenham um nome e valor específicos,* use:

```azurepowershell-interactive
(Get-AzResource -Tag @{ "Dept"="Finance"}).Name
```

Para obter *recursos que tenham um nome de etiqueta específico,* use:

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

Pode ter mais de um recurso com o mesmo nome num grupo de recursos. Nesse caso, pode definir cada recurso com os seguintes comandos:

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

Para aplicar todas as etiquetas de um grupo de recursos aos seus recursos, e *não manter as etiquetas existentes nos recursos,* utilize o seguinte script:

```azurepowershell-interactive
$group = Get-AzResourceGroup -Name examplegroup
Get-AzResource -ResourceGroupName $group.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag $group.Tags -Force }
```

Para aplicar todas as etiquetas de um grupo de recursos aos seus recursos, e *manter as etiquetas existentes em recursos que não sejam duplicados,* use o seguinte script:

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

Ou, para ver as etiquetas existentes para um recurso que tenha um nome, tipo e grupo de *recursos especificados,* utilize:

```azurecli-interactive
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

Ao passar por uma coleção de recursos, é melhor mostrar o recurso por id de recursos. Um exemplo completo é mostrado mais tarde neste artigo. Para ver as etiquetas existentes de um *recurso que tem um ID de recurso específico*, utilize:

```azurecli-interactive
az resource show --id <resource-id> --query tags
```

Para obter grupos de recursos que tenham uma etiqueta específica, use `az group list`:

```azurecli-interactive
az group list --tag Dept=IT
```

Para obter todos os recursos que tenham uma etiqueta e valor particulares, use `az resource list`:

```azurecli-interactive
az resource list --tag Dept=Finance
```

Ao adicionar tags a um grupo de recursos ou recurso, pode substituir as etiquetas existentes ou anexar novas etiquetas às etiquetas existentes.

Para substituir as etiquetas existentes num grupo de recursos, utilize:

```azurecli-interactive
az group update -n examplegroup --tags 'Environment=Test' 'Dept=IT'
```

Para anexar uma etiqueta às etiquetas existentes num grupo de recursos, utilize:

```azurecli-interactive
az group update -n examplegroup --set tags.'Status'='Approved'
```

Para substituir as etiquetas num recurso, utilize:

```azurecli-interactive
az resource tag --tags 'Dept=IT' 'Environment=Test' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Para anexar uma etiqueta às etiquetas existentes num recurso, utilize:

```azurecli-interactive
az resource update --set tags.'Status'='Approved' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Para aplicar todas as etiquetas de um grupo de recursos aos seus recursos, e *não manter as etiquetas existentes nos recursos,* utilize o seguinte script:

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '"{},' | sed 's/: /=/g')
resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  az resource tag --tags $tags --id $id
done
```

Para aplicar todas as etiquetas de um grupo de recursos aos seus recursos, e *manter as etiquetas existentes nos recursos,* utilize o seguinte script:

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

Se os seus nomes ou valores incluem espaços, deve dar alguns passos extra. O exemplo seguinte aplica todas as etiquetas de um grupo de recursos aos seus recursos quando as etiquetas podem conter espaços.

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

Para marcar um recurso durante a implementação, adicione o elemento `tags` ao recurso que está a implantar. Forneça o nome de etiqueta e o valor.

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

Para definir uma etiqueta para um valor de data, utilize a [função utcNow](../templates/template-functions-string.md#utcnow).

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

Para armazenar muitos valores numa única etiqueta, aplique uma cadeia JSON que represente os valores. Toda a cadeia JSON é armazenada como uma etiqueta que não pode exceder 256 caracteres. O exemplo seguinte tem uma única etiqueta com o nome `CostCenter` que contém vários valores de uma cadeia JSON:  

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

### <a name="apply-tags-from-resource-group"></a>Aplicar etiquetas do grupo de recursos

Para aplicar etiquetas de um grupo de recursos a um recurso, utilize a função [resourceGroup.](../templates/template-functions-resource.md#resourcegroup) Ao obter o valor da etiqueta, use a sintaxe `tags[tag-name]` em vez da sintaxe `tags.tag-name`, porque alguns caracteres não são analisados corretamente na notação do ponto.

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

O portal Azure e a PowerShell usam ambos a [API REST API do Gestor](/rest/api/resources/) de Recursos nos bastidores. Se precisar de integrar a marcação noutro ambiente, pode obter etiquetas utilizando o **GET** no ID de recurso e atualizar o conjunto de tags utilizando uma chamada **PATCH.**

## <a name="tags-and-billing"></a>Etiquetas e faturação

Pode utilizar etiquetas para agrupar os seus dados de faturação. Por exemplo, se estiver a executar vários VMs para diferentes organizações, use as etiquetas para agrupar o uso por centro de custos. Também pode usar etiquetas para categorizar custos através de um ambiente de tempo de funcionamento, como o uso de faturação para VMs em funcionamento no ambiente de produção.

Pode obter informações sobre etiquetas através do ficheiro [De utilização de recursos Azure e RateCard APIs](../../billing/billing-usage-rate-card-overview.md) ou do ficheiro de valores separados pela vírposta (CSV). Você descarrega o ficheiro de uso do Centro de [Conta Azure](https://account.azure.com/Subscriptions) ou do portal Azure. Para mais informações, consulte O Download ou veja a sua fatura de [faturação Do Azure e os dados de utilização diária](../../billing/billing-download-azure-invoice-daily-usage-date.md). Ao descarregar o ficheiro de utilização do Centro de Conta Azure, selecione **versão 2**. Para serviços que suportam etiquetas com faturação, as etiquetas aparecem na coluna **Tags.**

Para operações rest API, consulte [Azure Billing REST API Reference](/rest/api/billing/).

## <a name="next-steps"></a>Passos seguintes

* Nem todos os tipos de recursos suportam tags. Para determinar se pode aplicar uma etiqueta a um tipo de recurso, consulte o [suporte da Tag para os recursos Azure](tag-support.md).
* Para uma introdução à utilização do portal, consulte [Utilizar o portal Azure para gerir os seus recursos Azure.](manage-resource-groups-portal.md)  
