---
title: Sintaxe e expressões do modelo de Azure Resource Manager
description: Descreve a sintaxe JSON declarativa para modelos de Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: tomfitz
ms.openlocfilehash: 1e7288da19e2e81d609b952e03d5143b03a65c63
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259486"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Sintaxe e expressões em modelos de Azure Resource Manager

A sintaxe básica do modelo é JSON. No entanto, você pode usar expressões para estender os valores JSON disponíveis no modelo.  As expressões começam e terminam com colchetes `]`: `[` e, respectivamente. O valor da expressão é avaliado quando o modelo é implantado. Uma expressão pode retornar uma cadeia de caracteres, um inteiro, um booliano, uma matriz ou um objeto.

Uma expressão de modelo não pode exceder 24.576 caracteres.

## <a name="use-functions"></a>Usar funções

O exemplo a seguir mostra uma expressão no valor padrão de um parâmetro:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

Dentro da expressão, a sintaxe `resourceGroup()` chama uma das funções que o Gerenciador de recursos fornece para uso em um modelo. Nesse caso, é a função [resourcegroup](resource-group-template-functions-resource.md#resourcegroup) . Assim como no JavaScript, as chamadas de função são `functionName(arg1,arg2,arg3)`formatadas como. A sintaxe `.location` recupera uma propriedade do objeto retornado por essa função.

As funções de modelo e seus parâmetros não diferenciam maiúsculas de minúsculas. Por exemplo, o Resource Manager resolve **variáveis (' var1 ')** e **variáveis (' var1 ')** como a mesma. Quando avaliado, a menos que a função modifique expressamente maiúsculas de minúsculas (como toUpper ou toLower), a função preserva o caso. Determinados tipos de recursos podem ter requisitos de caso separados de como as funções são avaliadas.

Para passar um valor de cadeia de caracteres como um parâmetro para uma função, use aspas simples.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

## <a name="escape-characters"></a>Caracteres de escape

Para que uma cadeia de caracteres literal comece com um `[` colchete esquerdo e termine com um `]`colchete direito, mas não a tenha interpretada como uma expressão, adicione um colchete extra para `[[`iniciar a cadeia de caracteres com. Por exemplo, a variável:

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

## <a name="next-steps"></a>Passos Seguintes

* Para obter a lista completa de funções de modelo, consulte [Azure Resource Manager funções de modelo](resource-group-template-functions.md).
* Para obter mais informações sobre arquivos de modelo, consulte [entender a estrutura e a sintaxe de modelos de Azure Resource Manager](resource-group-authoring-templates.md).
