---
title: Criar funções de definição de UI
description: Descreve as funções a utilizar na construção de definições de UI para aplicações geridas azure
author: tfitzmac
ms.topic: conceptual
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: 6e56c5e528a17d42a75da54158f00857a917645c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78380860"
---
# <a name="createuidefinition-functions"></a>Funções CreateUiDefini
Esta secção contém as assinaturas para todas as funções suportadas de uma CreateUiDefinition.

Para utilizar uma função, rodeie a declaração com suportes quadrados. Por exemplo:

```json
"[function()]"
```

Cordas e outras funções podem ser referenciadas como parâmetros para uma função, mas as cordas devem ser rodeadas em citações únicas. Por exemplo:

```json
"[fn1(fn2(), 'foobar')]"
```

Quando aplicável, pode fazer referência às propriedades da saída de uma função utilizando o operador de pontos. Por exemplo:

```json
"[func().prop1]"
```

## <a name="referencing-functions"></a>Funções de referenciação
Estas funções podem ser utilizadas para referência de saídas a partir das propriedades ou contexto de uma CreateUiDefinition.

### <a name="basics"></a>básicos
Devolve os valores de saída de um elemento definido na etapa basics.

O exemplo seguinte devolve a saída do elemento denominado `foo` na etapa basics:

```json
"[basics('foo')]"
```

### <a name="steps"></a>passos
Devolve os valores de saída de um elemento definido no passo especificado. Para obter os valores de saída dos elementos na etapa Basics, use `basics()` em vez disso.

O exemplo seguinte devolve a saída do elemento denominado `bar` no passo denominado `foo`:

```json
"[steps('foo').bar]"
```

### <a name="location"></a>localização
Devolve a localização selecionada na etapa basics ou no contexto atual.

O exemplo que se segue pode voltar `"westus"`:

```json
"[location()]"
```

## <a name="string-functions"></a>Funções de cadeia
Estas funções só podem ser utilizadas com cordas JSON.

### <a name="concat"></a>concat
Concatenates uma ou mais cordas.

Por exemplo, se o valor de saída de `element1` se `"bar"`, então este exemplo devolve a cadeia `"foobar!"`:

```json
"[concat('foo', steps('step1').element1, '!')]"
```

### <a name="substring"></a>substring
Devolve a subcadeia da corda especificada. O substring começa no índice especificado e tem o comprimento especificado.

O exemplo seguinte retorna `"ftw"`:

```json
"[substring('azure-ftw!!!1one', 6, 3)]"
```

### <a name="replace"></a>substituir
Devolve uma corda na qual todas as ocorrências da corda especificada na cadeia atual são substituídas por outra corda.

O exemplo seguinte retorna `"Everything is awesome!"`:

```json
"[replace('Everything is terrible!', 'terrible', 'awesome')]"
```

### <a name="guid"></a>guid
Gera uma corda globalmente única (GUID).

O exemplo que se segue pode voltar `"c7bc8bdc-7252-4a82-ba53-7c468679a511"`:

```json
"[guid()]"
```

### <a name="tolower"></a>toLower
Devolve uma corda convertida em minúscula.

O exemplo seguinte retorna `"foobar"`:

```json
"[toLower('FOOBAR')]"
```

### <a name="toupper"></a>toUpper
Devolve uma corda convertida em maiúscula.

O exemplo seguinte retorna `"FOOBAR"`:

```json
"[toUpper('foobar')]"
```

## <a name="collection-functions"></a>Funções de recolha
Estas funções podem ser usadas com coleções, como cordas JSON, matrizes e objetos.

### <a name="contains"></a>Contém
Devoluções `true` se uma cadeia contiver o substring especificado, uma matriz contém o valor especificado, ou um objeto contém a chave especificada.

#### <a name="example-1-string"></a>Exemplo 1: corda
O exemplo seguinte retorna `true`:

```json
"[contains('foobar', 'foo')]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Assuma `element1` devoluções `[1, 2, 3]`. O exemplo seguinte retorna `false`:

```json
"[contains(steps('foo').element1, 4)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Assuma `element1` devoluções:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

O exemplo seguinte retorna `true`:

```json
"[contains(steps('foo').element1, 'key1')]"
```

### <a name="length"></a>length
Devolve o número de caracteres numa corda, o número de valores numa matriz ou o número de teclas num objeto.

#### <a name="example-1-string"></a>Exemplo 1: corda
O exemplo seguinte retorna `6`:

```json
"[length('foobar')]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Assuma `element1` devoluções `[1, 2, 3]`. O exemplo seguinte retorna `3`:

```json
"[length(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Assuma `element1` devoluções:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

