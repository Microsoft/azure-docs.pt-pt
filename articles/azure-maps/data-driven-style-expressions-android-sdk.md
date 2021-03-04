---
title: Expressões de estilo orientadas por dados em mapas Android | Microsoft Azure Maps
description: Saiba mais sobre expressões de estilo baseadas em dados. Veja como usar estas expressões no Azure Maps Android SDK para ajustar estilos em mapas.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 1babf1feb550109486089c45469ab4ce32f72cb3
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097419"
---
# <a name="data-driven-style-expressions-android-sdk"></a>Expressões de estilo orientadas por dados (Android SDK)

As expressões permitem aplicar a lógica de negócio às opções de estilo que observam as propriedades definidas em cada formato numa fonte de dados. As expressões podem filtrar dados numa fonte de dados ou numa camada. As expressões podem consistir em lógica condicional, como se-declarações. E podem ser usados para manipular dados usando: operadores de cordas, operadores lógicos e operadores matemáticos.

Os estilos orientados por dados reduzem a quantidade de código necessária para implementar a lógica do negócio em torno do styling. Quando utilizadas com camadas, as expressões são avaliadas no tempo de renderização num fio separado. Esta funcionalidade proporciona um desempenho acrescido em comparação com a avaliação da lógica de negócio na linha UI.

O Azure Maps Android SDK suporta quase todas as expressões de estilo do Azure Maps Web SDK, pelo que todos os mesmos conceitos delineados nas [Expressões de Estilo orientadas por Dados (Web SDK)](data-driven-style-expressions-web-sdk.md) podem ser transportados para uma aplicação Android. Todas as expressões de estilo no Azure Maps Android SDK estão disponíveis no espaço de `com.microsoft.azure.maps.mapcontrol.options.Expression` nomes. Existem muitos tipos diferentes de expressões de estilo.

