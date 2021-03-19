---
title: Criar funções de recolha de definição de UI
description: Descreve as funções a utilizar quando se trabalha com coleções, como matrizes e objetos.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 2a075c5c99f457681cd49e75014487bf9cca263c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87098201"
---
# <a name="createuidefinition-collection-functions"></a>Funções de recolha CreateUiDefinition

Estas funções podem ser usadas com coleções, como cordas JSON, matrizes e objetos.

## <a name="contains"></a>contains

Devoluções `true` se uma cadeia contiver o sub-adc de substring especificado, uma matriz contém o valor especificado, ou um objeto contém a chave especificada.

### <a name="example-string-contains"></a>Exemplo: cadeia contém

O seguinte exemplo `true` regressa:

```json
"[contains('webapp', 'web')]"
```

### <a name="example-array-contains"></a>Exemplo: matriz contém

Assuma `element1` devoluções `[1, 2, 3]` . O seguinte exemplo `false` regressa:

```json
"[contains(steps('demoStep').element1, 4)]"
```

### <a name="example-object-contains"></a>Exemplo: objeto contém

Assuma `element1` devoluções:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

O seguinte exemplo `true` regressa:

```json
"[contains(steps('demoStep').element1, 'key1')]"
```

## <a name="empty"></a>vazio

Retorna `true` se a corda, matriz ou objeto for nulo ou vazio.

### <a name="example-string-empty"></a>Exemplo: corda vazia

O seguinte exemplo `true` regressa:

```json
"[empty('')]"
```

### <a name="example-array-empty"></a>Exemplo: matriz vazia

Assuma `element1` devoluções `[1, 2, 3]` . O seguinte exemplo `false` regressa:

```json
"[empty(steps('demoStep').element1)]"
```

### <a name="example-object-empty"></a>Exemplo: objeto vazio

Assuma `element1` devoluções:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

O seguinte exemplo `false` regressa:

```json
"[empty(steps('demoStep').element1)]"
```

### <a name="example-null-and-undefined"></a>Exemplo: nulo e indefinido

Assumir `element1` é `null` ou indefinido. O seguinte exemplo `true` regressa:

```json
"[empty(steps('demoStep').element1)]"
```

## <a name="filter"></a>filter

Devolve uma nova matriz após a aplicação da lógica de filtragem fornecida como função lambda. O primeiro parâmetro é a matriz a utilizar para filtragem. O segundo parâmetro é a função lambda que especifica a lógica de filtragem.

A amostra que se segue devolve a `[ { "name": "abc" } ]` matriz.

```json
"[filter(parse('[{\"name\":\"abc\"},{\"name\":\"xyz\"}]'), (item) => contains(item.name, 'abc'))]"
```

## <a name="first"></a>primeiro

Devolve o primeiro carácter da cadeia especificada; primeiro valor da matriz especificada; ou a primeira chave e valor do objeto especificado.

### <a name="example-string-first"></a>Exemplo: corda primeiro

O seguinte exemplo `"c"` regressa:

```json
"[first('contoso')]"
```

### <a name="example-array-first"></a>Exemplo: matriz primeiro

Assuma `element1` devoluções `[1, 2, 3]` . O seguinte exemplo `1` regressa:

```json
"[first(steps('demoStep').element1)]"
```

#### <a name="example-object-first"></a>Exemplo: objeto primeiro

Assuma `element1` devoluções:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

O seguinte exemplo `{"key1": "Linux"}` regressa:

```json
"[first(steps('demoStep').element1)]"
```

## <a name="last"></a>último

Devolve o último carácter da cadeia especificada, o último valor da matriz especificada ou a última chave e valor do objeto especificado.

### <a name="example-string-last"></a>Exemplo: cadeia por último

O seguinte exemplo `"o"` regressa:

```json
"[last('contoso')]"
```

### <a name="example-array-last"></a>Exemplo: matriz por último

Assuma `element1` devoluções `[1, 2, 3]` . O seguinte exemplo `3` regressa:

```json
"[last(steps('demoStep').element1)]"
```

### <a name="example-object-last"></a>Exemplo: objeto por último

Assuma `element1` devoluções:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

O seguinte exemplo `{"key2": "Windows"}` regressa:

```json
"[last(steps('demoStep').element1)]"
```

## <a name="length"></a>length

Devolve o número de caracteres numa corda, o número de valores numa matriz ou o número de teclas num objeto.

### <a name="example-string-length"></a>Exemplo: comprimento da corda

O seguinte exemplo `7` regressa:

```json
"[length('Contoso')]"
```

### <a name="example-array-length"></a>Exemplo: comprimento da matriz

Assuma `element1` devoluções `[1, 2, 3]` . O seguinte exemplo `3` regressa:

```json
"[length(steps('demoStep').element1)]"
```

### <a name="example-object-length"></a>Exemplo: comprimento do objeto

Assuma `element1` devoluções:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

O seguinte exemplo `2` regressa:

```json
"[length(steps('demoStep').element1)]"
```

## <a name="map"></a>mapa

Retorna uma nova matriz depois de chamar uma função lambda em uma matriz fornecida. O primeiro parâmetro é a matriz a utilizar para a função lambda. O segundo parâmetro é a função lambda.

A amostra seguinte devolve uma nova matriz com cada valor duplicado. O resultado é `[2, 4, 6]` que.

```json
"[map(parse('[1, 2, 3]'), (item) => mul(2, item))]"
```

A amostra que se segue devolve uma nova `["abc", "xyz"]` matriz.

```json
"[map(parse('[{\"name\":\"abc\"},{\"name\":\"xyz\"}]'), (item) => item.name)]"
```

## <a name="skip"></a>saltar

Contorna um número especificado de elementos numa coleção e, em seguida, devolve os restantes elementos.

### <a name="example-string-skip"></a>Exemplo: salto de corda

O seguinte exemplo `"app"` regressa:

```json
"[skip('webapp', 3)]"
```

### <a name="example-array-skip"></a>Exemplo: salto de matriz

Assuma `element1` devoluções `[1, 2, 3]` . O seguinte exemplo `[3]` regressa:

```json
"[skip(steps('demoStep').element1, 2)]"
```

### <a name="example-object-skip"></a>Exemplo: salto de objeto

Assuma `element1` devoluções:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```
O seguinte exemplo `{"key2": "Windows"}` regressa:

```json
"[skip(steps('demoStep').element1, 1)]"
```

## <a name="split"></a>dividir

Devolve uma matriz de cordas que contém os sublutos da entrada delimitada pelo separador.

A amostra que se segue devolve a `[ "555", "867", "5309" ]` matriz.

```json
"[split('555-867-5309', '-')]"
```

## <a name="take"></a>take

Devolve um número especificado de caracteres contíguos desde o início da cadeia, um número especificado de valores contíguos desde o início da matriz, ou um número especificado de teclas e valores contíguos desde o início do objeto.

### <a name="example-string-take"></a>Exemplo: tomada de corda

O seguinte exemplo `"web"` regressa:

```json
"[take('webapp', 3)]"
```

### <a name="example-array-take"></a>Exemplo: tomada de matriz

Assuma `element1` devoluções `[1, 2, 3]` . O seguinte exemplo `[1, 2]` regressa:

```json
"[take(steps('demoStep').element1, 2)]"
```

### <a name="example-object-take"></a>Exemplo: tomada de objeto

Assuma `element1` devoluções:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

O seguinte exemplo `{"key1": "Linux"}` regressa:

```json
"[take(steps('demoStep').element1, 1)]"
```

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução ao Azure Resource Manager, consulte [a visão geral do Azure Resource Manager](../management/overview.md).
