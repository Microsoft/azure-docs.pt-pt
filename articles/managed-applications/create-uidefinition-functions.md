---
title: Aplicação gerida do Azure criar funções da definição de interface do Usuário | Documentos da Microsoft
description: Descreve as funções para utilizar durante a criação de definições de IU para aplicações geridas do Azure
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: 80fd593eecf189d516a8c9d7ef2a94ec9f23fc39
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587925"
---
# <a name="createuidefinition-functions"></a>Funções de CreateUiDefinition
Esta secção contém as assinaturas para todas as funções suportadas de um CreateUiDefinition.

Para utilizar uma função, coloque a declaração com Parênteses Retos. Por exemplo:

```json
"[function()]"
```

Cadeias de caracteres e outras funções podem ser referenciadas como parâmetros para uma função, mas tem rodeadas cadeias de caracteres de plicas. Por exemplo:

```json
"[fn1(fn2(), 'foobar')]"
```

Quando aplicável, pode referenciar propriedades do resultado de uma função ao utilizar o operador de ponto. Por exemplo:

```json
"[func().prop1]"
```

## <a name="referencing-functions"></a>Funções de referência
Estas funções podem ser utilizadas para referenciar o saídas das propriedades ou contexto de um CreateUiDefinition.

### <a name="basics"></a>Noções básicas
Devolve os valores de saída de um elemento que está definido no passo Noções básicas.

O exemplo seguinte devolve o resultado do elemento denominado `foo` no passo Noções básicas:

```json
"[basics('foo')]"
```

### <a name="steps"></a>passos
Devolve os valores de saída de um elemento que está definido no passo especificado. Para obter os valores de saída de elementos no passo Noções básicas, utilize `basics()` em vez disso.

O exemplo seguinte devolve o resultado do elemento denominado `bar` no passo com o nome `foo`:

```json
"[steps('foo').bar]"
```

### <a name="location"></a>localização
Devolve a localização que selecionou no passo as noções básicas ou contexto atual.

O exemplo a seguir poderia retornar `"westus"`:

```json
"[location()]"
```

## <a name="string-functions"></a>Funções de cadeia
Estas funções só podem ser utilizadas com cadeias de caracteres do JSON.

### <a name="concat"></a>concat
Concatena cadeias de caracteres de um ou mais.

Por exemplo, se o valor de saída `element1` se `"bar"`, em seguida, neste exemplo devolve a cadeia de caracteres `"foobar!"`:

```json
"[concat('foo', steps('step1').element1, '!')]"
```

### <a name="substring"></a>subcadeia
Devolve a subcadeia da cadeia de caracteres especificada. A subcadeia começa no índice especificado e tem o comprimento especificado.

O exemplo seguinte devolve `"ftw"`:

```json
"[substring('azure-ftw!!!1one', 6, 3)]"
```

### <a name="replace"></a>substituir
Devolve uma cadeia de caracteres em que todas as ocorrências da cadeia de caracteres especificada na cadeia de caracteres atual são substituídas por outra cadeia.

O exemplo seguinte devolve `"Everything is awesome!"`:

```json
"[replace('Everything is terrible!', 'terrible', 'awesome')]"
```

### <a name="guid"></a>GUID
Gera uma cadeia de caracteres exclusiva global (GUID).

O exemplo a seguir poderia retornar `"c7bc8bdc-7252-4a82-ba53-7c468679a511"`:

```json
"[guid()]"
```

### <a name="tolower"></a>toLower
Devolve uma cadeia de caracteres convertida em minúscula.

O exemplo seguinte devolve `"foobar"`:

```json
"[toLower('FOOBAR')]"
```

### <a name="toupper"></a>toUpper
Devolve uma cadeia de caracteres convertida em maiúsculas.

O exemplo seguinte devolve `"FOOBAR"`:

```json
"[toUpper('foobar')]"
```

## <a name="collection-functions"></a>Funções de coleção
Estas funções podem ser utilizadas com coleções, como cadeias de caracteres do JSON, matrizes e objetos.

### <a name="contains"></a>contém
Devolve `true` se uma cadeia de caracteres contém a subcadeia especificada, uma matriz contém o valor especificado ou um objeto contém a chave especificada.

#### <a name="example-1-string"></a>Exemplo 1: cadeia
O exemplo seguinte devolve `true`:

```json
"[contains('foobar', 'foo')]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Suponha `element1` retorna `[1, 2, 3]`. O exemplo seguinte devolve `false`:

```json
"[contains(steps('foo').element1, 4)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Partem do princípio de `element1` devolve:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

O exemplo seguinte devolve `true`:

```json
"[contains(steps('foo').element1, 'key1')]"
```

### <a name="length"></a>Comprimento
Devolve o número de carateres numa cadeia de caracteres, o número de valores numa matriz ou o número de chaves num objeto.

