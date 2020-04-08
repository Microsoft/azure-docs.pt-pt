---
title: Expressões de estilo orientados por dados no Azure Maps Web SDK [ Microsoft Azure Maps
description: Neste artigo, você aprenderá sobre como usar expressões de estilo baseadas em dados no Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: d6009a655adcc26ebef31588eff2332a05f3a001
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804729"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Expressões de estilo orientadas por dados (Web SDK)

Expressões permitem-lhe aplicar lógica de negócio a opções de estilo que observam as propriedades definidas em cada forma numa fonte de dados. As expressões podem filtrar dados numa fonte de dados ou numa camada. Expressões podem consistir em lógica condicional, como declarações se. E podem ser usados para manipular dados utilizando: operadores de cordas, operadores lógicos e operadores matemáticos.

Os estilos orientados por dados reduzem a quantidade de código necessário para implementar a lógica do negócio em torno do styling. Quando usados com camadas, as expressões são avaliadas no tempo de renderização num fio separado. Esta funcionalidade proporciona um desempenho acrescido em comparação com a avaliação da lógica do negócio no fio UI.

Este vídeo fornece uma visão geral do estilo baseado em dados no Azure Maps Web SDK.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

As expressões são representadas como matrizes JSON. O primeiro elemento de uma expressão na matriz é uma cadeia que especifica o nome do operador de expressão. Por exemplo, "+" ou "caso". Os elementos seguintes (se houver) são os argumentos para a expressão. Cada argumento é um valor literal (uma corda, `null`número, booleano, ou), ou outra matriz de expressão. O pseudocódigo seguinte define a estrutura básica de uma expressão. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

O Azure Maps Web SDK suporta muitos tipos de expressões. As expressões podem ser usadas por si próprias ou em combinação com outras expressões.

| Tipo de expressões | Descrição |
|---------------------|-------------|
| [Expressão agregada](#aggregate-expression) | Uma expressão que define um cálculo que é processado sobre um `clusterProperties` conjunto de `DataSource`dados e pode ser usado com a opção de a . |
| [Expressões booleanas](#boolean-expressions) | Expressões booleanas fornecem um conjunto de expressões de operadores booleanos para avaliar comparações booleanas. |
| [Expressões de cor](#color-expressions) | As expressões de cor facilitam a criação e manipulação dos valores de cor. |
| [Expressões condicionais](#conditional-expressions) | Expressões condicionais fornecem operações lógicas que são como declarações se. |
| [Expressões de dados](#data-expressions) | Fornece acesso aos dados da propriedade numa funcionalidade. |
| [Expressões Interpolate e Step](#interpolate-and-step-expressions) | As expressões interpoladas e de passopodem ser usadas para calcular valores ao longo de uma curva ou função de passo interpolada. |
| [Expressões específicas da camada](#layer-specific-expressions) | Expressões especiais que só são aplicáveis a uma única camada. |
| [Expressões matemáticas](#math-expressions) | Fornece aos operadores matemáticos a realização de cálculos baseados em dados no quadro de expressão. |
| [Expressões de operador de cordas](#string-operator-expressions) | As expressões do operador de cordas realizam operações de conversão em cordas como a concatenação e a conversão do caso. |
| [Expressão suóloga](#type-expressions) | As expressões de tipo fornecem ferramentas para testar e converter diferentes tipos de dados, como cordas, números e valores booleanos. |
| [Expressões de ligação variáveis](#variable-binding-expressions) | Expressões de ligação variáveis armazenam os resultados de um cálculo numa variável e referenciados em outros lugares numa expressão múltiplas vezes sem ter que recalcular o valor armazenado. |
| [Expressão de zoom](#zoom-expression) | Recupera o nível de zoom atual do mapa no momento de renderização. |

Todos os exemplos deste documento utilizam a seguinte funcionalidade para demonstrar diferentes formas de utilização dos diferentes tipos de expressões. 

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
        "zoneColor": "red"
    }
}
```

## <a name="data-expressions"></a>Expressões de dados

As expressões de dados fornecem acesso aos dados da propriedade numa funcionalidade. 

| Expressão | Tipo de devolução | Descrição |
|------------|-------------|-------------|
| `['at', number, array]` | objeto | Recupera um item de uma matriz. |
| `['geometry-type']` | string | Obtém o tipo de geometria da funcionalidade: Ponto, MultiPoint, LineString, MultiLineString, Polygon, MultiPolygon. |
| `['get', string]` | valor | Obtém o valor da propriedade a partir das propriedades da funcionalidade atual. Devoluções nulas se a propriedade solicitada estiver em falta. |
| `['get', string, object]` | valor | Obtém o valor da propriedade a partir das propriedades do objeto fornecido. Devoluções nulas se a propriedade solicitada estiver em falta. |
| `['has', string]` | boolean | Determina se as propriedades de uma funcionalidade têm a propriedade especificada. |
| `['has', string, object]` | boolean | Determina se as propriedades do objeto têm a propriedade especificada. |
| `['id']` | valor | Obtém a identificação da funcionalidade se tiver uma. |
| `['length', string | array]` | número | Tem o comprimento de uma corda ou de uma matriz. |
| `['in', boolean | string | number, array]` | boolean | Determina se um item existe numa matriz |
| `['in', substring, string]` | boolean | Determina se uma subcadeia existe numa corda |

**Exemplos**

As propriedades de uma funcionalidade podem ser acedidas diretamente numa expressão utilizando uma `get` expressão. Este exemplo utiliza o valor "zoneColor" da funcionalidade para especificar a propriedade da cor de uma camada de bolha. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

O exemplo acima funcionará bem, se `zoneColor` todas as características de ponto tiverem a propriedade. Se não o fizerem, a cor provavelmente voltará a "preto". Para modificar a cor de `case` recuo, use `has` uma expressão em combinação com a expressão para verificar se a propriedade existe. Se a propriedade não existir, devolva uma cor de recuo.

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

Camadas de bolhas e símbolos tornarão as coordenadas de todas as formas numa fonte de dados, por padrão. Este comportamento pode destacar os vértices de um polígono ou uma linha. A `filter` opção da camada pode ser usada para limitar o tipo de `['geometry-type']` geometria das características que presta, utilizando uma expressão dentro de uma expressão booleana. O exemplo seguinte limita uma camada `Point` de bolha para que apenas as características sejam renderizadas.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

O exemplo seguinte `Point` `MultiPoint` permite que ambas e características sejam renderizadas. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

Da mesma forma, o contorno dos Polígonos renderizará em camadas de linha. Para desativar este comportamento numa camada de `LineString` `MultiLineString` linha, adicione um filtro que só permite e possui.  

## <a name="math-expressions"></a>Expressões matemáticas

As expressões matemáticas fornecem aos operadores matemáticos a realização de cálculos baseados em dados no quadro de expressão.

| Expressão | Tipo de devolução | Descrição |
|------------|-------------|-------------|
| `['+', number, number, …]` | número | Calcula a soma dos números especificados. |
| `['-', number]` | número | Subtrai 0 pelo número especificado. |
| `['-', number, number]` | número | Subtrai os primeiros números pelo segundo número. |
| `['*', number, number, …]` | número | Multiplica os números especificados em conjunto. |
| `['/', number, number]` | número | Divide o primeiro número pelo segundo número. |
| `['%', number, number]` | número | Calcula o restante ao dividir o primeiro número pelo segundo número. |
| `['^', number, number]` | número | Calcula o valor do primeiro valor elevado ao poder do segundo número. |
| `['abs', number]` | número | Calcula o valor absoluto do número especificado. |
| `['acos', number]` | número | Calcula a arccosina do número especificado. |
| `['asin', number]` | número | Calcula o arco do número especificado. |
| `['atan', number]` | número | Calcula o arquitangente do número especificado. |
| `['ceil', number]` | número | Arredonda o número até ao próximo inteiro inteiro inteiro. |
| `['cos', number]` | número | Calcula os cos do número especificado. |
| `['e']` | número | Devolve a `e`constante matemática. |
| `['floor', number]` | número | Arredonda o número para o inteiro inteiro anterior. |
| `['ln', number]` | número | Calcula o logarithm natural do número especificado. |
| `['ln2']` | número | Devolve a `ln(2)`constante matemática. |
| `['log10', number]` | número | Calcula o logarithm base-dez do número especificado. |
| `['log2', number]` | número | Calcula o logarithm base-dois do número especificado. |
| `['max', number, number, …]` | número | Calcula o número máximo no conjunto de números especificado. |
| `['min', number, number, …]` | número | Calcula o número mínimo no conjunto de números especificado. |
| `['pi']` | número | Devolve a `PI`constante matemática. |
| `['round', number]` | número | Arredonda o número para o inteiro mais próximo. Os valores intermédios são arredondados de zero. Por exemplo, `['round', -1.5]` avalia para -2. |
| `['sin', number]` | número | Calcula o seno do número especificado. |
| `['sqrt', number]` | número | Calcula a raiz quadrada do número especificado. |
| `['tan', number]` | número | Calcula a tangente do número especificado. |

## <a name="aggregate-expression"></a>Expressão agregada

Uma expressão agregada define um cálculo que é processado sobre um `clusterProperties` conjunto `DataSource`de dados e pode ser usado com a opção de a . A saída destas expressões deve ser um número ou um booleano. 

Uma expressão agregada requer três valores: um valor do operador e um valor inicial, e uma expressão para recuperar um imóvel de cada recurso em dados para aplicar o funcionamento agregado. Esta expressão tem o seguinte formato:

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- operador: Uma função de expressão que é então aplicada contra todos os valores calculados pelo `mapExpression` ponto para cada ponto do cluster. Operadores apoiados: 
    - Para números: `+`, `*`, `max``min`
    - Para Booleans: `all``any`
- inicialValor: Um valor inicial no qual o primeiro valor calculado é agregado contra.
- mapExpression: Uma expressão que é aplicada contra cada ponto no conjunto de dados.

**Exemplos**

Se todas as funcionalidades `revenue` de um conjunto de dados tiverem uma propriedade, que é um número. Em seguida, a receita total de todos os pontos de um cluster, que são criados a partir do conjunto de dados, pode ser calculada. Este cálculo é feito utilizando a seguinte expressão agregada:`['+', 0, ['get', 'revenue']]`

## <a name="boolean-expressions"></a>Expressões booleanas

Expressões booleanas fornecem um conjunto de expressões de operadores booleanos para avaliar comparações booleanas.

Ao comparar valores, a comparação é estritamente dactilografada. Valores de diferentes tipos são sempre considerados desiguais. Os casos em que os tipos são conhecidos por serem diferentes no momento da análise são considerados inválidos e produzirão um erro de análise. 

| Expressão | Tipo de devolução | Descrição |
|------------|-------------|-------------|
| `['! ', boolean]` | boolean | Negação lógica. Devoluções `true` se a `false`entrada `false` for , `true`e se a entrada for . |
| `['!= ', value, value]` | boolean | Devoluções `true` se os valores `false` de entrada não forem iguais, caso contrário. |
| `['<', value, value]` | boolean | Devoluções `true` se a primeira entrada for `false` estritamente inferior à segunda, caso contrário. Os argumentos são necessários para serem cordas ou ambos os números. |
| `['<=', value, value]` | boolean | Devoluções `true` se a primeira entrada for inferior `false` ou igual à segunda, caso contrário. Os argumentos são necessários para serem cordas ou ambos os números. |
| `['==', value, value]` | boolean | Devoluções `true` se os valores `false` de entrada forem iguais, caso contrário. Os argumentos são necessários para serem cordas ou ambos os números. |
| `['>', value, value]` | boolean | Devoluções `true` se a primeira entrada for `false` estritamente maior do que a segunda, caso contrário. Os argumentos são necessários para serem cordas ou ambos os números. |
| `['>=' value, value]` | boolean | Devoluções `true` se a primeira entrada for maior `false` ou igual à segunda, caso contrário. Os argumentos são necessários para serem cordas ou ambos os números. |
| `['all', boolean, boolean, …]` | boolean | Devoluções `true` se todas as `true` `false` inputs forem, caso contrário. |
| `['any', boolean, boolean, …]` | boolean | Devoluções `true` se alguma das `true`inputs for, `false` caso contrário. |

## <a name="conditional-expressions"></a>Expressões condicionais

Expressões condicionais fornecem operações lógicas que são como declarações se.

As seguintes expressões realizam operações lógicas condicionais nos dados de entrada. Por exemplo, `case` a expressão fornece lógica "se/então/outra" enquanto a `match` expressão é como uma "switch-statement". 

### <a name="case-expression"></a>Expressão de caso

Uma `case` expressão é um tipo de expressão condicional que fornece lógica "se/então/eelse". Este tipo de expressão passa por uma lista de condições booleanas. Devolve o valor de saída da primeira condição booleana para avaliar a verdade.

O pseudocódigo seguinte define a `case` estrutura da expressão. 

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

O exemplo seguinte passa por diferentes condições booleanas até encontrar uma que `true`avalie, e depois devolve esse valor associado. Se nenhuma condição booleana `true`avaliar, um valor de recuo será devolvido. 

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

### <a name="match-expression"></a>Expressão do jogo

Uma `match` expressão é um tipo de expressão condicional que fornece a switch-statement como lógica. A entrada pode ser qualquer `['get', 'entityType']` expressão como a que devolve uma corda ou um número. Cada etiqueta deve ser um único valor literal ou uma série de valores literais, cujos valores devem ser todas cordas ou todos os números. A entrada corresponde se algum dos valores da matriz coincidir. Cada etiqueta deve ser única. Se o tipo de entrada não corresponder ao tipo das etiquetas, o resultado será o valor de recuo.

O pseudocódigo seguinte define a `match` estrutura da expressão. 

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

O exemplo seguinte `entityType` olha para a propriedade de uma característica point em uma camada de bolha procura uma correspondência. Se encontrar uma correspondência, esse valor especificado é devolvido ou devolve o valor de recuo.

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

O exemplo seguinte utiliza uma matriz para listar um conjunto de etiquetas que devem devolver todos o mesmo valor. Esta abordagem é muito mais eficiente do que a listagem individual de cada rótulo. Neste caso, se `entityType` a propriedade for "restaurante" ou "grocery_store", a cor "vermelho" será devolvida.

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

O exemplo seguinte utiliza uma expressão de correspondência para executar um filtro do tipo "in array" ou "array contains". Neste caso, a expressão filtra dados que têm um valor de ID que está numa lista de IDs permitidos. Ao utilizar expressões com filtros, o resultado tem de ser um valor booleano.

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

### <a name="coalesce-expression"></a>Expressão de coalesce

Uma `coalesce` expressão passa por um conjunto de expressões até que o primeiro valor não nulo seja obtido e devolve esse valor. 

O pseudocódigo seguinte define a `coalesce` estrutura da expressão. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Exemplo**

O exemplo seguinte `coalesce` usa uma `textField` expressão para definir a opção de uma camada de símbolo. Se `title` a propriedade estiver em falta `null`da funcionalidade ou definida `subtitle` para, a expressão `null`tentará então procurar a propriedade, se estiver em falta ou, então voltará a cair para uma corda vazia. 

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

O exemplo seguinte `coalesce` usa uma expressão para recuperar o primeiro ícone de imagem disponível no sprite do mapa a partir de uma lista de nomes de imagem especificados.

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

## <a name="type-expressions"></a>Expressão suóloga

As expressões de tipo fornecem ferramentas para testar e converter diferentes tipos de dados, como cordas, números e valores booleanos.

| Expressão | Tipo de devolução | Descrição |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | objeto \| matriz | Devolve uma matriz literal ou valor de objeto. Utilize esta expressão para evitar que uma matriz ou objeto seja avaliado como expressão. Isto é necessário quando uma matriz ou objeto precisa de ser devolvido por uma expressão. |
| `['image', string]` | string | Verifica se um ID de imagem especificado é carregado no sprite de imagem dos mapas. Se for, a identificação é devolvida, caso contrário, o nudismo é devolvido. |
| `['to-boolean', value]` | boolean | Converte o valor de entrada para um booleano. O resultado `false` é quando a entrada `0`é `false` `null`uma `NaN`corda vazia, , ou; caso contrário, o seu `true`. |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | Converte o valor de entrada para uma cor. Se forem fornecidos vários valores, cada um é avaliado em ordem até que a primeira conversão bem sucedida seja obtida. Se nenhuma das inputas puder ser convertida, a expressão é um erro. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | número | Converte o valor de entrada para um número, se possível. Se a entrada `null` `false`for ou , o resultado é 0. Se a entrada `true`for, o resultado é 1. Se a entrada for uma corda, é convertida para um número utilizando a função de cadeia [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) da Especificação de Idioma ECMAScript. Se forem fornecidos vários valores, cada um é avaliado em ordem até que a primeira conversão bem sucedida seja obtida. Se nenhuma das inputas puder ser convertida, a expressão é um erro. |
| `['to-string', value]` | string | Converte o valor de entrada numa corda. Se a entrada `null`for, `""`o resultado é . Se a entrada for booleana, `"true"` `"false"`o resultado é ou . Se a entrada for um número, é convertida numa cadeia utilizando a função de número [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) da Especificação de Idioma ECMAScript. Se a entrada for uma cor, é convertida em fio `"rgba(r,g,b,a)"`de cor CSS RGBA . Caso contrário, a entrada é convertida numa cadeia utilizando a função [JSON.stringify](https://tc39.github.io/ecma262/#sec-json.stringify) da Especificação de Idioma ECMAScript. |
| `['typeof', value]` | string | Devolve uma corda descrevendo o tipo do valor dado. |

> [!TIP]
> Se uma mensagem `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` de erro semelhante à aparece na consola do navegador, significa que existe uma expressão algures no seu código que tem uma matriz que não tem uma cadeia para o seu primeiro valor. Se quiser que a expressão devolva uma `literal` matriz, envolva a matriz com a expressão. O exemplo seguinte define `offset` a opção de ícone de uma camada de símbolo, que precisa ser uma matriz contendo dois números, usando uma `match` expressão para escolher entre dois valores de compensação com base no valor da `entityType` propriedade da característica do ponto.
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

As expressões de cor facilitam a criação e manipulação dos valores de cor.

| Expressão | Tipo de devolução | Descrição |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | Cria um valor de cor a partir de componentes `0` `255` `1` *vermelhos,* *verdes*e *azuis* que devem variar entre e, e um componente alfa de . Se algum componente estiver fora de alcance, a expressão é um erro. |
| `['rgba', number, number, number, number]` | color | Cria um valor de cor a partir de `0` componentes `255` *vermelhos,* *verdes,* `0` *azuis* que devem variar entre e, e um componente alfa dentro de um intervalo de e `1`. Se algum componente estiver fora de alcance, a expressão é um erro. |
| `['to-rgba']` | \[número, número, número, número\] | Devolve uma matriz de quatro elementos contendo os componentes *vermelhos,* *verdes,* *azuis*e *alfa* da cor de entrada, por esta ordem. |

**Exemplo**

O exemplo seguinte cria um valor *red* de cor `255`RGB que tem um valor `2.5` vermelho de , `temperature` e valores *verdes* e *azuis* que são calculados multiplicando-se pelo valor da propriedade. À medida que a temperatura muda, a cor mudará para diferentes tons de *vermelho.*

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

## <a name="string-operator-expressions"></a>Expressões de operador de cordas

As expressões do operador de cordas realizam operações de conversão em cordas como a concatenação e a conversão do caso. 

| Expressão | Tipo de devolução | Descrição |
|------------|-------------|-------------|
| `['concat', string, string, …]` | string | Concatenates várias cordas juntas. Cada valor deve ser uma corda. Utilize `to-string` a expressão do tipo para converter outros tipos de valor em cadeia, se necessário. |
| `['downcase', string]` | string | Converte a corda especificada para minúscula. |
| `['upcase', string]` | string | Converte a corda especificada em maiúsculas. |

**Exemplo**

O exemplo seguinte `temperature` converte a propriedade da característica do ponto numa corda e, em seguida, concatena "°F" até ao fim da sua.

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

A expressão acima torna um pino no mapa com o texto "64°F" sobreposto em cima, como mostrado na imagem abaixo.

<center>

![Exemplo de](media/how-to-expressions/string-operator-expression.png) expressão do operador de cordas</center>

## <a name="interpolate-and-step-expressions"></a>Expressões Interpolate e Step

As expressões interpoladas e de passopodem ser usadas para calcular valores ao longo de uma curva ou função de passo interpolada. Estas expressões assumem uma expressão que devolve um valor `['get',  'temperature']`numérico como sua entrada, por exemplo. O valor de entrada é avaliado com pares de valores de entrada e de saída, para determinar o valor que melhor se adequa à curva ou função de passo interpolada. Os valores de saída são chamados de "paragens". Os valores de entrada para cada paragem devem ser um número e estar em ordem ascendente. Os valores de saída devem ser um número, e uma variedade de números, ou uma cor.

### <a name="interpolate-expression"></a>Expressão interpolada

Uma `interpolate` expressão pode ser usada para calcular um conjunto contínuo e suave de valores, interpolando entre valores de paragem. Uma `interpolate` expressão que retorna os valores de cor produz um gradiente de cor no qual os valores de resultado são selecionados.

Existem três tipos de métodos de `interpolate` interpolação que podem ser utilizados numa expressão:
 
* `['linear']`- Interpola linearmente entre o par de paragens.
* `['exponential', base]`- Interpola exponencialmente entre as paragens. O `base` valor controla a taxa a que a produção aumenta. Valores mais elevados fazem com que a produção aumente mais para a extremidade alta da gama. Um `base` valor próximo de 1 produz uma saída que aumenta mais linearmente.
* `['cubic-bezier', x1, y1, x2, y2]`- Interpola utilizando uma [curva cúbica de Bezier](https://developer.mozilla.org/docs/Web/CSS/timing-function) definida pelos pontos de controlo dados.

Aqui está um exemplo de como são estes diferentes tipos de interpolações. 

| Linear  | Exponencial | Bezier cúbico |
|---------|-------------|--------------|
| ![Gráfico de interpolação linear](media/how-to-expressions/linear-interpolation.png) | ![Gráfico de interpolação exponencial](media/how-to-expressions/exponential-interpolation.png) | ![Gráfico de interpolação de Bezier cúbico](media/how-to-expressions/bezier-curve-interpolation.png) |

O pseudocódigo seguinte define a `interpolate` estrutura da expressão. 

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

O exemplo seguinte `linear interpolate` usa uma `color` expressão para definir a `temperature` propriedade de uma camada de bolha com base na propriedade da característica do ponto. Se `temperature` o valor for inferior a 60, "azul" será devolvido. Se estiver entre 60 e menos de 70, o amarelo será devolvido. Se estiver entre 70 e menos de 80, "laranja" será devolvido. Se for 80 ou maior, "vermelho" será devolvido.

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

A imagem que se segue demonstra como as cores são escolhidas para a expressão acima.
 
<center>

![Exemplo](media/how-to-expressions/interpolate-expression-example.png) de expressão interpolado</center>

### <a name="step-expression"></a>Expressão do passo

Uma `step` expressão pode ser usada para calcular valores de resultados discretos e pisados avaliando uma [função de constante peça](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) definida por paragens. 

O pseudocódigo seguinte define a `step` estrutura da expressão. 

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

As expressões de passo devolvem o valor de saída da paragem pouco antes do valor de entrada, ou o primeiro valor de entrada se a entrada for inferior à primeira paragem. 

**Exemplo**

O exemplo seguinte `step` usa uma `color` expressão para definir a `temperature` propriedade de uma camada de bolha com base na propriedade da característica do ponto. Se `temperature` o valor for inferior a 60, "azul" será devolvido. Se estiver entre 60 e menos de 70, "amarelo" será devolvido. Se estiver entre 70 e menos de 80, "laranja" será devolvido. Se for 80 ou maior, "vermelho" será devolvido.

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

A imagem que se segue demonstra como as cores são escolhidas para a expressão acima.
 
<center>

![Exemplo de expressão de passo](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Expressões específicas da camada

Expressões especiais que se aplicam apenas a camadas específicas.

### <a name="heat-map-density-expression"></a>Expressão de densidade do mapa de calor

Uma expressão de densidade de mapa de calor recupera o valor de densidade `['heatmap-density']`do mapa de calor para cada pixel numa camada de mapa de calor e é definido como . Este valor é `0` um `1`número entre e . É usado em combinação `interpolation` `step` com uma ou expressão para definir o gradiente de cor usado para colorir o mapa de calor. Esta expressão só pode ser usada na [opção](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color) de cor da camada de mapa de calor.

> [!TIP]
> A cor no índice 0, numa expressão de interpolação ou na cor padrão de uma cor passo, define a cor da área onde não há dados. A cor no índice 0 pode ser usada para definir uma cor de fundo. Muitos preferem definir este valor para um preto transparente ou semi-transparente.

**Exemplo**

Este exemplo usa uma expressão de interpolação liner para criar um gradiente de cor suave para renderizar o mapa de calor. 

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

Além de usar um gradiente liso para colorir um mapa de calor, as `step` cores podem ser especificadas dentro de um conjunto de gamas usando uma expressão. Usando `step` uma expressão para colorir o mapa de calor, quebra visualmente a densidade em intervalos que se assemelham a um contorno ou mapa de estilo de radar.  

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

Para mais informações, consulte a documentação da camada de um mapa de [calor.](map-add-heat-map-layer.md)

### <a name="line-progress-expression"></a>Expressão de progresso de linha

Uma expressão de progresso de linha recupera o progresso ao longo `['line-progress']`de uma linha de gradiente em uma camada de linha e é definida como . Este valor é um número entre 0 e 1. É usado em combinação `interpolation` `step` com uma ou expressão. Esta expressão só pode ser usada com a [opção de acidente vascular cerebral gradiente]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient) da camada de linha. 

> [!NOTE]
> A `strokeGradient` opção da camada `lineMetrics` de linha requer que a `true`opção da fonte de dados seja definida para .

**Exemplo**

Este exemplo `['line-progress']` usa a expressão para aplicar um gradiente de cor ao curso de uma linha.

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

### <a name="text-field-format-expression"></a>Expressão do formato do campo de texto

A expressão do formato do `textField` campo de `textOptions` texto pode ser usada com a opção da propriedade de camadas de símbolo para fornecer formatação de texto misto. Esta expressão permite especificar um conjunto de cordas de entrada e opções de formatação. As seguintes opções podem ser especificadas para cada cadeia de entrada nesta expressão.

 * `'font-scale'`- Especifica o fator de escala para o tamanho da fonte. Se especificado, este valor `size` irá sobrepor-se à propriedade da `textOptions` corda individual.
 * `'text-font'`- Especifica uma ou mais famílias de fontes que devem ser utilizadas para esta cadeia. Se especificado, este valor `font` irá sobrepor-se à propriedade da `textOptions` corda individual.
 * `'text-color'`- Especifica uma cor para aplicar a um texto durante a renderização. 

O pseudocódigo seguinte define a estrutura da expressão do formato do campo de texto. 

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

O exemplo seguinte formata o campo de texto adicionando uma fonte `title` arrojada e aumentando o tamanho da fonte da propriedade da funcionalidade. Este exemplo também `subtitle` adiciona a propriedade da funcionalidade numa novalinha, com um tamanho de fonte escalado para baixo e vermelho colorido.

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

Esta camada irá tornar a característica de ponto como mostrado na imagem abaixo:
 
<center>

![Imagem da característica point com](media/how-to-expressions/text-field-format-expression.png) campo de texto formatado</center>

### <a name="number-format-expression"></a>Expressão do formato de número

A `number-format` expressão só pode `textField` ser usada com a opção de uma camada de símbolo. Esta expressão converte o número fornecido numa corda formatada. Esta expressão envolve a função [Número.para LocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) do JavaScript e suporta o seguinte conjunto de opções.

 * `locale`- Especifique esta opção de conversão de números em cordas de forma a alinhar-se com a linguagem especificada. Passe uma [etiqueta de língua bcp 47](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) nesta opção.
 * `currency`- Converter o número numa corda representando uma moeda. Os valores possíveis são os [códigos cambiais ISO 4217](https://en.wikipedia.org/wiki/ISO_4217), como "USD" para o dólar norte-americano, "EUR" para o euro, ou "CNY" para o RMB chinês.
 * `'min-fraction-digits'`- Especifica o número mínimo de locais decimais a incluir na versão de cadeia do número.
 * `'max-fraction-digits'`- Especifica o número máximo de locais decimais a incluir na versão de cadeia do número.

O pseudocódigo seguinte define a estrutura da expressão do formato do campo de texto. 

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

O exemplo seguinte `number-format` usa uma `revenue` expressão para modificar a forma `textField` como a propriedade da característica de ponto é renderizada na opção de uma camada de símbolo de tal forma que parece um valor em dólares dos EUA.

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

Esta camada irá tornar a característica de ponto como mostrado na imagem abaixo:

<center>

![Exemplo de](media/how-to-expressions/number-format-expression.png) expressão de formato de número</center>

### <a name="image-expression"></a>Expressão de imagem

Uma expressão de imagem `image` pode `textField` ser usada com as `fillPattern` e opções de uma camada de símbolo, e a opção da camada de polígono. Esta expressão verifica que a imagem solicitada existe no estilo e `null`devolverá quer o nome de imagem resolvido quer, dependendo se a imagem está ou não no estilo. Este processo de validação é sincronizado e requer que a imagem tenha sido adicionada ao estilo antes de a solicitar no argumento da imagem.

**Exemplo**

O exemplo seguinte `image` usa uma expressão para adicionar um ícone inline com texto numa camada de símbolo. 

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

Esta camada irá renderizar o campo de texto na camada de símbolo, como mostrado na imagem abaixo:

<center>

![Exemplo de](media/how-to-expressions/image-expression.png) expressão de imagem</center>

## <a name="zoom-expression"></a>Expressão de zoom

Uma `zoom` expressão é usada para recuperar o nível de zoom `['zoom']`atual do mapa no momento de renderização e é definida como . Esta expressão devolve um número entre a gama mínima e máxima de zoom do mapa. Os controlos de mapas interativos do Azure Maps para web e Android suportam 25 níveis de zoom, numerados de 0 a 24. A `zoom` utilização da expressão permite que os estilos sejam modificados dinamicamente à medida que o nível de zoom do mapa é alterado. A `zoom` expressão só pode `interpolate` `step` ser usada com e expressões.

**Exemplo**

Por padrão, o rádio dos pontos de dados renderizados na camada do mapa de calor tem um raio de pixel fixo para todos os níveis de zoom. À medida que o mapa é ampliado, os dados agregam-se e a camada do mapa de calor parece diferente. Uma `zoom` expressão pode ser usada para escalar o raio para cada nível de zoom de modo que cada ponto de dados cubra a mesma área física do mapa. Fará com que a camada do mapa de calor pareça mais estática e consistente. Cada nível de zoom do mapa tem o dobro de pixels vertical e horizontalmente do que o nível de zoom anterior. Dimensionar o raio, de tal forma que duplica a cada nível de zoom, criará um mapa de calor que parece consistente em todos os níveis de zoom. Pode ser realizado utilizando `zoom` a `base 2 exponential interpolation` expressão com expressão, com o raio de pixel definido para o nível mínimo de zoom e um raio escalado para o nível máximo de zoom calculado como `2 * Math.pow(2, minZoom - maxZoom)` mostrado abaixo.

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

## <a name="variable-binding-expressions"></a>Expressões de ligação variáveis

Expressões de ligação variáveis armazenam os resultados de um cálculo numa variável. Assim, que os resultados do cálculo podem ser referenciados em outro lugar numa expressão várias vezes. É uma otimização útil para expressões que envolvem muitos cálculos.

| Expressão | Tipo de devolução | Descrição |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'vamos',<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome1: corda,<br/>&nbsp;&nbsp;&nbsp;&nbsp;valor1: qualquer,<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome2: corda,<br/>&nbsp;&nbsp;&nbsp;&nbsp;valor2: qualquer,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;criançaExpressão<br/>\] | | Armazena um ou mais valores `var` como variáveis para uso pela expressão da criança que devolve o resultado. |
| `['var', name: string]` | qualquer | Referencia uma variável que `let` foi criada usando a expressão. |

**Exemplo**

Este exemplo usa uma expressão que calcula a relação `case` de receitas em relação à temperatura e, em seguida, usa uma expressão para avaliar diferentes operações booleanas sobre este valor. A `let` expressão é usada para armazenar a receita em relação à relação com a temperatura, de modo que só precisa de ser calculada uma vez. A `var` expressão refere esta variável quantas vezes for necessário sem ter que recalculá-la.

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
> [Opções de bolhas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [Opções de HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [Opções de Camadas de Linha](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [Opções polygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [Opções de Matador de Símbolos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 
