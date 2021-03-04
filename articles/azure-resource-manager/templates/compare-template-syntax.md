---
title: Compare a sintaxe para modelos de Gestor de Recursos Azure em JSON e Bicep
description: Compara os modelos do Azure Resource Manager desenvolvidos com JSON e Bicep, e mostra como se converter entre os idiomas.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 29c2b9948957ebc10a26f22f0fe3daf383dfe5ba
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036219"
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

## <a name="decompile-json-to-bicep"></a>Decompile JSON a Bicep

O Bicep CLI fornece um comando para decompiler qualquer modelo ARM existente para um ficheiro Bicep. Para descompiler um ficheiro JSON, utilize: `bicep decompile "path/to/file.json"`

Este comando fornece um ponto de partida para a autoria de Bicep, mas o comando não funciona para todos os modelos. O comando pode falhar ou poderá ter de corrigir problemas após a descompilação. Atualmente, os modelos aninhados só podem ser descompilados se usarem o âmbito de avaliação de expressão 'interior'.

Pode exportar o modelo para um grupo de recursos e, em seguida, passá-lo diretamente para o comando decompile bicep. O exemplo a seguir mostra como descompilar um modelo exportado.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group export --name "your_resource_group_name" > main.json
bicep decompile main.json
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Export-AzResourceGroup -ResourceGroupName "your_resource_group_name" -Path ./main.json
bicep decompile main.json
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

[Exporte o modelo](export-template-portal.md) através do portal. Use `bicep decompile <filename>` no ficheiro descarregado.

---

## <a name="build-json-from-bicep"></a>Construir JSON de Bicep

O Bicep CLI também fornece um comando para converter Bicep para JSON. Para construir um ficheiro JSON, utilize: `bicep build "path/to/file.json"`

## <a name="side-by-side-view"></a>Vista lado a lado

O [recreio Bicep](https://aka.ms/bicepdemo) permite-lhe visualizar ficheiros JSON e Bicep equivalentes lado a lado. Pode selecionar um modelo de amostra para ver ambas as versões. Ou, selecione `Decompile` para carregar o seu próprio modelo JSON e ver o ficheiro Bicep equivalente.

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre o Bicep, consulte o [tutorial de Bicep.](./bicep-tutorial-create-first-bicep.md)
