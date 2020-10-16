---
title: Etiquetar recursos, grupos de recursos e subscrições para organização lógica
description: Mostra como aplicar tags para organizar recursos Azure para faturação e gestão.
ms.topic: conceptual
ms.date: 07/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3ffcb4a0f2f5dc64b165fcdec03f7c3ced258cc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90086764"
---
# <a name="use-tags-to-organize-your-azure-resources-and-management-hierarchy"></a>Use etiquetas para organizar os seus recursos Azure e hierarquia de gestão

Aplica etiquetas nos seus recursos Azure, grupos de recursos e subscrições para organizá-las logicamente numa taxonomia. Cada etiqueta é composta por um nome e um par de valor. Por exemplo, pode aplicar o nome "Ambiente" e o valor "Produção" em todos os recursos na produção.

Para obter recomendações sobre como implementar uma estratégia de marcação, consulte [o guia de decisão de nomeação de recursos e marcação.](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

> [!IMPORTANT]
> Os nomes das etiquetas são insensíveis para operações. Uma etiqueta com um nome de etiqueta, independentemente do invólucro, é atualizada ou recuperada. No entanto, o fornecedor de recursos pode manter o invólucro que fornece para o nome da etiqueta. Verá o invólucro nos relatórios de custos.
> 
> Os valores da etiqueta são sensíveis a maiôs.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="required-access"></a>Acesso obrigatório

Para aplicar tags a um recurso, tem de ter acesso de escrita ao tipo de recurso **Microsoft.Resources/tags.** A função [Tag Contributor](../../role-based-access-control/built-in-roles.md#tag-contributor) permite aplicar tags a uma entidade sem ter acesso à própria entidade. Atualmente, o papel de contribuinte de etiquetas não pode aplicar tags a recursos ou grupos de recursos através do portal. Pode aplicar tags a subscrições através do portal. Suporta todas as operações de tag através da PowerShell e da REST API.  

A [função Contribuinte](../../role-based-access-control/built-in-roles.md#contributor) também concede o acesso necessário à aplicação de etiquetas a qualquer entidade. Para aplicar etiquetas a apenas um tipo de recurso, utilize a função de contribuinte para esse recurso. Por exemplo, para aplicar tags em máquinas virtuais, utilize o [Contribuinte Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

## <a name="powershell"></a>PowerShell

### <a name="apply-tags"></a>Aplicar tags

A Azure PowerShell oferece dois comandos para a aplicação de tags - [New-AzTag](/powershell/module/az.resources/new-aztag) e [Update-AzTag](/powershell/module/az.resources/update-aztag). Deve ter o módulo Az.Resources 1.12.0 ou mais tarde. Pode verificar a sua versão com `Get-Module Az.Resources` . Pode instalar esse módulo ou [instalar o Azure PowerShell](/powershell/azure/install-az-ps) 3.6.1 ou mais tarde.

O **New-AzTag** substitui todas as tags no recurso, grupo de recursos ou subscrição. Ao ligar para o comando, passe na identificação de recursos da entidade que deseja marcar.

O exemplo a seguir aplica um conjunto de etiquetas a uma conta de armazenamento:

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

Para adicionar tags a um recurso que já tem tags, utilize **Update-AzTag**. Desa estação o parâmetro **de operação** **para a fusão**.

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

Cada nome de etiqueta pode ter apenas um valor. Se fornecer um novo valor para uma etiqueta, o valor antigo é substituído mesmo que utilize a operação de fusão. O exemplo a seguir altera a etiqueta de Estado de Normal para Verde.

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

Quando definir o parâmetro **de operação** para **substituir,** as etiquetas existentes são substituídas pelo novo conjunto de etiquetas.

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

Os mesmos comandos também funcionam com grupos de recursos ou subscrições. Você passa no identificador para o grupo de recursos ou subscrição que deseja marcar.

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

Pode ter mais do que um recurso com o mesmo nome num grupo de recursos. Nesse caso, pode definir cada recurso com os seguintes comandos:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Update-AzTag -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Operation Merge }
```

### <a name="list-tags"></a>Listar etiquetas

Para obter as etiquetas para um recurso, grupo de recursos ou subscrição, utilize o comando [Get-AzTag](/powershell/module/az.resources/get-aztag) e passe no ID de recursos para a entidade.

Para ver as etiquetas para um recurso, use:

```azurepowershell-interactive
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
Get-AzTag -ResourceId $resource.id
```

Para ver as etiquetas de um grupo de recursos, utilize:

```azurepowershell-interactive
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Get-AzTag -ResourceId $resourceGroup.ResourceId
```

Para ver as etiquetas para uma subscrição, utilize:

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Get-AzTag -ResourceId "/subscriptions/$subscription"
```

### <a name="list-by-tag"></a>Lista por etiqueta

Para obter recursos que tenham um nome e valor específicos, use:

```azurepowershell-interactive
(Get-AzResource -Tag @{ "CostCenter"="00123"}).Name
```

Para obter recursos que tenham um nome de identificação específico com qualquer valor de etiqueta, use:

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

Para obter grupos de recursos que tenham um nome e valor específicos, utilize:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "CostCenter"="00123" }).ResourceGroupName
```

### <a name="remove-tags"></a>Remover tags

Para remover etiquetas específicas, utilize **Update-AzTag** e desemote **-Operação** para **Eliminar**. Passe as etiquetas que pretende apagar.

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

### <a name="apply-tags"></a>Aplicar tags

Ao adicionar tags a um grupo de recursos ou a um recurso, pode substituir as etiquetas existentes ou anexar novas etiquetas às etiquetas existentes.

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

Atualmente, o Azure CLI não tem um comando para aplicar tags a subscrições. No entanto, pode utilizar o CLI para implementar um modelo ARM que aplica as tags a uma subscrição. Consulte [as etiquetas aplicadas a grupos de recursos ou subscrições.](#apply-tags-to-resource-groups-or-subscriptions)

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

### <a name="list-by-tag"></a>Lista por etiqueta

Para obter todos os recursos que têm uma etiqueta e valor particulares, `az resource list` use:

```azurecli-interactive
az resource list --tag Dept=Finance
```

Para obter grupos de recursos que tenham uma etiqueta específica, `az group list` use:

```azurecli-interactive
az group list --tag Dept=IT
```

### <a name="handling-spaces"></a>Espaços de manuseamento

Se os seus nomes ou valores incluem espaços, deve dar alguns passos extras. 

Os `--tags` parâmetros no CLI Azure podem aceitar uma corda que consiste numa matriz de cordas. O exemplo a seguir substitui as etiquetas num grupo de recursos onde as etiquetas têm espaços e hífen: 

```azurecli-interactive
TAGS=("Cost Center=Finance-1222" "Location=West US")
az group update --name examplegroup --tags "${TAGS[@]}"
```

Pode utilizar a mesma sintaxe quando criar ou atualizar um grupo de recursos ou recursos utilizando o `--tags` parâmetro.

Para atualizar as etiquetas utilizando o `--set` parâmetro, tem de passar a chave e o valor como uma cadeia. O exemplo a seguir anexa uma única etiqueta a um grupo de recursos:

```azurecli-interactive
TAG="Cost Center='Account-56'"
az group update --name examplegroup --set tags."$TAG"
```

Neste caso, o valor da etiqueta é marcado com cotações únicas porque o valor tem um hífen.

Também pode precisar de aplicar etiquetas a muitos recursos. O exemplo a seguir aplica todas as tags de um grupo de recursos aos seus recursos quando as tags podem conter espaços:

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

Pode marcar recursos, grupos de recursos e subscrições durante a implementação com um modelo de Gestor de Recursos.

### <a name="apply-values"></a>Aplicar valores

O exemplo a seguir implanta uma conta de armazenamento com três etiquetas. Duas das etiquetas `Dept` `Environment` (e) são definidas para valores literais. Uma etiqueta `LastDeployed` () é definida como um parâmetro que predefini a data atual.

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

### <a name="apply-tags-from-resource-group"></a>Aplicar tags do grupo de recursos

Para aplicar tags de um grupo de recursos a um recurso, utilize a função [Grupo de Recursos()](../templates/template-functions-resource.md#resourcegroup) Ao obter o valor da etiqueta, use a `tags[tag-name]` sintaxe em vez da `tags.tag-name` sintaxe, porque alguns caracteres não são analisados corretamente na notação do ponto.

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

### <a name="apply-tags-to-resource-groups-or-subscriptions"></a>Aplicar tags em grupos de recursos ou subscrições

Pode adicionar tags a um grupo de recursos ou subscrição através da implementação do tipo de recurso **Microsoft.Resources/tags.** As etiquetas são aplicadas ao grupo de recursos-alvo ou subscrição para a implementação. Cada vez que implementar o modelo substitui quaisquer tags, foram previamente aplicadas.

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

Para aplicar as etiquetas num grupo de recursos, utilize o PowerShell ou o Azure CLI. Implemente para o grupo de recursos que pretende marcar.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName exampleGroup -TemplateFile https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment group create --resource-group exampleGroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

Para aplicar as etiquetas numa subscrição, utilize o PowerShell ou o Azure CLI. Implemente a subscrição que pretende marcar.

```azurepowershell-interactive
New-AzSubscriptionDeployment -name tagresourcegroup -Location westus2 -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment sub create --name tagresourcegroup --location westus2 --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