#### <a name="example-1-string"></a>Exemplo 1: cadeia
O exemplo seguinte devolve `6`:

```json
"[length('foobar')]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Suponha `element1` retorna `[1, 2, 3]`. O exemplo seguinte devolve `3`:

```json
"[length(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Partem do princípio de `element1` devolve:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

O exemplo seguinte devolve `2`:

```json
"[length(steps('foo').element1)]"
```

### <a name="empty"></a>Vazio
Devolve `true` se a cadeia de caracteres, uma matriz ou um objeto é nulo ou está vazio.

#### <a name="example-1-string"></a>Exemplo 1: cadeia
O exemplo seguinte devolve `true`:

```json
"[empty('')]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Suponha `element1` retorna `[1, 2, 3]`. O exemplo seguinte devolve `false`:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Partem do princípio de `element1` devolve:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

O exemplo seguinte devolve `false`:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-4-null-and-undefined"></a>Exemplo 4: nulo e não definida
Suponha `element1` é `null` ou indefinido. O exemplo seguinte devolve `true`:

```json
"[empty(steps('foo').element1)]"
```

### <a name="first"></a>primeiro
Devolve o primeiro caráter da cadeia de caracteres especificado; primeiro valor da matriz especificada; ou a primeira chave e valor do objeto especificado.

#### <a name="example-1-string"></a>Exemplo 1: cadeia
O exemplo seguinte devolve `"f"`:

```json
"[first('foobar')]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Suponha `element1` retorna `[1, 2, 3]`. O exemplo seguinte devolve `1`:

```json
"[first(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Partem do princípio de `element1` devolve:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
O exemplo seguinte devolve `{"key1": "foobar"}`:

```json
"[first(steps('foo').element1)]"
```

### <a name="last"></a>última
Devolve o último caráter da cadeia especificada, o último valor da matriz especificada ou a última chave e valor do objeto especificado.

#### <a name="example-1-string"></a>Exemplo 1: cadeia
O exemplo seguinte devolve `"r"`:

```json
"[last('foobar')]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Suponha `element1` retorna `[1, 2, 3]`. O exemplo seguinte devolve `2`:

```json
"[last(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Partem do princípio de `element1` devolve:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

O exemplo seguinte devolve `{"key2": "raboof"}`:

```json
"[last(steps('foo').element1)]"
```

### <a name="take"></a>tirar
Devolve um número especificado de carateres contíguos desde o início da cadeia de caracteres, um número especificado de valores contíguos desde o início da matriz ou um número especificado de contíguas chaves e valores desde o início do objeto.

#### <a name="example-1-string"></a>Exemplo 1: cadeia
O exemplo seguinte devolve `"foo"`:

```json
"[take('foobar', 3)]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Suponha `element1` retorna `[1, 2, 3]`. O exemplo seguinte devolve `[1, 2]`:

```json
"[take(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Partem do princípio de `element1` devolve:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

O exemplo seguinte devolve `{"key1": "foobar"}`:

```json
"[take(steps('foo').element1, 1)]"
```

### <a name="skip"></a>ignorar
Ignora um número especificado de elementos numa coleção e, em seguida, devolve os elementos restantes.

#### <a name="example-1-string"></a>Exemplo 1: cadeia
O exemplo seguinte devolve `"bar"`:

```json
"[skip('foobar', 3)]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Suponha `element1` retorna `[1, 2, 3]`. O exemplo seguinte devolve `[3]`:

```json
"[skip(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Partem do princípio de `element1` devolve:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
O exemplo seguinte devolve `{"key2": "raboof"}`:

```json
"[skip(steps('foo').element1, 1)]"
```

## <a name="logical-functions"></a>Funções lógicas
Estas funções podem ser utilizadas em condicionais. Algumas funções podem não suportar todos os tipos de dados JSON.

### <a name="equals"></a>é igual a
Devolve `true` se ambos os parâmetros têm o mesmo tipo e valor. Esta função oferece suporte a todos os tipos de dados JSON.

O exemplo seguinte devolve `true`:

```json
"[equals(0, 0)]"
```

O exemplo seguinte devolve `true`:

```json
"[equals('foo', 'foo')]"
```

O exemplo seguinte devolve `false`:

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

### <a name="less"></a>menos
Devolve `true` se o primeiro parâmetro é estritamente menor que o segundo parâmetro. Esta função oferece suporte a parâmetros apenas de tipo número e a cadeia de caracteres.

O exemplo seguinte devolve `true`:

```json
"[less(1, 2)]"
```

O exemplo seguinte devolve `false`:

```json
"[less('9', '10')]"
```

### <a name="lessorequals"></a>lessOrEquals
Devolve `true` se o primeiro parâmetro é inferior ou igual ao segundo parâmetro. Esta função oferece suporte a parâmetros apenas de tipo número e a cadeia de caracteres.

O exemplo seguinte devolve `true`:

```json
"[lessOrEquals(2, 2)]"
```

### <a name="greater"></a>maior
Devolve `true` se o primeiro parâmetro é estritamente maior do que o segundo parâmetro. Esta função oferece suporte a parâmetros apenas de tipo número e a cadeia de caracteres.

O exemplo seguinte devolve `false`:

```json
"[greater(1, 2)]"
```

O exemplo seguinte devolve `true`:

```json
"[greater('9', '10')]"
```

### <a name="greaterorequals"></a>greaterOrEquals
Devolve `true` se o primeiro parâmetro é maior que ou igual ao segundo parâmetro. Esta função oferece suporte a parâmetros apenas de tipo número e a cadeia de caracteres.

O exemplo seguinte devolve `true`:

```json
"[greaterOrEquals(2, 2)]"
```

### <a name="and"></a>e
Devolve `true` se todos os parâmetros avaliam como `true`. Esta função oferece suporte a dois ou mais parâmetros apenas do tipo Booleano.

O exemplo seguinte devolve `true`:

```json
"[and(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

