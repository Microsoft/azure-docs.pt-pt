---
title: Implementar recursos cruze subscrição & grupo de recursos
description: Mostra como direcionar mais de um grupo de subscrição e recursos Azure durante a implantação.
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 2ef68dcb933075833c323d973b023cdaee61bd2f
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650632"
---
# <a name="deploy-azure-resources-across-subscriptions-or-resource-groups"></a>Implementar recursos Azure em assinaturas ou grupos de recursos

O Gestor de Recursos permite-lhe implantar-se em mais de um grupo de recursos numa única implementação. Utiliza modelos aninhados para especificar grupos de recursos diferentes do grupo de recursos na operação de implantação. Os grupos de recursos podem existir em diferentes subscrições.

> [!NOTE]
> Pode **implantar-se em 800 grupos** de recursos numa única implantação. Normalmente, esta limitação significa que pode implantar-se num grupo de recursos especificado para o modelo de progenitor, e até 799 grupos de recursos em implementações aninhadas ou ligadas. No entanto, se o seu modelo de progenitor contiver apenas modelos aninhados ou ligados e não implementar quaisquer recursos, então pode incluir até 800 grupos de recursos em implementações aninhadas ou ligadas.

## <a name="specify-subscription-and-resource-group"></a>Especificar grupo de subscrição e recursos

Para direcionar um grupo de recursos diferente do modelo dos pais, use um [modelo aninhado ou ligado](linked-templates.md). Dentro do tipo de recurso de implementação, especifique valores para o ID de subscrição e grupo de recursos para o qual pretende que o modelo aninhado seja implantado.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json" range="38-43" highlight="5-6":::

Se não especificar o ID de subscrição ou o grupo de recursos, o grupo de subscrição e recursos do modelo de progenitor é utilizado. Todos os grupos de recursos devem existir antes de executar a implantação.

A conta que implementa o modelo deve ter permissão para implantar no ID de subscrição especificado. Se a subscrição especificada existir num inquilino do Diretório Ativo Azure diferente, deve [adicionar utilizadores convidados de outro diretório](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

O exemplo seguinte implementa duas contas de armazenamento. A primeira conta de armazenamento é implantada para o grupo de recursos especificado na operação de implantação. A segunda conta de armazenamento é implantada para o grupo de recursos especificado nos `secondResourceGroup` e `secondSubscriptionID` parâmetros:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

Se definir `resourceGroup` o nome de um grupo de recursos que não existe, a implementação falha.

Para testar o modelo anterior e ver os resultados, utilize powerShell ou Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para implementar duas contas de armazenamento para dois grupos de recursos na **mesma subscrição,** use:

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

Para implementar duas contas de armazenamento em **duas subscrições,** use:

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

Para implementar duas contas de armazenamento para dois grupos de recursos na **mesma subscrição,** use:

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

Para implementar duas contas de armazenamento em **duas subscrições,** use:

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

As funções do Grupo de [Recursos()](template-functions-resource.md#resourcegroup) e [da subscrição](template-functions-resource.md#subscription) resolvem de forma diferente com base na forma como especifica o modelo. Quando se liga a um modelo externo, as funções resolvem-se sempre ao âmbito desse modelo. Quando nidificar um modelo dentro de um modelo de progenitor, use a `expressionEvaluationOptions` propriedade para especificar se as funções resolvem o grupo de recursos e a subscrição para o modelo dos pais ou o modelo aninhado. Desloque a propriedade `inner` para resolver o âmbito para o modelo aninhado. Desloque a propriedade `outer` para resolver o âmbito do modelo de progenitor.

O quadro seguinte mostra se as funções resolvem para o grupo de recursos e subscrição incorporados.

| Tipo de modelo | Âmbito | Resolução |
| ------------- | ----- | ---------- |
| aninhada        | exterior (padrão) | Grupo de recursos-mãe |
| aninhada        | interior | Grupo de subrecursos |
| ligado        | N/D   | Grupo de subrecursos |

O [seguinte modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) de exemplo mostra:

* modelo aninhado com âmbito padrão (exterior)
* modelo aninhado com âmbito interior
* modelo ligado

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crossresourcegroupproperties.json":::

Para testar o modelo anterior e ver os resultados, utilize powerShell ou Azure CLI.

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

## <a name="next-steps"></a>Passos seguintes

* Para entender como definir parâmetros no seu modelo, consulte [Compreender a estrutura e a sintaxe dos modelos do Gestor](template-syntax.md)de Recursos Azure .
* Para obter dicas sobre a resolução de erros comuns de implementação, consulte erros comuns de [implantação do Azure com o Azure Resource Manager](common-deployment-errors.md).
* Para obter informações sobre a implementação de um modelo que requer uma ficha SAS, consulte [Implementar modelo privado com token SAS](secure-template-with-sas-token.md).
