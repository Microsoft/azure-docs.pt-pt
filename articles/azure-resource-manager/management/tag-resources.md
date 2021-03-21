---
title: Etiquetar recursos, grupos de recursos e subscrições para organização lógica
description: Mostra como aplicar tags para organizar recursos Azure para faturação e gestão.
ms.topic: conceptual
ms.date: 01/04/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: fb93673b643fd13efe9ffea148c5fb1d072f9e05
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896228"
---
# <a name="use-tags-to-organize-your-azure-resources-and-management-hierarchy"></a>Utilizar etiquetas para organizar os recursos do Azure e a hierarquia de gestão

Aplica etiquetas nos seus recursos Azure, grupos de recursos e subscrições para organizá-las logicamente numa taxonomia. Cada etiqueta é composta por um nome e um par de valor. Por exemplo, pode aplicar o nome "Ambiente" e o valor "Produção" em todos os recursos na produção.

Para obter recomendações sobre como implementar uma estratégia de marcação, consulte [o guia de decisão de nomeação de recursos e marcação.](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

> [!IMPORTANT]
> Os nomes das etiquetas são insensíveis para operações. Uma etiqueta com um nome de etiqueta, independentemente do invólucro, é atualizada ou recuperada. No entanto, o fornecedor de recursos pode manter o invólucro que fornece para o nome da etiqueta. Verá o invólucro nos relatórios de custos.
> 
> Os valores da etiqueta são sensíveis a maiôs.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="required-access"></a>Acesso obrigatório

Há duas maneiras de obter o acesso necessário aos recursos de marcação.

- Pode ter acesso de escrita ao tipo de recurso **Microsoft.Resources/tags.** Este acesso permite marcar qualquer recurso, mesmo que não tenha acesso ao próprio recurso. A [função De Colaborador tag](../../role-based-access-control/built-in-roles.md#tag-contributor) concede este acesso. Atualmente, o papel de contribuinte de etiquetas não pode aplicar tags a recursos ou grupos de recursos através do portal. Pode aplicar tags a subscrições através do portal. Suporta todas as operações de tag através da PowerShell e da REST API.  

- Pode escrever acesso ao próprio recurso. A função [Contribuinte](../../role-based-access-control/built-in-roles.md#contributor) concede o acesso necessário à aplicação de etiquetas a qualquer entidade. Para aplicar etiquetas a apenas um tipo de recurso, utilize a função de contribuinte para esse recurso. Por exemplo, para aplicar tags em máquinas virtuais, utilize o [Contribuinte Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

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

O Azure CLI oferece dois comandos para a aplicação de tags - [az tag create](/cli/azure/tag#az_tag_create) e [a az tag update](/cli/azure/tag#az_tag_update). Deve ter Azure CLI 2.10.0 ou mais tarde. Pode verificar a sua versão com `az version` . Para atualizar ou instalar, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

A **marca az cria** substitui todas as tags no recurso, grupo de recursos ou subscrição. Ao ligar para o comando, passe na identificação de recursos da entidade que deseja marcar.

O exemplo a seguir aplica um conjunto de etiquetas a uma conta de armazenamento:

```azurecli-interactive
resource=$(az resource show -g demoGroup -n demoStorage --resource-type Microsoft.Storage/storageAccounts --query "id" --output tsv)
az tag create --resource-id $resource --tags Dept=Finance Status=Normal
```

Quando o comando estiver concluído, note que o recurso tem duas etiquetas.

```output
"properties": {
  "tags": {
    "Dept": "Finance",
    "Status": "Normal"
  }
},
```

Se voltar a executar o comando, mas desta vez com etiquetas diferentes, note que as etiquetas anteriores são removidas.

```azurecli-interactive
az tag create --resource-id $resource --tags Team=Compliance Environment=Production
```

```output
"properties": {
  "tags": {
    "Environment": "Production",
    "Team": "Compliance"
  }
},
```

Para adicionar tags a um recurso que já tenha etiquetas, use `az tag update` . Desa estale o `--operation` parâmetro para `Merge` .

```azurecli-interactive
az tag update --resource-id $resource --operation Merge --tags Dept=Finance Status=Normal
```

Note que as duas novas etiquetas foram adicionadas às duas etiquetas existentes.

```output
"properties": {
  "tags": {
    "Dept": "Finance",
    "Environment": "Production",
    "Status": "Normal",
    "Team": "Compliance"
  }
},
```

Cada nome de etiqueta pode ter apenas um valor. Se fornecer um novo valor para uma etiqueta, o valor antigo é substituído mesmo que utilize a operação de fusão. O exemplo a seguir altera a etiqueta de Estado de Normal para Verde.

```azurecli-interactive
az tag update --resource-id $resource --operation Merge --tags Status=Green
```

```output
"properties": {
  "tags": {
    "Dept": "Finance",
    "Environment": "Production",
    "Status": "Green",
    "Team": "Compliance"
  }
},
```

Quando se define o `--operation` parâmetro para , as `Replace` etiquetas existentes são substituídas pelo novo conjunto de etiquetas.

```azurecli-interactive
az tag update --resource-id $resource --operation Replace --tags Project=ECommerce CostCenter=00123 Team=Web
```

Apenas as novas etiquetas permanecem no recurso.

```output
"properties": {
  "tags": {
    "CostCenter": "00123",
    "Project": "ECommerce",
    "Team": "Web"
  }
},
```

Os mesmos comandos também funcionam com grupos de recursos ou subscrições. Você passa no identificador para o grupo de recursos ou subscrição que deseja marcar.

Para adicionar um novo conjunto de tags a um grupo de recursos, utilize:

```azurecli-interactive
group=$(az group show -n demoGroup --query id --output tsv)
az tag create --resource-id $group --tags Dept=Finance Status=Normal
```

Para atualizar as etiquetas para um grupo de recursos, utilize:

```azurecli-interactive
az tag update --resource-id $group --operation Merge --tags CostCenter=00123 Environment=Production
```

Para adicionar um novo conjunto de tags a uma subscrição, utilize:

```azurecli-interactive
sub=$(az account show --subscription "Demo Subscription" --query id --output tsv)
az tag create --resource-id /subscriptions/$sub --tags CostCenter=00123 Environment=Dev
```

Para atualizar as etiquetas para uma subscrição, utilize:

```azurecli-interactive
az tag update --resource-id /subscriptions/$sub --operation Merge --tags Team="Web Apps"
```

### <a name="list-tags"></a>Listar etiquetas

Para obter as etiquetas para um recurso, grupo de recursos ou subscrição, utilize o comando [da lista de etiquetas az](/cli/azure/tag#az_tag_list) e passe no ID de recursos para a entidade.

Para ver as etiquetas para um recurso, use:

```azurecli-interactive
resource=$(az resource show -g demoGroup -n demoStorage --resource-type Microsoft.Storage/storageAccounts --query "id" --output tsv)
az tag list --resource-id $resource
```

Para ver as etiquetas de um grupo de recursos, utilize:

```azurecli-interactive
group=$(az group show -n demoGroup --query id --output tsv)
az tag list --resource-id $group
```

Para ver as etiquetas para uma subscrição, utilize:

```azurecli-interactive
sub=$(az account show --subscription "Demo Subscription" --query id --output tsv)
az tag list --resource-id /subscriptions/$sub
```

### <a name="list-by-tag"></a>Lista por etiqueta

Para obter recursos que tenham um nome e valor específicos, use:

```azurecli-interactive
az resource list --tag CostCenter=00123 --query [].name
```

Para obter recursos que tenham um nome de identificação específico com qualquer valor de etiqueta, use:

```azurecli-interactive
az resource list --tag Team --query [].name
```

Para obter grupos de recursos que tenham um nome e valor específicos, utilize:

```azurecli-interactive
az group list --tag Dept=Finance
```

### <a name="remove-tags"></a>Remover tags

Para remover etiquetas específicas, utilize `az tag update` e desateia `--operation` `Delete` . Passe as etiquetas que pretende apagar.

```azurecli-interactive
az tag update --resource-id $resource --operation Delete --tags Project=ECommerce Team=Web
```

As etiquetas especificadas são removidas.

```output
"properties": {
  "tags": {
    "CostCenter": "00123"
  }
},
```

Para remover todas as etiquetas, utilize o comando [de exclusão da marca az.](/cli/azure/tag#az_tag_delete)

```azurecli-interactive
az tag delete --resource-id $resource
```

### <a name="handling-spaces"></a>Espaços de manuseamento

Se os nomes ou valores da sua etiqueta incluirem espaços, inclua-os em ações duplas.

```azurecli-interactive
az tag update --resource-id $group --operation Merge --tags "Cost Center"=Finance-1222 Location="West US"
```

## <a name="arm-templates"></a>Modelos de ARM

Pode marcar recursos, grupos de recursos e subscrições durante a implementação com um modelo de Gestor de Recursos Azure (modelo ARM).

> [!NOTE]
> As etiquetas que aplica através do modelo ARM substituem as etiquetas existentes.

### <a name="apply-values"></a>Aplicar valores

O exemplo a seguir implanta uma conta de armazenamento com três etiquetas. Duas das etiquetas `Dept` `Environment` (e) são definidas para valores literais. Uma etiqueta `LastDeployed` () é definida como um parâmetro que predefini a data atual.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

Pode obter informações sobre tags descarregando o ficheiro de utilização, um ficheiro de valores separados por vírgula (CSV) disponível a partir do portal Azure. Para mais informações, consulte [Download ou consulte a sua faturação Azure e dados de utilização diários.](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md) Ao descarregar o ficheiro de utilização do Centro de Conta Azure, selecione **a Versão 2**. Para serviços que suportam etiquetas com faturação, as etiquetas aparecem na coluna **Tags.**

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
