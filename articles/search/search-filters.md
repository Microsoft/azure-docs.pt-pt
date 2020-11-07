---
title: Filtrar os resultados da pesquisa
titleSuffix: Azure Cognitive Search
description: Filtrar pela identidade de segurança do utilizador, linguagem, geolocalização ou valores numéricos para reduzir os resultados de pesquisa em consultas em Azure Cognitive Search, um serviço de pesquisa em nuvem hospedado no Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 6d83e5c39f97db49e2cc9b77cc806cff0a1fa6de
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94355989"
---
# <a name="filters-in-azure-cognitive-search"></a>Filtros em Pesquisa Cognitiva Azure 

Um *filtro* fornece critérios para selecionar documentos utilizados numa consulta de Pesquisa Cognitiva Azure. A pesquisa não filtrada inclui todos os documentos no índice. Um filtro coloca uma consulta de pesquisa num subconjunto de documentos. Por exemplo, um filtro poderia restringir a pesquisa completa de texto apenas a esses produtos com uma marca ou cor específica, a pontos de preço acima de um determinado limiar.

Algumas experiências de pesquisa impõem requisitos de filtro como parte da implementação, mas você pode usar filtros sempre que quiser limitar a pesquisa usando critérios *baseados em valor* (pesquisar para o tipo de produto "livros" para categoria "não-ficção" publicado por "Simon & Schuster").

Se, em vez disso, o seu objetivo for direcionado pesquisar estruturas de *dados específicas* (pesquisar para um campo de avaliações de clientes), existem métodos alternativos, descritos abaixo.

## <a name="when-to-use-a-filter"></a>Quando usar um filtro

Os filtros são fundamentais para várias experiências de pesquisa, incluindo "encontrar perto de mim", navegação com cara e filtros de segurança que mostram apenas esses documentos que um utilizador pode ver. Se implementar alguma destas experiências, é necessário um filtro. É o filtro anexado à consulta de pesquisa que fornece as coordenadas de geolocalização, a categoria faceta selecionada pelo utilizador ou o ID de segurança do solicitador.

Os cenários de exemplo incluem:

1. Utilize um filtro para cortar o seu índice com base nos valores de dados do índice. Dado um esquema com cidade, tipo de habitação e comodidades, você pode criar um filtro para selecionar explicitamente documentos que satisfaçam seus critérios (em Seattle, condomínios, frente ao mar). 

   A pesquisa completa de texto com as mesmas entradas produz frequentemente resultados semelhantes, mas um filtro é mais preciso na medida em que requer uma correspondência exata do termo filtro com o conteúdo do seu índice. 

2. Utilize um filtro se a experiência de pesquisa vier com um requisito de filtro:

   * [A navegação com faceta](search-faceted-navigation.md) utiliza um filtro para passar para trás a categoria faceta selecionada pelo utilizador.
   * A geo-pesquisa usa um filtro para passar coordenadas da localização atual em aplicações "find near me". 
   * Os filtros de segurança passam os identificadores de segurança como critérios de filtragem, onde uma correspondência no índice serve como procuração para direitos de acesso ao documento.

3. Utilize um filtro se quiser critérios de pesquisa num campo numérico. 

   Os campos numéricos são recuperáveis no documento e podem aparecer nos resultados da pesquisa, mas não são pesmáveis (sujeitos a pesquisa completa de texto) individualmente. Se precisar de critérios de seleção com base em dados numéricos, utilize um filtro.

### <a name="alternative-methods-for-reducing-scope"></a>Métodos alternativos para reduzir o âmbito

Se quiser um efeito de redução nos resultados da sua pesquisa, os filtros não são a sua única escolha. Estas alternativas poderiam ser mais adequadas, dependendo do seu objetivo:

 + `searchFields` consulta de parâmetros pegs pesquisa para campos específicos. Por exemplo, se o seu índice fornecer campos separados para descrições em inglês e espanhol, pode utilizar searchFields para direcionar quais os campos a utilizar para pesquisa de texto completo. 

+ `$select` o parâmetro é utilizado para especificar quais os campos a incluir num conjunto de resultados, aparando eficazmente a resposta antes de enviá-la para a aplicação de chamada. Este parâmetro não refina a consulta ou reduz a recolha do documento, mas se uma resposta menor é o seu objetivo, este parâmetro é uma opção a considerar. 

