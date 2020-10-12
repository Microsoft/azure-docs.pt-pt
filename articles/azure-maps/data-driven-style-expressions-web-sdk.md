---
title: Expressões de estilo orientados por dados no Azure Maps Web SDK Microsoft Azure Maps
description: Saiba mais sobre expressões de estilo baseadas em dados. Veja como usar estas expressões no Azure Maps Web SDK para ajustar estilos em mapas.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 539145836849bb66bcf1f12a97ea405fe84c47bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91311381"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Expressões de estilo orientadas por dados (Web SDK)

As expressões permitem aplicar a lógica de negócio às opções de estilo que observam as propriedades definidas em cada formato numa fonte de dados. As expressões podem filtrar dados numa fonte de dados ou numa camada. As expressões podem consistir em lógica condicional, como se-declarações. E podem ser usados para manipular dados usando: operadores de cordas, operadores lógicos e operadores matemáticos.

Os estilos orientados por dados reduzem a quantidade de código necessária para implementar a lógica do negócio em torno do styling. Quando utilizadas com camadas, as expressões são avaliadas no tempo de renderização num fio separado. Esta funcionalidade proporciona um desempenho acrescido em comparação com a avaliação da lógica de negócio na linha UI.

Este vídeo fornece uma visão geral do estilo orientado por dados no Azure Maps Web SDK.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

