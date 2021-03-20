---
title: Criar funções de conversão de definição de UI
description: Descreve as funções a utilizar ao converter valores entre tipos de dados e codificações.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: b69cd35b27b343da08727b4c4ee9b4fd025e1df7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87098194"
---
# <a name="createuidefinition-conversion-functions"></a>Funções de conversão CreateUiDefinition

Estas funções podem ser utilizadas para converter valores entre tipos de dados JSON e codificações.

## <a name="bool"></a>bool

Converte o parâmetro num booleano. Esta função suporta parâmetros de número de tipo, string e Boolean. Semelhante a booleanas em JavaScript, qualquer valor exceto `0` ou `'false'` devoluções `true` .

O seguinte exemplo `true` regressa:

```json
"[bool(1)]"
```

O seguinte exemplo `false` regressa:

```json
"[bool(0)]"
```

O seguinte exemplo `true` regressa:

```json
"[bool(true)]"
```

O seguinte exemplo `true` regressa:

```json
"[bool('true')]"
```

## <a name="decodebase64"></a>descodificarBase64

Descodifica o parâmetro de uma cadeia codificada base-64. Esta função suporta parâmetros apenas de tipo de cadeia.

O seguinte exemplo `"Contoso"` regressa:

```json
"[decodeBase64('Q29udG9zbw==')]"
```

## <a name="decodeuricomponent"></a>decodeUriComponent

Descodifica o parâmetro de uma cadeia codificada url. Esta função suporta parâmetros apenas de tipo de cadeia.

O seguinte exemplo `"https://portal.azure.com/"` regressa:

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="encodebase64"></a>codificarBase64

Codifica o parâmetro para uma cadeia codificada base-64. Esta função suporta parâmetros apenas de tipo de cadeia.

O seguinte exemplo `"Q29udG9zbw=="` regressa:

```json
"[encodeBase64('Contoso')]"
```

## <a name="encodeuricomponent"></a>encodeUriComponent

Codifica o parâmetro para uma cadeia codificada url. Esta função suporta parâmetros apenas de tipo de cadeia.

O seguinte exemplo `"https%3A%2F%2Fportal.azure.com%2F"` regressa:

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

## <a name="float"></a>float

Converte o parâmetro num ponto flutuante. Esta função suporta parâmetros de número e corda do tipo.

O seguinte exemplo `1.0` regressa:

```json
"[float('1.0')]"
```

O seguinte exemplo `2.9` regressa:

```json
"[float(2.9)]"
```

## <a name="int"></a>int

Converte o parâmetro num inteiro. Esta função suporta parâmetros de número e corda do tipo.

O seguinte exemplo `1` regressa:

```json
"[int('1')]"
```

O seguinte exemplo `2` regressa:

```json
"[int(2.9)]"
```

## <a name="parse"></a>parse

Converte o parâmetro para um tipo nativo. Por outras palavras, esta função é o inverso de `string()` . Esta função suporta parâmetros apenas de tipo de cadeia.

O seguinte exemplo `1` regressa:

```json
"[parse('1')]"
```

O seguinte exemplo `true` regressa:

```json
"[parse('true')]"
```

O seguinte exemplo `[1,2,3]` regressa:

```json
"[parse('[1,2,3]')]"
```

O seguinte exemplo `{"type":"webapp"}` regressa:

```json
"[parse('{\"type\":\"webapp\"}')]"
```

## <a name="string"></a>string

Converte o parâmetro numa corda. Esta função suporta parâmetros de todos os tipos de dados JSON.

O seguinte exemplo `"1"` regressa:

```json
"[string(1)]"
```

O seguinte exemplo `"2.9"` regressa:

```json
"[string(2.9)]"
```

O seguinte exemplo `"[1,2,3]"` regressa:

```json
"[string([1,2,3])]"
```

O seguinte exemplo `"{"type":"webapp"}"` regressa:

```json
"[string({\"type\":\"webapp\"})]"
```

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução ao Azure Resource Manager, consulte [a visão geral do Azure Resource Manager](../management/overview.md).