Para obter mais informações sobre qualquer um dos [parâmetros, consulte documentos de pesquisa > Solicite > parâmetros de consulta](/rest/api/searchservice/search-documents#query-parameters).


## <a name="how-filters-are-executed"></a>Como os filtros são executados

No tempo de consulta, um analisador de filtros aceita critérios como entrada, converte a expressão em expressões atómicas booleanas representadas como uma árvore, e depois avalia a árvore filtrante sobre campos filtrados em um índice.

A filtragem ocorre em conjunto com a pesquisa, qualificando quais os documentos a incluir no processamento a jusante para recuperação de documentos e pontuação de relevância. Quando emparelhado com uma cadeia de pesquisa, o filtro reduz eficazmente o conjunto de recolha da operação de pesquisa subsequente. Quando utilizado sozinho (por exemplo, quando a cadeia de consulta está vazia `search=*` onde), os critérios do filtro são a única entrada. 

## <a name="defining-filters"></a>Filtragem de definição
Os filtros são expressões OData, articuladas utilizando um [subconjunto de sintaxe OData V4 suportado na Pesquisa Cognitiva Azure](/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

Pode especificar um filtro para cada operação **de pesquisa,** mas o filtro em si pode incluir vários campos, vários critérios, e se utilizar uma função **ismatch,** várias expressões de pesquisa de texto completo. Numa expressão de filtro multi-partes, pode especificar predicados em qualquer ordem (sujeito às regras de precedência do operador). Não há ganhos apreciáveis no desempenho se tentar reorganizar predicados numa determinada sequência.

Um dos limites de uma expressão de filtro é o limite máximo de tamanho do pedido. Todo o pedido, incluindo o filtro, pode ser um máximo de 16 MB para POST, ou 8 KB para GET. Existe também um limite para o número de cláusulas na sua expressão de filtro. Uma boa regra é que se tiver centenas de cláusulas, corre o risco de chegar ao limite. Recomendamos que desenhe a sua aplicação de forma a não gerar filtros de tamanho ilimitado.

Os exemplos a seguir representam definições de filtros prototípicos em várias APIs.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?api-version=2020-06-30&search=*&$filter=Rooms/any(room: room/BaseRate lt 150.0)&$select=HotelId, HotelName, Rooms/Description, Rooms/BaseRate

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate lt 150.0)",
    "select": "HotelId, HotelName, Rooms/Description, Rooms/BaseRate"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(room: room/BaseRate lt 150.0)",
            Select = new[] { "HotelId", "HotelName", "Rooms/Description" ,"Rooms/BaseRate"}
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>Padrões de utilização do filtro

Os exemplos a seguir ilustram vários padrões de utilização para cenários de filtro. Para mais ideias, consulte [a sintaxe de expressão OData > Exemplos](./search-query-odata-filter.md#examples).

+ Autónomo **$filter** , sem uma cadeia de consulta, útil quando a expressão do filtro é capaz de qualificar totalmente documentos de interesse. Sem uma cadeia de consultas, não há análise lexical ou linguística, nem pontuação, nem classificação. Note que a cadeia de pesquisa é apenas um asterisco, o que significa "combinar todos os documentos".

   ```
   search=*&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Honolulu'
   ```

+ Combinação de cadeia de consulta e **$filter** , onde o filtro cria o subconjunto, e a cadeia de consulta fornece o termo entradas para pesquisa completa de texto sobre o subconjunto filtrado. A adição de termos (teatros de distância a pé) introduz pontuações de pesquisa nos resultados, onde os documentos que melhor correspondem aos termos são classificados mais alto. Usar um filtro com uma corda de consulta é o padrão de utilização mais comum.

   ```
  search=walking distance theaters&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Seattle'&$count=true
   ```

+ Consultas compostas, separadas por "ou", cada uma com os seus próprios critérios de filtro (por exemplo, 'beagles' em 'cão' ou 'siamese' em 'gato'). As expressões combinadas `or` são avaliadas individualmente, com a união de documentos correspondente a cada expressão enviada de volta na resposta. Este padrão de utilização é alcançado através da `search.ismatchscoring` função. Também pode utilizar a versão sem `search.ismatch` pontuação, .

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'Description') or Category eq 'Luxury'&$count=true
   ```

  Também é possível combinar pesquisa de texto completo através de `search.ismatchscoring` filtros que usam `and` em vez de , mas isso é `or` funcionalmente equivalente a usar o e `search` `$filter` parâmetros em um pedido de pesquisa. Por exemplo, as duas seguintes consultas produzem o mesmo resultado:

  ```
  $filter=search.ismatchscoring('pool') and Rating ge 4

  search=pool&$filter=Rating ge 4
  ```

Acompanhe estes artigos para uma orientação abrangente sobre casos específicos de utilização:

+ [Filtros de facetas](search-filters-facets.md)
+ [Filtros de idioma](search-filters-language.md)
+ [limitação de segurança](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Requisitos de campo para filtragem

Na API REST, filtrado é *por* defeito para campos simples. Os campos filtrados aumentam o tamanho do índice; certifique-se de definir `"filterable": false` para campos que não pretende realmente usar em um filtro. Para obter mais informações sobre as definições de campo, consulte [Criar Índice](/rest/api/searchservice/create-index).

No .NET SDK, o filtrado está *desligado* por defeito. Pode tornar um campo filtrado definindo a [propriedade IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) do objeto [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) correspondente a `true` . No exemplo abaixo, o atributo é definido na `BaseRate` propriedade de uma classe modelo que mapeia para a definição de índice.

```csharp
[IsFilterable, IsSortable, IsFacetable]
public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Tornar um campo existente filtrado

Não se pode modificar os campos existentes para os tornar filtrados. Em vez disso, é necessário adicionar um novo campo ou reconstruir o índice. Para obter mais informações sobre a reconstrução de um índice ou campos de repovoamento, consulte [Como reconstruir um índice de Pesquisa Cognitiva Azure](search-howto-reindex.md).

## <a name="text-filter-fundamentals"></a>Fundamentos do filtro de texto

Os filtros de texto combinam os campos de cordas com as cordas literais que fornece no filtro. Ao contrário da pesquisa por texto completo, não existe uma análise lexical ou quebra de palavras para filtros de texto, por isso as comparações são apenas para correspondências exatas. Por exemplo, assuma que um campo *f* contém "dia ensolarado", `$filter=f eq 'Sunny'` não corresponde, mas `$filter=f eq 'sunny day'` vai. 

As cordas de texto são sensíveis a maiôs. Não há invólucro inferior de palavras maiússãos: `$filter=f eq 'Sunny day'` não vai encontrar "dia ensolarado".

### <a name="approaches-for-filtering-on-text"></a>Abordagens para filtragem em texto

| Abordagem | Description | Quando utilizar |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | Uma função que corresponde a um campo com uma lista delimitada de cordas. | Recomendado para [filtros de segurança](search-security-trimming-for-azure-search.md) e para quaisquer filtros onde muitos valores de texto brutos precisam de ser combinados com um campo de cordas. A função **search.in** é concebida para a velocidade e é muito mais rápida do que comparar explicitamente o campo com cada corda utilizando `eq` e `or` . | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | Uma função que permite misturar operações de pesquisa de texto completo com operações estritamente de filtro Boolean na mesma expressão de filtro. | Utilize **search.ismatch** (ou o seu equivalente de pontuação, **search.ismatchscoring** ) quando pretender várias combinações de filtros de pesquisa num único pedido. Também pode usá-lo para um filtro *contém* para filtrar uma corda parcial dentro de uma corda maior. |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | Uma expressão definida pelo utilizador composta por campos, operadores e valores. | Use isto quando quiser encontrar correspondências exatas entre um campo de cordas e um valor de corda. |

## <a name="numeric-filter-fundamentals"></a>Fundamentos do filtro numérico

Os campos numéricos não estão `searchable` no contexto da pesquisa completa por texto. Apenas as cordas estão sujeitas a uma pesquisa completa por texto. Por exemplo, se introduzir 99.99 como termo de pesquisa, não receberá itens de volta com o preço de $99.99. Em vez disso, veria itens que têm o número 99 nos campos de cordas do documento. Assim, se tiver dados numéricos, o pressuposto é que os utilizará para filtros, incluindo gamas, facetas, grupos, etc. 

Os documentos que contêm campos numéricos (preço, tamanho, SKU, ID) fornecem esses valores nos resultados de pesquisa se o campo estiver marcado `retrievable` . A questão aqui é que a pesquisa completa por texto em si não é aplicável aos tipos de campo numérico.

## <a name="next-steps"></a>Passos seguintes

Primeiro, tente pesquisar o **explorador** no portal para submeter consultas com **$filter** parâmetros. O [índice de amostra imobiliária](search-get-started-portal.md) fornece resultados interessantes para as seguintes consultas filtradas quando as colam na barra de pesquisa:

```
# Geo-filter returning documents within 5 kilometers of Redmond, Washington state
# Use $count=true to get a number of hits returned by the query
# Use $select to trim results, showing values for named fields only
# Use search=* for an empty query string. The filter is the sole input

search=*&$count=true&$select=description,city,postCode&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5

# Numeric filters use comparison like greater than (gt), less than (lt), not equal (ne)
# Include "and" to filter on multiple fields (baths and bed)
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=baths gt 3 and beds gt 4

# Text filters can also use comparison operators
# Wrap text in single or double quotes and use the correct case
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=city gt 'Seattle'
```

Para trabalhar com mais exemplos, consulte [a sintaxe de expressão do filtro OData > Exemplos](./search-query-odata-filter.md#examples).

## <a name="see-also"></a>Ver também

+ [Como funciona a pesquisa em texto completo no Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Search Documents REST API](/rest/api/searchservice/search-documents) (Pesquisar Documentos com a API REST)
+ [Sintaxe de consulta simples](/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Sintaxe de consulta Lucene](/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Tipos de dados suportados](/rest/api/searchservice/supported-data-types)