---
title: Sintaxe da expressão OData para filtros e por ordem cláusulas - Azure Search
description: Filtro e ordem por expressão sintaxe OData para consultas de pesquisa do Azure.
ms.date: 01/31/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 06478cb3366054bd20239bf80f026562efd26232
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58087402"
---
# <a name="odata-expression-syntax-for-filters-and-order-by-clauses-in-azure-search"></a>Sintaxe da expressão OData para filtros e cláusulas de ordem no Azure Search

O Azure Search suporta um subconjunto da sintaxe da expressão OData para **$filter** e **$orderby** expressões. Expressões de filtro são avaliadas durante a consulta de análise, a restringir a pesquisa a campos específicos ou a adicionar critérios de correspondência utilizados durante as análises de índice. Expressões order by são aplicadas como um passo de pós-processamento ao longo de um conjunto de resultados. Os filtros e expressões order by estão incluídas numa solicitação de consulta, a cumprir uma sintaxe de OData independente do [simples](query-simple-syntax.md) ou [completo](query-lucene-syntax.md) consultar a sintaxe usada num **pesquisa** parâmetro. Este artigo fornece a documentação de referência para as expressões de OData utilizadas em filtros e expressões de ordenação.

## <a name="filter-syntax"></a>Sintaxe de filtro

R **$filter** expressão pode executar autónomo como uma consulta totalmente expresso ou refinar uma consulta que contém parâmetros adicionais. Os exemplos seguintes mostram alguns cenários essenciais. No primeiro exemplo, o filtro é a substância da consulta.


```POST
POST /indexes/hotels/docs/search?api-version=2017-11-11
    {
      "filter": "(baseRate ge 60 and baseRate lt 300) or hotelName eq 'Fancy Stay'"
    }
```

Outro caso de uso comum é facetamento filtros combinados, em que o filtro reduz a área de superfície de consulta com base numa seleção de navegação de faceta controlada pelo usuário:

```POST
POST /indexes/hotels/docs/search?api-version=2017-11-11
    {
      "search": "test",
      "facets": [ "tags", "baseRate,values:80|150|220" ],
      "filter": "rating eq 3 and category eq 'Motel'"
    }
```

### <a name="filter-operators"></a>Operadores de filtro  

- Operadores lógicos (e, em alternativa, não).  

- Expressões de comparação (`eq, ne, gt, lt, ge, le`). As comparações de cadeias são sensíveis às maiúsculas e minúsculas.  