As expressões são representadas como matrizes JSON. O primeiro elemento de uma expressão na matriz é uma cadeia que especifica o nome do operador de expressão. Por exemplo, "+" ou "caso". Os próximos elementos (se houver) são os argumentos para a expressão. Cada argumento é um valor literal (uma corda, número, booleano, `null` ou outro conjunto de expressão. O pseudocódigo que se segue define a estrutura básica de uma expressão. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

O Azure Maps Web SDK suporta muitos tipos de expressões. Expressões podem ser usadas por si próprias ou em combinação com outras expressões.

| Tipo de expressões | Descrição |
|---------------------|-------------|
| [Expressão agregada](#aggregate-expression) | Uma expressão que define um cálculo que é processado sobre um conjunto de dados e pode ser usado com a `clusterProperties` opção de a `DataSource` . |
| [Expressões booleanas](#boolean-expressions) | Expressões booleanas fornecem um conjunto de expressões de operadores booleanos para avaliar comparações booleanas. |
| [Expressões de cor](#color-expressions) | Expressões de cor facilitam a criação e manipulação dos valores de cor. |
| [Expressões condicionais](#conditional-expressions) | Expressões condicionais fornecem operações lógicas que são como se-declarações. |
| [Expressões de dados](#data-expressions) | Fornece acesso aos dados da propriedade numa funcionalidade. |
| [Expressões interpoladas e passo](#interpolate-and-step-expressions) | Expressões interpoladas e passos podem ser usadas para calcular valores ao longo de uma função de curva ou passo interpolado. |
| [Expressões específicas da camada](#layer-specific-expressions) | Expressões especiais que só são aplicáveis a uma única camada. |
| [Expressões matemáticas](#math-expressions) | Fornece aos operadores matemáticos a realização de cálculos baseados em dados dentro do quadro de expressão. |
| [Expressões do operador de cordas](#string-operator-expressions) | As expressões do operador de cordas executam operações de conversão em cordas como concatenatos e convertendo a caixa. |
| [Expressões de tipos](#type-expressions) | As expressões de tipo fornecem ferramentas para testar e converter diferentes tipos de dados, como cordas, números e valores booleanos. |
| [Expressões de ligação variável](#variable-binding-expressions) | Expressões de ligação variáveis armazenam os resultados de um cálculo numa variável e referenciadas em outros lugares numa expressão múltipla sem ter de recalcular o valor armazenado. |
| [Expressão de zoom](#zoom-expression) | Recupera o nível de zoom atual do mapa no momento de renderização. |

Todos os exemplos deste documento utilizam a seguinte característica para demonstrar diferentes formas de utilização dos diferentes tipos de expressões. 

```javascript
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
        "temperature": 72,
        "title": "Cafeteria", 
        "zoneColor": "red",
        "abcArray": ['a', 'b', 'c'],
        "array2d": [['a', 'b'], ['x', 'y']],
        "_style": {
            "fillColor": 'red'
        }
    }
}
```

## <a name="data-expressions"></a>Expressões de dados

As expressões de dados fornecem acesso aos dados da propriedade numa funcionalidade. 

| Expression | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `['at', number, array]` | objeto | Recupera um item de uma matriz. |
| `['geometry-type']` | string | Obtém o tipo de geometria da funcionalidade: Point, MultiPoint, LineString, MultiLineString, Polygon, MultiPolygon. |
| `['get', string]` | valor | Obtém o valor da propriedade a partir das propriedades da funcionalidade atual. Devoluções nulas se o imóvel solicitado faltar. |
| `['get', string, object]` | valor | Obtém o valor da propriedade a partir das propriedades do objeto fornecido. Devoluções nulas se o imóvel solicitado faltar. |
| `['has', string]` | boolean | Determina se as propriedades de uma característica têm a propriedade especificada. |
| `['has', string, object]` | boolean | Determina se as propriedades do objeto têm a propriedade especificada. |
| `['id']` | valor | Obtém a identificação da funcionalidade se tiver uma. |
| `['length', string | array]` | número | Obtém o comprimento de uma corda ou de uma matriz. |
| `['in', boolean | string | number, array]` | boolean | Determina se um item existe numa matriz |
| `['in', substring, string]` | boolean | Determina se um sub-adupe existe numa cadeia |
| `['index-of', boolean | string | number, array | string]`<br/><br/>`['index-of', boolean | string | number, array | string, number]` | número | Retorna a primeira posição na qual um item pode ser encontrado numa matriz ou um sublpemento pode ser encontrado numa cadeia, ou `-1` se a entrada não puder ser encontrada. Aceita um índice opcional de onde iniciar a pesquisa. |
| `['slice', array | string, number]`<br/><br/>`['slice', array | string, number, number]` | `string`\|matriz | Devolve um item de uma matriz ou de um sub-cordão a partir de uma cadeia a partir de um índice de início especificado, ou entre um índice inicial e um índice final se definido. O valor de retorno é inclusivo do índice inicial, mas não do índice final. |

**Exemplos**

As propriedades de uma funcionalidade podem ser acedidas diretamente numa expressão usando uma `get` expressão. Este exemplo utiliza o valor "zoneColor" da funcionalidade para especificar a propriedade colorida de uma camada de bolha. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

O exemplo acima funcionará bem, se todas as características de pontos tiverem a `zoneColor` propriedade. Se não o fizerem, a cor provavelmente voltará a "preto". Para modificar a cor do recuo, utilize uma `case` expressão em combinação com a `has` expressão para verificar se a propriedade existe. Se a propriedade não existir, devolva uma cor de retorno.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case', //Use a conditional case expression.

        ['has', 'zoneColor'],   //Check to see if feature has a "zoneColor" property
        ['get', 'zoneColor'],   //If it does, use it.

        'blue'  //If it doesn't, default to blue.
    ]
});
```

As camadas de bolha e símbolos tornarão as coordenadas de todas as formas numa fonte de dados, por padrão. Este comportamento pode destacar os vértices de um polígono ou de uma linha. A `filter` opção da camada pode ser usada para limitar o tipo de geometria das características que presta, usando uma `['geometry-type']` expressão dentro de uma expressão booleana. O exemplo a seguir limita uma camada de bolha para que apenas `Point` as características sejam renderizadas.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

O exemplo a seguir permite que tanto as `Point` `MultiPoint` características como as funcionalidades sejam prestadas. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

Da mesma forma, o contorno dos Polígonos renderizará em camadas de linha. Para desativar este comportamento numa camada de linha, adicione um filtro que apenas permite `LineString` e `MultiLineString` apresenta.  

Aqui estão alguns exemplos adicionais de como usar expressões de dados:

```javascript
//Get item [2] from an array "properties.abcArray[1]" = "c"
['at', 2, ['get', 'abcArray']]

//Get item [0][1] from a 2D array "properties.array2d[0][1]" = "b"
['at', 1, ['at', 0, ['get', 'array2d']]]

//Check to see if a value is in an array "properties.abcArray.indexOf('a') !== -1" = true
['in', 'a', ['get', 'abcArray']]

//Gets the index of the value 'b' in an array "properties.abcArray.indexOf('b')" = 1
['index-of', 'b', ['get', 'abcArray']]

//Get the length of an array "properties.abcArray.length" = 3
['length', ['get', 'abcArray']]

//Get the value of a subproperty "properties._style.fillColor" = "red"
['get', 'fillColor', ['get', '_style']]

//Check that "fillColor" exists as a subproperty of "_style".
['has', 'fillColor', ['get', '_style']]

//Slice an array starting at index 2 "properties.abcArray.slice(2)" = ['c']
['slice', ['get', 'abcArray'], 2]

//Slice a string from index 0 to index 4 "properties.entityType.slice(0, 4)" = 'rest'
['slice', ['get', 'entityType'], 0, 4]
```

## <a name="math-expressions"></a>Expressões matemáticas

As expressões matemáticas fornecem aos operadores matemáticos a realização de cálculos orientados para dados dentro do quadro de expressão.

| Expression | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `['+', number, number, …]` | número | Calcula a soma dos números especificados. |
| `['-', number]` | número | Subtrai 0 pelo número especificado. |
| `['-', number, number]` | número | Subtrai os primeiros números pelo segundo número. |
| `['*', number, number, …]` | número | Multiplica os números especificados em conjunto. |
| `['/', number, number]` | número | Divide o primeiro número pelo segundo número. |
| `['%', number, number]` | número | Calcula o restante ao dividir o primeiro número pelo segundo número. |
| `['^', number, number]` | número | Calcula o valor do primeiro valor elevado à potência do segundo número. |
| `['abs', number]` | número | Calcula o valor absoluto do número especificado. |
| `['acos', number]` | número | Calcula a arccosina do número especificado. |
| `['asin', number]` | número | Calcula a arcsina do número especificado. |
| `['atan', number]` | número | Calcula o arctangent do número especificado. |
| `['ceil', number]` | número | Arredonda o número até ao próximo inteiro inteiro inteiro. |
| `['cos', number]` | número | Calcula o porção do número especificado. |
| `['e']` | número | Devolve a constante `e` matemática. |
| `['floor', number]` | número | Arredonda o número para o inteiro inteiro anterior. |
| `['ln', number]` | número | Calcula o logaritmo natural do número especificado. |
| `['ln2']` | número | Devolve a constante `ln(2)` matemática. |
| `['log10', number]` | número | Calcula o logaritmo base-dez do número especificado. |
| `['log2', number]` | número | Calcula o logaritmo base-dois do número especificado. |
| `['max', number, number, …]` | número | Calcula o número máximo no conjunto especificado de números. |
| `['min', number, number, …]` | número | Calcula o número mínimo no conjunto especificado de números. |
| `['pi']` | número | Devolve a constante `PI` matemática. |
| `['round', number]` | número | Arredonda o número para o número mais próximo. Os valores intermédios estão arredondados para longe de zero. Por exemplo, `['round', -1.5]` avalia para -2. |
| `['sin', number]` | número | Calcula o seno do número especificado. |
| `['sqrt', number]` | número | Calcula a raiz quadrada do número especificado. |
| `['tan', number]` | número | Calcula a tangente do número especificado. |

## <a name="aggregate-expression"></a>Expressão agregada

Uma expressão agregada define um cálculo que é processado sobre um conjunto de dados e pode ser usado com a `clusterProperties` opção de um `DataSource` . A saída destas expressões deve ser um número ou um booleano. 

Uma expressão agregada requer três valores: um valor do operador e valor inicial, e uma expressão para recuperar uma propriedade de cada recurso em dados para aplicar a operação agregada. Esta expressão tem o seguinte formato:

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- operador: Uma função de expressão que é então aplicada contra todos os valores calculados `mapExpression` pelo ponto para cada ponto do cluster. Operadores apoiados: 
    - Para números: `+` `*` , `max` , `min`
    - Para Booleans: `all` , `any`
- valor inicial: Um valor inicial em que o primeiro valor calculado é agregado.
- mapExpression: Uma expressão que é aplicada contra cada ponto no conjunto de dados.

**Exemplos**

Se todas as funcionalidades de um conjunto de dados tiverem uma `revenue` propriedade, que é um número. Em seguida, a receita total de todos os pontos de um cluster, que são criados a partir do conjunto de dados, pode ser calculada. Este cálculo é feito utilizando a seguinte expressão agregada: `['+', 0, ['get', 'revenue']]`

## <a name="boolean-expressions"></a>Expressões booleanas

Expressões booleanas fornecem um conjunto de expressões de operadores booleanos para avaliar comparações booleanas.

Ao comparar valores, a comparação é estritamente dactilografada. Valores de diferentes tipos são sempre considerados desiguais. Os casos em que os tipos são conhecidos por serem diferentes no momento da análise são considerados inválidos e produzirão um erro de análise. 

| Expression | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `['!', boolean]` | boolean | Negação lógica. Retorna `true` se a entrada for , e se a entrada for `false` `false` `true` . |
| `['!=', value, value]` | boolean | Retorna `true` se os valores de entrada não forem iguais, `false` caso contrário. |
| `['<', value, value]` | boolean | Devoluções `true` se a primeira entrada for estritamente inferior à segunda, caso `false` contrário. Os argumentos são necessários para ser ambas as cordas ou ambos os números. |
| `['<=', value, value]` | boolean | Devoluções `true` se a primeira entrada for inferior ou igual à segunda, `false` caso contrário. Os argumentos são necessários para ser ambas as cordas ou ambos os números. |
| `['==', value, value]` | boolean | Retorna `true` se os valores de entrada forem iguais, `false` caso contrário. Os argumentos são necessários para ser ambas as cordas ou ambos os números. |
| `['>', value, value]` | boolean | Devoluções `true` se a primeira entrada for estritamente maior do que a segunda, caso `false` contrário. Os argumentos são necessários para ser ambas as cordas ou ambos os números. |
| `['>=' value, value]` | boolean | Devoluções `true` se a primeira entrada for maior ou igual à segunda, `false` caso contrário. Os argumentos são necessários para ser ambas as cordas ou ambos os números. |
| `['all', boolean, boolean, …]` | boolean | Devoluções `true` se todas as entradas `true` forem, caso `false` contrário. |
| `['any', boolean, boolean, …]` | boolean | Devoluções `true` se alguma das entradas `true` for, caso `false` contrário. |

## <a name="conditional-expressions"></a>Expressões condicionais

Expressões condicionais fornecem operações lógicas que são como se-declarações.

As seguintes expressões realizam operações lógicas condicionais nos dados de entrada. Por exemplo, a `case` expressão fornece lógica "se/então/outra" enquanto a `match` expressão é como uma "declaração de comutação". 

### <a name="case-expression"></a>Expressão do caso

Uma `case` expressão é um tipo de expressão condicional que fornece lógica "se/então/outra". Este tipo de expressão passa por uma lista de condições booleanas. Devolve o valor de saída da primeira condição booleana para avaliar a verdade.

O pseudocódigo que se segue define a estrutura da `case` expressão. 

```javascript
[
    'case',
    condition1: boolean, 
    output1: value,
    condition2: boolean, 
    output2: value,
    ...,
    fallback: value
]
```

**Exemplo**

O exemplo a seguir passa por diferentes condições booleanas até encontrar uma que `true` avalie, e depois retorne esse valor associado. Se nenhuma condição booleana `true` avaliar, um valor de retorno será devolvido. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case',

        //Check to see if the first boolean expression is true, and if it is, return its assigned result.
        ['has', 'zoneColor'],
        ['get', 'zoneColor'],

        //Check to see if the second boolean expression is true, and if it is, return its assigned result.
        ['all', ['has', ' temperature '], ['>', ['get', 'temperature'], 100]],
        'red',

        //Specify a default value to return.
        'green'
    ]
});
```

### <a name="match-expression"></a>Expressão de correspondência

Uma `match` expressão é um tipo de expressão condicional que proporciona uma afirmação de comutação como a lógica. A entrada pode ser qualquer expressão como `['get', 'entityType']` a que devolve uma corda ou um número. Cada rótulo deve ser um único valor literal ou uma matriz de valores literais, cujos valores devem ser todos cordas ou todos os números. A entrada corresponde se algum dos valores da matriz corresponder. Cada rótulo deve ser único. Se o tipo de entrada não corresponder ao tipo de etiquetas, o resultado será o valor de retorno.

O pseudocódigo que se segue define a estrutura da `match` expressão. 

```javascript
[
    'match',
    input: number | string,
    label1: number | string | (number | string)[], 
    output1: value,
    label2: number | string | (number | string)[], 
    output2: value,
    ...,
    fallback: value
]
```

**Exemplos**

O exemplo a seguir olha para a `entityType` propriedade de uma característica Point em uma camada de bolha procura por uma correspondência. Se encontrar uma correspondência, esse valor especificado é devolvido ou devolve o valor de retorno.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        'restaurant', 'red',
        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

O exemplo a seguir utiliza um conjunto para listar um conjunto de etiquetas que devem devolver todos o mesmo valor. Esta abordagem é muito mais eficiente do que a listagem individual de cada rótulo. Neste caso, se a `entityType` propriedade for "restaurante" ou "grocery_store", a cor "vermelho" será devolvida.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        ['restaurant', 'grocery_store'], 'red',

        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

O exemplo a seguir utiliza uma expressão de correspondência para executar um filtro do tipo "in array" ou "array contains". Neste caso, a expressão filtra dados que têm um valor de ID que está numa lista de IDs permitidos. Ao utilizar expressões com filtros, o resultado tem de ser um valor Boolean.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: [
        'match',  

        //Get the property to match.
        ['get', 'id'],  

         //List of values to match.
        [24, 53, 98], 

        //If there is a match, return true.
        true,
    
        //Otherwise return false.
        false
    ]
});
```

### <a name="coalesce-expression"></a>Expressão do coalesce

Uma `coalesce` expressão passa por um conjunto de expressões até que o primeiro valor não nulo seja obtido e devolva esse valor. 

O pseudocódigo que se segue define a estrutura da `coalesce` expressão. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Exemplo**

O exemplo a seguir utiliza uma `coalesce` expressão para definir a `textField` opção de uma camada de símbolo. Se a `title` propriedade faltar da funcionalidade ou definida `null` para, a expressão tentará então procurar a `subtitle` propriedade, se a sua falta `null` ou, então, cairá de volta para uma corda vazia. 

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'coalesce',

            //Try getting the title property.
            ['get', 'title'],

            //If there is no title, try getting the subtitle. 
            ['get', 'subtitle'],

            //Default to an empty string.
            ''
        ]
    }
});
```

O exemplo a seguir utiliza uma `coalesce` expressão para recuperar o primeiro ícone de imagem disponível disponível no mapa de uma lista de nomes de imagem especificados.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    iconOptions: {
        image: [
            'coalesce',

            //Try getting the image with id 'missing-image'.
            ['image', 'missing-image'],

            //Specify an image id to fallback to. 
            'marker-blue'
        ]
    }
});
``` 

## <a name="type-expressions"></a>Expressões de tipos

As expressões de tipo fornecem ferramentas para testar e converter diferentes tipos de dados, como cordas, números e valores booleanos.

| Expression | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | objeto de matriz \| | Devolve um conjunto literal ou valor de objeto. Utilize esta expressão para evitar que uma matriz ou objeto seja avaliado como uma expressão. Isto é necessário quando uma matriz ou objeto precisa ser devolvido por uma expressão. |
| `['image', string]` | string | Verifica se um ID de imagem especificado é carregado no sprite de imagem dos mapas. Se for, o ID é devolvido, caso contrário, o nulo é devolvido. |
| `['to-boolean', value]` | boolean | Converte o valor de entrada num booleano. O resultado é `false` quando a entrada é uma corda vazia, , , ou `0` , caso contrário a sua `false` `null` `NaN` `true` . |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | Converte o valor de entrada para uma cor. Se forem fornecidos vários valores, cada um é avaliado por ordem até obter a primeira conversão bem sucedida. Se nenhuma das entradas puder ser convertida, a expressão é um erro. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | número | Converte o valor de entrada num número, se possível. Se a entrada for `null` `false` ou, o resultado é 0. Se a entrada `true` for, o resultado é 1. Se a entrada for uma cadeia, é convertida para um número utilizando a função de cadeia [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) da Especificação de Idioma ECMAScript. Se forem fornecidos vários valores, cada um é avaliado por ordem até obter a primeira conversão bem sucedida. Se nenhuma das entradas puder ser convertida, a expressão é um erro. |
| `['to-string', value]` | string | Converte o valor de entrada numa cadeia. Se a entrada for `null` , o resultado é `""` . Se a entrada for um boolean, o resultado é `"true"` ou `"false"` . Se a entrada for um número, é convertido para uma cadeia utilizando a função número [tostring](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) da especificação de idioma ECMAScript. Se a entrada for uma cor, é convertida para cordão de cores CSS RGBA `"rgba(r,g,b,a)"` . Caso contrário, a entrada é convertida numa cadeia utilizando a função [JSON.stringify](https://tc39.github.io/ecma262/#sec-json.stringify) da Especificação de Idioma ECMAScript. |
| `['typeof', value]` | string | Devolve uma corda descrevendo o tipo do valor dado. |

> [!TIP]
> Se uma mensagem de erro semelhante a `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` aparecer na consola do navegador, significa que há uma expressão algures no seu código que tem uma matriz que não tem uma cadeia para o seu primeiro valor. Se quiser que a expressão devolva uma matriz, embrulhe a matriz com a `literal` expressão. O exemplo a seguir define a opção de ícone `offset` de uma camada de símbolo, que precisa de ser uma matriz contendo dois números, utilizando uma expressão para escolher entre dois `match` valores de compensação com base no valor da  `entityType` propriedade da característica de ponto.
>
> ```javascript
> var layer = new atlas.layer.SymbolLayer(datasource, null, {
>     iconOptions: {
>         offset: [
>             'match',
>
>             //Get the entityType value.
>             ['get', 'entityType'],
>
>             //If the entity type is 'restaurant', return a different pixel offset. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>Expressões de cor

Expressões de cor facilitam a criação e manipulação dos valores de cor.

| Expression | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | Cria um valor de cor a partir de componentes *vermelhos,* *verdes*e *azuis* que devem variar entre `0` `255` e, e um componente alfa de `1` . Se algum componente estiver fora de alcance, a expressão é um erro. |
| `['rgba', number, number, number, number]` | color | Cria um valor de cor a partir de componentes *vermelhos,* *verdes,* *azuis* que devem variar entre `0` `255` e, e um componente alfa dentro de uma gama de `0` e . `1` . Se algum componente estiver fora de alcance, a expressão é um erro. |
| `['to-rgba']` | \[número, número, número, número\] | Devolve uma matriz de quatro elementos contendo os componentes *vermelhos,* *verdes,* *azuis*e *alfa* da cor da entrada, por esta ordem. |

**Exemplo**

O exemplo a seguir cria um valor de cor RGB que tem um valor *vermelho* de `255` valores *verdes* e *azuis* que são calculados multiplicando `2.5` pelo valor da `temperature` propriedade. À medida que a temperatura muda, a cor mudará para diferentes tons de *vermelho.*

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'rgb', //Create a RGB color value.

        255,    //Set red value to 255.

        ['*', 2.5, ['get', 'temperature']], //Multiple the temperature by 2.5 and set the green value.

        ['*', 2.5, ['get', 'temperature']]  //Multiple the temperature by 2.5 and set the blue value.
    ]
});
```

## <a name="string-operator-expressions"></a>Expressões do operador de cordas

As expressões do operador de cordas executam operações de conversão em cordas como concatenatos e convertendo a caixa. 

| Expression | Tipo de retorno | Description |
|------------|-------------|-------------|
| `['concat', string, string, …]` | cadeia (de carateres) | Concatenates várias cordas juntas. Cada valor deve ser uma corda. Utilize a `to-string` expressão tipo para converter outros tipos de valor para corda, se necessário. |
| `['downcase', string]` | string | Converte a cadeia especificada para minúscula. |
| `['upcase', string]` | string | Converte a cadeia especificada para maiúscula. |

**Exemplo**

O exemplo seguinte converte a `temperature` propriedade da característica do ponto numa corda e, em seguida, concatena "°F" até ao fim do mesmo.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: ['concat', ['to-string', ['get', 'temperature']], '°F'],

        //Some additional style options.
        offset: [0, -1.5],
        size: 12,
        color: 'white'
    }
});
```

A expressão acima torna um pino no mapa com o texto "64°F" sobreposto em cima dele, como mostra a imagem abaixo.

<center>

![Exemplo de expressão do operador de cordas ](media/how-to-expressions/string-operator-expression.png)</center>

## <a name="interpolate-and-step-expressions"></a>Expressões interpoladas e passo

Expressões interpoladas e passos podem ser usadas para calcular valores ao longo de uma função de curva ou passo interpolado. Estas expressões têm uma expressão que devolve um valor numérico como sua entrada, por `['get',  'temperature']` exemplo. O valor de entrada é avaliado em pares de valores de entrada e saída, para determinar o valor que melhor se adequa à função de curva ou passo interpolado. Os valores de saída são chamados de "paragens". Os valores de entrada para cada paragem devem ser um número e estar em ordem ascendente. Os valores de saída devem ser um número, um conjunto de números, ou uma cor.

### <a name="interpolate-expression"></a>Expressão interpolada

Uma `interpolate` expressão pode ser usada para calcular um conjunto contínuo e suave de valores interpolando entre valores de paragem. Uma `interpolate` expressão que devolve valores de cor produz um gradiente de cor no qual os valores dos resultados são selecionados.

Existem três tipos de métodos de interpolação que podem ser usados numa `interpolate` expressão:
 
* `['linear']` - Interpola-se linearmente entre o par de paragens.
* `['exponential', base]` - Interpola exponencialmente entre as paragens. O `base` valor controla a taxa a que a produção aumenta. Valores mais elevados fazem com que a saída aumente mais para a extremidade alta da gama. Um `base` valor próximo de 1 produz uma saída que aumenta mais linearmente.
* `['cubic-bezier', x1, y1, x2, y2]` - Interpolados utilizando uma [curva de Bezier cúbica](https://developer.mozilla.org/docs/Web/CSS/timing-function) definida pelos pontos de controlo determinados.

Aqui está um exemplo de como estes diferentes tipos de interpolações se parecem. 

| Linear  | Exponencial | Bezier cúbico |
|---------|-------------|--------------|
| ![Gráfico linear de interpolação](media/how-to-expressions/linear-interpolation.png) | ![Gráfico de interpolação exponencial](media/how-to-expressions/exponential-interpolation.png) | ![Gráfico de interpolação cúbico bezier](media/how-to-expressions/bezier-curve-interpolation.png) |

O pseudocódigo que se segue define a estrutura da `interpolate` expressão. 

```javascript
[
    'interpolate',
    interpolation: ['linear'] | ['exponential', base] | ['cubic-bezier', x1, y1, x2, y2],
    input: number,
    stopInput1: number, 
    stopOutput1: value1,
    stopInput2: number, 
    stopOutput2: value2, 
    ...
]
```

**Exemplo**

O exemplo a seguir usa uma `linear interpolate` expressão para definir a propriedade de uma camada de bolha com base na propriedade da característica de `color` `temperature` ponto. Se o valor for inferior a `temperature` 60, "azul" será devolvido. Se estiver entre 60 e menos de 70, o amarelo será devolvido. Se estiver entre 70 e menos de 80, "laranja" será devolvida. Se for 80 ou mais, "vermelho" será devolvido.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['get', 'temperature'],
        50,        
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

A imagem a seguir demonstra como as cores são escolhidas para a expressão acima.
 
<center>

![Exemplo ](media/how-to-expressions/interpolate-expression-example.png) de expressão interpolada </center>

### <a name="step-expression"></a>Expressão de passo

Uma `step` expressão pode ser usada para calcular valores de resultados discretos e pisados, avaliando uma [função constante de peça](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) definida por paragens. 

O pseudocódigo que se segue define a estrutura da `step` expressão. 

```javascript
[
    'step',
    input: number,
    output0: value0,
    stop1: number, 
    output1: value1,
    stop2: number, 
    output2: value2, 
    ...
]
```

As expressões de passo devolvem o valor de saída do stop pouco antes do valor de entrada, ou o primeiro valor de entrada se a entrada for inferior à primeira paragem. 

**Exemplo**

O exemplo a seguir usa uma `step` expressão para definir a propriedade de uma camada de bolha com base na propriedade da característica de `color` `temperature` ponto. Se o valor for inferior a `temperature` 60, "azul" será devolvido. Se estiver entre 60 e menos de 70, "amarelo" será devolvido. Se estiver entre 70 e menos de 80, "laranja" será devolvida. Se for 80 ou mais, "vermelho" será devolvido.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'step',
        ['get', 'temperature'],
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

A imagem a seguir demonstra como as cores são escolhidas para a expressão acima.
 
<center>

![Exemplo de expressão de passo](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Expressões específicas da camada

Expressões especiais que se aplicam apenas a camadas específicas.

### <a name="heat-map-density-expression"></a>Expressão de densidade de mapa de calor

Uma expressão de densidade de mapa de calor recupera o valor de densidade do mapa de calor para cada pixel numa camada de mapa de calor e é definida como `['heatmap-density']` . Este valor é um número entre `0` e `1` . . É usado em combinação com uma `interpolation` ou expressão para definir o `step` gradiente de cor usado para colorir o mapa de calor. Esta expressão só pode ser usada na [opção](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions#color) de cor da camada do mapa de calor.

> [!TIP]
> A cor no índice 0, numa expressão de interpolação ou na cor padrão de uma cor de passo, define a cor da área onde não há dados. A cor do índice 0 pode ser usada para definir uma cor de fundo. Muitos preferem definir este valor para um preto transparente ou semi-transparente.

**Exemplo**

Este exemplo usa uma expressão de interpolação de liner para criar um gradiente de cor suave para renderizar o mapa de calor. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['heatmap-density'],
        0, 'transparent',
        0.01, 'purple',
        0.5, '#fb00fb',
        1, '#00c3ff'
    ]
});
```

Além de usar um gradiente liso para colorir um mapa de calor, as cores podem ser especificadas dentro de um conjunto de gamas usando uma `step` expressão. Usar uma `step` expressão para colorir o mapa de calor separa visualmente a densidade em intervalos que se assemelham a um mapa de contorno ou estilo de radar.  

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'step',
        ['heatmap-density'],
        'transparent',
        0.01, 'navy',
        0.25, 'navy',
        0.5, 'green',
        0.75, 'yellow',
        1, 'red'
    ]
});
```

Para obter mais informações, consulte a documentação da camada de [mapa de calor](map-add-heat-map-layer.md) adicionar.

### <a name="line-progress-expression"></a>Expressão de progresso de linha

Uma expressão de progresso de linha recupera o progresso ao longo de uma linha de gradiente numa camada de linha e é definida como `['line-progress']` . Este valor é um número entre 0 e 1. É usado em combinação com uma `interpolation` `step` ou expressão. Esta expressão só pode ser utilizada com a [opção strokeGradient]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions#strokegradient) da camada de linha. 

> [!NOTE]
> A `strokeGradient` opção da camada de linha requer que a `lineMetrics` opção da fonte de dados seja definida para `true` .

**Exemplo**

Este exemplo usa a `['line-progress']` expressão para aplicar um gradiente de cor ao curso de uma linha.

```javascript
var layer = new atlas.layer.LineLayer(datasource, null, {
    strokeGradient: [
        'interpolate',
        ['linear'],
        ['line-progress'],
        0, "blue",
        0.1, "royalblue",
        0.3, "cyan",
        0.5, "lime",
        0.7, "yellow",
        1, "red"
    ]
});
```

[Ver exemplo ao vivo](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Expressão de formato de campo de texto

A expressão do formato de campo de texto pode ser usada com a `textField` opção da propriedade de camadas de símbolo `textOptions` para fornecer formatação de texto misto. Esta expressão permite especificar um conjunto de cadeias de entrada e opções de formatação. As seguintes opções podem ser especificadas para cada cadeia de entrada nesta expressão.

 * `'font-scale'` - Especifica o fator de escala para o tamanho do tipo de letra. Se especificado, este valor irá sobrepor-se à `size` propriedade da `textOptions` cadeia individual.
 * `'text-font'` - Especifica uma ou mais famílias de fontes que devem ser utilizadas para esta cadeia. Se especificado, este valor irá sobrepor-se à `font` propriedade da `textOptions` cadeia individual.
 * `'text-color'` - Especifica uma cor para aplicar a um texto ao renderizar. 

O pseudocódigo que se segue define a estrutura da expressão do formato de campo de texto. 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[],
        'text-color': color
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[] ,
        'text-color': color
    },
    …
]
```

**Exemplo**

O exemplo seguinte forma o campo de texto adicionando um tipo de letra arrojado e aumentando o tamanho da fonte da `title` propriedade da funcionalidade. Este exemplo também adiciona a `subtitle` propriedade da funcionalidade numa nova linha, com um tamanho de fonte escalonado e vermelho colorido.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'format',

            //Bold the title property and scale its font size up.
            ['get', 'title'],
            {
                'text-font': ['literal', ['StandardFont-Bold']],
                'font-scale': 1.25
            },

            '\n', {},   //Add a new line without any formatting.

            //Scale the font size down of the subtitle property. 
            ['get', 'subtitle'],
            { 
                'font-scale': 0.75, 
                'text-color': 'red' 
            }
        ]
    }
});
```

Esta camada tornará a característica de ponto como mostrado na imagem abaixo:
 
<center>

![Imagem de ponto com campo ](media/how-to-expressions/text-field-format-expression.png) de texto formatado </center>

### <a name="number-format-expression"></a>Expressão de formato de número

A `number-format` expressão só pode ser utilizada com a `textField` opção de uma camada de símbolo. Esta expressão converte o número fornecido numa cadeia formatada. Esta expressão envolve a função [Número.toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) do JavaScript e suporta o seguinte conjunto de opções.

 * `locale` - Especifique esta opção para converter números em cordas de uma forma que se alinhe com a linguagem especificada. Passe uma [etiqueta de idiomas BCP 47](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) nesta opção.
 * `currency` - Converter o número numa cadeia representando uma moeda. Os valores possíveis são os [códigos cambiais ISO 4217](https://en.wikipedia.org/wiki/ISO_4217), tais como "USD" para o dólar norte-americano, "EUR" para o euro, ou "CNY" para o RMB chinês.
 * `'min-fraction-digits'` - Especifica o número mínimo de locais decimais a incluir na versão de cadeia do número.
 * `'max-fraction-digits'` - Especifica o número máximo de locais decimais a incluir na versão de cadeia do número.

O pseudocódigo que se segue define a estrutura da expressão do formato de campo de texto. 

```javascript
[
    'number-format', 
    input: number, 
    options: {
        locale: string, 
        currency: string, 
        'min-fraction-digits': number, 
        'max-fraction-digits': number
    }
]
```

**Exemplo**

O exemplo a seguir usa uma `number-format` expressão para modificar a forma como a `revenue` propriedade da característica de ponto é renderizada na `textField` opção de uma camada de símbolo de tal forma que parece um valor em dólar dos EUA.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { ‘currency': 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

Esta camada tornará a característica de ponto como mostrado na imagem abaixo:

<center>

![Exemplo de expressão de formato de número ](media/how-to-expressions/number-format-expression.png)</center>

### <a name="image-expression"></a>Expressão de imagem

Uma expressão de imagem pode ser usada com as `image` opções e `textField` opções de uma camada de símbolo, e a `fillPattern` opção da camada de polígono. Esta expressão verifica se a imagem solicitada existe no estilo e devolverá o nome de imagem resolvido `null` ou, dependendo se a imagem está ou não no estilo. Este processo de validação é sincronizado e requer que a imagem tenha sido adicionada ao estilo antes de a solicitar no argumento da imagem.

**Exemplo**

O exemplo a seguir usa uma `image` expressão para adicionar um ícone inline com texto numa camada de símbolo. 

```javascript
 //Load the custom image icon into the map resources.
map.imageSprite.add('wifi-icon', 'wifi.png').then(function () {

    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);

    //Create a point feature and add it to the data source.
    datasource.add(new atlas.data.Point(map.getCamera().center));

    //Add a layer for rendering point data as symbols.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none'
        },
        textOptions: {
            //Create a formatted text string that has an icon in it.
            textField: ["format", 'Ricky\'s ', ["image", "wifi-icon"], ' Palace']
        }
    }));
});
```

Esta camada tornará o campo de texto na camada de símbolo, como mostrado na imagem abaixo:

<center>

![Exemplo de expressão de imagem ](media/how-to-expressions/image-expression.png)</center>

## <a name="zoom-expression"></a>Expressão de zoom

Uma `zoom` expressão é usada para recuperar o nível de zoom atual do mapa no momento de renderização e é definida como `['zoom']` . Esta expressão devolve um número entre a gama de zoom mínima e máxima do mapa. O mapa interativo Azure Maps controla os controlos de mapas web e Android de suporte 25 níveis de zoom, numerados de 0 a 24. A utilização da `zoom` expressão permite que os estilos sejam modificados dinamicamente à medida que o nível de zoom do mapa é alterado. A `zoom` expressão só pode ser utilizada com `interpolate` expressões e `step` expressões.

**Exemplo**

Por padrão, o raio dos pontos de dados renderizados na camada do mapa de calor tem um raio de pixel fixo para todos os níveis de zoom. À medida que o mapa é ampliado, os dados agregam-se e a camada do mapa de calor parece diferente. Uma `zoom` expressão pode ser usada para escalar o raio para cada nível de zoom de modo que cada ponto de dados cubra a mesma área física do mapa. Fará com que a camada do mapa de calor pareça mais estática e consistente. Cada nível de zoom do mapa tem o dobro dos pixéis vertical e horizontalmente do nível de zoom anterior. Escalar o raio, de modo que duplique a cada nível de zoom, irá criar um mapa de calor que parece consistente em todos os níveis de zoom. Pode ser realizado usando a `zoom` expressão com uma `base 2 exponential interpolation` expressão, com o raio de pixel definido para o nível mínimo de zoom e um raio escalonado para o nível máximo de zoom calculado `2 * Math.pow(2, minZoom - maxZoom)` como mostrado abaixo.

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        10, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * Math.pow(2, 19 - 1) pixels (524,288 pixels).
        19, 2 * Math.pow(2, 19 - 1)
    ]
};
```

[Ver exemplo ao vivo](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>Expressões de ligação variável

Expressões de ligação variável armazenam os resultados de um cálculo numa variável. Assim, que os resultados do cálculo podem ser referenciados em outros lugares numa expressão várias vezes. É uma otimização útil para expressões que envolvem muitos cálculos.

| Expression | Tipo de retorno | Descrição |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"Vamos",<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome1: cadeia,<br/>&nbsp;&nbsp;&nbsp;&nbsp;valor1: qualquer,<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome2: cadeia,<br/>&nbsp;&nbsp;&nbsp;&nbsp;valor2: qualquer,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;expressão infantil<br/>\] | | Armazena um ou mais valores como variáveis para utilização pela `var` expressão na expressão da criança que devolve o resultado. |
| `['var', name: string]` | qualquer | Refere uma variável que foi criada usando a `let` expressão. |

**Exemplo**

Este exemplo usa uma expressão que calcula a receita em relação ao rácio de temperatura e, em seguida, usa uma `case` expressão para avaliar diferentes operações booleanas neste valor. A `let` expressão é usada para armazenar a receita relativamente ao rácio de temperatura, de modo que só precisa de ser calculada uma vez. A `var` expressão refere esta variável sempre que necessário sem ter que recalculá-la.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
        'let', 'ratio', ['/', ['get', 'revenue'], ['get', 'temperature']],
        //Evaluate the child expression in which the stored variable will be used.
        [
            'case',

            //Check to see if the ratio is less than 100, return 'red'.
            ['<', ['var', 'ratio'], 100],
            'red',

            //Check to see if the ratio is less than 200, return 'green'.
            ['<', ['var', 'ratio'], 200],
            'green',

            //Return `blue` for values greater or equal to 200.
            'blue'
        ]
    ]
});
```

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para obter mais amostras de código que implementem expressões:

> [!div class="nextstepaction"] 
> [Adicionar uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Adicionar uma camada de bolha](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linhas](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígonos](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Adicionar uma camada de mapa térmico](map-add-heat-map-layer.md)

Saiba mais sobre as opções de camada que suportam expressões:

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions)

> [!div class="nextstepaction"] 
> [Opções HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)

> [!div class="nextstepaction"] 
> [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions)

> [!div class="nextstepaction"] 
> [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions)
