---
title: Criar funções de definição de UI
description: Descreve as funções a utilizar na construção de definições de UI para aplicações geridas azure
author: tfitzmac
ms.topic: conceptual
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: a93f4ff2ddc0737692de9e5619cf7a7521936224
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82980818"
---
# <a name="createuidefinition-functions"></a>Funções CreateUiDefini
Esta secção contém as assinaturas para todas as funções suportadas de uma CreateUiDefinition.

Para utilizar uma função, rodeie a invocação com suportes quadrados. Por exemplo:

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

O exemplo seguinte devolve a `foo` saída do elemento nomeado na etapa basics:

```json
"[basics('foo')]"
```

### <a name="steps"></a>passos
Devolve os valores de saída de um elemento definido no passo especificado. Para obter os valores de saída dos `basics()` elementos na etapa Basics, utilize em vez disso.

O exemplo seguinte devolve a `bar` saída do `foo`elemento nomeado no passo denominado:

```json
"[steps('foo').bar]"
```

### <a name="location"></a>localização
Devolve a localização selecionada na etapa basics ou no contexto atual.

O seguinte exemplo `"westus"`pode voltar:

```json
"[location()]"
```

## <a name="string-functions"></a>Funções de cadeia
Estas funções só podem ser utilizadas com cordas JSON.

### <a name="concat"></a>concat
Concatenates uma ou mais cordas.

Por exemplo, se o `element1` `"bar"`valor de saída de `"foobar!"`se , então este exemplo devolve a cadeia:

```json
"[concat('foo', steps('step1').element1, '!')]"
```

### <a name="substring"></a>substring
Devolve a subcadeia da corda especificada. O substring começa no índice especificado e tem o comprimento especificado.

O exemplo `"ftw"`seguinte retorna:

```json
"[substring('azure-ftw!!!1one', 6, 3)]"
```

### <a name="replace"></a>substituir
Devolve uma corda na qual todas as ocorrências da corda especificada na cadeia atual são substituídas por outra corda.

O exemplo `"Everything is awesome!"`seguinte retorna:

```json
"[replace('Everything is terrible!', 'terrible', 'awesome')]"
```

### <a name="guid"></a>guid
Gera uma corda globalmente única (GUID).

O seguinte exemplo `"c7bc8bdc-7252-4a82-ba53-7c468679a511"`pode voltar:

```json
"[guid()]"
```

### <a name="tolower"></a>toLower
Devolve uma corda convertida em minúscula.

O exemplo `"foobar"`seguinte retorna:

```json
"[toLower('FOOBAR')]"
```

### <a name="toupper"></a>toUpper
Devolve uma corda convertida em maiúscula.

O exemplo `"FOOBAR"`seguinte retorna:

```json
"[toUpper('foobar')]"
```

## <a name="collection-functions"></a>Funções de recolha
Estas funções podem ser usadas com coleções, como cordas JSON, matrizes e objetos.

### <a name="contains"></a>contém
Devoluções `true` se uma cadeia contiver o substring especificado, uma matriz contém o valor especificado, ou um objeto contém a chave especificada.

#### <a name="example-1-string"></a>Exemplo 1: corda
O exemplo `true`seguinte retorna:

```json
"[contains('foobar', 'foo')]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Assuma declarações. `element1` `[1, 2, 3]` O exemplo `false`seguinte retorna:

```json
"[contains(steps('foo').element1, 4)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Assumir `element1` devoluções:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

O exemplo `true`seguinte retorna:

```json
"[contains(steps('foo').element1, 'key1')]"
```

### <a name="length"></a>length
Devolve o número de caracteres numa corda, o número de valores numa matriz ou o número de teclas num objeto.

#### <a name="example-1-string"></a>Exemplo 1: corda
O exemplo `6`seguinte retorna:

```json
"[length('foobar')]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Assuma declarações. `element1` `[1, 2, 3]` O exemplo `3`seguinte retorna:

```json
"[length(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Assumir `element1` devoluções:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

O exemplo `2`seguinte retorna:

```json
"[length(steps('foo').element1)]"
```

### <a name="empty"></a>vazio
Devoluções `true` se a corda, a matriz ou o objeto estiverem nulos ou vazios.

#### <a name="example-1-string"></a>Exemplo 1: corda
O exemplo `true`seguinte retorna:

