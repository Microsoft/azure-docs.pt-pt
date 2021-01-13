---
title: Âmbito dos tipos de recursos de extensão
description: Descreve como utilizar a propriedade de âmbito ao implementar tipos de recursos de extensão.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: 75c2c8b8409cc9f8e7a8e71965589ece6660607a
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179984"
---
# <a name="setting-scope-for-extension-resources-in-arm-templates"></a>Definição de margem para recursos de extensão em modelos ARM

Um recurso de extensão é um recurso que modifica outro recurso. Por exemplo, pode atribuir uma função a um recurso. A atribuição de funções é um tipo de recurso de extensão.

Para obter uma lista completa dos tipos de recursos de extensão, consulte [tipos de recursos que alargam as capacidades de outros recursos](../management/extension-resource-types.md).

Este artigo mostra como definir a margem para um tipo de recurso de extensão quando implementado com um modelo de Gestor de Recursos Azure (modelo ARM). Descreve a propriedade de âmbito que está disponível para recursos de extensão quando se aplica a um recurso.

> [!NOTE]
> A propriedade de âmbito só está disponível para tipos de recursos de extensão. Para especificar um âmbito diferente para um tipo de recurso que não seja um tipo de extensão, utilize uma implantação aninhada ou ligada. Para obter mais informações, consulte [as implementações de grupos de recursos,](deploy-to-resource-group.md) [implementações de subscrições,](deploy-to-subscription.md) [implementações de grupos](deploy-to-management-group.md)de gestão e [implantações de inquilinos.](deploy-to-tenant.md)

## <a name="apply-at-deployment-scope"></a>Aplicar no âmbito de implantação

Para aplicar um tipo de recurso de extensão no âmbito de implementação do alvo, adicione o recurso ao seu modelo, como faria com qualquer tipo de recurso. Os âmbitos disponíveis são [grupo de recursos,](deploy-to-resource-group.md) [subscrição,](deploy-to-subscription.md) [grupo de gestão](deploy-to-management-group.md)e [inquilino.](deploy-to-tenant.md) O âmbito de implantação deve suportar o tipo de recurso.

O modelo a seguir implementa uma fechadura.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/locktargetscope.json":::

Quando implantado num grupo de recursos, bloqueia o grupo de recursos.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/locktargetscope.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
 New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/locktargetscope.json"
```

---

O próximo exemplo atribui um papel.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/roletargetscope.json":::

Quando implementado numa subscrição, atribui o papel à subscrição.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/roletargetscope.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/roletargetscope.json"
```

---

## <a name="apply-to-resource"></a>Aplicar ao recurso

Para aplicar um recurso de extensão a um recurso, utilize a `scope` propriedade. Desaprote a propriedade de âmbito para o nome do recurso a que está a adicionar a extensão. A propriedade de âmbito é uma propriedade raiz para o tipo de recurso de extensão.

O exemplo a seguir cria uma conta de armazenamento e aplica-lhe uma função.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/storageandrole.json" highlight="56":::

## <a name="next-steps"></a>Passos seguintes

* Para entender como definir parâmetros no seu modelo, consulte [a estrutura e sintaxe dos modelos Azure Resource Manager](template-syntax.md).
* Para obter dicas sobre a resolução de erros comuns de implementação, consulte [os erros comuns de implementação do Azure com o Azure Resource Manager](common-deployment-errors.md).
* Para obter informações sobre a implementação de um modelo que requer um token SAS, consulte [implementar o modelo privado com o token SAS](secure-template-with-sas-token.md).