Para obter mais informações sobre as implementações de subscrição, consulte [Criar grupos de recursos e recursos ao nível da subscrição.](../templates/deploy-to-subscription.md)

O modelo a seguir adiciona as tags de um objeto a um grupo de recursos ou a uma subscrição.

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

Para trabalhar com tags através da AZure REST API, utilize:

* [Tags - Criar ou atualizar no âmbito](/rest/api/resources/tags/createorupdateatscope) (operação PUT)
* [Tags - Atualização no âmbito](/rest/api/resources/tags/updateatscope) (operação PATCH)
* [Tags - Get At Scope](/rest/api/resources/tags/getatscope) (operação GET)
* [Tags - Excluir no âmbito](/rest/api/resources/tags/deleteatscope) (operação DELETE)

## <a name="inherit-tags"></a>Herdar etiquetas

As etiquetas aplicadas ao grupo de recursos ou subscrição não são herdadas pelos recursos. Para aplicar tags de um grupo de subscrição ou de recursos aos recursos, consulte [Azure Policies - tags](tag-policies.md).

## <a name="tags-and-billing"></a>Etiquetas e faturação

Pode utilizar etiquetas para agrupar os seus dados de faturação. Por exemplo, se estiver a executar múltiplas VMs para organizações diferentes, utilize as etiquetas para agrupar a utilização por centro de custos. Também pode utilizar etiquetas para categorizar os custos por ambiente de runtime, tal como a utilização de faturação das VMs executadas no ambiente de produção.

