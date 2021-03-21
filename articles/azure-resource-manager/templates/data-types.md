---
title: Tipos de dados em modelos
description: Descreve os tipos de dados que estão disponíveis nos modelos do Gestor de Recursos Azure.
ms.topic: conceptual
ms.author: tomfitz
author: tfitzmac
ms.date: 03/04/2021
ms.openlocfilehash: 7d3f15c8852e6e25c621baad9bc6f20c303ffdb9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102125141"
---
# <a name="data-types-in-arm-templates"></a>Tipos de dados em modelos ARM

Este artigo descreve os tipos de dados suportados nos modelos Azure Resource Manager (modelos ARM). Abrange tanto os tipos de dados JSON como os Bicep.

## <a name="supported-types"></a>Tipos suportados

Dentro de um modelo ARM, pode utilizar estes tipos de dados:

* matriz
* bool
* int
* objeto
* secureObject - indicado por modificador em Bicep
* secureString - indicado por modificador em Bicep
* string

## <a name="arrays"></a>Matrizes

As matrizes começam com um suporte esquerdo `[` () e terminam com um suporte direito `]` ().

Em JSON, uma matriz pode ser declarada numa única linha ou em várias linhas. Cada elemento é separado por uma vírgula.

Em Bicep, uma matriz deve ser declarada em várias linhas. Não use vírgulas entre valores.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleArray": {
    "type": "array",
    "defaultValue": [
      1,
      2,
      3
    ]
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleArray array = [
  1
  2
  3
]
```

---

Os elementos de uma matriz podem ser do mesmo tipo ou tipos diferentes.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "mixedArray": [
    "[resourceGroup().name]",
    1,
    true,
    "example string"
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var mixedArray = [
  resourceGroup().name
  1
  true
  'example string'
]
```

---

## <a name="booleans"></a>Booleanos

Ao especificar valores boolean, use `true` ou `false` . Não rodeie o valor com aspas.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleBool": {
    "type": "bool",
    "defaultValue": true
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleBool bool = true
```

---

## <a name="integers"></a>Números inteiros

Ao especificar valores inteiros, não utilize aspas.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleInt": {
    "type": "int",
    "defaultValue": 1
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleInt int = 1
```

---

Para os inteiros passados como parâmetros inline, a gama de valores pode ser limitada pela ferramenta SDK ou linha de comando que utiliza para implantação. Por exemplo, quando se utiliza o PowerShell para implantar um modelo, os tipos inteiros podem variar entre -2147483648 e 2147483647. Para evitar esta limitação, especifique grandes valores inteiros num [ficheiro de parâmetros](parameter-files.md). Os tipos de recursos aplicam os seus próprios limites para propriedades inteiros.

## <a name="objects"></a>Objetos

Os objetos começam com uma cinta esquerda `{` e terminam com uma cinta direita ( `}` ). Cada propriedade num objeto consiste em chave e valor. A chave e o valor são separados por um cólon `:` ( ).

No JSON, a chave é incluída em ações duplas. Cada propriedade é separada por uma vírgula.

Em Bicep, a chave não está fechada por citações. Não use vírgulas entre propriedades.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleObject": {
    "type": "object",
    "defaultValue": {
      "name": "test name",
      "id": "123-abc",
      "isCurrent": true,
      "tier": 1
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleObject object = {
  name: 'test name'
  id: '123-abc'
  isCurrent: true
  tier: 1
}
```

---

## <a name="strings"></a>Cadeias

No JSON, as cordas são marcadas com citações duplas. Em Bicep, as cordas são marcadas com citações individuais.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleString": {
    "type": "string",
    "defaultValue": "test value"
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleString string = 'test value'
```
---

## <a name="secure-strings-and-objects"></a>Prenda cordas e objetos

A cadeia segura utiliza o mesmo formato que o string, e o objeto seguro utiliza o mesmo formato que o objeto. Quando se define um parâmetro para uma cadeia segura ou objeto seguro, o valor do parâmetro não é guardado para o histórico de implementação e não está registado. No entanto, se definir esse valor seguro para uma propriedade que não está à espera de um valor seguro, o valor não está protegido. Por exemplo, se definir uma cadeia segura numa etiqueta, esse valor é armazenado como texto simples. Use cordas seguras para senhas e segredos.

Com o Bicep, adicione o `@secure()` modificador a uma corda ou objeto.

O exemplo a seguir mostra dois parâmetros seguros:

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "password": {
    "type": "secureString"
  },
  "configValues": {
    "type": "secureObject"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@secure()
param password string

@secure()
param configValues object
```

---

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a sintaxe do modelo, consulte [compreender a estrutura e a sintaxe dos modelos ARM](template-syntax.md).
