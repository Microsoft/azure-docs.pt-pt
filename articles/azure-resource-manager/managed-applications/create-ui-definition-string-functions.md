---
title: Criar funções de cadeia de definição de UI
description: Descreve as funções de corda a utilizar ao construir definições de UI para aplicações geridas azure
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: c662948542c36cd93f889ca045ee245c15c7bb11
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87098161"
---
# <a name="createuidefinition-string-functions"></a>Funções de cadeia CreateUiDefinition

Estas funções podem ser utilizadas com cordas JSON.

## <a name="concat"></a>concat

Concatenates uma ou mais cordas.

Por exemplo, se o valor de saída de `element1` `"Contoso"` se, então este exemplo devolve a `"Demo Contoso app"` cadeia:

```json
"[concat('Demo ', steps('step1').element1, ' app')]"
```

## <a name="endswith"></a>endsWith

Determina se uma corda termina com um valor.

A amostra que se segue retorna verdadeiramente.

```json
"[endsWith('tuvwxyz', 'xyz')]"
```

## <a name="guid"></a>guid

Gera uma cadeia globalmente única (GUID).

O exemplo a seguir devolve um valor `"c7bc8bdc-7252-4a82-ba53-7c468679a511"` como:

```json
"[guid()]"
```

## <a name="indexof"></a>indexOf

Devolve a primeira posição de um valor dentro de uma corda ou -1 se não for encontrada.

A amostra seguinte devolve 2.

```json
"[indexOf('abcdef', 'cd')]"
```

## <a name="lastindexof"></a>lastIndexOf

Devolve a última posição de um valor numa cadeia ou -1 se não for encontrada.

A amostra seguinte devolve 3.

```json
"[lastIndexOf('test', 't')]"
```

## <a name="replace"></a>substituir

Devolve uma cadeia na qual todas as ocorrências da corda especificada na cadeia atual são substituídas por outra corda.

O seguinte exemplo `"Contoso.com web app"` regressa:

```json
"[replace('Contoso.net web app', '.net', '.com')]"
```

## <a name="startswith"></a>startsWith

Determina se uma corda começa com um valor.

A amostra que se segue retorna verdadeiramente.

```json
"[startsWith('abcdefg', 'ab')]"
```

## <a name="substring"></a>substring

Devolve o sub-cordão da cadeia especificada. O sub-adc de sublagem começa no índice especificado e tem o comprimento especificado.

O seguinte exemplo `"web"` regressa:

```json
"[substring('Contoso.com web app', 12, 3)]"
```

## <a name="tolower"></a>toLower

Devolve uma corda convertida em minúsculas.

O seguinte exemplo `"contoso"` regressa:

```json
"[toLower('CONTOSO')]"
```

## <a name="toupper"></a>toupper

Devolve uma corda convertida em maiúscula.

O seguinte exemplo `"CONTOSO"` regressa:

```json
"[toUpper('contoso')]"
```

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução ao Azure Resource Manager, consulte [a visão geral do Azure Resource Manager](../management/overview.md).

