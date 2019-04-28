---
title: Controlado por dados expressões de estilo no SDK de Web do Azure Maps | Documentos da Microsoft
description: Como utilizar expressões de estilo condicionada por dados no SDK de Web do Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 3b234ca37783fe557baf307f198de9636b06a382
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60904980"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Expressões de estilo condicionada por dados (Web SDK)

Expressões permitem-lhe aplicar lógica de negócio para opções de estilo que observe as propriedades definidas em cada forma numa origem de dados. Expressões também podem ser utilizadas para filtrar dados numa origem de dados ou camada. Expressões podem consistir em lógica condicional, como instruções if e também podem ser usadas para manipular dados cadeia de caracteres, os operadores lógicos e matemáticos. 

Estilos de orientados a dados podem reduzir a quantidade de código necessário para implementar a lógica de negócio em torno de estilo. Quando utilizado com camadas, as expressões são avaliadas no tempo de processamento num thread separado, que fornece um desempenho superior quando comparado ao avaliar a lógica de negócios no thread da interface do Usuário.

O vídeo seguinte apresenta uma visão geral do estilo condicionada por dados no SDK de Web do Azure Maps.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

As expressões são representadas como matrizes JSON. O primeiro elemento de uma expressão na matriz é uma cadeia de caracteres que especifica o nome do operador de expressão. Por exemplo, "+" ou "case". Os próximos elementos (se houver) são os argumentos para a expressão. Cada argumento é um valor de literal (uma cadeia de caracteres, número, booleano, ou `null`), ou outra matriz de expressão. O pseudocódigo seguinte define a estrutura básica de uma expressão. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

O SDK de Web de mapas do Azure suporta muitos tipos de expressões que podem ser usados por conta própria ou em combinação com outras expressões.

