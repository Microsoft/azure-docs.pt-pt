---
title: Sintaxe de modelo e expressões
description: Descreve a sintaxe declarativa da JSON para os modelos do Gestor de Recursos Azure.
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 172838fa24709eb60fbcb6a68277f44bbd42f01e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460114"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Sintaxe e expressões em modelos de Gestor de Recursos Azure

A sintaxe básica do modelo é JSON. No entanto, pode utilizar expressões para estender os valores JSON disponíveis dentro do modelo.  As expressões começam e terminam com parênteses retos: `[` e `]`, respetivamente. O valor da expressão é avaliado quando o modelo é implementado. Uma expressão pode devolver: uma cadeia de carateres, um número inteiro, um valor booleano, uma matriz ou um objeto.

Uma expressão de modelo não pode exceder 24.576 caracteres.

## <a name="use-functions"></a>Utilizar funções

O Azure Resource Manager fornece [funções](template-functions.md) que pode utilizar num modelo. O exemplo seguinte mostra uma expressão que utiliza uma função no valor padrão de um parâmetro:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

Dentro da expressão, a `resourceGroup()` sintaxe chama uma das funções que o Gestor de Recursos fornece para uso dentro de um modelo. Neste caso, é a função do [Grupo de Recursos.](template-functions-resource.md#resourcegroup) Tal como no JavaScript, as `functionName(arg1,arg2,arg3)`chamadas de função são formatadas como . A sintaxe `.location` recupera uma propriedade do objeto devolvido por essa função.

As funções do modelo e os seus parâmetros são insensíveis aos casos. Por exemplo, o Gestor de Recursos resolve **variáveis ('var1')** e **VARIÁVEIs ('VAR1')** como as mesmas. Quando avaliada, a menos que a função modifica expressamente o caso (como o toUpper ou o toLower), a função preserva a caixa. Certos tipos de recursos podem ter requisitos de caso sem serem separados da forma como as funções são avaliadas.

Para passar um valor de cadeia como parâmetro para uma função, utilize aspas únicas.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

A maioria das funções funciona da mesma forma, quer seja implantada num grupo de recursos, subscrição, grupo de gestão ou inquilino. As seguintes funções têm restrições baseadas no âmbito:

* [recursosGroup](template-functions-resource.md#resourcegroup) - só pode ser utilizado em implantações para um grupo de recursos.
* [recursosId](template-functions-resource.md#resourceid) - pode ser usado em qualquer âmbito, mas os parâmetros válidos mudam dependendo do âmbito.
* [subscrição](template-functions-resource.md#subscription) - só pode ser utilizada em implantações para um grupo de recursos ou subscrição.

## <a name="escape-characters"></a>Personagens de fuga

Para ter um início de `[` corda literal com `]`um suporte esquerdo e terminar com um suporte direito, mas `[[`não tê-lo interpretado como uma expressão, adicione um suporte extra para iniciar a corda com . Por exemplo, a variável:

```json
"demoVar1": "[[test value]"
```

Resolve-se `[test value]`a.

No entanto, se a corda literal não terminar com um suporte, não escape do primeiro suporte. Por exemplo, a variável:

```json
"demoVar2": "[test] value"
```

Resolve-se `[test] value`a.

Para escapar a citações duplas numa expressão, como adicionar um objeto JSON no modelo, use o backslash.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

Ao passar em valores de parâmetros, o uso de caracteres de fuga depende de onde o valor do parâmetro é especificado. Se definir um valor predefinido no modelo, precisa do suporte extra esquerdo.

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

Se utilizar o valor predefinido, o modelo volta `[test value]`.

No entanto, se passar em um valor de parâmetro através da linha de comando, os caracteres são interpretados literalmente. Implantação do modelo anterior com:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[[test value]"
```

Devoluções. `[[test value]` Em vez disso, use:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[test value]"
```

A mesma formatação aplica-se ao passar valores a partir de um ficheiro de parâmetros. Os personagens são interpretados literalmente. Quando utilizado com o modelo anterior, o `[test value]`ficheiro do parâmetro seguinte devolve:

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

Para fixar um imóvel a nu, pode utilizar **nulo** ou **[json('null')]**. A [função json](template-functions-array.md#json) devolve um `null` objeto vazio quando fornece como parâmetro. Em ambos os casos, os modelos do Gestor de Recursos tratam-no como se a propriedade não estivesse presente.

```json
"stringValue": null,
"objectValue": "[json('null')]"
```

## <a name="next-steps"></a>Passos seguintes

* Para obter a lista completa das funções do modelo, consulte as funções do modelo do Gestor de [Recursos do Azure](template-functions.md).
* Para obter mais informações sobre ficheiros de modelos, consulte [Compreender a estrutura e a sintaxe dos modelos do Gestor de Recursos Azure](template-syntax.md).
