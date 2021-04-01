---
title: Sintaxe de modelo e expressões
description: Descreve a sintaxe declarativa JSON para modelos de Gestor de Recursos Azure (modelos ARM).
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 44a386ed849771dfba717c8d1414e64422d0c7bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97797048"
---
# <a name="syntax-and-expressions-in-arm-templates"></a>Sintaxe e expressões em modelos ARM

A sintaxe básica do modelo Azure Resource Manager (modelo ARM) é a Notação de Objetos JavaScript (JSON). No entanto, pode utilizar expressões para estender os valores JSON disponíveis dentro do modelo.  As expressões começam e terminam com parênteses retos: `[` e `]`, respetivamente. O valor da expressão é avaliado quando o modelo é implementado. Uma expressão pode devolver: uma cadeia de carateres, um número inteiro, um valor booleano, uma matriz ou um objeto.

Uma expressão de modelo não pode exceder 24.576 caracteres.

## <a name="use-functions"></a>Utilizar funções

O Azure Resource Manager fornece [funções](template-functions.md) que pode usar num modelo. O exemplo a seguir mostra uma expressão que utiliza uma função no valor predefinido de um parâmetro:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

Dentro da expressão, a sintaxe `resourceGroup()` chama uma das funções que o Gestor de Recursos fornece para ser usada dentro de um modelo. Neste caso, é a função grupo de [recursos.](template-functions-resource.md#resourcegroup) Tal como no JavaScript, as chamadas de função são formatadas como `functionName(arg1,arg2,arg3)` . A sintaxe `.location` recupera uma propriedade do objeto devolvido por essa função.

As funções do modelo e os seus parâmetros são insensíveis ao caso. Por exemplo, o Gestor de Recursos resolve `variables('var1')` e `VARIABLES('VAR1')` como o mesmo. Quando avaliada, a menos que a função modifie expressamente o caso (como `toUpper` `toLower` ou), a função preserva o caso. Certos tipos de recursos podem ter requisitos de casos separados da forma como as funções são avaliadas.

Para passar um valor de cadeia como parâmetro para uma função, utilize aspas únicas.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

A maioria das funções funciona da mesma forma quer seja implantada num grupo de recursos, subscrição, grupo de gestão ou inquilino. As seguintes funções têm restrições baseadas no âmbito:

* [resourceGroup](template-functions-resource.md#resourcegroup) - só pode ser utilizado em implementações para um grupo de recursos.
* [resourceId](template-functions-resource.md#resourceid) - pode ser usado em qualquer âmbito, mas os parâmetros válidos mudam dependendo do âmbito.
* [subscrição](template-functions-resource.md#subscription) - só pode ser usada em implementações para um grupo de recursos ou subscrição.

## <a name="escape-characters"></a>Personagens de fuga

Para ter um arranque de corda literal com um suporte esquerdo `[` e terminar com um suporte direito , mas não o `]` interprete como uma expressão, adicione um suporte extra para iniciar a corda com `[[` . Por exemplo, a variável:

```json
"demoVar1": "[[test value]"
```

Resolve para `[test value]` .

No entanto, se a corda literal não terminar com um suporte, não escape ao primeiro suporte. Por exemplo, a variável:

```json
"demoVar2": "[test] value"
```

Resolve para `[test] value` .

Para escapar a citações duplas numa expressão, como adicionar um objeto JSON no modelo, use o backslash.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

Ao passar em valores de parâmetros, a utilização de caracteres de fuga depende de onde o valor do parâmetro é especificado. Se definir um valor predefinido no modelo, precisa do suporte extra esquerdo.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1":{
            "type": "string",
            "defaultValue": "[[test value]"
        }
    },
    "resources": [],
    "outputs": {
        "exampleOutput": {
            "type": "string",
            "value": "[parameters('demoParam1')]"
        }
    }
}
```

Se utilizar o valor predefinido, o modelo retorna `[test value]` .

No entanto, se passarmos num valor de parâmetro através da linha de comando, os caracteres são interpretados literalmente. Implantação do modelo anterior com:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[[test value]"
```

Devoluções `[[test value]` . Em vez disso, use:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[test value]"
```

A mesma formatação aplica-se ao passar valores a partir de um ficheiro de parâmetro. Os personagens são interpretados literalmente. Quando utilizado com o modelo anterior, o seguinte ficheiro de parâmetros `[test value]` devolve:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1": {
            "value": "[test value]"
        }
   }
}
```

## <a name="null-values"></a>Valores nulos

Para definir uma propriedade como nula, pode utilizar `null` ou `[json('null')]`. A [função json](template-functions-object.md#json) devolve um objeto vazio quando fornece `null` como parâmetro. Em ambos os casos, os modelos do Gestor de Recursos tratam-no como se a propriedade não estivesse presente.

```json
"stringValue": null,
"objectValue": "[json('null')]"
```

## <a name="next-steps"></a>Passos seguintes

* Para a lista completa das funções do modelo, consulte as [funções do modelo ARM](template-functions.md).
* Para obter mais informações sobre ficheiros de modelos, consulte [a estrutura e sintaxe dos modelos ARM](template-syntax.md).
