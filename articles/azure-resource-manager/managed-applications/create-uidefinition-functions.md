---
title: Criar funções de definição de UI
description: Descreve as funções a utilizar na construção de definições de UI para aplicações geridas azure
author: tfitzmac
ms.topic: conceptual
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: a93f4ff2ddc0737692de9e5619cf7a7521936224
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82980818"
---
# <a name="createuidefinition-functions"></a>Funções createUiDefinition
Esta secção contém as assinaturas para todas as funções suportadas de uma CreateUiDefinition.

Para utilizar uma função, rodeie a invocação com suportes quadrados. Por exemplo:

```json
"[function()]"
```

Cordas e outras funções podem ser referenciadas como parâmetros para uma função, mas as cordas devem ser cercadas em citações únicas. Por exemplo:

```json
"[fn1(fn2(), 'foobar')]"
```

Se for caso disso, pode referenciar propriedades da saída de uma função utilizando o operador de pontos. Por exemplo:

```json
"[func().prop1]"
```

## <a name="referencing-functions"></a>Funções de referência
Estas funções podem ser usadas para referência de saídas a partir das propriedades ou contexto de uma CreateUiDefinition.

### <a name="basics"></a>básicos
Devolve os valores de saída de um elemento que é definido na etapa Básico.

O exemplo a seguir devolve a saída do elemento nomeado `foo` na etapa Basics:

```json
"[basics('foo')]"
```

### <a name="steps"></a>passos
Devolve os valores de saída de um elemento definido no passo especificado. Para obter os valores de saída dos elementos na etapa Basics, use `basics()` em vez disso.

O exemplo a seguir devolve a saída do elemento nomeado `bar` no passo `foo` denominado:

```json
"[steps('foo').bar]"
```

### <a name="location"></a>localização
Devolve o local selecionado na etapa Básica ou no contexto atual.

O seguinte exemplo poderia `"westus"` voltar:

```json
"[location()]"
```

## <a name="string-functions"></a>Funções de cadeia
Estas funções só podem ser utilizadas com cordas JSON.

### <a name="concat"></a>concat
Concatenates uma ou mais cordas.

Por exemplo, se o valor de saída de `element1` `"bar"` se, então este exemplo devolve a `"foobar!"` cadeia:

```json
"[concat('foo', steps('step1').element1, '!')]"
```

### <a name="substring"></a>substring
Devolve o sub-cordão da cadeia especificada. O sub-adc de sublagem começa no índice especificado e tem o comprimento especificado.

O seguinte exemplo `"ftw"` regressa:

```json
"[substring('azure-ftw!!!1one', 6, 3)]"
```

### <a name="replace"></a>substituir
Devolve uma cadeia na qual todas as ocorrências da corda especificada na cadeia atual são substituídas por outra corda.

O seguinte exemplo `"Everything is awesome!"` regressa:

```json
"[replace('Everything is terrible!', 'terrible', 'awesome')]"
```

### <a name="guid"></a>guid
Gera uma cadeia globalmente única (GUID).

O seguinte exemplo poderia `"c7bc8bdc-7252-4a82-ba53-7c468679a511"` voltar:

```json
"[guid()]"
```

### <a name="tolower"></a>toLower
Devolve uma corda convertida em minúsculas.

O seguinte exemplo `"foobar"` regressa:

```json
"[toLower('FOOBAR')]"
```

### <a name="toupper"></a>toupper
Devolve uma corda convertida em maiúscula.

O seguinte exemplo `"FOOBAR"` regressa:

```json
"[toUpper('foobar')]"
```

## <a name="collection-functions"></a>Funções de recolha
Estas funções podem ser usadas com coleções, como cordas JSON, matrizes e objetos.

### <a name="contains"></a>contains
Devoluções `true` se uma cadeia contiver o sub-adc de substring especificado, uma matriz contém o valor especificado, ou um objeto contém a chave especificada.

#### <a name="example-1-string"></a>Exemplo 1: corda
O seguinte exemplo `true` regressa:

```json
"[contains('foobar', 'foo')]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Assuma `element1` devoluções `[1, 2, 3]` . O seguinte exemplo `false` regressa:

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

O seguinte exemplo `true` regressa:

```json
"[contains(steps('foo').element1, 'key1')]"
```

### <a name="length"></a>length
Devolve o número de caracteres numa corda, o número de valores numa matriz ou o número de teclas num objeto.

#### <a name="example-1-string"></a>Exemplo 1: corda
O seguinte exemplo `6` regressa:

```json
"[length('foobar')]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Assuma `element1` devoluções `[1, 2, 3]` . O seguinte exemplo `3` regressa:

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