```json
"[empty('')]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Assuma declarações. `element1` `[1, 2, 3]` O exemplo `false`seguinte retorna:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Assumir `element1` devoluções:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

O exemplo `false`seguinte retorna:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-4-null-and-undefined"></a>Exemplo 4: nulo e indefinido
Assumir `element1` `null` é ou não definido. O exemplo `true`seguinte retorna:

```json
"[empty(steps('foo').element1)]"
```

### <a name="first"></a>primeiro
Devolve o primeiro carácter da corda especificada; primeiro valor da matriz especificada; ou a primeira chave e valor do objeto especificado.

#### <a name="example-1-string"></a>Exemplo 1: corda
O exemplo `"f"`seguinte retorna:

```json
"[first('foobar')]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Assuma declarações. `element1` `[1, 2, 3]` O exemplo `1`seguinte retorna:

```json
"[first(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Assumir `element1` devoluções:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
O exemplo `{"key1": "foobar"}`seguinte retorna:

```json
"[first(steps('foo').element1)]"
```

### <a name="last"></a>última
Devolve o último carácter da corda especificada, o último valor da matriz especificada, ou a última chave e valor do objeto especificado.

#### <a name="example-1-string"></a>Exemplo 1: corda
O exemplo `"r"`seguinte retorna:

```json
"[last('foobar')]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Assuma declarações. `element1` `[1, 2, 3]` O exemplo `2`seguinte retorna:

```json
"[last(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Assumir `element1` devoluções:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

O exemplo `{"key2": "raboof"}`seguinte retorna:

```json
"[last(steps('foo').element1)]"
```

### <a name="take"></a>tomar
Devolve um número especificado de caracteres contíguos desde o início da corda, um número especificado de valores contíguos desde o início da matriz, ou um número especificado de chaves e valores contíguos desde o início do objeto.

#### <a name="example-1-string"></a>Exemplo 1: corda
O exemplo `"foo"`seguinte retorna:

```json
"[take('foobar', 3)]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Assuma declarações. `element1` `[1, 2, 3]` O exemplo `[1, 2]`seguinte retorna:

```json
"[take(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Assumir `element1` devoluções:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

O exemplo `{"key1": "foobar"}`seguinte retorna:

```json
"[take(steps('foo').element1, 1)]"
```

### <a name="skip"></a>saltar
Contorna um número especificado de elementos numa coleção e, em seguida, devolve os elementos restantes.

#### <a name="example-1-string"></a>Exemplo 1: corda
O exemplo `"bar"`seguinte retorna:

```json
"[skip('foobar', 3)]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Assuma declarações. `element1` `[1, 2, 3]` O exemplo `[3]`seguinte retorna:

```json
"[skip(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Assumir `element1` devoluções:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
O exemplo `{"key2": "raboof"}`seguinte retorna:

```json
"[skip(steps('foo').element1, 1)]"
```

## <a name="logical-functions"></a>Funções lógicas
Estas funções podem ser utilizadas em condicional. Algumas funções podem não suportar todos os tipos de dados da JSON.

### <a name="equals"></a>equals
Devoluções `true` se ambos os parâmetros tiverem o mesmo tipo e valor. Esta função suporta todos os tipos de dados da JSON.

O exemplo `true`seguinte retorna:

```json
"[equals(0, 0)]"
```

O exemplo `true`seguinte retorna:

```json
"[equals('foo', 'foo')]"
```

O exemplo `false`seguinte retorna:

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

### <a name="less"></a>less
Devoluções `true` se o primeiro parâmetro for estritamente inferior ao segundo parâmetro. Esta função suporta parâmetros apenas de número e corda tipo.

O exemplo `true`seguinte retorna:

```json
"[less(1, 2)]"
```

O exemplo `false`seguinte retorna:

```json
"[less('9', '10')]"
```

### <a name="lessorequals"></a>lessOrEquals
Devoluções `true` se o primeiro parâmetro for inferior ou igual ao segundo parâmetro. Esta função suporta parâmetros apenas de número e corda tipo.

O exemplo `true`seguinte retorna:

```json
"[lessOrEquals(2, 2)]"
```

### <a name="greater"></a>greater
Devoluções `true` se o primeiro parâmetro for estritamente maior que o segundo parâmetro. Esta função suporta parâmetros apenas de número e corda tipo.

O exemplo `false`seguinte retorna:

```json
"[greater(1, 2)]"
```

O exemplo `true`seguinte retorna:

```json
"[greater('9', '10')]"
```

### <a name="greaterorequals"></a>greaterOrEquals
Devoluções `true` se o primeiro parâmetro for maior ou igual ao segundo parâmetro. Esta função suporta parâmetros apenas de número e corda tipo.

O exemplo `true`seguinte retorna:

```json
"[greaterOrEquals(2, 2)]"
```

### <a name="and"></a>e
Devoluções `true` se todos os `true`parâmetros avaliarem para . Esta função suporta apenas dois ou mais parâmetros do tipo Boolean.

O exemplo `true`seguinte retorna:

```json
"[and(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

