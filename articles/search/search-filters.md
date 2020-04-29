---
title: Filtrar os resultados da pesquisa
titleSuffix: Azure Cognitive Search
description: Filtrar por identidade de segurança do utilizador, idioma, geolocalização ou valores numéricos para reduzir os resultados de pesquisa em consultas em Azure Cognitive Search, um serviço de pesquisa em nuvem hospedado no Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 03333e853a2ab7606ebe60cc3f68bcb5facfbdb4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77191006"
---
# <a name="filters-in-azure-cognitive-search"></a>Filtros em Pesquisa Cognitiva Azure 

Um *filtro* fornece critérios para selecionar documentos utilizados numa consulta de Pesquisa Cognitiva Azure. A pesquisa não filtrada inclui todos os documentos do índice. Um filtro aplica uma consulta de pesquisa a um subconjunto de documentos. Por exemplo, um filtro poderia restringir a procura completa de texto apenas a esses produtos com uma marca ou cor específica, a preços acima de um determinado limiar.

Algumas experiências de pesquisa impõem requisitos de filtro como parte da implementação, mas você pode usar filtros sempre que você quiser limitar a pesquisa usando critérios *baseados em valor* (pesquisa de pesquisa de produto "livros" para categoria "não-ficção" publicado por "Simon & Schuster").

Se, em vez disso, o seu objetivo for a pesquisa direcionada a *estruturas* específicas de dados (pesquisa de pesquisa de um campo de avaliação de clientes), existem métodos alternativos, descritos abaixo.

## <a name="when-to-use-a-filter"></a>Quando usar um filtro

Os filtros são fundamentais para várias experiências de pesquisa, incluindo "encontrar perto de mim", navegação faceada e filtros de segurança que mostram apenas esses documentos que um utilizador pode ver. Se implementar qualquer uma destas experiências, é necessário um filtro. É o filtro ligado à consulta de pesquisa que fornece as coordenadas de geolocalização, a categoria faceta selecionada pelo utilizador, ou o ID de segurança do solicitador.

Os cenários de exemplo incluem o seguinte:

1. Utilize um filtro para cortar o seu índice com base nos valores de dados no índice. Dado um esquema com cidade, tipo de habitação e comodidades, você pode criar um filtro para selecionar explicitamente documentos que satisfaçam os seus critérios (em Seattle, condomínios, beira-mar). 

   A pesquisa completa de texto com as mesmas inputs produz frequentemente resultados semelhantes, mas um filtro é mais preciso na medida em que requer uma correspondência exata do termo do filtro contra o conteúdo no seu índice. 

2. Utilize um filtro se a experiência de pesquisa vier com um requisito de filtro:

   * [A navegação facetada](search-faceted-navigation.md) utiliza um filtro para passar a categoria faceta selecionada pelo utilizador.
   * A geo-pesquisa utiliza um filtro para passar coordenadas da localização atual em aplicações "encontre perto de mim". 
   * Os filtros de segurança passam identificadores de segurança como critérios de filtro, onde uma correspondência no índice serve de procuração para os direitos de acesso ao documento.

3. Utilize um filtro se pretender critérios de pesquisa num campo numérico. 

   Os campos numéricos são recuperáveis no documento e podem aparecer nos resultados da pesquisa, mas não são pesquisáveis (sujeitos a pesquisa completa de texto) individualmente. Se necessitar de critérios de seleção baseados em dados numéricos, utilize um filtro.

### <a name="alternative-methods-for-reducing-scope"></a>Métodos alternativos para a redução do âmbito

Se quiser um efeito de redução nos resultados da sua pesquisa, os filtros não são a sua única escolha. Estas alternativas poderiam ser mais adequadas, dependendo do seu objetivo:

 + `searchFields`cavilhas de parâmetro sérmetros de consulta procuram em campos específicos. Por exemplo, se o seu índice fornecer campos separados para descrições em inglês e espanhol, pode usar searchFields para direcionar quais os campos a utilizar para pesquisa completa de texto. 

+ `$select`O parâmetro é utilizado para especificar quais os campos a incluir num conjunto de resultados, aparando efetivamente a resposta antes de enviá-la para o pedido de chamada. Este parâmetro não refina a consulta nem reduz a recolha de documentos, mas se uma resposta menor for o seu objetivo, este parâmetro é uma opção a considerar. 