Pode obter informações sobre tags através das APIs de [Utilização de Recursos Azure e tarifas](../../cost-management-billing/manage/usage-rate-card-overview.md) ou do ficheiro valores separados por vírgulas de utilização (CSV). Descarregue o ficheiro de utilização do portal Azure. Para mais informações, consulte [Download ou consulte a sua faturação Azure e dados de utilização diários.](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md) Ao descarregar o ficheiro de utilização do Centro de Conta Azure, selecione **a Versão 2**. Para serviços que suportam etiquetas com faturação, as etiquetas aparecem na coluna **Tags.**

Para operações rest API, consulte [referência API API de Faturação AZure](/rest/api/billing/).

## <a name="limitations"></a>Limitações

As seguintes limitações aplicam-se às etiquetas:

* Nem todos os tipos de recursos suportam tags. Para determinar se pode aplicar uma etiqueta a um tipo de recurso, consulte [o suporte da Tag para os recursos do Azure.](tag-support.md)
* Cada recurso, grupo de recursos e subscrição podem ter um máximo de 50 pares de nome/valor de etiqueta. Se precisar de aplicar mais etiquetas do que o número máximo permitido, utilize uma corda JSON para o valor da etiqueta. A cadeia JSON pode conter muitos valores que são aplicados a um nome de etiqueta individual. Um grupo de recursos ou subscrição pode conter muitos recursos que cada um tem 50 pares de nome/valor de tag.
* O nome de etiqueta está limitado a 512 caracteres e o valor a 256. Nas contas de armazenamento, o nome da etiqueta está limitado a 128 caracteres e o valor a 256.
* As etiquetas não podem ser aplicadas a recursos clássicos como os Cloud Services.
* Os nomes das etiquetas não podem conter estes caracteres: `<` , , , , `>` `%` `&` `\` `?` , `/`

   > [!NOTE]
   > Atualmente, as zonas de DNS do Azure e os serviços de Gerente de Tráfego também não permitem a utilização de espaços na etiqueta.
   >
   > AZure Front Door não suporta o uso `#` do nome da etiqueta.
   >
   > A Azure Automation e a Azure CDN suportam apenas 15 tags em recursos.

## <a name="next-steps"></a>Passos seguintes

* Nem todos os tipos de recursos suportam tags. Para determinar se pode aplicar uma etiqueta a um tipo de recurso, consulte [o suporte da Tag para os recursos do Azure.](tag-support.md)
* Para obter recomendações sobre como implementar uma estratégia de marcação, consulte [o guia de decisão de nomeação de recursos e marcação.](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)