O exemplo `false`seguinte retorna:

```json
"[and(equals(0, 0), greater(1, 2))]"
```

### <a name="or"></a>ou
Devoluções `true` se pelo menos um dos `true`parâmetros avaliar para . Esta função suporta apenas dois ou mais parâmetros do tipo Boolean.

O exemplo `true`seguinte retorna:

```json
"[or(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

O exemplo `true`seguinte retorna:

```json
"[or(equals(0, 0), greater(1, 2))]"
```

### <a name="not"></a>not
Devoluções `true` se o parâmetro `false`avaliar para . Esta função suporta parâmetros apenas do tipo Boolean.

O exemplo `true`seguinte retorna:

```json
"[not(false)]"
```

O exemplo `false`seguinte retorna:

```json
"[not(equals(0, 0))]"
```

### <a name="coalesce"></a>coalesce
Devolve o valor do primeiro parâmetro não nulo. Esta função suporta todos os tipos de dados da JSON.

Assuma `element1` e `element2` não estão definidos. O exemplo `"foobar"`seguinte retorna:

```json
"[coalesce(steps('foo').element1, steps('foo').element2, 'foobar')]"
```

Esta função é especialmente útil no contexto da invocação opcional que acontece devido à ação do utilizador após as cargas da página. Um exemplo é se os constrangimentos colocados num campo na UI dependem do valor atualmente selecionado de outro campo, **inicialmente não visível.** Neste caso, `coalesce()` pode ser utilizado para permitir que a função seja sintáticamente válida no tempo de carga da página, tendo o efeito desejado quando o utilizador interage com o campo.

Considere `DropDown`isto, que permite ao utilizador escolher entre vários tipos diferentes de bases de dados:

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

Para condicionar a ação de outro campo sobre `coalesce()`o valor escolhido atual mente deste campo, utilize, como aqui se mostra:

```
"regex": "[concat('^jdbc:', coalesce(steps('section_database').databaseConnectionInfo.databaseType, ''), '.*$')]",
```

Isto é necessário `databaseType` porque inicialmente não é visível e, portanto, não tem um valor. Isto faz com que toda a expressão não avalie corretamente.

## <a name="conversion-functions"></a>Funções de conversão
Estas funções podem ser utilizadas para converter valores entre tipos de dados JSON e codificações.

### <a name="int"></a>int
Converte o parâmetro para um inteiro. Esta função suporta parâmetros de tipo de número e corda.

O exemplo `1`seguinte retorna:

```json
"[int('1')]"
```

O exemplo `2`seguinte retorna:

```json
"[int(2.9)]"
```

### <a name="float"></a>float
Converte o parâmetro num ponto flutuante. Esta função suporta parâmetros de tipo de número e corda.

O exemplo `1.0`seguinte retorna:

```json
"[float('1.0')]"
```

O exemplo `2.9`seguinte retorna:

```json
"[float(2.9)]"
```

### <a name="string"></a>string
Converte o parâmetro numa corda. Esta função suporta parâmetros de todos os tipos de dados da JSON.

O exemplo `"1"`seguinte retorna:

```json
"[string(1)]"
```

O exemplo `"2.9"`seguinte retorna:

```json
"[string(2.9)]"
```

O exemplo `"[1,2,3]"`seguinte retorna:

```json
"[string([1,2,3])]"
```

O exemplo `"{"foo":"bar"}"`seguinte retorna:

```json
"[string({\"foo\":\"bar\"})]"
```

### <a name="bool"></a>bool
Converte o parâmetro para um Boolean. Esta função suporta parâmetros de tipo número, corda e Boolean. Semelhante a Booleans no JavaScript, `'false'` `true`qualquer valor exceto `0` ou devoluções.

O exemplo `true`seguinte retorna:

```json
"[bool(1)]"
```

O exemplo `false`seguinte retorna:

```json
"[bool(0)]"
```

O exemplo `true`seguinte retorna:

```json
"[bool(true)]"
```

O exemplo `true`seguinte retorna:

```json
"[bool('true')]"
```

### <a name="parse"></a>parse
Converte o parâmetro para um tipo nativo. Por outras palavras, esta `string()`função é o inverso de . Esta função suporta parâmetros apenas de cadeia de tipo.

O exemplo `1`seguinte retorna:

```json
"[parse('1')]"
```

O exemplo `true`seguinte retorna:

```json
"[parse('true')]"
```

O exemplo `[1,2,3]`seguinte retorna:

```json
"[parse('[1,2,3]')]"
```

O exemplo `{"foo":"bar"}`seguinte retorna:

```json
"[parse('{\"foo\":\"bar\"}')]"
```

### <a name="encodebase64"></a>codificarBase64
Codifica o parâmetro a uma cadeia codificada base-64. Esta função suporta parâmetros apenas de cadeia de tipo.

O exemplo `"Zm9vYmFy"`seguinte retorna:

```json
"[encodeBase64('foobar')]"
```

### <a name="decodebase64"></a>descodificarBase64
Descodifica o parâmetro de uma corda codificada base-64. Esta função suporta parâmetros apenas de cadeia de tipo.

O exemplo `"foobar"`seguinte retorna:

```json
"[decodeBase64('Zm9vYmFy')]"
```

### <a name="encodeuricomponent"></a>codificar Componente UriUri
Codifica o parâmetro a uma cadeia codificada por URL. Esta função suporta parâmetros apenas de cadeia de tipo.

O exemplo `"https%3A%2F%2Fportal.azure.com%2F"`seguinte retorna:

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

### <a name="decodeuricomponent"></a>descodificar Componente Uri
Descodifica o parâmetro de uma cadeia codificada por URL. Esta função suporta parâmetros apenas de cadeia de tipo.

O exemplo `"https://portal.azure.com/"`seguinte retorna:

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="math-functions"></a>Funções matemáticas
### <a name="add"></a>adicionar
Adiciona dois números e devolve o resultado.

O exemplo `3`seguinte retorna:

```json
"[add(1, 2)]"
```

### <a name="sub"></a>sub
Subtrai o segundo número do primeiro número e devolve o resultado.

O exemplo `1`seguinte retorna:

```json
"[sub(3, 2)]"
```

### <a name="mul"></a>mul
Multiplica dois números e devolve o resultado.

O exemplo `6`seguinte retorna:

```json
"[mul(2, 3)]"
```

### <a name="div"></a>div
Divide o primeiro número pelo segundo número e devolve o resultado. O resultado é sempre um inteiro.

O exemplo `2`seguinte retorna:

```json
"[div(6, 3)]"
```

### <a name="mod"></a>mod
Divide o primeiro número pelo segundo número e devolve o restante.

O exemplo `0`seguinte retorna:

```json
"[mod(6, 3)]"
```

O exemplo `2`seguinte retorna:

```json
"[mod(6, 4)]"
```

### <a name="min"></a>min
Devolve o pequeno dos dois números.

O exemplo `1`seguinte retorna:

```json
"[min(1, 2)]"
```

### <a name="max"></a>máximo
Devolve o maior dos dois números.

O exemplo `2`seguinte retorna:

```json
"[max(1, 2)]"
```

### <a name="range"></a>alcance
Gera uma sequência de números integrais dentro do intervalo especificado.

O exemplo `[1,2,3]`seguinte retorna:

```json
"[range(1, 3)]"
```

### <a name="rand"></a>rand
Devolve um número integral aleatório dentro do intervalo especificado. Esta função não gera números aleatórios criptograficamente seguros.

O seguinte exemplo `42`pode voltar:

```json
"[rand(-100, 100)]"
```

### <a name="floor"></a>piso
Devolve o maior inteiro inferior ou igual ao número especificado.

O exemplo `3`seguinte retorna:

```json
"[floor(3.14)]"
```

### <a name="ceil"></a>ceil
Devolve o maior inteiro maior ou igual ao número especificado.

O exemplo `4`seguinte retorna:

```json
"[ceil(3.14)]"
```

## <a name="date-functions"></a>Funções de data
### <a name="utcnow"></a>utcNow
Devolve uma cadeia no formato ISO 8601 da data e hora atuais no computador local.

O seguinte exemplo `"1990-12-31T23:59:59.000Z"`pode voltar:

```json
"[utcNow()]"
```

### <a name="addseconds"></a>adicionarSeconds
Adiciona um número integral de segundos à marca de tempo especificada.

O exemplo `"1991-01-01T00:00:00.000Z"`seguinte retorna:

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

### <a name="addminutes"></a>addMinutes
Adiciona um número integral de minutos à marca de tempo especificada.

O exemplo `"1991-01-01T00:00:59.000Z"`seguinte retorna:

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

### <a name="addhours"></a>addHours
Adiciona um número integral de horas à marca de tempo especificada.

O exemplo `"1991-01-01T00:59:59.000Z"`seguinte retorna:

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="next-steps"></a>Passos seguintes
* Para uma introdução ao Gestor de Recursos Azure, consulte a [visão geral do Gestor de Recursos do Azure.](../management/overview.md)

