---
title: Compare a sintaxe para modelos de Gestor de Recursos Azure em JSON e Bicep
description: Compara os modelos do Azure Resource Manager desenvolvidos com JSON e Bicep, e mostra como se converter entre os idiomas.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 85f85e66e69eede68bab847e4bc68514e65115eb
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418050"
---
# <a name="comparing-json-and-bicep-for-templates"></a>Comparando JSON e Bicep para modelos

Este artigo compara a sintaxe Bicep com a sintaxe JSON para os modelos Azure Resource Manager (modelos ARM). Na maioria dos casos, bicep fornece sintaxe que é menos verbose do que o equivalente em JSON.

## <a name="syntax-equivalents"></a>Equivalentes de sintaxe

Se estiver familiarizado com a utilização de JSON para desenvolver modelos ARM, use a seguinte tabela para aprender sobre a sintaxe equivalente para Bicep.

| Scenario | Bicep | JSON |
| -------- | ------------ | ----- |
| Autor de uma expressão | `func()` | `"[func()]"` |
| Obtenha valor de parâmetro | `exampleParameter` | `[parameters('exampleParameter'))]` |
| Obtenha valor variável | `exampleVar` | `[variables('exampleVar'))]` |
| Concatenar cadeias | `'${namePrefix}-vm'` | `[concat(parameters('namePrefix'), '-vm')]` |
| Definir propriedade de recursos | `sku: '2016-Datacenter'` | `"sku": "2016-Datacenter",` |
| Devolva o lógico E | `isMonday && isNovember` | `[and(parameter('isMonday'), parameter('isNovember'))]` |
| Obtenha iD de recurso de recurso no modelo | `nic1.id` | `[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]` |
| Obtenha propriedade a partir de recurso no modelo | `diagsAccount.properties.primaryEndpoints.blob` | `[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]` |
| Configurar um valor condicional | `isMonday ? 'valueIfTrue' : 'valueIfFalse'` | `[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]` |
| Separar uma solução em vários ficheiros | Utilizar módulos | Use modelos ligados |
| Definir o âmbito alvo da implantação | `targetScope = 'subscription'` | `"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"` |
| Definir dependência | Ou depende da deteção automática de dependências ou da dependência manualmente definida com `dependsOn: [ stg ]` | `"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]` |
| Declaração de recursos | `resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {...}` | `"resources": [ { "type": "Microsoft.Compute/virtualMachines", "apiVersion": "2020-06-01", ... } ]` |

## <a name="recommendations"></a>Recomendações

* Quando possível, evite utilizar as funções [de referência](template-functions-resource.md#reference) e [recursosId](template-functions-resource.md#resourceid) no seu ficheiro Bicep. Quando referenciar um recurso na mesma implantação bicep, utilize o identificador de recursos. Por exemplo, se implementou um recurso no seu ficheiro Bicep `stg` como identificador de recursos, use sintaxe como `stg.id` ou para obter `stg.properties.primaryEndpoints.blob` valores de propriedade. Ao utilizar o identificador de recursos, cria-se uma dependência implícita entre recursos. Não precisa definir explicitamente a dependência com a propriedade dependOn.
* Utilize invólucros consistentes para identificadores. Se não tem certeza de que tipo de invólucro usar, experimente o invólucro de camelo. Por exemplo, `param myCamelCasedParameter string`.
* Adicione uma descrição a um parâmetro apenas quando a descrição fornecer informações essenciais aos utilizadores. Pode utilizar `//` comentários para obter algumas informações.

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre o Bicep, consulte o [tutorial de Bicep.](./bicep-tutorial-create-first-bicep.md)
* Para aprender sobre a conversão de modelos entre as línguas, consulte [os modelos DE CONVERSÃO ARM entre JSON e Bicep](bicep-decompile.md).
