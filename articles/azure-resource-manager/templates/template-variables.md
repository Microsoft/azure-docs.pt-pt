---
title: Variáveis em modelos
description: Descreve como definir variáveis num modelo de Gestor de Recursos Azure (modelo ARM) e ficheiro Bicep.
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: e00a9e8e1801725707bac2abdc67512477e2cf07
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700342"
---
# <a name="variables-in-arm-templates"></a>Variáveis em modelos ARM

Este artigo descreve como definir e usar variáveis no seu modelo de Gestor de Recursos Azure (modelo ARM) ou ficheiro Bicep. Usa variáveis para simplificar o seu modelo. Em vez de repetir expressões complicadas ao longo do seu modelo, define uma variável que contém a expressão complicada. Em seguida, use essa variável conforme necessário em todo o seu modelo.

O Gestor de Recursos resolve variáveis antes de iniciar as operações de implantação. Onde quer que a variável seja utilizada no modelo, o Resource Manager substitui-a pelo valor resolvido.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="define-variable"></a>Definir variável

Ao definir uma variável, não se especifica um tipo de [dado para](template-syntax.md#data-types) a variável. Em vez disso, forneça um valor ou expressão de modelo. O tipo variável é deduzido do valor resolvido. O exemplo a seguir define uma variável a uma corda.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "stringVar": "example value"
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var stringVar = 'example value'
```

---

Pode utilizar o valor a partir de um parâmetro ou de outra variável ao construir a variável.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "inputValue": {
    "defaultValue": "deployment parameter",
    "type": "string"
  }
},
"variables": {
  "stringVar": "myVariable",
  "concatToVar": "[concat(variables('stringVar'), '-addtovar') ]",
  "concatToParam": "[concat(parameters('inputValue'), '-addtoparam')]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param inputValue string = 'deployment parameter'

var stringVar = 'myVariable'
var concatToVar =  '${stringVar}-addtovar'
var concatToParam = '${inputValue}-addtoparam'
```

---

Pode utilizar [funções de modelo](template-functions.md) para construir o valor variável.

O exemplo a seguir cria um valor de cadeia para um nome de conta de armazenamento. Ele usa várias funções de modelo para obter um valor de parâmetro, e concatena-lo para uma corda única.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'
```

---

Nos modelos JSON, não é possível utilizar a função de [referência](template-functions-resource.md#reference) ou qualquer uma das funções da [lista](template-functions-resource.md#list) na declaração variável. Estas funções obtêm o estado de funcionamento de um recurso, e não podem ser executadas antes da implementação quando as variáveis são resolvidas.

Nos ficheiros Bicep, as funções de referência e lista são válidas quando se declara uma variável.

## <a name="use-variable"></a>Utilizar variável

O exemplo a seguir mostra como usar a variável para uma propriedade de recurso.

# <a name="json"></a>[JSON](#tab/json)

Num modelo JSON, refere-se o valor da variável utilizando a função [de variáveis.](template-functions-deployment.md#variables)

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

Num ficheiro Bicep, refere-se o valor da variável fornecendo o nome variável.

```bicep
var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'

resource demoAccount 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageName
```

---

## <a name="example-template"></a>Modelo de exemplo

O modelo a seguir não implementa quaisquer recursos. Mostra algumas formas de declarar variáveis de diferentes tipos.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variables.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

Bicep não suporta loops atualmente.

:::code language="bicep" source="~/resourcemanager-templates/azure-resource-manager/variables.bicep":::

---

## <a name="configuration-variables"></a>Variáveis de configuração

Pode definir variáveis que possuam valores relacionados para configurar um ambiente. Define-se a variável como um objeto com os valores. O exemplo a seguir mostra um objeto que detém valores para dois ambientes - **teste** e **prod**. Passe num destes valores durante a implantação.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.bicep":::

---

## <a name="next-steps"></a>Passos seguintes

* Para conhecer as propriedades disponíveis para variáveis, consulte [a estrutura e sintaxe dos modelos ARM.](template-syntax.md)
* Para recomendações sobre a criação de variáveis, consulte [as melhores práticas - variáveis.](template-best-practices.md#variables)
* Para um modelo de exemplo que atribui regras de segurança a um grupo de segurança de rede, consulte [as regras de segurança da rede](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) e o ficheiro de [parâmetros](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json).