- Constantes suportadas [Entity Data Model](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) tipos (EDM) (consulte [tipos de dados suportados &#40;Azure Search&#41; ](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) para obter uma lista dos tipos suportados). Não são suportadas constantes de tipos de coleção.  

- Referências aos nomes de campos. Apenas `filterable` campos podem ser utilizados em expressões de filtro.  

- `any` sem parâmetros. Esta ação testa se um campo do tipo `Collection(Edm.String)` contém elementos.  

- `any` e `all` com suporte a expressões lambda limitado. 
    
  -   `any/all` são suportadas nos campos do tipo `Collection(Edm.String)`. 
    
  -   `any` só pode ser utilizado com expressões de igualdade simples ou com um `search.in` função. Expressões simples consistem numa comparação entre um único campo e um valor literal, por exemplo, `Title eq 'Magna Carta'`.
    
  -   `all` só pode ser utilizado com expressões de desigualdade simples ou com um `not search.in`.   

- Funções Geoespaciais `geo.distance` e `geo.intersects`. O `geo.distance` função retorna a distância em quilômetros entre dois pontos, sendo um um campo e outro que está a ser uma constante passados como parte do filtro. O `geo.intersects` função devolve VERDADEIRO se um determinado ponto estiver dentro de um polígono determinado, em que o ponto é um campo e o polígono é especificado como constante passada como parte do filtro.  

  O polígono é uma superfície bidimensional armazenada como uma seqüência de pontos de definir um delimitação de anel (veja o exemplo abaixo). O polígono tem de ser fechado, que significa que o primeiro e último ponto conjuntos têm de ser iguais. [Pontos de um polígono tem de ser por ordem para a esquerda](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

  `geo.distance` Devolve a distância em quilômetros no Azure Search. Isto é diferente de outros serviços que suportam operações de dados geoespaciais de OData, que normalmente é retornado distâncias em metros.  

  > [!NOTE]  
  >  Ao utilizar geo.distance num filtro, tem de comparar a distância devolvida pela função com uma utilização constante `lt`, `le`, `gt`, ou `ge`. Os operadores `eq` e `ne` não são suportadas ao comparar distâncias. Por exemplo, este é um uso correto de geo.distance: `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`.  

- O `search.in` função testa se um campo de determinada seqüência de caracteres é igual a uma determinada lista de valores. Também pode ser utilizado em qualquer ou todos os comparar um valor único de um campo de coleção de cadeia de caracteres com uma determinada lista de valores. Igualdade entre o campo e cada valor na lista é determinada de forma diferencia maiúsculas de minúsculas, da mesma forma como para o `eq` operador. Por conseguinte, como uma expressão `search.in(myfield, 'a, b, c')` é equivalente ao `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`, exceto pelo fato de `search.in` resulta num desempenho muito melhor. 

  O primeiro parâmetro para o `search.in` função é a referência de campo de cadeia de caracteres (ou uma variável de alcance ao longo de um campo de coleção de cadeia de caracteres no caso em que `search.in` é utilizado dentro de um `any` ou `all` expressão). O segundo parâmetro é uma cadeia de caracteres que contém a lista de valores, separados por espaços e/ou vírgulas. Se precisar de utilizar os separadores que não sejam espaços e vírgulas porque os valores incluem aqueles caracteres, pode especificar um terceiro parâmetro opcional para `search.in`. 

  Neste terceiro parâmetro é uma cadeia de caracteres em que cada caractere da cadeia de caracteres ou subconjunto da cadeia de caracteres é tratado como um separador ao analisar a lista de valores no segundo parâmetro.

  > [!NOTE]   
  > Alguns cenários requerem a comparar um campo em relação a um grande número de valores de constantes. Por exemplo, a implementação de remoção de segurança com filtros pode exigir comparando o campo de ID do documento numa lista de IDs para o qual o utilizador requerente é concedido acesso de leitura. Em cenários como isso é altamente recomendável usar o `search.in` função em vez de uma disjunção mais complicada de expressões de igualdade. Por exemplo, usar `search.in(Id, '123, 456, ...')` em vez de `Id eq 123 or Id eq 456 or ....`. 
  >
  > Se usar `search.in`, pode esperar quando o segundo parâmetro contém uma lista de centenas ou milhares de valores de tempo de resposta de frações de segundos. Tenha em atenção que não existe nenhum limite explícito sobre o número de itens, pode passar para `search.in`, apesar de ainda está limitado pelo tamanho máximo do pedido. No entanto, a latência aumentará à medida que aumenta o número de valores.

- O `search.ismatch` função avalia a consulta de pesquisa como parte de uma expressão de filtro. Os documentos que correspondam à consulta de pesquisa serão retornados no conjunto de resultados. As seguintes sobrecargas dessa função estão disponíveis:
  - `search.ismatch(search)`
  - `search.ismatch(search, searchFields)`
  - `search.ismatch(search, searchFields, queryType, searchMode)`

  Em que: 
  
  - `search`: a consulta de pesquisa (em qualquer um [simples](query-simple-syntax.md) ou [completo](query-lucene-syntax.md) sintaxe de consulta). 
  - `queryType`: "simples" ou "completa", está predefinida como "simples". Especifica qual linguagem de consulta foi utilizada no `search` parâmetro.
  - `searchFields`: lista separada por vírgulas de campos pesquisáveis para pesquisar, está predefinida para todos os campos pesquisáveis no índice.    
  - `searchMode`: "qualquer" ou "all", a predefinição é "qualquer". Indica se devem corresponder algum ou todos os termos de pesquisa para contar o documento como uma correspondência.

  Todos os parâmetros acima são equivalentes a correspondente [parâmetros de solicitação de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents).

- O `search.ismatchscoring` funcione, como o `search.ismatch` de função, que retorna verdadeiro para documentos que correspondem a consulta de pesquisa transmitida como um parâmetro. A diferença entre eles é que a relevância pontuação de documentos que correspondam a `search.ismatchscoring` consulta irá contribuir para a classificação de documento geral, embora, no caso de `search.ismatch`, a pontuação de documento não ser alterada. As seguintes sobrecargas dessa função estão disponíveis com parâmetros idênticos do `search.ismatch`:
  - `search.ismatchscoring(search)`
  - `search.ismatchscoring(search, searchFields)`
  - `search.ismatchscoring(search, searchFields, queryType, searchMode)`

  O `search.ismatch` e `search.ismatchscoring` as funções são totalmente ortogonal entre si e o restante de álgebra o filtro. Isso significa que ambas as funções podem ser utilizadas na mesma expressão de filtro. 

### <a name="geospatial-queries-and-polygons-spanning-the-180th-meridian"></a>Consultas Geoespaciais e polígonos que abrange dos meridianos 180th  
 Para consultas de geoespaciais muitas bibliotecas formular uma consulta que inclui dos meridianos 180th (perto dateline) é continua fora dos limites ou requer uma solução, por exemplo, dividindo o polígono em dois, um em ambos os lados dos meridianos.  

 No Azure Search, consultas geoespaciais que incluem a 180 graus de longitude funcionará como esperado se a forma de consulta é retangular e suas coordenadas alinham-se com um esquema de grelha ao longo de longitude e latitude (por exemplo, `geo.intersects(location, geography'POLYGON((179 65,179 66,-179 66,-179 65,179 65))'`). Caso contrário, para as formas não retangulares ou unaligned, considere a abordagem de polígono de divisão.  

<a name="bkmk_limits"></a>

## <a name="filter-size-limitations"></a>Limitações de tamanho de filtro 

 Existem limites para o tamanho e a complexidade de expressões de filtro que pode enviar para o Azure Search. Os limites baseiam-se mais ou menos o número de cláusulas na sua expressão de filtro. Uma boa regra prática é que se tiver centenas de cláusulas, está em risco de em execução para o limite. Recomendamos a criação do seu aplicativo de tal forma que ele não gera filtros de tamanho não vinculado.  


## <a name="filter-examples"></a>Exemplos de filtro  

 Localize todos os hotéis com uma taxa base com menos de US $100 que são classificados igual ou superior a 4:  

```
$filter=baseRate lt 100.0 and rating ge 4
```

 Localize hotéis todos os que não seja "Motéis Motel" que foi renovated desde 2010:  

```
$filter=hotelName ne 'Roach Motel' and lastRenovationDate ge 2010-01-01T00:00:00Z
```

 Localize todos os hotéis com uma taxa base menos de US $200 que tenham sido renovated desde 2012, com um literal de datetime com informações de fuso horário para hora padrão do Pacífico:  

```
$filter=baseRate lt 200 and lastRenovationDate ge 2012-01-01T00:00:00-08:00
```

 Localize hotéis todos os que têm de estacionamento incluída e não permitir fumar:  

```
$filter=parkingIncluded and not smokingAllowed
```

 \- OU-  

```
$filter=parkingIncluded eq true and smokingAllowed eq false
```

 Localize hotéis todos os que são luxo ou incluem estacionamento e tem uma classificação de 5:  

```
$filter=(category eq 'Luxury' or parkingIncluded eq true) and rating eq 5
```

 Localize todos os hotéis com a etiqueta "Wi-Fi" (em que cada hotel tem etiquetas armazenadas num campo de Collection(Edm.String)):  

```
$filter=tags/any(t: t eq 'wifi')
```

 Localize todos os hotéis sem a etiqueta "hotel":  

```
$filter=tags/all(t: t ne 'motel')
```

 Localize todos os hotéis com todas as marcas:  

```
$filter=tags/any()
```

Localize hotéis todos os que não tem etiquetas:  

```
$filter=not tags/any()
```


 Localize todos os hotéis dentro de 10 quilómetros de distância de um ponto de referência especificado (onde a localização é um campo do tipo geographypoint):  

```
$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10
```

 Encontre todos os hotéis dentro de uma determinado da janela viewport descrita como um polígono (onde a localização é um campo do tipo geographypoint). Tenha em atenção que o polígono é fechado (os conjuntos de primeiro e último ponto devem ser o mesmo) e [os pontos devem ser listados na ordem para a esquerda](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

```
$filter=geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')
```

 Localizar hotéis todos os que não têm nenhum valor no campo "Descrição", ou que o valor é explicitamente definido como null:  

```
$filter=description eq null
```

Localizar todos os hotéis com nome igual de qualquer motel motéis ' ou "Hotel orçamento"):  

```
$filter=search.in(name, 'Roach motel,Budget hotel', ',')
```

Localizar todos os hotéis com nome igual de qualquer motel motéis ' ou 'hotel orçamento"separados por" | "):  

```
$filter=search.in(name, 'Roach motel|Budget hotel', '|')
```

Localize todos os hotéis com a etiqueta "Wi-Fi" ou "agrupamento":  

```
$filter=tags/any(t: search.in(t, 'wifi, pool'))
```

Localize todos os hotéis sem o hotel"etiqueta' nem"cabine é":  

```
$filter=tags/all(t: not search.in(t, 'motel, cabin'))
```

Encontre os documentos com a palavra "frente marítima". Esta consulta de filtro é idêntica para um [solicitação de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents) com `search=waterfront`.

```
$filter=search.ismatchscoring('waterfront')
```

Encontre os documentos com hostel"palavra" e classificação superior ou igual a 4 ou documentos com a palavra "motel" e classificação igual a 5. Tenha em atenção de que este pedido não foi possível ser expressado sem o `search.ismatchscoring` função.

```
$filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5
```

Encontre documentos sem a palavra "luxo".

```
$filter=not search.ismatch('luxury') 
```

Encontre os documentos com a frase "Oceano view" ou uma classificação igual a 5. O `search.ismatchscoring` consulta será executada apenas em relação a campos `hotelName` e `description`.
Tenha em atenção que apenas a segunda cláusula da disjunção de correspondentes serão devolvidos documentos demasiado - hotéis com classificação igual a 5. Para torná-lo a limpar esses documentos não correspondam a qualquer das partes com a pontuação da expressão, vai ser devolvidos com score igual a zero.

```
$filter=search.ismatchscoring('"ocean view"', 'description,hotelName') or rating eq 5
```

Localize documentos onde o hotel"termos" e "aeroporto" estão dentro de 5 palavras uns dos outros na descrição do hotel e onde fumar não é permitida. Esta consulta utiliza a [completa de linguagem de consulta Lucene](query-lucene-syntax.md).

```
$filter=search.ismatch('"hotel airport"~5', 'description', 'full', 'any') and not smokingAllowed 
```

## <a name="order-by-syntax"></a>Sintaxe de ordem

O **$orderby** parâmetro aceita uma lista separada por vírgulas de até 32 expressões do formulário `sort-criteria [asc|desc]`. Os critérios de classificação podem ser o nome de um `sortable` campo ou uma chamada para ao `geo.distance` ou o `search.score` funções. Pode utilizar tanto `asc` ou `desc` especificar explicitamente a ordem de classificação. A ordem predefinida é ascendente.

Se vários documentos têm os mesmos critérios de ordenação e `search.score` a função não é utilizada (por exemplo, se ordenar por um numérico `rating` campo e de três documentos para todos os tem uma classificação de 4), empates irão ser corrompidas pela pontuação de documentos por ordem descendente. Quando as pontuações de documento são as mesmas (por exemplo, quando há qualquer consulta de pesquisa em texto completo especificada no pedido), em seguida, a ordenação relativa dos documentos empatados é indeterminada.
 
É possível especificar vários critérios de ordenação. A ordem das expressões determina a ordem de classificação final. Por exemplo, para classificar a ordem decrescente, pela pontuação, seguida de classificação, a sintaxe seria `$orderby=search.score() desc,rating desc`.

A sintaxe para `geo.distance` no **$orderby** é o mesmo que está a ser **$filter**. Ao usar `geo.distance` no **$orderby**, o campo aos quais se aplica tem de ser do tipo `Edm.GeographyPoint` e também tem de ser `sortable`.  

A sintaxe para `search.score` no **$orderby** é `search.score()`. A função `search.score` não assume quaisquer parâmetros.  
 

## <a name="order-by-examples"></a>Exemplos de ordem

Hotéis de ordenação ascendente por taxa base:

```
$orderby=baseRate asc
```

Ordenar hotéis em ordem decrescente, pela classificação, em seguida, ascendente por taxa base (Lembre-se de que ascendente é a predefinição):

```
$orderby=rating desc,baseRate
```

Ordenar hotéis em ordem decrescente, pela classificação, em seguida, ascendente por distância das determinado ordinates conjunta:

```
$orderby=rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

Ordenar hotéis em ordem decrescente pela search.score e classificação e, em seguida, em ordem por distância crescente das coordenadas de determinado, para que entre dois hotéis com classificações idênticas, aquele mais próxima é listada em primeiro lugar:

```
$orderby=search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```
<a name="bkmk_unsupported"></a>

## <a name="unsupported-odata-syntax"></a>Sintaxe de OData não suportada

-   Expressões aritméticas  

-   Funções (exceto a distância e intersetar-funções geoespaciais)  

-   `any/all` com as expressões lambda arbitrário  

## <a name="see-also"></a>Consulte também  

+ [Navegação por facetas no Azure Search](search-faceted-navigation.md) 
+ [Filtros no Azure Search](search-filters.md) 
+ [Procurar nos documentos &#40;API de REST do serviço Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Sintaxe de consulta Lucene](query-lucene-syntax.md)
+ [Sintaxe de consulta simples no Azure Search](query-simple-syntax.md)   
