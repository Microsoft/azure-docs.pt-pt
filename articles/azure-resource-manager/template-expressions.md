---
title: Sintaxe e expressões de modelo
description: Descreve a sintaxe JSON declarativa para modelos de Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/03/2019
ms.openlocfilehash: 046f7f4866e9b5933c55bc5a9d0ee96c945bff0e
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149190"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Sintaxe e expressões em modelos de Azure Resource Manager

A sintaxe básica do modelo é JSON. No entanto, você pode usar expressões para estender os valores JSON disponíveis no modelo.  As expressões começam e terminam com colchetes: `[` e `]`, respectivamente. O valor da expressão é avaliado quando o modelo é implantado. Uma expressão pode retornar uma cadeia de caracteres, um inteiro, um booliano, uma matriz ou um objeto.

Uma expressão de modelo não pode exceder 24.576 caracteres.

## <a name="use-functions"></a>Utilizar funções

O exemplo a seguir mostra uma expressão no valor padrão de um parâmetro:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

Dentro da expressão, a sintaxe `resourceGroup()` chama uma das funções que o Gerenciador de recursos fornece para uso em um modelo. Nesse caso, é a função [resourcegroup](resource-group-template-functions-resource.md#resourcegroup) . Assim como no JavaScript, as chamadas de função são formatadas como `functionName(arg1,arg2,arg3)`. A sintaxe `.location` recupera uma propriedade do objeto retornado por essa função.

As funções de modelo e seus parâmetros não diferenciam maiúsculas de minúsculas. Por exemplo, o Resource Manager resolve **variáveis (' var1 ')** e **variáveis (' var1 ')** como a mesma. Quando avaliado, a menos que a função modifique expressamente maiúsculas de minúsculas (como toUpper ou toLower), a função preserva o caso. Determinados tipos de recursos podem ter requisitos de caso separados de como as funções são avaliadas.

Para passar um valor de cadeia de caracteres como um parâmetro para uma função, use aspas simples.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

## <a name="escape-characters"></a>Caracteres de escape

Para que uma cadeia de caracteres literal comece com um colchete esquerdo `[` e termine com um colchete direito `]`, mas não o tenha interpretado como uma expressão, adicione um colchete extra para iniciar a cadeia de caracteres com `[[`. Por exemplo, a variável:

```json
"demoVar1": "[[test value]"
```

Resolve para `[test value]`.

No entanto, se a cadeia de caracteres literal não terminar com um colchete, não Percorra o primeiro colchete. Por exemplo, a variável:

```json
"demoVar2": "[test] value"
```

Resolve para `[test] value`.

Para escapar aspas duplas em uma expressão, como adicionar um objeto JSON no modelo, use a barra invertida.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

## <a name="next-steps"></a>Passos seguintes

* Para obter a lista completa de funções de modelo, consulte [Azure Resource Manager funções de modelo](resource-group-template-functions.md).
* Para obter mais informações sobre arquivos de modelo, consulte [entender a estrutura e a sintaxe de modelos de Azure Resource Manager](resource-group-authoring-templates.md).