Para mais informações sobre qualquer parâmetro, consulte [documentos de pesquisa > Solicitar > parâmetros](/rest/api/searchservice/search-documents#query-parameters)de consulta .


## <a name="how-filters-are-executed"></a>Como os filtros são executados

Na altura da consulta, um filtro aceita critérios como entrada, converte a expressão em expressões atómicas booleanas representadas como uma árvore, e depois avalia a árvore filtrante sobre campos filtrados num índice.

A filtragem ocorre em conjunto com a pesquisa, qualificando quais os documentos a incluir no processamento a jusante para recuperação de documentos e pontuação de relevância. Quando emparelhado com uma cadeia de pesquisa, o filtro reduz eficazmente o conjunto de recolha da operação de pesquisa subsequente. Quando utilizada sozinha (por exemplo, quando a `search=*`corda de consulta está vazia onde), os critérios do filtro são a entrada única. 

## <a name="defining-filters"></a>Definição de filtros
Os filtros são expressões OData, articuladas com um [subconjunto de sintaxe OData V4 suportado na Pesquisa Cognitiva Azure](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

Pode especificar um filtro para cada operação de **pesquisa,** mas o filtro em si pode incluir vários campos, múltiplos critérios, e se utilizar uma função **ismatch,** múltiplas expressões de pesquisa de texto completo. Numa expressão de filtro multi-partes, pode especificar predicados em qualquer ordem (sob reserva das regras de precedência do operador). Não há um ganho apreciável no desempenho se tentar reorganizar predicados numa determinada sequência.

Um dos limites para uma expressão de filtro é o limite máximo de tamanho do pedido. Todo o pedido, incluindo o filtro, pode ser um máximo de 16 MB para POST, ou 8 KB para GET. Há também um limite no número de cláusulas na expressão do filtro. Uma boa regra é que se tiver centenas de cláusulas, corre o risco de se esgotar no limite. Recomendamos que desenhá-lo de forma a não gerar filtros de tamanho ilimitado.

Os exemplos seguintes representam definições prototípicas de filtro em várias APIs.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?api-version=2019-05-06&search=*&$filter=Rooms/any(room: room/BaseRate lt 150.0)&$select=HotelId, HotelName, Rooms/Description, Rooms/BaseRate

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2019-05-06
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

## <a name="filter-usage-patterns"></a>Padrões de utilização de filtros

Os exemplos seguintes ilustram vários padrões de utilização para cenários de filtro. Para mais ideias, consulte a [expressão OData sintaxe > Exemplos](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples).

+ **$filter**autónomas, sem uma corda de consulta, úteis quando a expressão do filtro é capaz de qualificar totalmente documentos de interesse. Sem uma corda de consulta, não há análise léxica ou linguística, nem pontuação, nem classificação. Note que a cadeia de pesquisa é apenas um asterisco, o que significa "combinar todos os documentos".

   ```
   search=*&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Honolulu'
   ```

+ Combinação de cordas de consulta e **$filter,** onde o filtro cria o subconjunto, e a corda de consulta fornece as inputs do termo para pesquisa completa de texto sobre o subconjunto filtrado. A adição de termos (teatros a uma curta distância) introduz pontuações de pesquisa nos resultados, onde os documentos que melhor correspondem aos termos são classificados mais alto. Usar um filtro com uma corda de consulta é o padrão de utilização mais comum.

   ```
  search=walking distance theaters&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Seattle'&$count=true
   ```

+ Consultas compostas, separadas por "ou", cada uma com os seus próprios critérios de filtro (por exemplo, 'beagles' em 'cão' ou 'siamese' em 'gato'). As expressões `or` combinadas são avaliadas individualmente, com a união de documentos correspondentes a cada expressão enviada na resposta. Este padrão de utilização `search.ismatchscoring` é alcançado através da função. Também pode utilizar a versão sem pontuação, `search.ismatch`.

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'Description') or Category eq 'Luxury'&$count=true
   ```

  Também é possível combinar pesquisa `search.ismatchscoring` de texto `and` completo `or`através de filtros usando em `search` `$filter` vez de , mas isso é funcionalmente equivalente a usar os e parâmetros em um pedido de pesquisa. Por exemplo, as duas seguintes consultas produzem o mesmo resultado:

  ```
  $filter=search.ismatchscoring('pool') and Rating ge 4

  search=pool&$filter=Rating ge 4
  ```

Acompanhe estes artigos para obter orientações abrangentes sobre casos de utilização específica:

+ [Filtros de facetas](search-filters-facets.md)
+ [Filtros de idioma](search-filters-language.md)
+ [limitação de segurança](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Requisitos de campo para filtragem

Na API REST, a filtragem é *por* defeito para campos simples. Os campos filtrados aumentam o tamanho do índice; certifique-se `"filterable": false` de definir para campos que não planeia realmente usar num filtro. Para obter mais informações sobre definições de definições de campo, consulte [Criar Índice](https://docs.microsoft.com/rest/api/searchservice/create-index).

No SDK .NET, o filterável está *desligado* por defeito. Pode tornar um campo filtrado definindo a [propriedade isfilterável](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) do objeto de [campo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field?view=azure-dotnet) correspondente para `true`. Também pode fazê-lo declarativamente utilizando o [atributo IsFilterable](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). No exemplo abaixo, o atributo `BaseRate` é definido na propriedade de uma classe modelo que mapeia para a definição de índice.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Tornando um campo existente filtravel

Não se pode modificar os campos existentes para torná-los filtrados. Em vez disso, é preciso adicionar um novo campo ou reconstruir o índice. Para obter mais informações sobre a reconstrução de um índice ou campos de repovoação, consulte Como reconstruir um índice de [Pesquisa Cognitiva Azure](search-howto-reindex.md).

## <a name="text-filter-fundamentals"></a>Fundamentos do filtro de texto

Os filtros de texto combinam com os campos de cordas com as cordas literais que fornece no filtro. Ao contrário da pesquisa de texto completo, não há nenhuma análise lexical ou quebra de palavras para filtros de texto, por isso as comparações são apenas para correspondências exatas. Por exemplo, assumir *f* que um campo `$filter=f eq 'Sunny'` f contém `$filter=f eq 'sunny day'` "dia ensolarado", não corresponde, mas vai. 

As cordas de texto são sensíveis a casos. Não há um invólucro inferior de palavras `$filter=f eq 'Sunny day'` maiúsculas: não vai encontrar "dia ensolarado".

### <a name="approaches-for-filtering-on-text"></a>Abordagens para filtragem no texto

| Abordagem | Descrição | Quando utilizar |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | Uma função que combina com um campo com uma lista de cordas delimitada. | Recomendado para [filtros](search-security-trimming-for-azure-search.md) de segurança e para quaisquer filtros onde muitos valores de texto bruto precisam de ser combinados com um campo de cordas. A função **search.in** é concebida para a velocidade e é muito `eq` `or`mais rápida do que comparar explicitamente o campo com cada fio utilizando e . | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | Uma função que lhe permite misturar operações de pesquisa de texto completo com operações estritamente de filtro Boolean na mesma expressão de filtro. | Utilize **search.ismatch** (ou o seu equivalente de pontuação, **search.ismatchscoreing**) quando pretender múltiplas combinações de filtros de pesquisa num único pedido. Também pode usá-lo para um filtro *contém* para filtrar uma corda parcial dentro de uma corda maior. |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | Uma expressão definida pelo utilizador composta por campos, operadores e valores. | Use isto quando quiser encontrar correspondências exatas entre um campo de cordas e um valor de corda. |

## <a name="numeric-filter-fundamentals"></a>Fundamentos do filtro numérico

Os campos numéricos não `searchable` estão no contexto da pesquisa completa de texto. Apenas as cordas estão sujeitas a uma pesquisa completa de texto. Por exemplo, se introduzir 99.99 como termo de pesquisa, não receberá itens de volta com um preço de 99,99 dólares. Em vez disso, veria itens que têm o número 99 em campos de cordas do documento. Assim, se tiver dados numéricos, o pressuposto é que os utilizará para filtros, incluindo intervalos, facetas, grupos, etc. 

Documentos que contenham campos numéricos (preço, tamanho, SKU, ID) `retrievable`fornecem esses valores nos resultados da pesquisa se o campo estiver marcado . A questão aqui é que a pesquisa completa de texto em si não é aplicável aos tipos de campo numéricos.

## <a name="next-steps"></a>Passos seguintes

Primeiro, experimente **pesquisar** explorador no portal para submeter consultas com **$filter** parâmetros. O [índice de amostraimobiliária](search-get-started-portal.md) fornece resultados interessantes para as seguintes consultas filtradas quando as cola na barra de pesquisa:

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

Para trabalhar com mais exemplos, consulte [OData Filter Expression Syntax > Exemplos](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples).

## <a name="see-also"></a>Consulte também

+ [Como funciona a pesquisa em texto completo no Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Search Documents REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) (Pesquisar Documentos com a API REST)
+ [Sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Sintaxe de consulta Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Tipos de dados suportados](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