O exemplo seguinte retorna `2`:

```json
"[length(steps('foo').element1)]"
```

### <a name="empty"></a>vazio
Devoluções `true` se a corda, a matriz ou o objeto estiverem nulos ou vazios.

#### <a name="example-1-string"></a>Exemplo 1: corda
O exemplo seguinte retorna `true`:

```json
"[empty('')]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Assuma `element1` devoluções `[1, 2, 3]`. O exemplo seguinte retorna `false`:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Assuma `element1` devoluções:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

O exemplo seguinte retorna `false`:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-4-null-and-undefined"></a>Exemplo 4: nulo e indefinido
Assuma que `element1` é `null` ou indefinida. O exemplo seguinte retorna `true`:

```json
"[empty(steps('foo').element1)]"
```

### <a name="first"></a>primeiro
Devolve o primeiro carácter da corda especificada; primeiro valor da matriz especificada; ou a primeira chave e valor do objeto especificado.

#### <a name="example-1-string"></a>Exemplo 1: corda
O exemplo seguinte retorna `"f"`:

```json
"[first('foobar')]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Assuma `element1` devoluções `[1, 2, 3]`. O exemplo seguinte retorna `1`:

```json
"[first(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Assuma `element1` devoluções:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
O exemplo seguinte retorna `{"key1": "foobar"}`:

```json
"[first(steps('foo').element1)]"
```

### <a name="last"></a>última
Devolve o último carácter da corda especificada, o último valor da matriz especificada, ou a última chave e valor do objeto especificado.

#### <a name="example-1-string"></a>Exemplo 1: corda
O exemplo seguinte retorna `"r"`:

```json
"[last('foobar')]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Assuma `element1` devoluções `[1, 2, 3]`. O exemplo seguinte retorna `2`:

```json
"[last(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Assuma `element1` devoluções:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

O exemplo seguinte retorna `{"key2": "raboof"}`:

```json
"[last(steps('foo').element1)]"
```

### <a name="take"></a>tomar
Devolve um número especificado de caracteres contíguos desde o início da corda, um número especificado de valores contíguos desde o início da matriz, ou um número especificado de chaves e valores contíguos desde o início do objeto.

#### <a name="example-1-string"></a>Exemplo 1: corda
O exemplo seguinte retorna `"foo"`:

```json
"[take('foobar', 3)]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Assuma `element1` devoluções `[1, 2, 3]`. O exemplo seguinte retorna `[1, 2]`:

```json
"[take(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Assuma `element1` devoluções:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

O exemplo seguinte retorna `{"key1": "foobar"}`:

```json
"[take(steps('foo').element1, 1)]"
```

### <a name="skip"></a>saltar
Contorna um número especificado de elementos numa coleção e, em seguida, devolve os elementos restantes.

#### <a name="example-1-string"></a>Exemplo 1: corda
O exemplo seguinte retorna `"bar"`:

```json
"[skip('foobar', 3)]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Assuma `element1` devoluções `[1, 2, 3]`. O exemplo seguinte retorna `[3]`:

```json
"[skip(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Assuma `element1` devoluções:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
O exemplo seguinte retorna `{"key2": "raboof"}`:

```json
"[skip(steps('foo').element1, 1)]"
```

## <a name="logical-functions"></a>Funções lógicas
Estas funções podem ser utilizadas em condicional. Algumas funções podem não suportar todos os tipos de dados da JSON.

### <a name="equals"></a>é igual a
Devoluções `true` se ambos os parâmetros tiverem o mesmo tipo e valor. Esta função suporta todos os tipos de dados da JSON.

O exemplo seguinte retorna `true`:

```json
"[equals(0, 0)]"
```

O exemplo seguinte retorna `true`:

```json
"[equals('foo', 'foo')]"
```

O exemplo seguinte retorna `false`:

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

### <a name="less"></a>menos
Devoluções `true` se o primeiro parâmetro for estritamente inferior ao segundo parâmetro. Esta função suporta parâmetros apenas de número e corda tipo.

O exemplo seguinte retorna `true`:

```json
"[less(1, 2)]"
```

O exemplo seguinte retorna `false`:

```json
"[less('9', '10')]"
```

### <a name="lessorequals"></a>lessOrEquals
Devoluções `true` se o primeiro parâmetro for inferior ou igual ao segundo parâmetro. Esta função suporta parâmetros apenas de número e corda tipo.

O exemplo seguinte retorna `true`:

```json
"[lessOrEquals(2, 2)]"
```

### <a name="greater"></a>maior
Devoluções `true` se o primeiro parâmetro for estritamente maior que o segundo parâmetro. Esta função suporta parâmetros apenas de número e corda tipo.

O exemplo seguinte retorna `false`:

```json
"[greater(1, 2)]"
```

O exemplo seguinte retorna `true`:

```json
"[greater('9', '10')]"
```

### <a name="greaterorequals"></a>greaterOrEquals
Devoluções `true` se o primeiro parâmetro for maior ou igual ao segundo parâmetro. Esta função suporta parâmetros apenas de número e corda tipo.

O exemplo seguinte retorna `true`:

```json
"[greaterOrEquals(2, 2)]"
```

### <a name="and"></a>e
Devoluções `true` se todos os parâmetros avaliarem para `true`. Esta função suporta apenas dois ou mais parâmetros do tipo Boolean.

O exemplo seguinte retorna `true`:

```json
"[and(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

