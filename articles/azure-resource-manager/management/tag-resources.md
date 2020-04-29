---
title: Recursos de etiquetas, grupos de recursos e subscrições para organização lógica
description: Mostra como aplicar tags para organizar recursos Azure para faturação e gestão.
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: 2f437682a2ac415ce8478b09a44bff044bd9511b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81255129"
---
# <a name="use-tags-to-organize-your-azure-resources-and-management-hierarchy"></a>Use tags para organizar os seus recursos Azure e hierarquia de gestão

Você aplica etiquetas aos seus recursos Azure, grupos de recursos e subscrições para logicamente organizá-las em uma taxonomia. Cada etiqueta consiste num nome e num par de valor. Por exemplo, pode aplicar o nome "Ambiente" e o valor "Produção" em todos os recursos na produção.

Para recomendações sobre como implementar uma estratégia de marcação, consulte o guia de decisão de [nomeação e marcação](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)de recursos.

> [!IMPORTANT]
> Os nomes das etiquetas são insensíveis aos casos. Os valores das etiquetas são sensíveis aos casos.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="required-access"></a>Acesso obrigatório

Para aplicar etiquetas a um recurso, tem de ter acesso ao tipo de recursos **Microsoft.Resources/tags.** A função [Tag Contributor](../../role-based-access-control/built-in-roles.md#tag-contributor) permite aplicar tags a uma entidade sem ter acesso à própria entidade. Atualmente, a função de colaborador de etiquetas não pode aplicar tags a recursos ou grupos de recursos através do portal. Pode aplicar etiquetas a subscrições através do portal. Suporta todas as operações de marcação através da PowerShell e da REST API.  

O papel [de Contribuinte](../../role-based-access-control/built-in-roles.md#contributor) também concede o acesso necessário à aplicação de etiquetas a qualquer entidade. Para aplicar etiquetas a apenas um tipo de recurso, utilize a função de contribuinte para esse recurso. Por exemplo, para aplicar etiquetas em máquinas virtuais, utilize o Colaborador da [Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

## <a name="powershell"></a>PowerShell

### <a name="apply-tags"></a>Aplicar etiquetas

A Azure PowerShell oferece dois comandos para a aplicação de tags - [New-AzTag](/powershell/module/az.resources/new-aztag) e [Update-AzTag](/powershell/module/az.resources/update-aztag). Deve ter o módulo Az.Resources 1.12.0 ou mais tarde. Pode verificar a `Get-Module Az.Resources`sua versão com . Pode instalar esse módulo ou [instalar o Azure PowerShell](/powershell/azure/install-az-ps) 3.6.1 ou mais tarde.

O **New-AzTag** substitui todas as etiquetas no recurso, grupo de recursos ou subscrição. Ao ligar para o comando, passe a identificação de recursos da entidade que pretende marcar.

O seguinte exemplo aplica um conjunto de etiquetas a uma conta de armazenamento:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
New-AzTag -ResourceId $resource.id -Tag $tags
```

Quando o comando estiver concluído, note que o recurso tem duas etiquetas.

```output
Properties :
        Name    Value
        ======  =======
        Dept    Finance
        Status  Normal
```

Se voltar a executar o comando, mas desta vez com etiquetas diferentes, note que as etiquetas anteriores são removidas.

```azurepowershell-interactive
$tags = @{"Team"="Compliance"; "Environment"="Production"}
New-AzTag -ResourceId $resource.id -Tag $tags
```

```output
Properties :
        Name         Value
        ===========  ==========
        Environment  Production
        Team         Compliance
```

Para adicionar tags a um recurso que já tem tags, utilize **o Update-AzTag**. Defina o parâmetro **de funcionamento** para **fundir**.

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

Note que as duas novas etiquetas foram adicionadas às duas etiquetas existentes.

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Normal
        Dept         Finance
        Team         Compliance
        Environment  Production
```

Cada nome de etiqueta pode ter apenas um valor. Se fornecer um novo valor para uma etiqueta, o valor antigo é substituído mesmo que utilize a operação de fusão. O exemplo seguinte altera a etiqueta de Estado de Normal para Verde.

```azurepowershell-interactive
$tags = @{"Status"="Green"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Green
        Dept         Finance
        Team         Compliance
        Environment  Production
```

Quando definir o parâmetro **de funcionamento** para **substituir,** as etiquetas existentes são substituídas pelo novo conjunto de etiquetas.

```azurepowershell-interactive
$tags = @{"Project"="ECommerce"; "CostCenter"="00123"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Replace
```

Apenas as novas etiquetas permanecem no recurso.

```output
Properties :
        Name        Value
        ==========  =========
        CostCenter  00123
        Team        Web
        Project     ECommerce
```

Os mesmos comandos também funcionam com grupos de recursos ou subscrições. Você passa no identificador para o grupo de recursos ou subscrição que você quer marcar.

Para adicionar um novo conjunto de tags a um grupo de recursos, utilize:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
New-AzTag -ResourceId $resourceGroup.ResourceId -tag $tags
```

Para atualizar as etiquetas para um grupo de recursos, utilize:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Production"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Update-AzTag -ResourceId $resourceGroup.ResourceId -Tag $tags -Operation Merge
```

Para adicionar um novo conjunto de tags a uma subscrição, utilize:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Dev"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
New-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags
```

Para atualizar as etiquetas para uma subscrição, utilize:

```azurepowershell-interactive
$tags = @{"Team"="Web Apps"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Update-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags -Operation Merge
```

Pode ter mais de um recurso com o mesmo nome num grupo de recursos. Nesse caso, pode definir cada recurso com os seguintes comandos:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Update-AzTag -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Operation Merge }
```

### <a name="list-tags"></a>Listar etiquetas

Para obter as etiquetas para um recurso, grupo de recursos ou subscrição, use o comando [Get-AzTag](/powershell/module/az.resources/get-aztag) e passe no ID de recurso para a entidade.

Para ver as etiquetas para um recurso, utilize:

```azurepowershell-interactive
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
Get-AzTag -ResourceId $resource.id
```

Para ver as etiquetas para um grupo de recursos, utilize:

```azurepowershell-interactive
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Get-AzTag -ResourceId $resourceGroup.ResourceId
```

Para ver as etiquetas para uma subscrição, utilize:

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Get-AzTag -ResourceId "/subscriptions/$subscription"
```

### <a name="list-by-tag"></a>Lista por tag

Para obter recursos que tenham um nome e valor específicos, use:

```azurepowershell-interactive
(Get-AzResource -Tag @{ "CostCenter"="00123"}).Name
```

Para obter recursos que tenham um nome de etiqueta específico com qualquer valor de etiqueta, use:

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

Para obter grupos de recursos que tenham um nome e valor específicos, use:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "CostCenter"="00123" }).ResourceGroupName
```

### <a name="remove-tags"></a>Remover etiquetas

Para remover etiquetas específicas, utilize o **Update-AzTag** e deset **-Operação** para **Eliminar**. Passe nas etiquetas que pretende apagar.

```azurepowershell-interactive
$removeTags = @{"Project"="ECommerce"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $removeTags -Operation Delete
```

As etiquetas especificadas são removidas.

```output
Properties :
        Name        Value
        ==========  =====
        CostCenter  00123
```

Para remover todas as etiquetas, utilize o comando [Remove-AzTag.](/powershell/module/az.resources/remove-aztag)

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Remove-AzTag -ResourceId "/subscriptions/$subscription"
```

## <a name="azure-cli"></a>CLI do Azure

### <a name="apply-tags"></a>Aplicar etiquetas

Ao adicionar tags a um grupo de recursos ou recurso, pode substituir as etiquetas existentes ou anexar novas etiquetas às etiquetas existentes.

Para substituir as etiquetas num recurso, utilize:

```azurecli-interactive
az resource tag --tags 'Dept=IT' 'Environment=Test' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Para anexar uma etiqueta às etiquetas existentes num recurso, utilize:

```azurecli-interactive
az resource update --set tags.'Status'='Approved' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Para substituir as etiquetas existentes num grupo de recursos, utilize:

```azurecli-interactive
az group update -n examplegroup --tags 'Environment=Test' 'Dept=IT'
```

Para anexar uma etiqueta às etiquetas existentes num grupo de recursos, utilize:

```azurecli-interactive
az group update -n examplegroup --set tags.'Status'='Approved'
```

Atualmente, o Azure CLI não suporta a aplicação de tags a subscrições.

### <a name="list-tags"></a>Listar etiquetas

Para ver as etiquetas existentes para um recurso, utilize:

```azurecli-interactive
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

Para ver as etiquetas existentes de um grupo de recursos, utilize:

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

### <a name="list-by-tag"></a>Lista por tag

Para obter todos os recursos que tenham `az resource list`uma etiqueta e valor particulares, use:

```azurecli-interactive
az resource list --tag Dept=Finance
```

Para obter grupos de recursos `az group list`que tenham uma etiqueta específica, use:

```azurecli-interactive
az group list --tag Dept=IT
```

### <a name="handling-spaces"></a>Manuseamento de espaços

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

Você pode marcar recursos, grupos de recursos e subscrições durante a implementação com um modelo de Gestor de Recursos.

### <a name="apply-values"></a>Aplicar valores

O exemplo seguinte implementa uma conta de armazenamento com três etiquetas. Duas das etiquetas`Dept` `Environment`( e) estão definidas para valores literais. Uma etiqueta`LastDeployed`( ) é definida para um parâmetro que se incorre na data atual.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
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
                "Environment": "Production",
                "LastDeployed": "[parameters('utcShort')]"
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

### <a name="apply-an-object"></a>Aplicar um objeto

Pode definir um parâmetro de objeto que armazene várias etiquetas e aplicar esse objeto no elemento da etiqueta. Esta abordagem proporciona mais flexibilidade do que o exemplo anterior porque o objeto pode ter propriedades diferentes. Cada propriedade no objeto torna-se uma etiqueta separada para o recurso. O exemplo seguinte tem um parâmetro denominado `tagValues` que é aplicado no elemento da etiqueta.

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

### <a name="apply-a-json-string"></a>Aplique uma corda JSON

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

Para aplicar etiquetas de um grupo de recursos a um recurso, utilize a função [resourceGroup.](../templates/template-functions-resource.md#resourcegroup) Ao obter o valor `tags[tag-name]` da etiqueta, use `tags.tag-name` a sintaxe em vez da sintaxe, porque alguns caracteres não são analisados corretamente na notação do ponto.

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

### <a name="apply-tags-to-resource-groups-or-subscriptions"></a>Aplicar etiquetas a grupos de recursos ou subscrições

Pode adicionar tags a um grupo de recursos ou subscrição, implementando o tipo de recursos **Microsoft.Resources/tags.** As etiquetas são aplicadas ao grupo de recursos-alvo ou à subscrição para a implementação. Cada vez que implementa o modelo, substitui quaisquer etiquetas que foram aplicadas anteriormente.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tagName": {
            "type": "string",
            "defaultValue": "TeamName"
        },
        "tagValue": {
            "type": "string",
            "defaultValue": "AppTeam1"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": {
                    "[parameters('tagName')]": "[parameters('tagValue')]"
                }
            }
        }
    ]
}
```

Para aplicar as etiquetas a um grupo de recursos, utilize o PowerShell ou o Azure CLI. Desloque-se para o grupo de recursos que pretende marcar.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName exampleGroup -TemplateFile https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment group create --resource-group exampleGroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

Para aplicar as etiquetas a uma subscrição, utilize powerShell ou Azure CLI. Desloque-se para a subscrição que pretende marcar.

```azurepowershell-interactive
New-AzSubscriptionDeployment -name tagresourcegroup -Location westus2 -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment sub create --name tagresourcegroup --location westus2 --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

