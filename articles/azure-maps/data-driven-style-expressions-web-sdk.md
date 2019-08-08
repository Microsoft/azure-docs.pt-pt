---
title: Expressões de estilo controladas por dados no SDK da Web do Azure Maps | Microsoft Docs
description: Como usar expressões de estilo controladas por dados no SDK da Web do Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 18d8f2a974fb192578163f71a57d00824ae6b0fa
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839470"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Expressões de estilo controladas por dados (SDK da Web)

As expressões permitem aplicar lógica de negócios a opções de estilo que observam as propriedades definidas em cada forma em uma fonte de dados. As expressões também podem ser usadas para filtrar dados em uma fonte de dados ou camada. As expressões podem consistir em lógica condicional, como If-Statements, e também podem ser usadas para manipular dados com; operadores de cadeia de caracteres, lógicos e matemáticos. 

Os estilos controlados por dados podem reduzir a quantidade de código necessária para implementar a lógica de negócios em relação ao estilo. Quando usado com camadas, as expressões são avaliadas no tempo de renderização em um thread separado que fornece maior desempenho quando comparada à avaliação da lógica de negócios no thread da interface do usuário.

O vídeo a seguir fornece uma visão geral do estilo controlado por dados no SDK da Web do Azure Maps.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

As expressões são representadas como matrizes JSON. O primeiro elemento de uma expressão na matriz é uma cadeia de caracteres que especifica o nome do operador de expressão. Por exemplo, "+" ou "Case". Os próximos elementos (se houver) são os argumentos para a expressão. Cada argumento é um valor literal (uma cadeia de caracteres, número, booliano `null`ou) ou outra matriz de expressão. O pseudocódigo a seguir define a estrutura básica de uma expressão. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

O SDK da Web do Azure Maps dá suporte a muitos tipos de que podem ser usados por conta própria ou em combinação com outras expressões.