O seguinte exemplo `2` regressa:

```json
"[length(steps('foo').element1)]"
```

### <a name="empty"></a>vazio
Retorna `true` se a corda, matriz ou objeto for nulo ou vazio.

#### <a name="example-1-string"></a>Exemplo 1: corda
O seguinte exemplo `true` regressa:

```json
"[empty('')]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Assuma `element1` devoluções `[1, 2, 3]` . O seguinte exemplo `false` regressa:

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

O seguinte exemplo `false` regressa:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-4-null-and-undefined"></a>Exemplo 4: nulo e indefinido
Assumir `element1` é `null` ou indefinido. O seguinte exemplo `true` regressa:

```json
"[empty(steps('foo').element1)]"
```

### <a name="first"></a>primeiro
Devolve o primeiro carácter da cadeia especificada; primeiro valor da matriz especificada; ou a primeira chave e valor do objeto especificado.

#### <a name="example-1-string"></a>Exemplo 1: corda
O seguinte exemplo `"f"` regressa:

```json
"[first('foobar')]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Assuma `element1` devoluções `[1, 2, 3]` . O seguinte exemplo `1` regressa:

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
O seguinte exemplo `{"key1": "foobar"}` regressa:

```json
"[first(steps('foo').element1)]"
```

### <a name="last"></a>último
Devolve o último carácter da cadeia especificada, o último valor da matriz especificada ou a última chave e valor do objeto especificado.

#### <a name="example-1-string"></a>Exemplo 1: corda
O seguinte exemplo `"r"` regressa:

```json
"[last('foobar')]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Assuma `element1` devoluções `[1, 2, 3]` . O seguinte exemplo `2` regressa:

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

O seguinte exemplo `{"key2": "raboof"}` regressa:

```json
"[last(steps('foo').element1)]"
```

### <a name="take"></a>tomar
Devolve um número especificado de caracteres contíguos desde o início da cadeia, um número especificado de valores contíguos desde o início da matriz, ou um número especificado de teclas e valores contíguos desde o início do objeto.

#### <a name="example-1-string"></a>Exemplo 1: corda
O seguinte exemplo `"foo"` regressa:

```json
"[take('foobar', 3)]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Assuma `element1` devoluções `[1, 2, 3]` . O seguinte exemplo `[1, 2]` regressa:

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

O seguinte exemplo `{"key1": "foobar"}` regressa:

```json
"[take(steps('foo').element1, 1)]"
```

### <a name="skip"></a>saltar
Contorna um número especificado de elementos numa coleção e, em seguida, devolve os restantes elementos.

#### <a name="example-1-string"></a>Exemplo 1: corda
O seguinte exemplo `"bar"` regressa:

```json
"[skip('foobar', 3)]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Assuma `element1` devoluções `[1, 2, 3]` . O seguinte exemplo `[3]` regressa:

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
O seguinte exemplo `{"key2": "raboof"}` regressa:

```json
"[skip(steps('foo').element1, 1)]"
```

## <a name="logical-functions"></a>Funções lógicas
Estas funções podem ser utilizadas em condicional. Algumas funções podem não suportar todos os tipos de dados JSON.

### <a name="equals"></a>equals
Devoluções `true` se ambos os parâmetros tiverem o mesmo tipo e valor. Esta função suporta todos os tipos de dados JSON.

O seguinte exemplo `true` regressa:

```json
"[equals(0, 0)]"
```

O seguinte exemplo `true` regressa:

```json
"[equals('foo', 'foo')]"
```

O seguinte exemplo `false` regressa:

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

### <a name="less"></a>less
Devoluções `true` se o primeiro parâmetro for estritamente inferior ao segundo parâmetro. Esta função suporta parâmetros apenas de número de tipo e corda.

O seguinte exemplo `true` regressa:

```json
"[less(1, 2)]"
```

O seguinte exemplo `false` regressa:

```json
"[less('9', '10')]"
```

### <a name="lessorequals"></a>lessOrEquals
Devoluções `true` se o primeiro parâmetro for inferior ou igual ao segundo parâmetro. Esta função suporta parâmetros apenas de número de tipo e corda.

O seguinte exemplo `true` regressa:

```json
"[lessOrEquals(2, 2)]"
```

### <a name="greater"></a>greater
Devoluções `true` se o primeiro parâmetro for estritamente maior do que o segundo parâmetro. Esta função suporta parâmetros apenas de número de tipo e corda.

O seguinte exemplo `false` regressa:

```json
"[greater(1, 2)]"
```

O seguinte exemplo `true` regressa:

```json
"[greater('9', '10')]"
```

### <a name="greaterorequals"></a>greaterOrEquals
Devoluções `true` se o primeiro parâmetro for maior ou igual ao segundo parâmetro. Esta função suporta parâmetros apenas de número de tipo e corda.

O seguinte exemplo `true` regressa:

```json
"[greaterOrEquals(2, 2)]"
```

### <a name="and"></a>e
Devoluções `true` se todos os parâmetros avaliarem `true` . Esta função suporta apenas dois ou mais parâmetros do tipo Boolean.

O seguinte exemplo `true` regressa:

```json
"[and(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