O exemplo seguinte devolve `false`:

```json
"[and(equals(0, 0), greater(1, 2))]"
```

### <a name="or"></a>ou
Devolve `true` se, pelo menos, um dos parâmetros é avaliada como `true`. Esta função oferece suporte a dois ou mais parâmetros apenas do tipo Booleano.

O exemplo seguinte devolve `true`:

```json
"[or(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

O exemplo seguinte devolve `true`:

```json
"[or(equals(0, 0), greater(1, 2))]"
```

### <a name="not"></a>não
Devolve `true` se o parâmetro for avaliada como `false`. Esta função oferece suporte a parâmetros apenas do tipo Booleano.

O exemplo seguinte devolve `true`:

```json
"[not(false)]"
```

O exemplo seguinte devolve `false`:

```json
"[not(equals(0, 0))]"
```

### <a name="coalesce"></a>Coalesce
Devolve o valor do primeiro parâmetro não nulo. Esta função oferece suporte a todos os tipos de dados JSON.

Suponha `element1` e `element2` são indefinido. O exemplo seguinte devolve `"foobar"`:

```json
"[coalesce(steps('foo').element1, steps('foo').element2, 'foobar')]"
```

## <a name="conversion-functions"></a>Funções de conversão
Estas funções podem ser utilizadas para converter valores entre tipos de dados JSON e codificações.

### <a name="int"></a>int
Converte o parâmetro para um número inteiro. Esta função oferece suporte a parâmetros de tipo número e a cadeia de caracteres.

O exemplo seguinte devolve `1`:

```json
"[int('1')]"
```

O exemplo seguinte devolve `2`:

```json
"[int(2.9)]"
```

### <a name="float"></a>float
Converte o parâmetro para um ponto flutuante. Esta função oferece suporte a parâmetros de tipo número e a cadeia de caracteres.

O exemplo seguinte devolve `1.0`:

```json
"[float('1.0')]"
```

O exemplo seguinte devolve `2.9`:

```json
"[float(2.9)]"
```

### <a name="string"></a>string
Converte o parâmetro para uma cadeia de caracteres. Esta função oferece suporte a parâmetros de todos os tipos de dados JSON.

O exemplo seguinte devolve `"1"`:

```json
"[string(1)]"
```

O exemplo seguinte devolve `"2.9"`:

```json
"[string(2.9)]"
```

O exemplo seguinte devolve `"[1,2,3]"`:

```json
"[string([1,2,3])]"
```

O exemplo seguinte devolve `"{"foo":"bar"}"`:

```json
"[string({\"foo\":\"bar\"})]"
```

### <a name="bool"></a>booleano
Converte o parâmetro para um valor booleano. Esta função oferece suporte a parâmetros de tipo, cadeia de caracteres e booleanos. Assim como booleanos em JavaScript, qualquer valor, exceto `0` ou `'false'` devolve `true`.

O exemplo seguinte devolve `true`:

```json
"[bool(1)]"
```

O exemplo seguinte devolve `false`:

```json
"[bool(0)]"
```

O exemplo seguinte devolve `true`:

```json
"[bool(true)]"
```

O exemplo seguinte devolve `true`:

```json
"[bool('true')]"
```

### <a name="parse"></a>analisar
Converte o parâmetro para um tipo nativo. Em outras palavras, esta função é o inverso da `string()`. Esta função oferece suporte a parâmetros apenas do tipo cadeia.

O exemplo seguinte devolve `1`:

```json
"[parse('1')]"
```

O exemplo seguinte devolve `true`:

```json
"[parse('true')]"
```

O exemplo seguinte devolve `[1,2,3]`:

```json
"[parse('[1,2,3]')]"
```

O exemplo seguinte devolve `{"foo":"bar"}`:

```json
"[parse('{\"foo\":\"bar\"}')]"
```

### <a name="encodebase64"></a>encodeBase64
Codifica o parâmetro para uma cadeia codificada de base 64. Esta função oferece suporte a parâmetros apenas do tipo cadeia.

O exemplo seguinte devolve `"Zm9vYmFy"`:

```json
"[encodeBase64('foobar')]"
```

### <a name="decodebase64"></a>decodeBase64
Descodifica o parâmetro de uma cadeia com codificação base 64. Esta função oferece suporte a parâmetros apenas do tipo cadeia.

O exemplo seguinte devolve `"foobar"`:

```json
"[decodeBase64('Zm9vYmFy')]"
```

### <a name="encodeuricomponent"></a>encodeUriComponent
Codifica o parâmetro para uma cadeia com codificação URL. Esta função oferece suporte a parâmetros apenas do tipo cadeia.

O exemplo seguinte devolve `"https%3A%2F%2Fportal.azure.com%2F"`:

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

### <a name="decodeuricomponent"></a>decodeUriComponent
Descodifica o parâmetro de uma cadeia com codificação URL. Esta função oferece suporte a parâmetros apenas do tipo cadeia.

O exemplo seguinte devolve `"https://portal.azure.com/"`:

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="math-functions"></a>Funções matemáticas
### <a name="add"></a>adicionar
Adiciona dois números e devolve o resultado.

O exemplo seguinte devolve `3`:

```json
"[add(1, 2)]"
```

### <a name="sub"></a>sub
Subtrai o segundo número do primeiro número e devolve o resultado.

O exemplo seguinte devolve `1`:

```json
"[sub(3, 2)]"
```

### <a name="mul"></a>mul
Multiplica dois números e devolve o resultado.

O exemplo seguinte devolve `6`:

```json
"[mul(2, 3)]"
```

### <a name="div"></a>div
Divide o primeiro número, o segundo número e devolve o resultado. O resultado é sempre um número inteiro.

O exemplo seguinte devolve `2`:

```json
"[div(6, 3)]"
```

### <a name="mod"></a>mod
Divide o primeiro número, o segundo número e devolve o resto.

O exemplo seguinte devolve `0`:

```json
"[mod(6, 3)]"
```

O exemplo seguinte devolve `2`:

```json
"[mod(6, 4)]"
```

### <a name="min"></a>min.
Devolve a pequena dos dois números.

O exemplo seguinte devolve `1`:

```json
"[min(1, 2)]"
```

### <a name="max"></a>máx.
Devolve o maior entre os dois números.

O exemplo seguinte devolve `2`:

```json
"[max(1, 2)]"
```

### <a name="range"></a>Intervalo
Gera uma seqüência de números integrais dentro do intervalo especificado.

O exemplo seguinte devolve `[1,2,3]`:

```json
"[range(1, 3)]"
```

### <a name="rand"></a>rand
Devolve um número integral aleatório dentro do intervalo especificado. Esta função não gera números aleatórios criptograficamente seguros.

O exemplo a seguir poderia retornar `42`:

```json
"[rand(-100, 100)]"
```

### <a name="floor"></a>Piso
Devolve o maior número inteiro menor ou igual ao número especificado.

O exemplo seguinte devolve `3`:

```json
"[floor(3.14)]"
```

### <a name="ceil"></a>ceil
Devolve o maior número inteiro maior ou igual ao número especificado.

O exemplo seguinte devolve `4`:

```json
"[ceil(3.14)]"
```

## <a name="date-functions"></a>Funções de data
### <a name="utcnow"></a>utcNow
Devolve uma cadeia de caracteres no formato ISO 8601 da data e hora atuais no computador local.

O exemplo a seguir poderia retornar `"1990-12-31T23:59:59.000Z"`:

```json
"[utcNow()]"
```

### <a name="addseconds"></a>addSeconds
Adiciona um número integral de segundos para o período de tempo especificado.

O exemplo seguinte devolve `"1991-01-01T00:00:00.000Z"`:

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

### <a name="addminutes"></a>addMinutes
Adiciona um número integral de minutos para o período de tempo especificado.

O exemplo seguinte devolve `"1991-01-01T00:00:59.000Z"`:

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

### <a name="addhours"></a>addHours
Adiciona um número integral de horas para o período de tempo especificado.

O exemplo seguinte devolve `"1991-01-01T00:59:59.000Z"`:

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="next-steps"></a>Passos Seguintes
* Para obter uma introdução ao Azure Resource Manager, consulte [descrição geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

