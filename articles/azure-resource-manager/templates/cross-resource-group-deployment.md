---
title: Implementar recursos transpareque & grupo de recursos
description: Mostra como direcionar mais do que um grupo de subscrição e recursos Azure durante a implementação.
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 34de1d9df53d61d849ffbb81a57b468020bc3b65
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86057386"
---
# <a name="deploy-azure-resources-across-subscriptions-or-resource-groups"></a>Implementar recursos Azure em subscrições ou grupos de recursos

O Gestor de Recursos permite-lhe implementar para mais de um grupo de recursos numa única implantação. Utiliza modelos aninhados para especificar grupos de recursos diferentes do grupo de recursos na operação de implantação. Os grupos de recursos podem existir em diferentes subscrições.

> [!NOTE]
> Pode implantar **800 grupos de recursos** numa única implantação. Normalmente, esta limitação significa que pode implementar para um grupo de recursos especificado para o modelo principal, e até 799 grupos de recursos em implementações aninhadas ou ligadas. No entanto, se o seu modelo principal contiver apenas modelos aninhados ou ligados e não implementar quaisquer recursos, então pode incluir até 800 grupos de recursos em implementações aninhadas ou ligadas.

## <a name="specify-subscription-and-resource-group"></a>Especificar o grupo de subscrição e recursos

Para direcionar um grupo de recursos diferente do modelo dos pais, use um [modelo aninhado ou ligado.](linked-templates.md) Dentro do tipo de recurso de implementação, especifique os valores para o ID de subscrição e o grupo de recursos para o qual pretende que o modelo aninhado seja implantado.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json" range="38-43" highlight="5-6":::

Se não especificar o ID de subscrição ou o grupo de recursos, o grupo de subscrição e recursos do modelo principal são utilizados. Todos os grupos de recursos devem existir antes de executar a implantação.

A conta que implementa o modelo deve ter permissão para implantar no ID de subscrição especificado. Se a subscrição especificada existir num inquilino diferente do Azure Ative Directory, deve [adicionar utilizadores convidados de outro diretório](../../active-directory/b2b/what-is-b2b.md).

O exemplo a seguir implementa duas contas de armazenamento. A primeira conta de armazenamento é implantada para o grupo de recursos especificado na operação de implantação. A segunda conta de armazenamento é implantada no grupo de recursos especificado nos `secondResourceGroup` parâmetros e `secondSubscriptionID` parâmetros:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

Se definir `resourceGroup` o nome de um grupo de recursos que não existe, a implementação falha.

Para testar o modelo anterior e ver os resultados, utilize o PowerShell ou o Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para implantar duas contas de armazenamento em dois grupos de recursos na **mesma subscrição,** utilize:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

Para implantar duas contas de armazenamento em **duas subscrições,** utilize:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzSubscription -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Select-AzSubscription -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para implantar duas contas de armazenamento em dois grupos de recursos na **mesma subscrição,** utilize:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Para implantar duas contas de armazenamento em **duas subscrições,** utilize:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="use-functions"></a>Utilizar funções

As funções [de grupo de recursos](template-functions-resource.md#resourcegroup) e [subscrição](template-functions-resource.md#subscription) resolvem-se de forma diferente com base na forma como especifica o modelo. Quando se liga a um modelo externo, as funções resolvem-se sempre ao âmbito desse modelo. Quando você nidificar um modelo dentro de um modelo de pai, use a `expressionEvaluationOptions` propriedade para especificar se as funções resolvem para o grupo de recursos e subscrição para o modelo dos pais ou o modelo aninhado. Desa estariuse a propriedade `inner` para resolver o âmbito do modelo aninhado. Desa estariuse a propriedade `outer` para resolver o âmbito do modelo dos pais.

A tabela a seguir mostra se as funções resolvem-se para o grupo de recursos e subscrição do progenitor ou incorporado.

| Tipo de modelo | Âmbito | Resolução |
| ------------- | ----- | ---------- |
| aninhado        | exterior (padrão) | Grupo de recursos parentais |
| aninhado        | interior | Sub-grupo de recursos |
| ligado        | N/D   | Sub-grupo de recursos |

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) a seguir mostra:

* modelo aninhado com âmbito padrão (exterior)
* modelo aninhado com âmbito interno
* modelo ligado

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crossresourcegroupproperties.json":::

Para testar o modelo anterior e ver os resultados, utilize o PowerShell ou o Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

A saída do exemplo anterior é:

```output
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

A saída do exemplo anterior é:

```output
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---

## <a name="next-steps"></a>Próximos passos

* Para entender como definir parâmetros no seu modelo, consulte [a estrutura e sintaxe dos modelos Azure Resource Manager](template-syntax.md).
* Para obter dicas sobre a resolução de erros comuns de implementação, consulte [os erros comuns de implementação do Azure com o Azure Resource Manager](common-deployment-errors.md).
* Para obter informações sobre a implementação de um modelo que requer um token SAS, consulte [implementar o modelo privado com o token SAS](secure-template-with-sas-token.md).