O exemplo seguinte retorna `false`:

```json
"[and(equals(0, 0), greater(1, 2))]"
```

### <a name="or"></a>ou
Devoluções `true` se pelo menos um dos parâmetros avaliar para `true`. Esta função suporta apenas dois ou mais parâmetros do tipo Boolean.

O exemplo seguinte retorna `true`:

```json
"[or(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

O exemplo seguinte retorna `true`:

```json
"[or(equals(0, 0), greater(1, 2))]"
```

### <a name="not"></a>não
Devoluções `true` se o parâmetro avaliar para `false`. Esta função suporta parâmetros apenas do tipo Boolean.

O exemplo seguinte retorna `true`:

```json
"[not(false)]"
```

O exemplo seguinte retorna `false`:

```json
"[not(equals(0, 0))]"
```

### <a name="coalesce"></a>coalesce
Devolve o valor do primeiro parâmetro não nulo. Esta função suporta todos os tipos de dados da JSON.

Assuma que `element1` e `element2` são indefinidos. O exemplo seguinte retorna `"foobar"`:

```json
"[coalesce(steps('foo').element1, steps('foo').element2, 'foobar')]"
```

## <a name="conversion-functions"></a>Funções de conversão
Estas funções podem ser utilizadas para converter valores entre tipos de dados JSON e codificações.

### <a name="int"></a>int
Converte o parâmetro para um inteiro. Esta função suporta parâmetros de tipo de número e corda.

O exemplo seguinte retorna `1`:

```json
"[int('1')]"
```

O exemplo seguinte retorna `2`:

```json
"[int(2.9)]"
```

### <a name="float"></a>float
Converte o parâmetro num ponto flutuante. Esta função suporta parâmetros de tipo de número e corda.

O exemplo seguinte retorna `1.0`:

```json
"[float('1.0')]"
```

O exemplo seguinte retorna `2.9`:

```json
"[float(2.9)]"
```

### <a name="string"></a>string
Converte o parâmetro numa corda. Esta função suporta parâmetros de todos os tipos de dados da JSON.

O exemplo seguinte retorna `"1"`:

```json
"[string(1)]"
```

O exemplo seguinte retorna `"2.9"`:

```json
"[string(2.9)]"
```

O exemplo seguinte retorna `"[1,2,3]"`:

```json
"[string([1,2,3])]"
```

O exemplo seguinte retorna `"{"foo":"bar"}"`:

```json
"[string({\"foo\":\"bar\"})]"
```

### <a name="bool"></a>bool
Converte o parâmetro para um Boolean. Esta função suporta parâmetros de tipo número, corda e Boolean. Semelhante a Booleans no JavaScript, qualquer valor exceto `0` ou `'false'` retorna `true`.

O exemplo seguinte retorna `true`:

```json
"[bool(1)]"
```

O exemplo seguinte retorna `false`:

```json
"[bool(0)]"
```

O exemplo seguinte retorna `true`:

```json
"[bool(true)]"
```

O exemplo seguinte retorna `true`:

```json
"[bool('true')]"
```

### <a name="parse"></a>parse
Converte o parâmetro para um tipo nativo. Por outras palavras, esta função é o inverso de `string()`. Esta função suporta parâmetros apenas de cadeia de tipo.

O exemplo seguinte retorna `1`:

```json
"[parse('1')]"
```

O exemplo seguinte retorna `true`:

```json
"[parse('true')]"
```

O exemplo seguinte retorna `[1,2,3]`:

```json
"[parse('[1,2,3]')]"
```

O exemplo seguinte retorna `{"foo":"bar"}`:

```json
"[parse('{\"foo\":\"bar\"}')]"
```

### <a name="encodebase64"></a>encodeBase64
Codifica o parâmetro a uma cadeia codificada base-64. Esta função suporta parâmetros apenas de cadeia de tipo.

O exemplo seguinte retorna `"Zm9vYmFy"`:

```json
"[encodeBase64('foobar')]"
```

### <a name="decodebase64"></a>decodeBase64
Descodifica o parâmetro de uma corda codificada base-64. Esta função suporta parâmetros apenas de cadeia de tipo.

O exemplo seguinte retorna `"foobar"`:

```json
"[decodeBase64('Zm9vYmFy')]"
```

### <a name="encodeuricomponent"></a>encodeUriComponent
Codifica o parâmetro a uma cadeia codificada por URL. Esta função suporta parâmetros apenas de cadeia de tipo.

O exemplo seguinte retorna `"https%3A%2F%2Fportal.azure.com%2F"`:

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

### <a name="decodeuricomponent"></a>decodeUriComponent
Descodifica o parâmetro de uma cadeia codificada por URL. Esta função suporta parâmetros apenas de cadeia de tipo.

O exemplo seguinte retorna `"https://portal.azure.com/"`:

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="math-functions"></a>Funções matemáticas
### <a name="add"></a>adicionar
Adiciona dois números e devolve o resultado.

O exemplo seguinte retorna `3`:

```json
"[add(1, 2)]"
```

### <a name="sub"></a>sub
Subtrai o segundo número do primeiro número e devolve o resultado.

O exemplo seguinte retorna `1`:

```json
"[sub(3, 2)]"
```

### <a name="mul"></a>mul
Multiplica dois números e devolve o resultado.

O exemplo seguinte retorna `6`:

```json
"[mul(2, 3)]"
```

### <a name="div"></a>div
Divide o primeiro número pelo segundo número e devolve o resultado. O resultado é sempre um inteiro.

O exemplo seguinte retorna `2`:

```json
"[div(6, 3)]"
```

### <a name="mod"></a>mod
Divide o primeiro número pelo segundo número e devolve o restante.

O exemplo seguinte retorna `0`:

```json
"[mod(6, 3)]"
```

O exemplo seguinte retorna `2`:

```json
"[mod(6, 4)]"
```

### <a name="min"></a>mín
Devolve o pequeno dos dois números.

O exemplo seguinte retorna `1`:

```json
"[min(1, 2)]"
```

### <a name="max"></a>máx
Devolve o maior dos dois números.

O exemplo seguinte retorna `2`:

```json
"[max(1, 2)]"
```

### <a name="range"></a>alcance
Gera uma sequência de números integrais dentro do intervalo especificado.

O exemplo seguinte retorna `[1,2,3]`:

```json
"[range(1, 3)]"
```

### <a name="rand"></a>rand
Devolve um número integral aleatório dentro do intervalo especificado. Esta função não gera números aleatórios criptograficamente seguros.

O exemplo que se segue pode voltar `42`:

```json
"[rand(-100, 100)]"
```

### <a name="floor"></a>piso
Devolve o maior inteiro inferior ou igual ao número especificado.

O exemplo seguinte retorna `3`:

```json
"[floor(3.14)]"
```

### <a name="ceil"></a>ceil
Devolve o maior inteiro maior ou igual ao número especificado.

O exemplo seguinte retorna `4`:

```json
"[ceil(3.14)]"
```

## <a name="date-functions"></a>Funções de data
### <a name="utcnow"></a>utcNow
Devolve uma cadeia no formato ISO 8601 da data e hora atuais no computador local.

O exemplo que se segue pode voltar `"1990-12-31T23:59:59.000Z"`:

```json
"[utcNow()]"
```

### <a name="addseconds"></a>addSeconds
Adiciona um número integral de segundos à marca de tempo especificada.

O exemplo seguinte retorna `"1991-01-01T00:00:00.000Z"`:

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

### <a name="addminutes"></a>addMinutes
Adiciona um número integral de minutos à marca de tempo especificada.

O exemplo seguinte retorna `"1991-01-01T00:00:59.000Z"`:

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

### <a name="addhours"></a>addHours
Adiciona um número integral de horas à marca de tempo especificada.

O exemplo seguinte retorna `"1991-01-01T00:59:59.000Z"`:

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="next-steps"></a>Passos seguintes
* Para uma introdução ao Gestor de Recursos Azure, consulte a [visão geral do Gestor de Recursos do Azure.](../management/overview.md)