O seguinte exemplo `false` regressa:

```json
"[and(equals(0, 0), greater(1, 2))]"
```

### <a name="or"></a>ou
Devoluções `true` se pelo menos um dos parâmetros avaliar a `true` . Esta função suporta apenas dois ou mais parâmetros do tipo Boolean.

O seguinte exemplo `true` regressa:

```json
"[or(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

O seguinte exemplo `true` regressa:

```json
"[or(equals(0, 0), greater(1, 2))]"
```

### <a name="not"></a>not
Retorna `true` se o parâmetro avaliar a `false` . Esta função suporta parâmetros apenas do tipo Boolean.

O seguinte exemplo `true` regressa:

```json
"[not(false)]"
```

O seguinte exemplo `false` regressa:

```json
"[not(equals(0, 0))]"
```

### <a name="coalesce"></a>coalesce
Devolve o valor do primeiro parâmetro não nulo. Esta função suporta todos os tipos de dados JSON.

Assuma `element1` e `element2` é indefinido. O seguinte exemplo `"foobar"` regressa:

```json
"[coalesce(steps('foo').element1, steps('foo').element2, 'foobar')]"
```

Esta função é especialmente útil no contexto da invocação opcional que acontece devido à ação do utilizador após a carga da página. Um exemplo é se os constrangimentos colocados num campo na UI dependem do valor atualmente selecionado de outro campo, **inicialmente não visível.** Neste caso, `coalesce()` pode ser utilizado para permitir que a função seja sintaticamente válida no tempo de carregamento da página, tendo o efeito desejado quando o utilizador interage com o campo.

Considere `DropDown` isto, que permite ao utilizador escolher entre vários tipos de bases de dados diferentes:

```
{
    "name": "databaseType",
    "type": "Microsoft.Common.DropDown",
    "label": "Choose database type",
    "toolTip": "Choose database type",
    "defaultValue": "Oracle Database",
    "visible": "[bool(steps('section_database').connectToDatabase)]"
    "constraints": {
        "allowedValues": [
            {
                "label": "Azure Database for PostgreSQL",
                "value": "postgresql"
            },
            {
                "label": "Oracle Database",
                "value": "oracle"
            },
            {
                "label": "Azure SQL",
                "value": "sqlserver"
            }
        ],
        "required": true
    },
```

Para condicionar a ação de outro campo sobre o valor atual escolhido deste campo, `coalesce()` utilize, como mostrado aqui:

```
"regex": "[concat('^jdbc:', coalesce(steps('section_database').databaseConnectionInfo.databaseType, ''), '.*$')]",
```

Isto é necessário porque `databaseType` inicialmente não é visível e, portanto, não tem valor. Isto faz com que toda a expressão não avalie corretamente.

## <a name="conversion-functions"></a>Funções de conversão
Estas funções podem ser utilizadas para converter valores entre tipos de dados JSON e codificações.

### <a name="int"></a>int
Converte o parâmetro num inteiro. Esta função suporta parâmetros de número e corda do tipo.

O seguinte exemplo `1` regressa:

```json
"[int('1')]"
```

O seguinte exemplo `2` regressa:

```json
"[int(2.9)]"
```

### <a name="float"></a>float
Converte o parâmetro num ponto flutuante. Esta função suporta parâmetros de número e corda do tipo.

O seguinte exemplo `1.0` regressa:

```json
"[float('1.0')]"
```

O seguinte exemplo `2.9` regressa:

```json
"[float(2.9)]"
```

### <a name="string"></a>string
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

O seguinte exemplo `"{"foo":"bar"}"` regressa:

```json
"[string({\"foo\":\"bar\"})]"
```

### <a name="bool"></a>bool
Converte o parâmetro num Boolean. Esta função suporta parâmetros de número de tipo, string e Boolean. Semelhante a Booleans em JavaScript, qualquer valor exceto `0` ou `'false'` devoluções `true` .

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

### <a name="parse"></a>parse
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

O seguinte exemplo `{"foo":"bar"}` regressa:

```json
"[parse('{\"foo\":\"bar\"}')]"
```

### <a name="encodebase64"></a>codificarBase64
Codifica o parâmetro para uma cadeia codificada base-64. Esta função suporta parâmetros apenas de tipo de cadeia.

O seguinte exemplo `"Zm9vYmFy"` regressa:

```json
"[encodeBase64('foobar')]"
```

### <a name="decodebase64"></a>descodificarBase64
Descodifica o parâmetro de uma cadeia codificada base-64. Esta função suporta parâmetros apenas de tipo de cadeia.

O seguinte exemplo `"foobar"` regressa:

```json
"[decodeBase64('Zm9vYmFy')]"
```

### <a name="encodeuricomponent"></a>encodeUriComponent
Codifica o parâmetro para uma cadeia codificada url. Esta função suporta parâmetros apenas de tipo de cadeia.

O seguinte exemplo `"https%3A%2F%2Fportal.azure.com%2F"` regressa:

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

### <a name="decodeuricomponent"></a>decodeUriComponent
Descodifica o parâmetro de uma cadeia codificada url. Esta função suporta parâmetros apenas de tipo de cadeia.

O seguinte exemplo `"https://portal.azure.com/"` regressa:

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="math-functions"></a>Funções matemáticas
### <a name="add"></a>adicionar
Adiciona dois números e devolve o resultado.

O seguinte exemplo `3` regressa:

```json
"[add(1, 2)]"
```

### <a name="sub"></a>sub
Subtrai o segundo número do primeiro número e devolve o resultado.

O seguinte exemplo `1` regressa:

```json
"[sub(3, 2)]"
```

### <a name="mul"></a>mul
Multiplica dois números e devolve o resultado.

O seguinte exemplo `6` regressa:

```json
"[mul(2, 3)]"
```

### <a name="div"></a>div
Divide o primeiro número pelo segundo número e devolve o resultado. O resultado é sempre um inteiro.

O seguinte exemplo `2` regressa:

```json
"[div(6, 3)]"
```

### <a name="mod"></a>mod
Divide o primeiro número pelo segundo número, e devolve o restante.

O seguinte exemplo `0` regressa:

```json
"[mod(6, 3)]"
```

O seguinte exemplo `2` regressa:

```json
"[mod(6, 4)]"
```

### <a name="min"></a>min
Devolve o pequeno dos dois números.

O seguinte exemplo `1` regressa:

```json
"[min(1, 2)]"
```

### <a name="max"></a>máx
Devolve o maior dos dois números.

O seguinte exemplo `2` regressa:

```json
"[max(1, 2)]"
```

### <a name="range"></a>gama
Gera uma sequência de números integrais dentro do intervalo especificado.

O seguinte exemplo `[1,2,3]` regressa:

```json
"[range(1, 3)]"
```

### <a name="rand"></a>rand
Devolve um número integral aleatório dentro do intervalo especificado. Esta função não gera números aleatórios criptograficamente seguros.

O seguinte exemplo poderia `42` voltar:

```json
"[rand(-100, 100)]"
```

### <a name="floor"></a>chão
Devolve o maior número inteiro menos ou igual ao número especificado.

O seguinte exemplo `3` regressa:

```json
"[floor(3.14)]"
```

### <a name="ceil"></a>ceil
Devolve o maior número inteiro maior ou igual ao número especificado.

O seguinte exemplo `4` regressa:

```json
"[ceil(3.14)]"
```

## <a name="date-functions"></a>Funções de data
### <a name="utcnow"></a>utcNow
Devolve uma cadeia no formato ISO 8601 da data e hora atuais no computador local.

O seguinte exemplo poderia `"1990-12-31T23:59:59.000Z"` voltar:

```json
"[utcNow()]"
```

### <a name="addseconds"></a>addSeconds
Adiciona um número integral de segundos à estada de tempo especificada.

O seguinte exemplo `"1991-01-01T00:00:00.000Z"` regressa:

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

### <a name="addminutes"></a>addMinutes
Adiciona um número integral de minutos à estada de tempo especificada.

O seguinte exemplo `"1991-01-01T00:00:59.000Z"` regressa:

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

### <a name="addhours"></a>addHours
Adiciona um número integral de horas à hora de tempo especificada.

O seguinte exemplo `"1991-01-01T00:59:59.000Z"` regressa:

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="next-steps"></a>Próximos passos
* Para uma introdução ao Azure Resource Manager, consulte [a visão geral do Azure Resource Manager](../management/overview.md).