| Tipo de expressões | Descrição |
|---------------------|-------------|
| [Expressões booleanas](#boolean-expressions) | Expressões booleanas fornecem um conjunto de expressões de operadores booleanos para avaliar comparações booleanas. |
| [Expressões de cor](#color-expressions) | Expressões de cor facilitam a criação e manipulação dos valores de cor. |
| [Expressões condicionais](#conditional-expressions) | Expressões condicionais fornecem operações lógicas que são como se-declarações. |
| [Expressões de dados](#data-expressions) | Fornece acesso aos dados da propriedade numa funcionalidade. |
| [Expressões interpoladas e passo](#interpolate-and-step-expressions) | Expressões interpoladas e passos podem ser usadas para calcular valores ao longo de uma função de curva ou passo interpolado. |
| [Expressões baseadas em JSON](#json-based-expressions) | Facilita a reutilização de expressões baseadas em JSON em estilo cru criadas para o Web SDK com o Android SDK. |  
| [Expressões específicas da camada](#layer-specific-expressions) | Expressões especiais que só são aplicáveis a uma única camada. |
| [Expressões matemáticas](#math-expressions) | Fornece aos operadores matemáticos a realização de cálculos baseados em dados dentro do quadro de expressão. |
| [Expressões do operador de cordas](#string-operator-expressions) | As expressões do operador de cordas executam operações de conversão em cordas como concatenatos e convertendo a caixa. |
| [Expressões de tipos](#type-expressions) | As expressões de tipo fornecem ferramentas para testar e converter diferentes tipos de dados, como cordas, números e valores booleanos. |
| [Expressões de ligação variável](#variable-binding-expressions) | Expressões de ligação variáveis armazenam os resultados de um cálculo numa variável e referenciadas em outros lugares numa expressão múltipla sem ter de recalcular o valor armazenado. |
| [Expressão de zoom](#zoom-expression) | Recupera o nível de zoom atual do mapa no momento de renderização. |

> [!NOTE]
> A sintaxe para expressões é em grande parte idêntica em Java e Kotlin. Se tiver a documentação definida para Kotlin, mas consulte blocos de código para Java, o código é idêntico em ambos os idiomas.

Todos os exemplos nesta secção do documento utilizam a seguinte característica para demonstrar diferentes formas de utilização destas expressões.

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {
        "id": 123,
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 64,
        "title": "Cafeteria", 
        "zoneColor": "purple",
        "abcArray": ["a", "b", "c"],
        "array2d": [["a", "b"], ["x", "y"]],
        "_style": {
            "fillColor": "red"
        }
    }
}
```

O código que se segue mostra como criar manualmente esta funcionalidade GeoJSON numa aplicação.

::: zone pivot="programming-language-java-android"

```Java
//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45));

//Add properties to the feature.
feature.addNumberProperty("id", 123);
feature.addStringProperty("entityType", "restaurant");
feature.addNumberProperty("revenue", 12345);
feature.addStringProperty("subTitle", "Building 40");
feature.addNumberProperty("temperature", 64);
feature.addStringProperty("title", "Cafeteria");
feature.addStringProperty("zoneColor", "purple");

JsonArray abcArray = new JsonArray();
abcArray.add("a");
abcArray.add("b");
abcArray.add("c");

feature.addProperty("abcArray", abcArray);

JsonArray array1 = new JsonArray();
array1.add("a");
array1.add("b");

JsonArray array2 = new JsonArray();
array1.add("x");
array1.add("y");

JsonArray array2d = new JsonArray();
array2d.add(array1);
array2d.add(array2);

feature.addProperty("array2d", array2d);

JsonObject style = new JsonObject();
style.addProperty("fillColor", "red");

feature.addProperty("_style", style);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-100, 45))

//Add properties to the feature.
feature.addNumberProperty("id", 123)
feature.addStringProperty("entityType", "restaurant")
feature.addNumberProperty("revenue", 12345)
feature.addStringProperty("subTitle", "Building 40")
feature.addNumberProperty("temperature", 64)
feature.addStringProperty("title", "Cafeteria")
feature.addStringProperty("zoneColor", "purple")

val abcArray = JsonArray()
abcArray.add("a")
abcArray.add("b")
abcArray.add("c")

feature.addProperty("abcArray", abcArray)

val array1 = JsonArray()
array1.add("a")
array1.add("b")

val array2 = JsonArray()
array1.add("x")
array1.add("y")

val array2d = JsonArray()
array2d.add(array1)
array2d.add(array2)

feature.addProperty("array2d", array2d)

val style = JsonObject()
style.addProperty("fillColor", "red")

feature.addProperty("_style", style)
```

::: zone-end

O código que se segue mostra como desseializar a versão stringified do objeto JSON numa funcionalidade da GeoJSON numa aplicação.

::: zone pivot="programming-language-java-android"

```java
String featureString = "{\"type\":\"Feature\",\"geometry\":{\"type\":\"Point\",\"coordinates\":[-122.13284,47.63699]},\"properties\":{\"id\":123,\"entityType\":\"restaurant\",\"revenue\":12345,\"subTitle\":\"Building 40\",\"temperature\":64,\"title\":\"Cafeteria\",\"zoneColor\":\"purple\",\"abcArray\":[\"a\",\"b\",\"c\"],\"array2d\":[[\"a\",\"b\"],[\"x\",\"y\"]],\"_style\":{\"fillColor\":\"red\"}}}";

Feature feature = Feature.fromJson(featureString);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val featureString = "{\"type\":\"Feature\",\"geometry\":{\"type\":\"Point\",\"coordinates\":[-122.13284,47.63699]},\"properties\":{\"id\":123,\"entityType\":\"restaurant\",\"revenue\":12345,\"subTitle\":\"Building 40\",\"temperature\":64,\"title\":\"Cafeteria\",\"zoneColor\":\"purple\",\"abcArray\":[\"a\",\"b\",\"c\"],\"array2d\":[[\"a\",\"b\"],[\"x\",\"y\"]],\"_style\":{\"fillColor\":\"red\"}}}"

val feature = Feature.fromJson(featureString)
```

::: zone-end

## <a name="json-based-expressions"></a>Expressões baseadas em JSON

O Azure Maps Web SDK também suporta expressões de estilo orientadas por dados que são representadas usando uma matriz JSON. Estas mesmas expressões podem ser recriadas usando a classe nativa `Expression` no Android SDK. Alternativamente, estas expressões baseadas em JSON podem ser convertidas numa cadeia usando uma função web como `JSON.stringify` e passadas para o `Expression.raw(String rawExpression)` método. Por exemplo, tome a seguinte expressão JSON.

```javascript
var exp = ['get','title'];
JSON.stringify(exp); // = "['get','title']"
```

A versão stringified da expressão acima seria `"['get','title']"` e pode ser lida no Android SDK da seguinte forma.

::: zone pivot="programming-language-java-android"

```java
Expression exp = Expression.raw("['get','title']")
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val exp = Expression.raw("['get','title']")
```

::: zone-end

A utilização desta abordagem pode facilitar a reutilização de expressões de estilo entre aplicações móveis e web que utilizam o Azure Maps.

Este vídeo fornece uma visão geral do estilo baseado em dados no Azure Maps.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player?format=ny]

## <a name="data-expressions"></a>Expressões de dados

As expressões de dados fornecem acesso aos dados da propriedade numa funcionalidade.

| Expression | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `accumulated()` | número | Obtém o valor de uma propriedade de cluster acumulada até agora. |
| `at(number | Expression, Expression)` | valor | Recupera um item de uma matriz. |
| `geometryType()` | string | Obtém o tipo de geometria da funcionalidade: Point, MultiPoint, LineString, MultiLineString, Polygon, MultiPolygon. |
| `get(string | Expression)` \| `get(string | Expression, Expression)` | valor | Obtém o valor da propriedade a partir das propriedades do objeto fornecido. Devoluções nulas se o imóvel solicitado faltar. |
| `has(string | Expression)` \| `has(string | Expression, Expression)` | boolean | Determina se as propriedades de uma característica têm a propriedade especificada. |
| `id()` | valor | Obtém a identificação da funcionalidade se tiver uma. |
| `in(string | number | Expression, Expression)` | boolean | Determina se um item existe numa matriz |
| `length(string | Expression)` | número | Obtém o comprimento de uma corda ou de uma matriz. |
| `properties()`| valor | Obtém o objeto de propriedades de recurso. |

As seguintes expressões de estilo Web SDK não são suportadas no Android SDK:

- índice de
- fatia

**Exemplos**

As propriedades de uma funcionalidade podem ser acedidas diretamente numa expressão usando uma `get` expressão. Este exemplo usa o `zoneColor` valor da funcionalidade para especificar a propriedade colorida de uma camada de bolha.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    //Get the zoneColor value.
    bubbleColor(get("zoneColor"))
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    //Get the zoneColor value.
    bubbleColor(get("zoneColor"))
)
```

::: zone-end

O exemplo acima funcionará bem, se todas as características de pontos tiverem a `zoneColor` propriedade. Se não o fizerem, a cor provavelmente voltará a ser "preto". Para modificar a cor do recuo, utilize uma `switchCase` expressão em combinação com a `has` expressão para verificar se a propriedade existe. Se a propriedade não existir, devolva uma cor de retorno.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        //Use a conditional case expression.
        switchCase(
            //Check to see if feature has a "zoneColor" 
            has("zoneColor"), 

            //If it does, use it.
            get("zoneColor"), 

            //If it doesn't, default to blue.
            literal("blue")
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        //Use a conditional case expression.
        switchCase(
            //Check to see if feature has a "zoneColor" 
            has("zoneColor"), 

            //If it does, use it.
            get("zoneColor"), 

            //If it doesn't, default to blue.
            literal("blue")
        )
    )
)
```

::: zone-end

As camadas de bolha e símbolos tornarão as coordenadas de todas as formas numa fonte de dados, por padrão. Este comportamento pode destacar os vértices de um polígono ou de uma linha. A `filter` opção da camada pode ser usada para limitar o tipo de geometria das características que presta, usando uma `geometryType` expressão dentro de uma expressão booleana. O exemplo a seguir limita uma camada de bolha para que apenas `Point` as características sejam renderizadas.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    filter(eq(geometryType(), "Point"))
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    filter(eq(geometryType(), "Point"))
)
```

::: zone-end

O exemplo a seguir permite que tanto as `Point` `MultiPoint` características como as funcionalidades sejam prestadas.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    filter(any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint")))
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    filter(any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint")))
)
```

::: zone-end

Da mesma forma, o contorno dos Polígonos renderizará em camadas de linha. Para desativar este comportamento numa camada de linha, adicione um filtro que apenas permite `LineString` e `MultiLineString` apresenta.  

Aqui estão alguns exemplos adicionais de como usar expressões de dados:

```java
//Get item [2] from an array "properties.abcArray[1]" = "c"
at(2, get("abcArray"))

//Get item [0][1] from a 2D array "properties.array2d[0][1]" = "b"
at(1, at(0, get("array2d")))

//Check to see if a value is in an array "properties.abcArray.indexOf('a') !== -1" = true
in("a", get("abcArray"))

//Get the length of an array "properties.abcArray.length" = 3
length(get("abcArray"))

//Get the value of a subproperty "properties._style.fillColor" = "red"
get("fillColor", get("_style"))

//Check that "fillColor" exists as a subproperty of "_style".
has("fillColor", get("_style"))
```

## <a name="math-expressions"></a>Expressões matemáticas

As expressões matemáticas fornecem aos operadores matemáticos a realização de cálculos orientados para dados dentro do quadro de expressão.

| Expression | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `abs(number | Expression)` | número | Calcula o valor absoluto do número especificado. |
| `acos(number | Expression)` | número | Calcula a arccosina do número especificado. |
| `asin(number | Expression)` | número | Calcula a arcsina do número especificado. |
| `atan(number | Expression)` | número | Calcula o arctangent do número especificado. |
| `ceil(number | Expression)` | número | Arredonda o número até ao próximo inteiro inteiro inteiro. |
| `cos(number | Expression)` | número | Calcula o porção do número especificado. |
| `division(number, number)` \| `division(Expression, Expression)` | número | Divide o primeiro número pelo segundo número. Expressão equivalente web SDK: `/` |
| `e()` | número | Devolve a constante `e` matemática. |
| `floor(number | Expression)` | número | Arredonda o número para o inteiro inteiro anterior. |
| `log10(number | Expression)` | número | Calcula o logaritmo base-dez do número especificado. |
| `log2(number | Expression)` | número | Calcula o logaritmo base-dois do número especificado. |
| `ln(number | Expression)` | número | Calcula o logaritmo natural do número especificado. |
| `ln2()` | número | Devolve a constante `ln(2)` matemática. |
| `max(numbers... | expressions...)` | número | Calcula o número máximo no conjunto especificado de números. |
| `min(numbers... | expressions...)` | número | Calcula o número mínimo no conjunto especificado de números. |
| `mod(number, number)` \| `mod(Expression, Expression)` | número | Calcula o restante ao dividir o primeiro número pelo segundo número. Expressão equivalente web SDK: `%` |
| `pi()` | número | Devolve a constante `PI` matemática. |
| `pow(number, number)` \| `pow(Expression, Expression)` | número | Calcula o valor do primeiro valor elevado à potência do segundo número. |
| `product(numbers... | expressions...)` | número | Multiplica os números especificados em conjunto. Expressão equivalente web SDK: `*` |
| `round(number | Expression)` | número | Arredonda o número para o número mais próximo. Os valores intermédios estão arredondados para longe de zero. Por exemplo, `round(-1.5)` avalia para `-2` . |
| `sin(number | Expression)` | número | Calcula o seno do número especificado. |
| `sqrt(number | Expression)` | número | Calcula a raiz quadrada do número especificado. |
| `subtract(number | Expression` | número | Subtrai 0 pelo número especificado. |
| `subtract(number | Expression, number | Expression)` | número | Subtrai os primeiros números pelo segundo número. |
| `sum(numbers... | expressions...)` | número | Calcula a soma dos números especificados. |
| `tan(number | Expression)` | número | Calcula a tangente do número especificado. |

## <a name="boolean-expressions"></a>Expressões booleanas

Expressões booleanas fornecem um conjunto de expressões de operadores booleanos para avaliar comparações booleanas.

Ao comparar valores, a comparação é estritamente dactilografada. Valores de diferentes tipos são sempre considerados desiguais. Os casos em que os tipos são conhecidos por serem diferentes no momento da análise são considerados inválidos e produzirão um erro de análise.

| Expression | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `all(Expression...)` | boolean | Devoluções `true` se todas as entradas `true` forem, caso `false` contrário. |
| `any(Expression...)` | boolean | Devoluções `true` se alguma das entradas `true` for, caso `false` contrário. |
| `eq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `eq(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Retorna `true` se os valores de entrada forem iguais, `false` caso contrário. Os argumentos são necessários para ser ambas as cordas ou ambos os números. |
| `gt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gt(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Devoluções `true` se a primeira entrada for estritamente maior do que a segunda, caso `false` contrário. Os argumentos são necessários para ser ambas as cordas ou ambos os números. |
| `gte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gte(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Devoluções `true` se a primeira entrada for maior ou igual à segunda, `false` caso contrário. Os argumentos são necessários para ser ambas as cordas ou ambos os números. |
| `lt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lt(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Devoluções `true` se a primeira entrada for estritamente inferior à segunda, caso `false` contrário. Os argumentos são necessários para ser ambas as cordas ou ambos os números. |
| `lte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lte(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Devoluções `true` se a primeira entrada for inferior ou igual à segunda, `false` caso contrário. Os argumentos são necessários para ser ambas as cordas ou ambos os números. |
| `neq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `neq(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Retorna `true` se os valores de entrada não forem iguais, `false` caso contrário. |
| `not(Expression | boolean)` | boolean | Negação lógica. Retorna `true` se a entrada for , e se a entrada for `false` `false` `true` . |

## <a name="conditional-expressions"></a>Expressões condicionais

Expressões condicionais fornecem operações lógicas que são como se-declarações.

As seguintes expressões realizam operações lógicas condicionais nos dados de entrada. Por exemplo, a `switchCase` expressão fornece lógica "se/então/outra" enquanto a `match` expressão é como uma "declaração de comutação".

### <a name="switch-case-expression"></a>Mudar a expressão do caso

Uma `switchCase` expressão é um tipo de expressão condicional que fornece lógica "se/então/outra". Este tipo de expressão passa por uma lista de condições booleanas. Devolve o valor de saída da primeira condição booleana para avaliar a verdade.

O pseudocódigo que se segue define a estrutura da `switchCase` expressão.

```java
switchCase(
    condition1: boolean expression, 
    output1: value,
    condition2: boolean expression, 
    output2: value,
    ...,
    fallback: value
)
```

**Exemplo**

O exemplo a seguir passa por diferentes condições booleanas até encontrar uma que `true` avalie, e depois retorne esse valor associado. Se nenhuma condição booleana `true` avaliar, um valor de retorno será devolvido.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        switchCase(
            //Check to see if the first boolean expression is true, and if it is, return its assigned result.
            //If it has a zoneColor property, use its value as a color.
            has("zoneColor"), toColor(get("zoneColor")),

            //Check to see if the second boolean expression is true, and if it is, return its assigned result.
            //If it has a temperature property with a value greater than or equal to 100, make it red.
            all(has("temperature"), gte(get("temperature"), 100)), color(Color.RED),
            
            //Specify a default value to return. In this case green.
            color(Color.GREEN)
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        switchCase(
            //Check to see if the first boolean expression is true, and if it is, return its assigned result.
            //If it has a zoneColor property, use its value as a color.
            has("zoneColor"), toColor(get("zoneColor")),

            //Check to see if the second boolean expression is true, and if it is, return its assigned result.
            //If it has a temperature property with a value greater than or equal to 100, make it red.
            all(has("temperature"), gte(get("temperature"), 100)), color(Color.RED),
            
            //Specify a default value to return. In this case green.
            color(Color.GREEN)
        )
    )
)
```

::: zone-end

### <a name="match-expression"></a>Expressão de correspondência

Uma `match` expressão é um tipo de expressão condicional que proporciona uma afirmação de comutação como a lógica. A entrada pode ser qualquer expressão como `get( "entityType")` a que devolve uma corda ou um número. Cada paragem deve ter um rótulo que seja um único valor literal ou uma matriz de valores literais, cujos valores devem ser todos cordas ou todos os números. A entrada corresponde se algum dos valores da matriz corresponder. Cada etiqueta de paragem deve ser única. Se o tipo de entrada não corresponder ao tipo de etiquetas, o resultado será o valor de retorno predefinido.

O pseudocódigo que se segue define a estrutura da `match` expressão.

```java
match(Expression input, Expression defaultOutput, Expression.Stop... stops)
```

**Exemplos**

O exemplo a seguir olha para a `entityType` propriedade de uma característica Point em uma camada de bolha procura por uma correspondência. Se encontrar uma correspondência, esse valor especificado é devolvido ou devolve o valor de retorno.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" return "red".
            stop("restaurant", color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" return "red".
            stop("restaurant", color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
)
```

::: zone-end

O exemplo a seguir utiliza um conjunto para listar um conjunto de etiquetas que devem devolver todos o mesmo valor. Esta abordagem é muito mais eficiente do que a listagem individual de cada rótulo. Neste caso, se a `entityType` propriedade for "restaurante" ou "grocery_store", a cor "vermelho" será devolvida.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" or "grocery_store" return "red".
            stop(Arrays.asList("restaurant", "grocery_store"), color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" or "grocery_store" return "red".
            stop(arrayOf("restaurant", "grocery_store"), color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
)
```

::: zone-end

### <a name="coalesce-expression"></a>Expressão do coalesce

Uma `coalesce` expressão passa por um conjunto de expressões até que o primeiro valor não nulo seja obtido e devolva esse valor.

O pseudocódigo que se segue define a estrutura da `coalesce` expressão.

```java
coalesce(Expression... input)
```

**Exemplo**

O exemplo a seguir utiliza uma `coalesce` expressão para definir a `textField` opção de uma camada de símbolo. Se a `title` propriedade faltar da funcionalidade ou definida `null` para, a expressão tentará então procurar a `subTitle` propriedade, se a sua falta `null` ou, então, cairá de volta para uma corda vazia.

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    textField(
        coalesce(
            //Try getting the title property.
            get("title"),

            //If there is no title, try getting the subTitle. 
            get("subTitle"),

            //Default to an empty string.
            literal("")
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    textField(
        coalesce(
            //Try getting the title property.
            get("title"),

            //If there is no title, try getting the subTitle. 
            get("subTitle"),

            //Default to an empty string.
            literal("")
        )
    )
)
```

::: zone-end

## <a name="type-expressions"></a>Expressões de tipos

As expressões de tipo fornecem ferramentas para testar e converter diferentes tipos de dados, como cordas, números e valores booleanos.

| Expression | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `array(Expression)` | Objeto[] | Afirma que a entrada é uma matriz. |
| `bool(Expression)` | boolean | Afirma que o valor de entrada é um boolean. |
| `collator(boolean caseSensitive, boolean diacriticSensitive)` \| `collator(boolean caseSensitive, boolean diacriticSensitive, java.util.Locale locale)` \| `collator(Expression caseSensitive, Expression diacriticSensitive)` \| `collator(Expression caseSensitive, Expression diacriticSensitive, Expression locale)` | collator | Devolve um coletor para utilização em operações de comparação dependentes do local. As opções sensíveis ao caso e as informações diacríticas não são falsas. O argumento local especifica a etiqueta linguística IETF do local para usar. Se nenhum for fornecido, o local predefinido é utilizado. Se o local solicitado não estiver disponível, o coletor utilizará um local de retorno definido pelo sistema. Utilize localidade resolvida para testar os resultados do comportamento de recuo local.  |
| `literal(boolean \| number \| string \| Object \| Object[])` | \|booleana número objeto objeto \| \| \| objeto[] | Devolve um conjunto literal ou valor de objeto. Utilize esta expressão para evitar que uma matriz ou objeto seja avaliado como uma expressão. Isto é necessário quando uma matriz ou objeto precisa ser devolvido por uma expressão. |
| `number(Expression)` | número | Afirma que o valor de entrada é um número. |
| `object(Expression)` | Objeto | Afirma que o valor de entrada é um objeto. |
| `string(Expression)` | string | Afirma que o valor de entrada é uma corda. |
| `toArray(Expression)` | Objeto[] | Converte a expressão numa matriz de objeto JSON. |
| `toBool(Expression)` | boolean | Converte o valor de entrada num booleano. |
| `toNumber(Expression)` | número | Converte o valor de entrada num número, se possível. |
| `toString(Expression)` | string | Converte o valor de entrada numa cadeia. |
| `typeoOf(Expression)` | string | Devolve uma corda descrevendo o tipo do valor dado. |

## <a name="color-expressions"></a>Expressões de cor

Expressões de cor facilitam a criação e manipulação dos valores de cor.

| Expression | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `color(int)` | color | Converte um valor inteiro de cor numa expressão de cor. |
| `rgb(Expression red, Expression green, Expression blue)` \| `rgb(number red, number green, number blue)` | color | Cria um valor de cor a partir de componentes *vermelhos,* *verdes* e *azuis* que devem variar entre `0` `255` e, e um componente alfa de `1` . Se algum componente estiver fora de alcance, a expressão é um erro. |
| `rgba(Expression red, Expression green, Expression blue, Expression alpha)` \| `rgba(number red, number green, number blue, number alpha)` | color | Cria um valor de cor a partir de componentes *vermelhos,* *verdes,* *azuis* que devem variar entre `0` `255` e, e um componente alfa dentro de uma gama de `0` e . `1` . Se algum componente estiver fora de alcance, a expressão é um erro. |
| `toColor(Expression)` | color  | Converte o valor de entrada para uma cor. |
| `toRgba(Expression)` | color | Devolve uma matriz de quatro elementos contendo os componentes vermelhos, verdes, azuis e alfa da cor da entrada, por esta ordem. |

**Exemplo**

O exemplo a seguir cria um valor de cor RGB que tem um valor *vermelho* de `255` valores *verdes* e *azuis* que são calculados multiplicando `2.5` pelo valor da `temperature` propriedade. À medida que a temperatura muda, a cor mudará para diferentes tons de *vermelho.*

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            //Set red value to 255. Wrap with literal expression since using expressions for other values.
            literal(255f),    

            //Multiple the temperature by 2.5 and set the green value.
            product(literal(2.5f), get("temperature")), 

            //Multiple the temperature by 2.5 and set the blue value.
            product(literal(2.5f), get("temperature")) 
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            //Set red value to 255. Wrap with literal expression since using expressions for other values.
            literal(255f),    

            //Multiple the temperature by 2.5 and set the green value.
            product(literal(2.5f), get("temperature")), 

            //Multiple the temperature by 2.5 and set the blue value.
            product(literal(2.5f), get("temperature")) 
        )
    )
)
```

::: zone-end

Se todos os parâmetros de cor são números, não há necessidade de embrulhá-los com a `literal` expressão. Por exemplo:

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            255f,  //Set red value to 255.

            150f,  //Set green value to 150.

            0f     //Set blue value to 0.
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            255f,  //Set red value to 255.

            150f,  //Set green value to 150.

            0f     //Set blue value to 0.
        )
    )
)
```

::: zone-end

> [!TIP]
> Os valores de cor das cordas podem ser convertidos numa cor utilizando o `android.graphics.Color.parseColor` método. O seguinte converte uma corda de cor hexaduxia numa expressão de cor que pode ser usada com uma camada.
>
> ```java
> color(parseColor("#ff00ff"))
> ```

## <a name="string-operator-expressions"></a>Expressões do operador de cordas

As expressões do operador de cordas executam operações de conversão em cordas como concatenatos e convertendo a caixa.

| Expression | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `concat(string...)` \| `concat(Expression...)` | string | Concatenates várias cordas juntas. Cada valor deve ser uma corda. Utilize a `toString` expressão tipo para converter outros tipos de valor para corda, se necessário. |
| `downcase(string)` \| `downcase(Expression)` | string | Converte a cadeia especificada para minúscula. |
| `isSupportedScript(string)` \| `isSupportedScript(Expression)`| boolean | Determina se a cadeia de entrada utiliza um conjunto de caracteres suportado pela pilha de fontes atual. Por exemplo: `isSupportedScript("ಗೌರವಾರ್ಥವಾಗಿ")` |
| `resolvedLocale(Expression collator)` | string | Devolve a etiqueta linguística IETF do local utilizado pelo coletor fornecido. Isto pode ser usado para determinar o local do sistema padrão, ou para determinar se um local solicitado foi carregado com sucesso. |
| `upcase(string)` \| `upcase(Expression)` | string | Converte a cadeia especificada para maiúscula. |

**Exemplo**

O exemplo seguinte converte a `temperature` propriedade da característica do ponto numa corda e, em seguida, concatena "°F" até ao fim do mesmo.

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    textField(
        concat(Expression.toString(get("temperature")), literal("°F"))
    ),

    //Some additional style options.
    textOffset(new Float[] { 0f, -1.5f }),
    textSize(12f),
    textColor("white")
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    textField(
        concat(Expression.toString(get("temperature")), literal("°F"))
    ),

    //Some additional style options.
    textOffset(new Float[] { 0f, -1.5f }),
    textSize(12f),
    textColor("white")
)
```

::: zone-end

A expressão acima torna um pino no mapa com o texto "64°F" sobreposto em cima dele, como mostra a imagem abaixo.

![Exemplo de expressão do operador de cordas](media/how-to-expressions/string-operator-expression.png)

## <a name="interpolate-and-step-expressions"></a>Expressões interpoladas e de passo

Expressões interpoladas e passos podem ser usadas para calcular valores ao longo de uma função de curva ou passo interpolado. Estas expressões têm uma expressão que devolve um valor numérico como sua entrada, por `get("temperature")` exemplo. O valor de entrada é avaliado em pares de valores de entrada e saída, para determinar o valor que melhor se adequa à função de curva ou passo interpolado. Os valores de saída são chamados de "paragens". Os valores de entrada para cada paragem devem ser um número e estar em ordem ascendente. Os valores de saída devem ser um número, um conjunto de números, ou uma cor.

### <a name="interpolate-expression"></a>Expressão interpolada

Uma `interpolate` expressão pode ser usada para calcular um conjunto contínuo e suave de valores interpolando entre valores de paragem. Uma `interpolate` expressão que devolve valores de cor produz um gradiente de cor no qual os valores dos resultados são selecionados. A `interpolate` expressão tem os seguintes formatos:

```java
//Stops consist of two expressions.
interpolate(Expression.Interpolator interpolation, Expression number, Expression... stops)

//Stop expression wraps two values.
interpolate(Expression.Interpolator interpolation, Expression number, Expression.Stop... stops)
```

Existem três tipos de métodos de interpolação que podem ser usados numa `interpolate` expressão:

| Nome | Descrição |
|------|-------------|
| `linear()` | Interpola linearmente entre o par de paragens.  |
| `exponential(number)` \| `exponential(Expression)` | Interpola exponencialmente entre as paragens. Uma "base" é especificada e controla a taxa a que a saída aumenta. Valores mais elevados fazem com que a saída aumente mais para a extremidade alta da gama. Um valor "base" perto de 1 produz uma saída que aumenta mais linearmente.|
| `cubicBezier(number x1, number y1, number x2, number y2)` \| `cubicBezier(Expression x1, Expression y1, Expression x2, Expression y2)` | Interpolados utilizando uma [curva de Bezier cúbica](https://developer.mozilla.org/docs/Web/CSS/timing-function) definida pelos pontos de controlo dados. |

A `stop` expressão tem o `stop(stop, value)` formato.

Aqui está um exemplo de como estes diferentes tipos de interpolações se parecem.

| Linear  | Exponencial | Bezier cúbico |
|---------|-------------|--------------|
| ![Gráfico linear de interpolação](media/how-to-expressions/linear-interpolation.png) | ![Gráfico de interpolação exponencial](media/how-to-expressions/exponential-interpolation.png) | ![Gráfico de interpolação cúbico bezier](media/how-to-expressions/bezier-curve-interpolation.png) |

**Exemplo**

O exemplo a seguir usa uma `linear interpolate` expressão para definir a propriedade de uma camada de bolha com base na propriedade da característica de `bubbleColor` `temperature` ponto. Se o valor for inferior a `temperature` 60, "azul" será devolvido. Se estiver entre 60 e menos de 70, o amarelo será devolvido. Se for entre 70 e menos de 80, será `#FFA500` devolvido "laranja". Se for 80 ou mais, "vermelho" será devolvido.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        interpolate(
            linear(),
            get("temperature"),
            stop(50, color(Color.BLUE)),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        interpolate(
            linear(),
            get("temperature"),
            stop(50, color(Color.BLUE)),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
)
```

::: zone-end

A imagem a seguir demonstra como as cores são escolhidas para a expressão acima.

![Exemplo de expressão interpolada](media/how-to-expressions/interpolate-expression-example.png)

### <a name="step-expression"></a>Expressão de passo

Uma `step` expressão pode ser usada para calcular valores de resultados discretos e pisados, avaliando uma [função constante de peça](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) definida por paragens.

A `interpolate` expressão tem os seguintes formatos:

```java
step(Expression input, Expression defaultOutput, Expression... stops)

step(Expression input, Expression defaultOutput, Expression.Stop... stops)

step(Expression input, number defaultOutput, Expression... stops)

step(Expression input, number defaultOutput, Expression.Stop... stops)

step(number input, Expression defaultOutput, Expression... stops)

step(number input, Expression defaultOutput, Expression.Stop... stops)

step(number input, number defaultOutput, Expression... stops)

step(number input, number defaultOutput, Expression.Stop... stops)
```

As expressões de passo devolvem o valor de saída do stop pouco antes do valor de entrada, ou o primeiro valor de entrada se a entrada for inferior à primeira paragem.

**Exemplo**

O exemplo a seguir usa uma `step` expressão para definir a propriedade de uma camada de bolha com base na propriedade da característica de `bubbleColor` `temperature` ponto. Se o valor for inferior a `temperature` 60, "azul" será devolvido. Se estiver entre 60 e menos de 70, "amarelo" será devolvido. Se estiver entre 70 e menos de 80, "laranja" será devolvida. Se for 80 ou mais, "vermelho" será devolvido.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        step(
            get("temperature"),
            color(Color.BLUE),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        step(
            get("temperature"),
            color(Color.BLUE),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
)
```

::: zone-end

A imagem a seguir demonstra como as cores são escolhidas para a expressão acima.

![Exemplo de expressão de passo](media/how-to-expressions/step-expression-example.png)

## <a name="layer-specific-expressions"></a>Expressões específicas da camada

Expressões especiais que se aplicam apenas a camadas específicas.

### <a name="heat-map-density-expression"></a>Expressão de densidade de mapa de calor

Uma expressão de densidade de mapa de calor recupera o valor de densidade do mapa de calor para cada pixel numa camada de mapa de calor e é definida como `heatmapDensity` . Este valor é um número entre `0` e `1` . . É usado em combinação com uma `interpolation` ou expressão para definir o `step` gradiente de cor usado para colorir o mapa de calor. Esta expressão só pode ser utilizada na `heatmapColor` opção da camada do mapa de calor.

> [!TIP]
> A cor no índice 0, numa expressão de interpolação ou na cor padrão de uma cor de passo, define a cor da área onde não há dados. A cor do índice 0 pode ser usada para definir uma cor de fundo. Muitos preferem definir este valor para um preto transparente ou semi-transparente.

**Exemplo**

Este exemplo usa uma expressão de interpolação de liner para criar um gradiente de cor suave para renderizar o mapa de calor.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.MAGENTA)),
            stop(0.5, color(parseColor("#fb00fb"))),
            stop(1, color(parseColor("#00c3ff")))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.MAGENTA)),
            stop(0.5, color(parseColor("#fb00fb"))),
            stop(1, color(parseColor("#00c3ff")))
        )
    )
)
```

::: zone-end

Além de usar um gradiente liso para colorir um mapa de calor, as cores podem ser especificadas dentro de um conjunto de gamas usando uma `step` expressão. Usar uma `step` expressão para colorir o mapa de calor separa visualmente a densidade em intervalos que se assemelham a um mapa de contorno ou estilo de radar.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapColor(
        step(
            heatmapDensity(),
            color(Color.TRANSPARENT),
            stop(0.01, color(parseColor("#000080"))),
            stop(0.25, color(parseColor("#000080"))),
            stop(0.5, color(Color.GREEN)),
            stop(0.5, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapColor(
        step(
            heatmapDensity(),
            color(Color.TRANSPARENT),
            stop(0.01, color(parseColor("#000080"))),
            stop(0.25, color(parseColor("#000080"))),
            stop(0.5, color(Color.GREEN)),
            stop(0.5, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
)
```

::: zone-end

Para obter mais informações, consulte a documentação da camada de [mapa de calor](map-add-heat-map-layer-android.md) adicionar.

### <a name="line-progress-expression"></a>Expressão de progresso de linha

Uma expressão de progresso de linha recupera o progresso ao longo de uma linha de gradiente numa camada de linha e é definida como `lineProgress()` . Este valor é um número entre 0 e 1. É usado em combinação com uma `interpolation` `step` ou expressão. Esta expressão só pode ser utilizada com a `strokeGradient` opção da camada de linha.

> [!NOTE]
> A `strokeGradient` opção da camada de linha requer que a `lineMetrics` opção da fonte de dados seja definida para `true` .

**Exemplo**

Este exemplo usa a `lineProgress()` expressão para aplicar um gradiente de cor ao curso de uma linha.

::: zone pivot="programming-language-java-android"

```java
LineLayer layer = new LineLayer(source,
    strokeGradient(
        interpolate(
            linear(),
            lineProgress(),
            stop(0, color(Color.BLUE)),
            stop(0.1, color(Color.argb(255, 65, 105, 225))), //Royal Blue
            stop(0.3, color(Color.CYAN)),
            stop(0.5, color(Color.argb(255,0, 255, 0))), //Lime
            stop(0.7, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = LineLayer(source,
    strokeGradient(
        interpolate(
            linear(),
            lineProgress(),
            stop(0, color(Color.BLUE)),
            stop(0.1, color(Color.argb(255, 65, 105, 225))), //Royal Blue
            stop(0.3, color(Color.CYAN)),
            stop(0.5, color(Color.argb(255,0, 255, 0))), //Lime
            stop(0.7, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
)
```

::: zone-end

[Ver exemplo ao vivo](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Expressão de formato de campo de texto

A `format` expressão pode ser usada com a `textField` opção da camada de símbolo para fornecer formatação de texto misto. Esta expressão requer uma ou mais `formatEntry` expressões que especificam uma cadeia e um conjunto de anexação ao campo de `formatOptions` texto.

| Expressão | Descrição |
|------------|-------------|
| `format(Expression...)` | Devolve textos formatados contendo anotações para utilização em entradas de campo de texto em formato misto. |
| `formatEntry(Expression text)` \| `formatEntry(Expression text, Expression.FormatOption... formatOptions)` \| `formatEntry(String text)` \| `formatEntry(String text, Expression.FormatOption... formatOptions)` | Devolve uma entrada de corda formatada para utilização na `format` expressão. |

As seguintes opções de formato disponíveis são:

| Expressão | Descrição |
|------------|-------------|
| `formatFontScale(number)` \| `formatFontScale(Expression)` | Especifica o fator de escala para o tamanho do tipo de letra. Se especificado, este valor irá sobrepor-se à `textSize` propriedade para a cadeia individual. |
| `formatTextFont(string[])` \| `formatTextFont(Expression)` | Especifica uma cor para aplicar a um texto ao renderizar. |

**Exemplo**

O exemplo seguinte forma o campo de texto adicionando um tipo de letra arrojado e aumentando o tamanho da fonte da `title` propriedade da funcionalidade. Este exemplo também adiciona a `subTitle` propriedade da funcionalidade numa nova linha, com um tamanho de fonte escalonado.

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    textField(
        format(
            //Bold the title property and scale its font size up.
            formatEntry(
                get("title"),
                formatTextFont(new String[] { "StandardFont-Bold" }),
                formatFontScale(1.25)),

            //Add a new line without any formatting.
            formatEntry("\n"),

            //Scale the font size down of the subTitle property.
            formatEntry(
                get("subTitle"),
                formatFontScale(0.75))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    textField(
        format(
            //Bold the title property and scale its font size up.
            formatEntry(
                get("title"),
                formatTextFont(arrayOf("StandardFont-Bold")),
                formatFontScale(1.25)),

            //Add a new line without any formatting.
            formatEntry("\n"),

            //Scale the font size down of the subTitle property.
            formatEntry(
                get("subTitle"),
                formatFontScale(0.75))
        )
    )
)
```

::: zone-end

Esta camada tornará a característica de ponto como mostrado na imagem abaixo:

![Imagem de ponto com campo de texto formatado](media/how-to-expressions/text-field-format-expression.png)

## <a name="zoom-expression"></a>Expressão de zoom

Uma `zoom` expressão é usada para recuperar o nível de zoom atual do mapa no momento de renderização e é definida como `zoom()` . Esta expressão devolve um número entre a gama de zoom mínima e máxima do mapa. O mapa interativo Azure Maps controla os controlos de mapas web e Android de suporte 25 níveis de zoom, numerados de 0 a 24. A utilização da `zoom` expressão permite que os estilos sejam modificados dinamicamente à medida que o nível de zoom do mapa é alterado. A `zoom` expressão só pode ser utilizada com `interpolate` expressões e `step` expressões.

**Exemplo**

Por padrão, o raio dos pontos de dados renderizados na camada do mapa de calor tem um raio de pixel fixo para todos os níveis de zoom. À medida que o mapa é ampliado, os dados agregam-se e a camada do mapa de calor parece diferente. Uma `zoom` expressão pode ser usada para escalar o raio para cada nível de zoom de modo que cada ponto de dados cubra a mesma área física do mapa. Fará com que a camada do mapa de calor pareça mais estática e consistente. Cada nível de zoom do mapa tem o dobro dos pixéis vertical e horizontalmente do nível de zoom anterior. Escalar o raio, de modo que duplique a cada nível de zoom, irá criar um mapa de calor que parece consistente em todos os níveis de zoom. Pode ser realizado usando a `zoom` expressão com uma `base 2 exponential interpolation` expressão, com o raio de pixel definido para o nível mínimo de zoom e um raio escalonado para o nível máximo de zoom calculado `2 * Math.pow(2, minZoom - maxZoom)` como mostrado abaixo.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapRadius(
        interpolate(
            exponential(2),
            zoom(),

            //For zoom level 1 set the radius to 2 pixels.
            stop(1, 2),

            //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
            stop(19, 2 * Math.pow(2, 19 - 1))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapRadius(
        interpolate(
            exponential(2),
            zoom(),

            //For zoom level 1 set the radius to 2 pixels.
            stop(1, 2),

            //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
            stop(19, 2 * Math.pow(2, 19 - 1))
        )
    )
)
```

::: zone-end

## <a name="variable-binding-expressions"></a>Expressões de ligação variável

Expressões de ligação variável armazenam os resultados de um cálculo numa variável. Assim, que os resultados do cálculo podem ser referenciados em outros lugares numa expressão várias vezes. É uma otimização útil para expressões que envolvem muitos cálculos.

| Expression | Tipo de retorno | Descrição |
|--------------|---------------|--------------|
| `let(Expression... input)` | | Armazena um ou mais valores como variáveis para utilização pela `var` expressão na expressão da criança que devolve o resultado. |
| `var(Expression expression)` \| `var(string variableName)` | Objeto | Refere uma variável que foi criada usando a `let` expressão. |

**Exemplo**

Este exemplo usa uma expressão que calcula a receita em relação ao rácio de temperatura e, em seguida, usa uma `case` expressão para avaliar diferentes operações booleanas neste valor. A `let` expressão é usada para armazenar a receita relativamente ao rácio de temperatura, de modo que só precisa de ser calculada uma vez. A `var` expressão refere esta variável sempre que necessário sem ter que recalculá-la.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(           
        let(
            //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
            literal("ratio"), division(get("revenue"), get("temperature")),

            //Evaluate the child expression in which the stored variable will be used.
            switchCase(
                //Check to see if the ratio is less than 100, return 'red'.
                lt(var("ratio"), 100), color(Color.RED),

                //Check to see if the ratio is less than 200, return 'green'.
                lt(var("ratio"), 200), color(Color.GREEN),

                //Return `blue` for values greater or equal to 200.
                color(Color.BLUE)
            )
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(           
        let(
            //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
            literal("ratio"), division(get("revenue"), get("temperature")),

            //Evaluate the child expression in which the stored variable will be used.
            switchCase(
                //Check to see if the ratio is less than 100, return 'red'.
                lt(var("ratio"), 100), color(Color.RED),

                //Check to see if the ratio is less than 200, return 'green'.
                lt(var("ratio"), 200), color(Color.GREEN),

                //Return `blue` for values greater or equal to 200.
                color(Color.BLUE)
            )
        )
    )
)
```

::: zone-end

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as camadas que suportam expressões:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolha](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linhas](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígonos](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicione um mapa de calor](map-add-heat-map-layer-android.md)