| Tipo de expressões | Descrição |
|---------------------|-------------|
| [Expressão de agregação](#aggregate-expression) | Uma expressão que define um cálculo que é processado em um conjunto de dados e pode ser usado com a `clusterProperties` opção de a `DataSource`. |
| [Expressões booleanas](#boolean-expressions) | As expressões booleanas fornecem um conjunto de expressões de operadores boolianos para avaliar comparações boolianas. |
| [Expressões de cores](#color-expressions) | As expressões de cor facilitam a criação e a manipulação de valores de cores. |
| [Expressões condicionais](#conditional-expressions) | As expressões condicionais fornecem operações lógicas que são como instruções IF-. |
| [Expressões de dados](#data-expressions) | Fornece acesso aos dados de propriedade em um recurso. |
| [Expressões de interpolação e etapas](#interpolate-and-step-expressions) | As expressões Interpolate e Step podem ser usadas para calcular valores ao longo de uma curva interpolada ou função Step. |
| [Expressões específicas de camada](#layer-specific-expressions) | Expressões especiais que só se aplicam a uma única camada. |
| [Expressões matemáticas](#math-expressions) | Fornece operadores matemáticos para executar cálculos controlados por dados dentro do Expression Framework. |
| [Expressões de operador de cadeia de caracteres](#string-operator-expressions) | Expressões do operador String executam operações de conversão em cadeias de caracteres como concatenar e converter o caso. |
| [Expressões de tipo](#type-expressions) | As expressões de tipo fornecem ferramentas para testar e converter diferentes tipos de dados, como cadeias de caracteres, números e valores Boolianos. |
| [Expressões de associação de variáveis](#variable-binding-expressions) | As expressões de associação de variáveis permitem que os resultados de um cálculo sejam armazenados em uma variável e referenciados em outro lugar em uma expressão várias vezes sem precisar recalcular o valor armazenado. |
| [Expressão de zoom](#zoom-expression) | Recupera o nível de zoom atual do mapa no momento da renderização. |

Todos os exemplos neste documento usarão o recurso a seguir para demonstrar diferentes maneiras em que os diferentes tipos de expressões podem ser usados. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {     
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 72,
        "title": "Cafeteria", 
        "zoneColor": "red"
    }
}
```

## <a name="data-expressions"></a>Expressões de dados

As expressões de dados fornecem acesso aos dados de propriedade em um recurso. 

| Expressão | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `['at', number, array]` | object | Recupera um item de uma matriz. |
| `['geometry-type']` | cadeia | Obtém o tipo de geometria do recurso: Point, multiponto, LineString, MultiLineString, polígono, MultiPolygon. |
| `['get', string]` | value | Obtém o valor da propriedade das propriedades do recurso atual. Retornará NULL se a propriedade solicitada estiver ausente. |
| `['get', string, object]` | value | Obtém o valor da propriedade das propriedades do objeto fornecido. Retornará NULL se a propriedade solicitada estiver ausente. |
| `['has', string]` | boolean | Determina se as propriedades de um recurso têm a propriedade especificada. |
| `['has', string, object]` | boolean | Determina se as propriedades do objeto têm a propriedade especificada. |
| `['id']` | value | Obtém a ID do recurso se ele tiver um. |
| `['length', string | array]` | número | Obtém o comprimento de uma cadeia de caracteres ou matriz. |

**Exemplos**

As propriedades de um recurso podem ser acessadas diretamente em uma expressão `get` usando uma expressão. O exemplo a seguir usa o valor "zoneColor" do recurso para especificar a Propriedade Color de uma camada Bubble. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

O exemplo acima funcionará bem se todos os recursos de ponto tiverem `zoneColor` a propriedade, mas se não forem, a cor provavelmente retornará para "preto". Para modificar a cor de fallback, `case` uma expressão pode ser usada em combinação com `has` a expressão para verificar se a propriedade existe e, em vez disso, não retorna uma cor de fallback.

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

As camadas de bolhas e símbolos renderizarão as coordenadas de todas as formas em uma fonte de dados por padrão. Isso pode ser feito para realçar os vértices de um polígono ou linha. A `filter` opção da camada pode ser usada para limitar o tipo de geometria dos recursos que ele renderiza usando uma `['geometry-type']` expressão dentro de uma expressão booliana. O exemplo a seguir limita uma camada de bolha para `Point` que apenas os recursos sejam renderizados.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

O exemplo a seguir permitirá `Point` que `MultiPoint` ambos os recursos e sejam renderizados. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

Da mesma forma, a estrutura de polígonos será renderizada em camadas de linha. Para desabilitar esse comportamento em uma camada de linha, adicione um filtro que permita `LineString` apenas `MultiLineString` recursos e.  

## <a name="math-expressions"></a>Expressões matemáticas

As expressões matemáticas fornecem operadores matemáticos para executar cálculos controlados por dados dentro do Expression Framework.

| Expressão | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `['+', number, number, …]` | número | Calcula a soma dos números especificados. |
| `['-', number]` | número | Subtrai 0 pelo número especificado. |
| `['-', number, number]` | número | Subtrai os primeiros números pelo segundo número. |
| `['*', number, number, …]` | número | Multiplica os números especificados juntos. |
| `['/', number, number]` | número | Divide o primeiro número pelo segundo número. |
| `['%', number, number]` | número | Calcula o resto ao dividir o primeiro número pelo segundo número. |
| `['^', number, number]` | número | Calcula o valor do primeiro valor elevado à potência do segundo número. |
| `['abs', number]` | número | Calcula o valor absoluto do número especificado. |
| `['acos', number]` | número | Calcula o arco cosseno do número especificado. |
| `['asin', number]` | número | Calcula o arco seno do número especificado. |
| `['atan', number]` | número | Calcula o arco tangente do número especificado. |
| `['ceil', number]` | número | Arredonda o número para cima até o próximo número inteiro. |
| `['cos', number]` | número | Calcula o cos do número especificado. |
| `['e']` | número | Retorna a constante `e`matemática. |
| `['floor', number]` | número | Arredonda o número para baixo até o inteiro completo anterior. |
| `['ln', number]` | número | Calcula o logaritmo natural do número especificado. |
| `['ln2']` | número | Retorna a constante `ln(2)`matemática. |
| `['log10', number]` | número | Calcula o logaritmo de base 10 do número especificado. |
| `['log2', number]` | número | Calcula o logaritmo de base dois do número especificado. |
| `['max', number, number, …]` | número | Calcula o número máximo no conjunto de números especificado. |
| `['min', number, number, …]` | número | Calcula o número mínimo no conjunto de números especificado. |
| `['pi']` | número | Retorna a constante `PI`matemática. |
| `['round', number]` | número | Arredonda o número para o inteiro mais próximo. Valores intermediários são arredondados para longe de zero. Por exemplo, `['round', -1.5]` é avaliado como-2. |
| `['sin', number]` | número | Calcula o seno do número especificado. |
| `['sqrt', number]` | número | Calcula a raiz quadrada do número especificado. |
| `['tan', number]` | número | Calcula a tangente do número especificado. |

## <a name="aggregate-expression"></a>Expressão de agregação

Uma expressão de agregação define um cálculo que é processado em um conjunto de dados e pode ser usado `clusterProperties` com a opção `DataSource`de a. A saída dessas expressões deve ser um número ou booliano. 

Uma expressão de agregação leva em três valores; um valor de operador e um valor inicial e uma expressão para recuperar uma propriedade de cada recurso em um dado para aplicar a operação de agregação. Essa expressão tem o seguinte formato:

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- operador Uma função de expressão que é aplicada a todos os valores calculados pelo `mapExpression` para cada ponto no cluster. Operadores com suporte; 
    - Para números: `+`, `*`, `max`,`min`
    - Para boolianos: `all`,`any`
- inicialvalue: Um valor inicial no qual o primeiro valor calculado é agregado.
- mapExpression: Uma expressão que é aplicada a cada ponto no conjunto de dados.

**Exemplos**

Se todos os recursos em um conjunto de dados `revenue` tiverem uma propriedade que é um número. A receita total de todos os pontos em um cluster criado a partir do conjunto de dados pode ser calculada usando a seguinte expressão de agregação:`['+', 0, ['get', 'revenue']]`

## <a name="boolean-expressions"></a>Expressões booleanas

As expressões booleanas fornecem um conjunto de expressões de operadores boolianos para avaliar comparações boolianas.

Ao comparar valores, a comparação é estritamente digitada. Os valores de tipos diferentes são sempre considerados desiguais. Os casos em que os tipos são conhecidos como diferentes no momento da análise são considerados inválidos e produzirão um erro de análise. 

| Expressão | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `['! ', boolean]` | boolean | Negação lógica. Retorna `true` se a entrada é `false`e `false` se a entrada é `true`. |
| `['!= ', value, value]` | boolean | Retorna `true` se os valores de entrada não forem iguais `false` , caso contrário. |
| `['<', value, value]` | boolean | Retorna `true` se a primeira entrada é estritamente menor do que a segunda `false` , caso contrário. Os argumentos são necessários para serem tanto cadeias de caracteres quanto números. |
| `['<=', value, value]` | boolean | Retorna `true` se a primeira entrada for menor ou igual à segunda, `false` caso contrário. Os argumentos são necessários para serem tanto cadeias de caracteres quanto números. |
| `['==', value, value]` | boolean | Retorna `true` se os valores de entrada forem iguais `false` , caso contrário. Os argumentos são necessários para serem tanto cadeias de caracteres quanto números. |
| `['>', value, value]` | boolean | Retorna `true` se a primeira entrada é estritamente maior do que a segunda `false` , caso contrário. Os argumentos são necessários para serem tanto cadeias de caracteres quanto números. |
| `['>=' value, value]` | boolean | Retorna `true` se a primeira entrada for maior ou igual à segunda, `false` caso contrário. Os argumentos são necessários para serem tanto cadeias de caracteres quanto números. |
| `['all', boolean, boolean, …]` | boolean | Retorna `true` se todas as entradas forem `true`, `false` caso contrário,. |
| `['any', boolean, boolean, …]` | boolean | Retorna `true` se alguma das entradas for `true`, caso `false` contrário,. |

## <a name="conditional-expressions"></a>Expressões condicionais

As expressões condicionais fornecem operações lógicas que são como instruções IF-.

As expressões a seguir executam operações lógicas condicionais nos dados de entrada. Por exemplo, a `case` expressão fornece a lógica "if/then/else" enquanto `match` a expressão é como uma "switch-Statement". 

### <a name="case-expression"></a>Expressão Case

Uma `case` expressão é um tipo de expressão condicional que fornece If-Statement como lógica (if/then/else). Esse tipo de expressão percorre uma lista de condições booleanas e retorna o valor de saída da primeira condição booliana que é verdadeira.

O pseudocódigo a seguir define a estrutura da `case` expressão. 

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

O exemplo a seguir percorre diferentes condições booleanas até encontrar uma que seja avaliada como `true`e, em seguida, retorna esse valor associado. Se nenhuma condição booliana for avaliada como `true`, um valor de fallback será retornado. 

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

Uma `match` expressão é um tipo de expressão condicional que fornece a instrução switch, como lógica. A entrada pode ser qualquer expressão como, `['get', 'entityType']` que retorna uma cadeia de caracteres ou um número. Cada rótulo deve ser um único valor literal ou uma matriz de valores literais, cujos valores devem ser todas as cadeias de caracteres ou todos os números. A entrada corresponde se qualquer um dos valores na matriz corresponder. Cada rótulo deve ser exclusivo. Se o tipo de entrada não corresponder ao tipo dos rótulos, o resultado será o valor de fallback.

O pseudocódigo a seguir define a estrutura da `match` expressão. 

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

O exemplo a seguir examina a `entityType` propriedade de um recurso de ponto em uma camada de bolha que procura uma correspondência. Se encontrar uma correspondência, esse valor especificado será retornado ou retornará o valor de fallback.

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

O exemplo a seguir usa uma matriz para listar um conjunto de rótulos que devem retornar o mesmo valor. Isso é muito mais eficiente do que listar cada rótulo individualmente. Nesse caso, se a `entityType` propriedade for "restaurante" ou "grocery_store", a cor "vermelho" será retornada.

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

### <a name="coalesce-expression"></a>Expressão de adesão

Uma `coalesce` expressão percorre um conjunto de expressões até que o primeiro valor não nulo seja obtido e retorne esse valor. 

O pseudocódigo a seguir define a estrutura da `coalesce` expressão. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Exemplo**

O exemplo a seguir usa `coalesce` uma expressão para definir `textField` a opção de uma camada de símbolo. Se a `title` propriedade estiver ausente no recurso ou definida como `null`, a expressão tentará procurar a `subtitle` Propriedade, se ela estiver ausente ou `null`, em seguida, retornará a uma cadeia de caracteres vazia. 

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

## <a name="type-expressions"></a>Expressões de tipo

As expressões de tipo fornecem ferramentas para testar e converter diferentes tipos de dados, como cadeias de caracteres, números e valores Boolianos.

| Expressão | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | objeto \| de matriz | Retorna um valor de objeto ou matriz literal. Use esta expressão para impedir que uma matriz ou objeto seja avaliado como uma expressão. Isso é necessário quando uma matriz ou objeto precisa ser retornado por uma expressão. |
| `['to-boolean', value]` | boolean | Converte o valor de entrada em um booliano. O resultado é `false` quando a entrada é uma cadeia de caracteres `0`vazia `false`, `null`,, `NaN`ou; caso `true`contrário, seu. |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | cor | Converte o valor de entrada em uma cor. Se vários valores forem fornecidos, cada um será avaliado na ordem até que a primeira conversão bem-sucedida seja obtida. Se nenhuma das entradas puder ser convertida, a expressão será um erro. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | número | Converte o valor de entrada em um número, se possível. Se a entrada for `null` ou `false`, o resultado será 0. Se a entrada for `true`, o resultado será 1. Se a entrada for uma cadeia de caracteres, ela será convertida em um [](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) número usando a função de cadeia de caracteres ToNumber da especificação da linguagem ECMAScript. Se vários valores forem fornecidos, cada um será avaliado na ordem até que a primeira conversão bem-sucedida seja obtida. Se nenhuma das entradas puder ser convertida, a expressão será um erro. |
| `['to-string', value]` | cadeia | Converte o valor de entrada em uma cadeia de caracteres. Se a entrada for `null`, o resultado será `""`. Se a entrada for um booliano, o resultado `"true"` será `"false"`ou. Se a entrada for um número, ela será convertida em uma cadeia de caracteres usando a função de número [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) da especificação da linguagem ECMAScript. Se a entrada for uma cor, ela será convertida em cadeia de caracteres `"rgba(r,g,b,a)"`de cor de CSS RGBA. Caso contrário, a entrada é convertida em uma cadeia de caracteres usando a função [JSON. stringify](https://tc39.github.io/ecma262/#sec-json.stringify) da especificação da linguagem ECMAScript. |
| `['typeof', value]` | Cadeia de caracteres | Retorna uma cadeia de caracteres que descreve o tipo do valor especificado. |

> [!TIP]
> Se uma mensagem de erro semelhante `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` a aparecer no console do navegador, isso significa que há uma expressão em algum lugar em seu código que tem uma matriz que não tem uma cadeia de caracteres para seu primeiro valor. Se você quiser que a expressão retorne uma matriz, empacote a matriz com `literal` a expressão. O exemplo a seguir define a `offset` opção de ícone de uma camada de símbolo, que precisa ser uma matriz que contém dois números, `match` usando uma expressão para escolher entre dois valores de deslocamento com base `entityType` no valor da Propriedade do ponto recurso.
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
>             //If there is no title, try getting the subtitle. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>Expressões de cores

As expressões de cor facilitam a criação e a manipulação de valores de cores.

| Expressão | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | cor | Cria um valor de cor de componentes *vermelhos*, verdes e *azuis* que devem variar entre `0` e `255`e um componente alfa do. `1` Se algum componente estiver fora do intervalo, a expressão será um erro. |
| `['rgba', number, number, number, number]` | cor | Cria um valor de cor de componentes *vermelhos*, *verdes*e *azuis* que devem variar `0` entre `255`e e um componente alfa dentro de um intervalo `0` de `1`e. Se algum componente estiver fora do intervalo, a expressão será um erro. |
| `['to-rgba']` | \[number, number, number, number\] | Retorna uma matriz de quatro elementos que contém os componentes *vermelho*, *verde*, *azul*e *alfa* da cor de entrada, nessa ordem. |

**Exemplo**

O exemplo a seguir cria e o valor de cor RGB que tem um `255`valor vermelho de e valores *verdes* e *azuis* que são calculados `2.5` multiplicando pelo valor da `temperature` propriedade. À medida que a temperatura muda, a cor mudará para diferentes tons de *vermelho*.

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

## <a name="string-operator-expressions"></a>Expressões de operador de cadeia de caracteres

Expressões do operador String executam operações de conversão em cadeias de caracteres como concatenar e converter o caso. 

| Expressão | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `['concat', string, string, …]` | cadeia | Concatena várias cadeias de caracteres juntas. Cada valor deve ser uma cadeia de caracteres. Use a `to-string` expressão Type para converter outros tipos de valor em String, se necessário. |
| `['downcase', string]` | Cadeia de caracteres | Converte a cadeia de caracteres especificada em minúsculas. |
| `['upcase', string]` | Cadeia de caracteres | Converte a cadeia de caracteres especificada em maiúsculas. |

**Exemplo**

O exemplo a seguir converte `temperature` a propriedade do recurso Point em uma cadeia de caracteres e, em seguida, concatena "° f" ao final dela.

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

A expressão acima renderiza um PIN no mapa com o texto "64 ° f" sobreposto sobre ele, conforme mostrado na imagem abaixo.

<center>

![Exemplo](media/how-to-expressions/string-operator-expression.png) de expressão de operador de cadeia de caracteres</center>

## <a name="interpolate-and-step-expressions"></a>Expressões de interpolação e etapas

As expressões Interpolate e Step podem ser usadas para calcular valores ao longo de uma curva interpolada ou função Step. Essas expressões assumem uma expressão que retorna um valor numérico como sua entrada, por exemplo `['get',  'temperature']`. O valor de entrada é avaliado em pares de valores de entrada e saída, chamados "paradas", para determinar o valor que melhor se adapta à função de curva ou etapa interpolada. Os valores de entrada para cada parada devem ser um número e estar em ordem crescente. Os valores de saída devem ser um número e uma matriz de números ou uma cor.

### <a name="interpolate-expression"></a>Expressão Interpolate

Uma `interpolate` expressão pode ser usada para calcular um conjunto de valores suave e contínuo, interpolando entre os valores de parada. Uma `interpolate` expressão que retorna valores de cor produz um gradiente de cor no qual os valores de resultado são selecionados.

Há três tipos de métodos de interpolação que podem ser usados em `interpolate` uma expressão:
 
* `['linear']`– Interpola linearmente entre o par de paradas.
* `['exponential', base]`– Interpola exponencialmente entre as interrupções. O `base` valor controla a taxa na qual a saída aumenta. Valores mais altos fazem com que a saída aumente mais em direção ao alto fim do intervalo. Um `base` valor próximo de 1 produz uma saída que aumenta de forma linear.
* `['cubic-bezier', x1, y1, x2, y2]`-Interpolações usando uma [curva de Bézier cúbica](https://developer.mozilla.org/docs/Web/CSS/timing-function) definida pelos pontos de controle fornecidos.

Veja um exemplo de como esses diferentes tipos de interpolações se parecem. 

| Linear  | Exponencial | Bezier cubica |
|---------|-------------|--------------|
| ![Grafo de interpolação linear](media/how-to-expressions/linear-interpolation.png) | ![Grafo de interpolação exponencial](media/how-to-expressions/exponential-interpolation.png) | ![Grafo de interpolação Bézier cúbica](media/how-to-expressions/bezier-curve-interpolation.png) |

O pseudocódigo a seguir define a estrutura da `interpolate` expressão. 

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

O exemplo a seguir usa `linear interpolate` uma expressão para definir `color` a propriedade de uma camada de `temperature` bolha com base na Propriedade do recurso de ponto. Se o `temperature` valor for menor que 60, "Blue" será retornado, se entre 60 e menor que 70, amarelo será retornado, se entre 70 e menor que 80, "laranja" será retornado, se for 80 ou superior, "Red" será retornado.

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

![Exemplo](media/how-to-expressions/interpolate-expression-example.png) de expressão Interpolate</center>

### <a name="step-expression"></a>Expressão de etapa

Uma `step` expressão pode ser usada para calcular valores de resultado discretos, avaliando uma [função piecewise](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) definida por paradas. 

O pseudocódigo a seguir define a estrutura da `step` expressão. 

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

As expressões de etapa retornam o valor de saída da parada logo antes do valor de entrada ou o primeiro valor de entrada se a entrada for menor que a primeira parada. 

**Exemplo**

O exemplo a seguir usa `step` uma expressão para definir `color` a propriedade de uma camada de `temperature` bolha com base na Propriedade do recurso de ponto. Se o `temperature` valor for menor que 60, "Blue" será retornado, se entre 60 e menor que 70, "Yellow" será retornado, se entre 70 e menor que 80, "laranja" será retornado, se for 80 ou superior, "Red" será retornado.

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

![Exemplo de expressão de etapa](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Expressões específicas de camada

Expressões especiais que se aplicam somente a camadas específicas.

### <a name="heat-map-density-expression"></a>Expressão de densidade do mapa de calor

Uma expressão de densidade do mapa de calor recupera o valor de densidade do mapa de calor para cada pixel em uma camada `['heatmap-density']`do mapa de calor e é definida como. Esse valor é um número entre `0` e `1` e é usado em combinação com uma `interpolation` expressão `step` or para definir o gradiente de cor usado para colorir o mapa de calor. Essa expressão só pode ser usada na [opção de cor](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color) da camada do mapa de calor.

> [!TIP]
> A cor no índice 0 em uma expressão de interpolação ou a cor padrão de uma cor de etapa define a cor da área em que não há dados e pode ser usada para definir uma cor de plano de fundo. Muitos preferem definir esse valor como transparente ou um preto semitransparente. 

**Exemplo**

Este exemplo usa uma expressão de interpolação de capa para criar um gradiente de cor suave para renderizar o mapa de calor. 

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

Além de usar um gradiente suave para colorir um mapa de calor, as cores podem ser especificadas dentro de um conjunto de intervalos `step` usando uma expressão. O uso `step` de uma expressão para colorir o mapa de calor divide a densidade visualmente em intervalos que mais se assemelham a uma delimitação ou ao mapa de estilo de radar.  

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

Para obter mais informações, consulte a documentação [Adicionar uma camada do mapa de calor](map-add-heat-map-layer.md) .

### <a name="line-progress-expression"></a>Expressão de progresso da linha

Uma expressão de progresso de linha recupera o progresso ao longo de uma linha de gradiente em uma `['line-progress']`camada de linha e é definida como. Esse valor é um número entre 0 e 1 e é usado em combinação com uma `interpolation` expressão `step` ou. Essa expressão só pode ser usada com a [opção strokeGradient]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient) da camada de linha. 

> [!NOTE]
> A `strokeGradient` opção da camada de linha requer a `lineMetrics` opção da fonte de dados a ser definida como `true`.

**Exemplo**

O exemplo a seguir usa `['line-progress']` a expressão para aplicar um gradiente de cor ao traço de uma linha.

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

[Consulte o exemplo ao vivo](map-add-shape.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Expressão de formato de campo de texto

A expressão de formato de campo de texto pode ser `textField` usada com a opção da `textOptions` Propriedade camadas de símbolo para fornecer formatação de texto misto. Essa expressão permite que um conjunto de cadeias de caracteres de entrada e opções de formatação sejam especificados. As opções a seguir podem ser especificadas para cada cadeia de caracteres de entrada nesta expressão.

 * `'font-scale'`-Especifica o fator de dimensionamento para o tamanho da fonte. Se especificado, esse valor substituirá a `size` propriedade `textOptions` do para a cadeia de caracteres individual.
 * `'text-font'`-Especifica uma ou mais famílias de fontes que devem ser usadas para esta cadeia de caracteres. Se especificado, esse valor substituirá a `font` propriedade `textOptions` do para a cadeia de caracteres individual.

O pseudocódigo a seguir define a estrutura da expressão de formato de campo de texto. 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[] 
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[] 
    },
    …
]
```

**Exemplo**

O exemplo a seguir formata o campo de texto adicionando uma fonte em negrito e dimensionando o tamanho `title` da fonte da Propriedade do recurso. Este exemplo também adiciona a `subtitle` Propriedade do recurso em uma nova linha, com um tamanho de fonte reduzido.

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
            { 'font-scale': 0.75 }
        ]
    }
});
```

Essa camada renderizará o recurso de ponto, conforme mostrado na imagem abaixo:
 
<center>

![Imagem do recurso de ponto com campo](media/how-to-expressions/text-field-format-expression.png) de texto formatado</center>

### <a name="number-format-expression"></a>Expressão de formato de número

A `number-format` expressão só pode ser usada com a `textField` opção de uma camada de símbolo. Essa expressão converte o número fornecido em uma cadeia de caracteres formatada. Essa expressão encapsula a função [número.](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) tolocalstring do JavaScript e dá suporte ao seguinte conjunto de opções.

 * `locale`-Especifique essa opção para converter números em cadeias de caracteres de uma maneira que se alinhe com o idioma especificado. Passe uma [marca de idioma BCP 47](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) para essa opção.
 * `currency`-Para converter o número em uma cadeia de caracteres que representa uma moeda. Os valores possíveis são os [códigos de moeda ISO 4217](https://en.wikipedia.org/wiki/ISO_4217), como "USD" para o dólar americano, "EUR" para o euro ou "CNY" para o RMB do chinês.
 * `'min-fraction-digits'`-Especifica o número mínimo de casas decimais a serem incluídas na versão da cadeia de caracteres do número.
 * `'max-fraction-digits'`-Especifica o número máximo de casas decimais a serem incluídas na versão da cadeia de caracteres do número.

O pseudocódigo a seguir define a estrutura da expressão de formato de campo de texto. 

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

O exemplo a seguir usa `number-format` uma expressão para modificar como `revenue` a propriedade do `textField` recurso de ponto é renderizada na opção de uma camada de símbolo, de modo que ela pareça um valor de dólar americano.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { ‘currency’: 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

Essa camada renderizará o recurso de ponto, conforme mostrado na imagem abaixo:

<center>

![Exemplo](media/how-to-expressions/number-format-expression.png) de expressão de formato de número</center>

## <a name="zoom-expression"></a>Expressão de zoom

Uma `zoom` expressão é usada para recuperar o nível de zoom atual do mapa no momento da renderização e é definida `['zoom']`como. Essa expressão retorna um número entre o intervalo de nível de zoom mínimo e máximo do mapa. O uso dessa expressão permite que os estilos sejam modificados dinamicamente, pois o nível de zoom do mapa é alterado. A `zoom` expressão só pode ser usada com `interpolate` expressões `step` e.

**Exemplo**

Por padrão, o raios de pontos de dados renderizados na camada do mapa de calor têm um raio de pixel fixo para todos os níveis de zoom. Como o mapa é ampliado, os dados agregados em conjunto e a camada do mapa de calor é diferente. Uma `zoom` expressão pode ser usada para dimensionar o raio para cada nível de zoom, de modo que cada ponto de dados cubra a mesma área física do mapa. Isso fará com que a camada do mapa de calor pareça mais estática e consistente. Cada nível de zoom do mapa tem duas vezes mais pixels vertical e horizontalmente como o nível de zoom anterior. Dimensionar o raio de forma a dobrar com cada nível de zoom criará um mapa de calor que se parece consistente em todos os níveis de zoom. Isso pode ser feito usando a `zoom` expressão com uma `base 2 exponential interpolation` expressão, conforme mostrado abaixo. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        10, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 10,000 pixels.
        19, 10000
    ]
};
```

[Consulte o exemplo ao vivo](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>Expressões de associação de variáveis

As expressões de associação de variáveis armazenam os resultados de um cálculo em uma variável para que possam ser referenciadas em outro lugar em uma expressão várias vezes sem precisar recalculá-la. Essa é uma otimização útil para expressões que envolvem muitos cálculos

| Expressão | Tipo de retorno | Descrição |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;' Let ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome1: cadeia de caracteres,<br/>&nbsp;&nbsp;&nbsp;&nbsp;value1: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome2: cadeia de caracteres,<br/>&nbsp;&nbsp;&nbsp;&nbsp;value2: qualquer,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | Armazena um ou mais valores como variáveis para uso pela `var` expressão na expressão filho que retorna o resultado. |
| `['var', name: string]` | qualquer | Faz referência a uma variável que foi criada `let` usando a expressão. |

**Exemplo**

Este exemplo usa uma expressão que calcula a receita relativa à taxa de temperatura e, em seguida `case` , usa uma expressão para avaliar diferentes operações boolianas nesse valor. A `let` expressão é usada para armazenar a receita relativa à taxa de temperatura para que ela só precise ser calculada uma vez `var` e a expressão referencie essa variável com a frequência necessária, sem precisar recalculá-la.

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

## <a name="next-steps"></a>Passos Seguintes

Consulte os artigos a seguir para obter mais exemplos de código que implementam expressões:

> [!div class="nextstepaction"] 
> [Adicionar uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Adicionar uma camada de bolha](map-add-bubble-layer.md)

> [!div class="nextstepaction"] 
> [Adicionar formas](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Adicionar uma camada do mapa de calor](map-add-heat-map-layer.md)

Saiba mais sobre as opções de camada que dão suporte a expressões:

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 