O modelo seguinte adiciona as etiquetas de um objeto a um grupo de recursos ou subscrição.

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tags": {
            "type": "object",
            "defaultValue": {
                "TeamName": "AppTeam1",
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": "[parameters('tags')]"
            }
        }
    ]
}
```

## <a name="portal"></a>Portal

[!INCLUDE [resource-manager-tag-resource](../../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>API REST

Para trabalhar com etiquetas através da API De REPOUSO Azure, utilize:

* [Tags - Criar ou atualizar no âmbito](/rest/api/resources/tags/createorupdateatscope) (operação PUT)
* [Tags - Atualização no Âmbito](/rest/api/resources/tags/updateatscope) (operação PATCH)
* [Tags - Get At Scope](/rest/api/resources/tags/getatscope) (operação GET)
* [Tags - Eliminar no âmbito](/rest/api/resources/tags/deleteatscope) (OPERAÇÃO DELETE)

## <a name="inherit-tags"></a>Etiquetas herdadas

As etiquetas aplicadas ao grupo de recursos ou subscrição não são herdadas pelos recursos. Para aplicar etiquetas de um grupo de subscrição ou de recursos aos recursos, consulte [políticas do Azure - tags](tag-policies.md).

## <a name="tags-and-billing"></a>Etiquetas e faturação

Pode utilizar etiquetas para agrupar os seus dados de faturação. Por exemplo, se estiver a executar múltiplas VMs para organizações diferentes, utilize as etiquetas para agrupar a utilização por centro de custos. Também pode utilizar etiquetas para categorizar os custos por ambiente de runtime, tal como a utilização de faturação das VMs executadas no ambiente de produção.

Pode obter informações sobre etiquetas através do ficheiro [De utilização de recursos Azure e RateCard APIs](../../billing/billing-usage-rate-card-overview.md) ou do ficheiro de valores separados pela vírposta (CSV). Você descarrega o ficheiro de uso do Centro de [Conta Azure](https://account.azure.com/Subscriptions) ou do portal Azure. Para mais informações, consulte O Download ou veja a sua fatura de [faturação Do Azure e os dados de utilização diária](../../billing/billing-download-azure-invoice-daily-usage-date.md). Ao descarregar o ficheiro de utilização do Centro de Conta Azure, selecione **versão 2**. Para serviços que suportam etiquetas com faturação, as etiquetas aparecem na coluna **Tags.**

Para operações rest API, consulte [Azure Billing REST API Reference](/rest/api/billing/).

## <a name="limitations"></a>Limitações

As seguintes limitações aplicam-se às etiquetas:

* Nem todos os tipos de recursos suportam tags. Para determinar se pode aplicar uma etiqueta a um tipo de recurso, consulte o [suporte da Tag para os recursos Azure](tag-support.md).
* Atualmente, os grupos de gestão não suportam etiquetas.
* Cada recurso, grupo de recursos e subscrição pode ter um máximo de 50 pares de nome/valor de etiquetas. Se precisar aplicar mais etiquetas do que o número máximo permitido, utilize uma corda JSON para o valor da etiqueta. A cadeia JSON pode conter muitos valores que são aplicados a um nome de etiqueta individual. Um grupo de recursos ou subscrição pode conter muitos recursos que cada um tem 50 pares de nome/valor de etiqueta.
* O nome de etiqueta está limitado a 512 caracteres e o valor a 256. Nas contas de armazenamento, o nome da etiqueta está limitado a 128 caracteres e o valor a 256.
* VMs generalizados não suportam etiquetas.
* As etiquetas não podem ser aplicadas a recursos clássicos, como os Serviços cloud.
* Os nomes de etiquetas `<` `>`não `%` `&`podem `\` `?`conter estes caracteres: , , , ,`/`

   > [!NOTE]
   > Atualmente, as zonas DeDNs azure e os serviços de Traffic Manger também não permitem a utilização de espaços na etiqueta.

## <a name="next-steps"></a>Passos seguintes

* Nem todos os tipos de recursos suportam tags. Para determinar se pode aplicar uma etiqueta a um tipo de recurso, consulte o [suporte da Tag para os recursos Azure](tag-support.md).
* Para recomendações sobre como implementar uma estratégia de marcação, consulte o guia de decisão de [nomeação e marcação](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)de recursos.