| Tipo de expressões | Descrição |
|---------------------|-------------|
| [Expressões booleanas](#boolean-expressions) | Expressões booleanas fornecem um conjunto de expressões de operadores booleanos para comparações booleanas a avaliar. |
| [Expressões de cor](#color-expressions) | Expressões de cor tornam mais fácil criar e manipular os valores de cor. |
| [Expressões condicionais](#conditional-expressions) | Expressões condicionais oferecem operações de lógica que são como instruções if. |
| [Expressões de dados](#data-expressions) | Fornece acesso aos dados de propriedade num recurso. |
| [Fazer a interpolação e expressões de passo](#interpolate-and-step-expressions) | Fazer a interpolação e expressões de passo podem ser utilizadas para calcular valores ao longo de uma função de curva ou passo interpolada. |
| [Expressões de específicas de camada](#layer-specific-expressions) | Expressões especiais que só são aplicáveis a uma única camada. |
| [Expressões de matemática](#math-expressions) | Fornece operadores de matemáticos para realizar cálculos condicionada por dados dentro da estrutura de expressão. |
| [Expressões de operador de seqüência de caracteres](#string-operator-expressions) | Expressões de operador de seqüência de caracteres realizar operações de conversão em cadeias de caracteres, como a concatenação e convertendo o caso. |
| [Expressões de tipo](#type-expressions) | As expressões de tipo fornecem ferramentas de teste e conversão de diferentes tipos de dados como cadeias de caracteres, números e valores booleanos. |
| [Expressões de enlace variável](#variable-binding-expressions) | Expressões de enlace variável permitem que os resultados de um cálculo ser armazenado numa variável e referenciada em outro lugar numa expressão várias vezes sem ter de recalcular o valor armazenado. |
| [Expressão de zoom](#zoom-expression) | Obtém o nível de zoom atual do mapa em tempo de composição. |

Todos os exemplos neste documento irão utilizar a seguinte funcionalidade para demonstrar as diferentes formas em que os diferentes tipos de expressões que podem ser utilizados. 

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

As expressões de dados fornecem acesso aos dados de propriedade num recurso. 

| Expressão | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `['at', number, array]` | objeto | Obtém um item de uma matriz. |
| `['geometry-type']` | string | Obtém o tipo de geometria da funcionalidade: Ponto, MultiPoint, LineString, MultiLineString, Polygon, MultiPolygon. |
| `['get', string]` | value | Obtém o valor da propriedade a partir das propriedades do recurso atual. Devolve um valor nulo se a propriedade pedida está em falta. |
| `['get', string, object]` | value | Obtém o valor da propriedade das propriedades do objeto fornecido. Devolve um valor nulo se a propriedade pedida está em falta. |
| `['has', string]` | boolean | Determina se as propriedades de um recurso tem a propriedade especificada. |
| `['has', string, object]` | boolean | Determina se as propriedades do objeto tem a propriedade especificada. |
| `['id']` | value | Obtém de ID a funcionalidade se ele tiver um. |
| `['length', string | array]` | número | Obtém o comprimento de uma cadeia ou matriz. |

**Exemplos**

Propriedades de um recurso podem ser acedidas diretamente numa expressão, utilizando um `get` expressão. O exemplo seguinte utiliza o valor de "zoneColor" do recurso para especificar a propriedade color de uma camada de bolhas. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

O exemplo acima, funcionará bem se tem de todas as funcionalidades de ponto a `zoneColor` propriedade, mas se eles não o fizer, a cor provavelmente voltará a "preto". Para modificar a cor de contingência, uma `case` expressão pode ser utilizada em combinação com o `has` expressão para verificar se a propriedade existe e se ele não retorna uma cor de contingência em vez disso.

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

Camadas de bolhas e symbol processará as coordenadas de todas as formas numa origem de dados por predefinição. Isso pode ser feito para realçar os vértices de um polígono ou linha. O `filter` opção da camada pode ser utilizada para limitar os tipos de geometria dos recursos renderiza utilizando um `['geometry-type']` expressão dentro de uma expressão booleana. O exemplo seguinte limita para que apenas uma camada de bolhas `Point` funcionalidades são processadas.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

O exemplo seguinte irá permitir que os dois `Point` e `MultiPoint` recursos a ser processado. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

Da mesma forma, a estrutura de polígonos processará nas camadas de linha. Para desativar este comportamento numa camada de linha, adicione um filtro que só permite `LineString` e `MultiLineString` funcionalidades.  

## <a name="math-expressions"></a>Expressões de matemática

As expressões de matemática fornecem operadores de matemáticos para realizar cálculos condicionada por dados dentro da estrutura de expressão.

| Expressão | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `['+', number, number, …]` | número | Calcula a soma dos números especificados. |
| `['-', number]` | número | Subtrai 0 pelo número especificado. |
| `['-', number, number]` | número | Subtrai os primeiros números, o segundo número. |
| `['*', number, number, …]` | número | Multiplica os números especificados em conjunto. |
| `['/', number, number]` | número | Divide o primeiro número, o segundo número. |
| `['%', number, number]` | número | Calcula o resto ao dividir o primeiro número, o segundo número. |
| `['^', number, number]` | número | Calcula o valor do primeiro valor elevado à potência do segundo número. |
| `['abs', number]` | número | Calcula o valor absoluto do número especificado. |
| `['acos', number]` | número | Calcula o arco de cosseno do número especificado. |
| `['asin', number]` | número | Calcula o arco de seno do número especificado. |
| `['atan', number]` | número | Calcula o arco de tangente do número especificado. |
| `['ceil', number]` | número | Arredonda o número até o número inteiro inteiro seguinte. |
| `['cos', number]` | número | Calcula a cos do número especificado. |
| `['e']` | número | Devolve a constante matemática `e`. |
| `['floor', number]` | número | Arredonda o número para o número de inteiro de inteiro anterior. |
| `['ln', number]` | número | Calcula o logaritmo natural do número especificado. |
| `['ln2']` | número | Devolve a constante matemática `ln(2)`. |
| `['log10', number]` | número | Calcula o logaritmo base-10 do número especificado. |
| `['log2', number]` | número | Calcula o logaritmo de base de dois do número especificado. |
| `['max', number, number, …]` | número | Calcula o número máximo no conjunto especificado de números. |
| `['min', number, number, …]` | número | Calcula o número mínimo do conjunto especificado de números. |
| `['pi']` | número | Devolve a constante matemática `PI`. |
| `['round', number]` | número | Arredonda o número para o número inteiro mais próximo. Na metade valores são arredondados a zero. Por exemplo, `['round', -1.5]` é avaliada como -2. |
| `['sin', number]` | número | Calcula o seno do número especificado. |
| `['sqrt', number]` | número | Calcula a raiz quadrada do número especificado. |
| `['tan', number]` | número | Calcula a tangente do número especificado. |
## <a name="boolean-expressions"></a>Expressões booleanas

Expressões booleanas fornecem um conjunto de expressões de operadores booleanos para comparações booleanas a avaliar.

Ao comparar valores, a comparação é estritamente digitada. Valores de diferentes tipos são sempre considerados desiguais. Casos em que os tipos são conhecidos para ser diferente em tempo de análise são considerados inválidos e produzirão um erro de análise. 

| Expressão | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `['! ', boolean]` | boolean | Negação lógica. Devolve `true` se a entrada for `false`, e `false` se a entrada for `true`. |
| `['!= ', value, value]` | boolean | Devolve `true` se os valores de entrada não forem iguais, `false` caso contrário. |
| `['<', value, value]` | boolean | Devolve `true` se a primeira entrada é estritamente menor que o segundo, `false` caso contrário. Os argumentos têm de ser as cadeias de caracteres ou os dois números. |
| `['<=', value, value]` | boolean | Devolve `true` se a primeira entrada é menor ou igual ao segundo, `false` caso contrário. Os argumentos têm de ser as cadeias de caracteres ou os dois números. |
| `['==', value, value]` | boolean | Devolve `true` se os valores de entrada forem iguais, `false` caso contrário. Os argumentos têm de ser as cadeias de caracteres ou os dois números. |
| `['>', value, value]` | boolean | Devolve `true` se a primeira entrada é estritamente maior do que o segundo, `false` caso contrário. Os argumentos têm de ser as cadeias de caracteres ou os dois números. |
| `['>=' value, value]` | boolean | Devolve `true` se a primeira entrada é maior que ou igual ao segundo, `false` caso contrário. Os argumentos têm de ser as cadeias de caracteres ou os dois números. |
| `['all', boolean, boolean, …]` | boolean | Devolve `true` se todas as entradas forem `true`, `false` caso contrário. |
| `['any', boolean, boolean, …]` | boolean | Devolve `true` se qualquer uma das entradas estiver `true`, `false` caso contrário. |

## <a name="conditional-expressions"></a>Expressões condicionais

Expressões condicionais oferecem operações de lógica que são como instruções if.

As seguintes expressões realizar operações de lógica condicional nos dados de entrada. Por exemplo, o `case` expressão fornece lógica de "if/then/else" enquanto o `match` expressão é como um "-Falha da instrução". 

### <a name="case-expression"></a>Expressão Case

A `case` expressão é um tipo de expressão condicional que fornece a instrução if como a lógica (if/then/else). Esse tipo de expressão passos através de uma lista de condições booleanas e devolve o valor de saída da primeira condição booleana, que é verdadeiro.

O pseudocódigo seguinte define a estrutura do `case` expressão. 

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

O exemplo a seguir os passos por meio de diferentes condições booleanas até encontrar um que é avaliada como `true`e, em seguida, devolve associados valor. Se nenhuma condição booleana for avaliada como `true`, será devolvido um valor de contingência. 

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

### <a name="match-expression"></a>Corresponde à expressão

A `match` expressão é um tipo de expressão condicional que fornece a instrução switch como a lógica. A entrada pode ser qualquer expressão como `['get', 'entityType']` que retorna uma cadeia de caracteres ou um número. Cada etiqueta tem de ser um único valor literal ou uma matriz de valores literais, cujos valores tem de ser todas as cadeias de caracteres ou todos os números. A entrada corresponde se qualquer um dos valores na correspondência de matriz. Cada etiqueta tem de ser exclusiva. Se o tipo de entrada não corresponde ao tipo das etiquetas, o resultado será o valor de contingência.

O pseudocódigo seguinte define a estrutura do `match` expressão. 

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

O exemplo a seguir aborda o `entityType` propriedade de um recurso de ponto numa camada de bolhas procura uma correspondência. Se ele encontrar uma correspondência, o que é devolvido o valor ou retorna o valor de contingência especificados.

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

O exemplo seguinte utiliza uma matriz para listar um conjunto de etiquetas que deverá devolver todas o mesmo valor. Isso é muito mais eficiente do que a lista cada etiqueta individualmente. Neste caso, se o `entityType` propriedade é "restaurante" ou "grocery_store", a cor "red" será retornada.

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

### <a name="coalesce-expression"></a>Coalesce expressão

A `coalesce` expressão passos através de um conjunto de expressões até que o primeiro valor não null é obtido e devolve esse valor. 

O pseudocódigo seguinte define a estrutura do `coalesce` expressão. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Exemplo**

O exemplo seguinte utiliza uma `coalesce` expressão para definir o `textField` opção de uma camada de símbolo. Se o `title` propriedade está em falta a funcionalidade ou definido como `null`, a expressão será, em seguida, tentar procurar o `subtitle` propriedade, se seu em falta ou `null`, ele fará o fallback para uma cadeia vazia. 

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

As expressões de tipo fornecem ferramentas de teste e conversão de diferentes tipos de dados como cadeias de caracteres, números e valores booleanos.

| Expressão | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | matriz \| objeto | Devolve um valor de matriz ou um objeto literal. Utilize esta expressão para impedir que uma matriz ou objeto que está a ser avaliada como uma expressão. Isso é necessário quando uma matriz ou objeto tem de ser devolvido por uma expressão. |
| `['to-boolean', value]` | boolean | Converte o valor de entrada para um valor booleano. O resultado é `false` quando a entrada é uma cadeia de caracteres vazia `0`, `false`, `null`, ou `NaN`; caso contrário, seu `true`. |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | Converte o valor de entrada para uma cor. Se forem fornecidos vários valores, cada um deles é avaliado por ordem até que o primeiro conversão bem-sucedida é obtido. Se nenhuma das entradas pode ser convertida, a expressão é um erro. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | número | Converte o valor de entrada para um número, se possível. Se a entrada for `null` ou `false`, o resultado é 0. Se a entrada for `true`, o resultado é 1. Se a entrada for uma cadeia de caracteres, ela será convertida num número, utilizando o [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) cadeia de função da especificação da linguagem ECMAScript. Se forem fornecidos vários valores, cada um deles é avaliado por ordem até que o primeiro conversão bem-sucedida é obtido. Se nenhuma das entradas pode ser convertida, a expressão é um erro. |
| `['to-string', value]` | string | Converte o valor de entrada para uma cadeia de caracteres. Se a entrada for `null`, o resultado é `""`. Se a entrada é um valor booleano, o resultado será `"true"` ou `"false"`. Se a entrada for um número, ela será convertida para uma cadeia, utilizando o [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) número função da especificação da linguagem ECMAScript. Se a entrada é uma cor, é convertido em cadeia de caracteres de cor de CSS RGBA `"rgba(r,g,b,a)"`. Caso contrário, a entrada é convertida para uma cadeia, utilizando o [stringify](https://tc39.github.io/ecma262/#sec-json.stringify) função da especificação da linguagem ECMAScript. |
| `['typeof', value]` | string | Devolve uma cadeia de caracteres que descreve o tipo do valor especificado. |

> [!TIP]
> Se uma mensagem de erro semelhante a `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` aparecer na consola do browser-significa que existe uma expressão em algum lugar do seu código que possui uma matriz que não tem uma cadeia de caracteres para o primeiro valor. Se pretender que a expressão para devolver uma matriz, encapsule a matriz com o `literal` expressão. O exemplo seguinte define o ícone `offset` opção de uma camada de símbolo, que tem de ser uma matriz que contém dois números, utilizando um `match` expressão para escolher entre dois valores de desvio com base no valor da `entityType` propriedade do ponto de funcionalidade.
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

## <a name="color-expressions"></a>Expressões de cor

Expressões de cor tornam mais fácil criar e manipular os valores de cor.

| Expressão | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | Cria um valor de cor da *vermelho*, *verde*, e *azul* componentes que tem no intervalo entre `0` e `255`e um componente alfa de `1`. Se qualquer componente está fora do intervalo, a expressão é um erro. |
| `['rgba', number, number, number, number]` | color | Cria um valor de cor da *vermelho*, *verde*, *azul* componentes que tem no intervalo entre `0` e `255`e um componente alfa dentro de um intervalo de `0` e `1`. Se qualquer componente está fora do intervalo, a expressão é um erro. |
| `['to-rgba']` | \[number, number, number, number\] | Devolve uma matriz de quatro elemento que contém a cor de entrada *vermelho*, *verde*, *azul*, e *alpha* componentes, nessa ordem. |

**Exemplo**

O exemplo seguinte cria e valor de cor RGB que tem um *vermelho* valor de `255`, e *verde* e *azul* valores que são calculadas ao multiplicar `2.5` pelo valor o `temperature` propriedade. Como as alterações de temperatura a cor será alterado para diferentes tons de *vermelho*.

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

## <a name="string-operator-expressions"></a>Expressões de operador de seqüência de caracteres

Expressões de operador de seqüência de caracteres realizar operações de conversão em cadeias de caracteres, como a concatenação e convertendo o caso. 

| Expressão | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `['concat', string, string, …]` | string | Concatena várias cadeias de caracteres em conjunto. Cada valor tem de ser uma cadeia de caracteres. Utilize o `to-string` escreva a expressão para converter outros tipos de valor de cadeia de caracteres, se for necessário. |
| `['downcase', string]` | string | Converte a cadeia especificada em minúsculas. |
| `['upcase', string]` | string | Converte a cadeia especificada em maiúsculas. |

**Exemplo**

O exemplo a seguir converte a `temperature` propriedade do ponto de funcionalidade numa cadeia de caracteres e, em seguida, concatena "° F" para o final do mesmo.

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

A expressão acima é processado um pin no mapa com o texto "64° F/38 °" sobreposta sobre-lo, conforme mostrado na imagem abaixo.

<center>

![Exemplo de expressão de operador de seqüência de caracteres](media/how-to-expressions/string-operator-expression.png) </center>

## <a name="interpolate-and-step-expressions"></a>Fazer a interpolação e expressões de passo

Fazer a interpolação e expressões de passo podem ser utilizadas para calcular valores ao longo de uma função de curva ou passo interpolada. Essas expressões tirar uma expressão que devolve um valor numérico como sua entrada, por exemplo `['get',  'temperature']`. O valor de entrada é comparado com pares de valores de entrada e saídos, chamados "Parar", para determinar o valor que melhor se adequa a função de curva ou passo interpolada. Os valores de entrada para cada stop tem de ser um número e estar em ordem crescente. Os valores de saída tem de ser um número e matriz de números ou uma cor.

### <a name="interpolate-expression"></a>Fazer a interpolação de expressão

Um `interpolate` expressão pode ser utilizada para calcular um conjunto de valores contínuo e suave, interpolando os valores de paragem. Um `interpolate` expressão que devolve valores de cor produz uma gradação de cor no resultado de qual valores são selecionados entre.

Existem três tipos de métodos de interpolação que podem ser usados num `interpolate` expressão:
 
* `['linear']` -Faz a interpolação linearmente entre o par de paradas.
* `['exponential', base]` -Faz a interpolação exponencialmente entre o pára. O `base` valor controla a taxa a que aumenta a saída. Valores mais altos tornam a saída mais aumentar para o ponto mais alto do intervalo. A `base` valor próximas de 1 produz uma saída que aumenta linearmente mais.
* `['cubic-bezier', x1, y1, x2, y2]` -Faz a interpolação com um [cúbica curva de Bezier](https://developer.mozilla.org/docs/Web/CSS/timing-function) definidos pelos pontos de determinado controle.

Eis um exemplo da aparência esses diferentes tipos de interpolações. 

| Linear  | Exponencial | Cubic Bezier |
|---------|-------------|--------------|
| ![Gráfico de interpolação linear](media/how-to-expressions/linear-interpolation.png) | ![Gráfico de interpolação exponencial](media/how-to-expressions/exponential-interpolation.png) | ![Gráfico de interpolação cúbica Bezier](media/how-to-expressions/bezier-curve-interpolation.png) |

O pseudocódigo seguinte define a estrutura do `interpolate` expressão. 

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

O exemplo seguinte utiliza uma `linear interpolate` expressão para definir o `color` propriedade de uma camada de bolhas com base no `temperature` propriedade do recurso de ponto. Se o `temperature` valor é menor que 60, "blue" vai ser devolvido se entre 60 e menos de 70, amarelo vai ser devolvido, se entre 70 e inferior a 80, "laranja" vai ser devolvido, se 80 ou superior, "red" vai ser devolvido.

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

A imagem seguinte demonstra como as cores são escolhidas para a expressão acima.
 
<center>

![Fazer a interpolação de exemplo de expressão](media/how-to-expressions/interpolate-expression-example.png) </center>

### <a name="step-expression"></a>Expressão de passo

Uma `step` expressão pode ser utilizada para calcular os valores de resultado discretas e gradual através da avaliação de uma [piecewise constante função](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) definido pelo paradas. 

O pseudocódigo seguinte define a estrutura do `step` expressão. 

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

Expressões de passo retornam o valor de saída de parar imediatamente antes do valor de entrada ou o primeiro valor de entrada, se a entrada é menor do que a primeira parada. 

**Exemplo**

O exemplo seguinte utiliza uma `step` expressão para definir o `color` propriedade de uma camada de bolhas com base no `temperature` propriedade do recurso de ponto. Se o `temperature` valor é menor que 60, "blue" vai ser devolvido se entre 60 e menos de 70, "amarelo" vai ser devolvido, se entre 70 e inferior a 80, "laranja" vai ser devolvido, se 80 ou superior, "red" vai ser devolvido.

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

A imagem seguinte demonstra como as cores são escolhidas para a expressão acima.
 
<center>

![Exemplo de expressão de passo](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Expressões de específicas de camada

Expressões especiais que só se aplicam a camadas específicas.

### <a name="heat-map-density-expression"></a>Expressão de densidade de mapa térmico

Uma expressão de densidade de mapa térmico recupera o valor de densidade de mapa térmico para cada pixel numa camada de mapa de calor e é definida como `['heatmap-density']`. Este valor é um número entre `0` e `1` e é utilizado em combinação com um `interpolation` ou `step` expressão para definir o gradiente de cores utilizado para colorize o mapa gráfico. Só pode ser utilizada nesta expressão no [opção da cor](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color) da camada de mapa térmico.

> [!TIP]
> A cor no índice 0 numa expressão de interpolação ou a cor predefinida de uma cor de passo, define a cor da área onde há dados não e pode ser usado para definir uma cor de fundo. Muitos preferem definir este valor como transparente ou um preto semitransparente. 

**Exemplo**

Este exemplo utiliza uma expressão de interpolação de linha de comando para criar uma gradação de cor uniforme para renderizar o mapa gráfico. 

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

Além de usar uma gradação suave para colorize um mapa gráfico, as cores podem ser especificadas dentro de um conjunto de intervalos de utilizando um `step` expressão. Usando um `step` expressão para colorir o mapa gráfico divide a densidade visualmente em intervalos que mais se assemelha a um mapa de estilo contour ou radar.  

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

Para obter mais informações, consulte a [adicionar uma camada de mapa térmico](map-add-heat-map-layer.md) documentação.

### <a name="line-progress-expression"></a>Expressão de progresso de linha

Obtém o progresso ao longo de uma linha de gradação numa camada de linha de uma expressão de progresso de linha e é definida como `['line-progress']`. Este valor é um número entre 0 e 1 e é utilizado em combinação com um `interpolation` ou `step` expressão. Esta expressão só pode ser utilizada com o [strokeGradient opção]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient) da camada de linha. 

> [!NOTE]
> O `strokeGradient` opção da camada de linha requer o `lineMetrics` opção da origem de dados para ser definido como `true`.

**Exemplo**

O exemplo seguinte utiliza o `['line-progress']` expressão para aplicar um gradiente de cores para o traço de uma linha.

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

[Veja o exemplo em direto](map-add-shape.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Expressão de formato de campo de texto

A expressão de formato de campo de texto pode ser utilizada com o `textField` opção das camadas símbolo `textOptions` propriedade para fornecer a formatação de texto misto. Esta expressão permite que um conjunto de cadeias de caracteres de entrada e opções de formatação que seja especificado. As seguintes opções podem ser especificadas para cada cadeia de entrada nesta expressão.

 * `'font-scale'` -Especifica o fator de dimensionamento para o tamanho da fonte. Se for especificado, este valor irá substituir a `size` propriedade o `textOptions` para a cadeia de caracteres individual.
 * `'text-font'` -Especifica uma ou mais famílias de fontes que devem ser usadas para essa cadeia de caracteres. Se for especificado, este valor irá substituir a `font` propriedade o `textOptions` para a cadeia de caracteres individual.

O pseudocódigo seguinte define a estrutura da expressão de formato do campo de texto. 

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

O exemplo seguinte formata o campo de texto ao adicionar uma fonte em negrito e aumentar verticalmente o tamanho da fonte a `title` propriedade do recurso. Este exemplo também adiciona o `subtitle` propriedade do recurso numa nova linha, com um dimensionado reduzir o tamanho da fonte.

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

Essa camada processará a funcionalidade de ponto a como mostrado na imagem abaixo:
 
<center>

![Imagem da funcionalidade de ponto com o campo de texto formatado](media/how-to-expressions/text-field-format-expression.png) </center>

### <a name="number-format-expression"></a>Expressão de formato numérico

O `number-format` expressão só pode ser utilizada com o `textField` opção de uma camada de símbolo. Esta expressão converte o número fornecido numa cadeia de caracteres formatada. Esta expressão encapsula num wrapper do JavaScript [Number.toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) de função e suporta o seguinte conjunto de opções.

 * `locale` -Especifique esta opção para converter números em cadeias de caracteres de uma forma que se alinha com o idioma especificado. Passar um [etiqueta de idioma do BCP 47](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) para esta opção.
 * `currency` -Para converter o número numa cadeia de caracteres que representa uma moeda. Os valores possíveis são os [códigos de moeda ISO 4217](https://en.wikipedia.org/wiki/ISO_4217), tais como "USD" para o dólar norte-americano, "EUR" para o euro ou "CNY" para o RMB chinês.
 * `'min-fraction-digits'` -Especifica o número mínimo de casas decimais a incluir na versão de cadeia de caracteres do número.
 * `'max-fraction-digits'` -Especifica o número máximo de casas decimais a incluir na versão de cadeia de caracteres do número.

O pseudocódigo seguinte define a estrutura da expressão de formato do campo de texto. 

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

O exemplo seguinte utiliza uma `number-format` expressão modificar como o `revenue` propriedade do recurso de ponto é processada no `textField` opção de um símbolo de camada, de modo que ele é exibido um valor de dólar norte-AMERICANO.

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

Essa camada processará a funcionalidade de ponto a como mostrado na imagem abaixo:

<center>

![Exemplo de expressão de formato numérico](media/how-to-expressions/number-format-expression.png) </center>

## <a name="zoom-expression"></a>Expressão de zoom

R `zoom` expressão é utilizada para obter o nível de zoom atual do mapa em tempo de composição e é definida como `['zoom']`. Esta expressão devolve um número entre o intervalo de nível de zoom mínimo e máximo do mapa. Usando esta expressão permite que os estilos de ser modificados dinamicamente, como o nível de zoom do mapa é alterado. O `zoom` expressão só pode ser utilizada com `interpolate` e `step` expressões.

**Exemplo**

Por predefinição, dos raios de pontos de dados compostos na camada do mapa térmico têm um raio de pixel fixo para todos os níveis de zoom. Como o map é ampliado as agregações de dados em conjunto e a camada do mapa térmico parece diferente. A `zoom` expressão pode ser utilizada para aumentar o radius para cada nível de zoom, de modo a que cada ponto de dados abrange a mesma área física do mapa. Isso fará com que a camada do mapa térmico parecer mais estáticas e consistente. Cada nível de zoom do mapa tem duas vezes mais pixels na vertical e horizontalmente como o nível de zoom anterior. Dimensionar o radius, de modo que ele duplica com cada nível de zoom, irá criar um mapa de calor que parece consistente em todos os níveis de zoom. Isso pode ser feito utilizando o `zoom` expressão com um `base 2 exponential interpolation` expressão, conforme mostrado abaixo. 

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

[Veja o exemplo em direto](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>Expressões de enlace variável

Expressões de enlace variável armazenam os resultados de um cálculo numa variável para que possam ser referenciados em outro lugar numa expressão várias vezes sem que seja necessário que recalcular a ele. Esta é uma otimização útil para expressões que envolvem muitos cálculos

| Expressão | Tipo de retorno | Descrição |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"permitir que",<br/>&nbsp;&nbsp;&nbsp;&nbsp;name1: string,<br/>&nbsp;&nbsp;&nbsp;&nbsp;value1: qualquer,<br/>&nbsp;&nbsp;&nbsp;&nbsp;name2: string,<br/>&nbsp;&nbsp;&nbsp;&nbsp;value2: qualquer,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | Armazena um ou mais valores como variáveis para utilização pelo `var` expressão na expressão filho que devolve o resultado. |
| `['var', name: string]` | qualquer | Referencia uma variável que foi criada com o `let` expressão. |

**Exemplo**

Este exemplo utiliza uma expressão que calcula a receita em relação ao rácio de temperatura e, em seguida, utiliza um `case` expressão a avaliar as operações booleanas diferentes nesse valor. O `let` expressão é utilizada para armazenar a receita em relação ao rácio de temperatura, para que ele só precisa ser calculado uma vez e o `var` expressão referencia esta variável quantas vezes precisar sem que seja necessário que recalcular a ele.

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

Veja os artigos seguintes para obter mais amostras de código que implementam expressões:

> [!div class="nextstepaction"] 
> [Adicionar uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Adicionar uma camada de bolhas](map-add-bubble-layer.md)

> [!div class="nextstepaction"] 
> [Adicionar formas](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Adicionar uma camada de mapa térmico](map-add-heat-map-layer.md)

Saiba mais sobre as opções de camada que oferecem suporte a expressões:

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
