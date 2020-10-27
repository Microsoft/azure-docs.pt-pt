---
title: Mobilizar recursos através de âmbitos
description: Mostra como direcionar mais do que um âmbito durante uma implantação. O âmbito pode ser um inquilino, grupos de gestão, subscrições e grupos de recursos.
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 6161401ac039551a814b595715f56df1ac62dd6c
ms.sourcegitcommit: d3c3f2ded72bfcf2f552e635dc4eb4010491eb75
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92559002"
---
# <a name="deploy-azure-resources-across-scopes"></a>Implementar recursos Azure em âmbitos

Com os modelos Azure Resource Manager (modelos ARM), pode implementar para mais de um âmbito numa única implementação. Os âmbitos disponíveis são inquilinos, grupos de gestão, subscrições e grupos de recursos. Por exemplo, pode implementar recursos para um grupo de recursos, e no mesmo modelo implementar recursos para outro grupo de recursos. Ou, você pode implementar recursos para um grupo de gestão e também implementar recursos para um grupo de recursos dentro desse grupo de gestão.

Utiliza [modelos aninhados ou ligados](linked-templates.md) para especificar âmbitos diferentes do âmbito principal para a operação de implantação.

## <a name="available-scopes"></a>Âmbitos disponíveis

O âmbito que utiliza para a operação de implantação determina quais outros âmbitos disponíveis. Pode deslocar-se ao [arrendatário,](deploy-to-tenant.md) [grupo de gestão,](deploy-to-management-group.md) [subscrição](deploy-to-subscription.md)ou [grupo de recursos.](deploy-powershell.md) A partir do nível de implantação primária, não se pode subir os níveis na hierarquia. Por exemplo, se implementar para uma subscrição, não pode subir um nível para implantar recursos para um grupo de gestão. No entanto, pode deslocar-se ao grupo de gestão e descer os níveis para implantar num grupo de subscrição ou recursos.

Para cada âmbito, o utilizador que implementa o modelo deve ter as permissões necessárias para criar recursos.

## <a name="cross-resource-groups"></a>Grupos de recursos cruzados

Para direcionar um grupo de recursos diferente do modelo dos pais, use um [modelo aninhado ou ligado.](linked-templates.md) Dentro do tipo de recurso de implementação, especifique os valores para o ID de subscrição e o grupo de recursos para o qual pretende que o modelo aninhado seja implantado. Os grupos de recursos podem existir em diferentes subscrições.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json" range="38-43" highlight="5-6":::

Se não especificar o ID de subscrição ou o grupo de recursos, o grupo de subscrição e recursos do modelo principal são utilizados. Todos os grupos de recursos devem existir antes de executar a implantação.

> [!NOTE]
> Pode implantar **800 grupos de recursos** numa única implantação. Normalmente, esta limitação significa que pode implementar para um grupo de recursos especificado para o modelo principal, e até 799 grupos de recursos em implementações aninhadas ou ligadas. No entanto, se o seu modelo principal contiver apenas modelos aninhados ou ligados e não implementar quaisquer recursos, então pode incluir até 800 grupos de recursos em implementações aninhadas ou ligadas.

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

## <a name="cross-subscription-management-group-and-tenant"></a>Assinatura cruzada, grupo de gestão e inquilino

Ao especificar diferentes âmbitos para implementações de subscrição, grupo de gestão e nível de inquilino, utiliza implementações aninhadas como o exemplo para grupos de recursos. As propriedades que utiliza para especificar o âmbito podem diferir. Estes cenários são abordados nos artigos sobre os níveis de implantação. Para obter mais informações, consulte:

* [Criar grupos de recursos e recursos ao nível de subscrição](deploy-to-subscription.md)
* [Criar recursos ao nível do grupo de gestão](deploy-to-management-group.md)
* [Criar recursos ao nível dos inquilinos](deploy-to-tenant.md)

## <a name="how-functions-resolve-in-scopes"></a>Como as funções resolvem em âmbitos

Quando implementa para mais de um âmbito, as funções [de Grupo de Recursos ()](template-functions-resource.md#resourcegroup) e [subscrição resolvem-se](template-functions-resource.md#subscription) de forma diferente com base na forma como especifica o modelo. Quando se liga a um modelo externo, as funções resolvem-se sempre ao âmbito desse modelo. Quando você nidificar um modelo dentro de um modelo de pai, use a `expressionEvaluationOptions` propriedade para especificar se as funções resolvem para o grupo de recursos e subscrição para o modelo dos pais ou o modelo aninhado. Desa estariuse a propriedade `inner` para resolver o âmbito do modelo aninhado. Desa estariuse a propriedade `outer` para resolver o âmbito do modelo dos pais.

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



## <a name="next-steps"></a>Passos seguintes

* Para entender como definir parâmetros no seu modelo, consulte [a estrutura e sintaxe dos modelos Azure Resource Manager](template-syntax.md).
* Para obter dicas sobre a resolução de erros comuns de implementação, consulte [os erros comuns de implementação do Azure com o Azure Resource Manager](common-deployment-errors.md).
* Para obter informações sobre a implementação de um modelo que requer um token SAS, consulte [implementar o modelo privado com o token SAS](secure-template-with-sas-token.md).
