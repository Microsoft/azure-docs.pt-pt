---
title: Filtros para controlar o âmbito resultados da pesquisa de um índice - Azure Search
description: Filtrar por identidade de segurança do utilizador, idioma, localização geográfica ou valores numéricos para reduzir os resultados da pesquisa em consultas no Azure Search, um serviço de pesquisa de nuvem alojada no Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 8676ad48bc4fab6149db00d778349ac1acd7223d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67062379"
---
# <a name="filters-in-azure-search"></a>Filtros no Azure Search 

R *filtro* fornece critérios para a seleção de documentos usados numa consulta de pesquisa do Azure. Pesquisa não filtrada inclui todos os documentos no índice. Um filtro de âmbitos de uma consulta de pesquisa a um subconjunto de documentos. Por exemplo, um filtro poderia restringir a pesquisa em texto completo para apenas os produtos com uma marca específica ou a cor, em pontos de preço acima de um determinado limiar.

Algumas experiências de pesquisa impõem requisitos de filtro como parte da implementação, mas pode utilizar filtros sempre que quiser restringir a pesquisa usando *com base no valor* critérios (âmbito procure para o tipo de produto "livros" category" não-fiction"publicado por"Simon & Schuster").

Se em vez disso, o seu objetivo é uma pesquisa direcionada em dados específicas *estruturas* (pesquise âmbito para um campo de opiniões de clientes), existem métodos alternativos, descritos abaixo.

## <a name="when-to-use-a-filter"></a>Quando utilizar um filtro

Os filtros são fundamentais para várias experiências de pesquisa, incluindo "encontrar perto de mim", navegação por facetas e a segurança filtra que mostram apenas esses documentos de um utilizador tem permissão para ver. Se implementar qualquer um destas experiências, um filtro é necessário. É o filtro anexado à consulta de pesquisa que fornece as coordenadas de localização geográfica, a categoria de faceta selecionada pelo utilizador ou o ID de segurança do requerente.

Cenários de exemplo incluem o seguinte:

1. Utilize um filtro para segmentar o seu índice com base nos valores de dados no índice. Devido um esquema com a cidade, o tipo de alojamento e características, poderá criar um filtro para selecionar explicitamente os documentos que satisfazem os critérios (em Seattle, condos, frente marítima). 

   Pesquisa em texto completo com as entradas do mesmo, muitas vezes, produz resultados semelhantes, mas é mais preciso um filtro em que ele requer uma correspondência exata do termo de filtro estrita de conteúdos no seu índice. 

2. Utilize um filtro, se a experiência de pesquisa é fornecido com um requisito de filtro:

   * [Navegação por facetas](search-faceted-navigation.md) utiliza um filtro para transmitir de volta a categoria de faceta selecionada pelo usuário.
   * Pesquisa geográfica utiliza um filtro para passar as coordenadas da localização atual no "encontrar perto de mim" aplicações. 
   * Filtros de segurança passam identificadores de segurança como critérios de filtro, em que uma correspondência no índice serve como um proxy para os direitos de acesso ao documento.

3. Utilize um filtro, se pretender que os critérios de pesquisa num campo numérico. 

   Campos numéricos são recuperáveis no documento e podem aparecer nos resultados da pesquisa, mas não são pesquisáveis (sujeito a pesquisa em texto completo) individualmente. Se precisar de critérios de seleção com base em dados numéricos, utilize um filtro.

### <a name="alternative-methods-for-reducing-scope"></a>Métodos alternativos para reduzir o âmbito

Se quiser que um efeito de restrição de seus resultados de pesquisa, os filtros não são sua única escolha. Essas alternativas poderiam ser a melhor opção, consoante o seu objetivo:

 + `searchFields` parâmetro de consulta pegs pesquisa a campos específicos. Por exemplo, se o índice fornece campos separados para obter descrições em inglês e espanhol, pode utilizar searchFields para direcionar os campos a utilizar para pesquisa em texto completo. 

+ `$select` parâmetro é utilizado para especificar quais campos serão incluídos num resultado de definir, com eficiência cortar a resposta antes de os enviar ao aplicativo de chamada. Este parâmetro não refinar a consulta ou reduzir a coleção de documentos, mas se uma resposta menor é o seu objetivo, este parâmetro é uma opção a considerar. 

Para obter mais informações sobre qualquer parâmetro, consulte [documentos sobre pesquisa > Pedir > parâmetros de consulta](https://docs.microsoft.com/rest/api/searchservice/search-documents#request).


## <a name="how-filters-are-executed"></a>Como os filtros são executados

No momento da consulta, um analisador de filtro aceita critérios como entrada, converte a expressão em expressões booleanas atômicas, representadas como uma árvore e, em seguida, avalia a árvore de filtros sobre os campos filtráveis num índice.

Filtragem ocorre em conjunto com a pesquisa, qualificar quais documentos para incluir no processamento a jusante para obtenção de documento e a pontuação de relevância. Quando combinado com uma cadeia de caracteres de pesquisa, o filtro reduz efetivamente o conjunto de recolhimento da operação de pesquisa subsequentes. Quando usada sozinha (por exemplo, quando a cadeia de consulta está vazia onde `search=*`), os critérios de filtro é a única entrada. 

## <a name="defining-filters"></a>Definir filtros

Os filtros são expressões de OData, articuladas usando um [subconjunto da sintaxe de OData V4 suportado no Azure Search](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

Pode especificar um filtro para cada **pesquisa** operação, mas o filtro em si pode incluir vários campos, vários critérios, e se usar um **ismatch** função, várias expressões de pesquisa em texto completo. Numa expressão de filtro com várias partes, pode especificar predicados por qualquer ordem (sujeito às regras de precedência de operador). Não há nenhum ganho considerável no desempenho se tentar reorganizar predicados numa sequência específica.

Um dos limites numa expressão de filtro é o limite de tamanho máximo do pedido. O pedido completo, que inclui o filtro, pode ter um máximo de 16 MB para o POST ou 8 KB para o GET. Também existe um limite no número de cláusulas na sua expressão de filtro. Uma boa regra prática é que se tiver centenas de cláusulas, está em risco de em execução para o limite. Recomendamos a criação do seu aplicativo de tal forma que ele não gera filtros de tamanho não vinculado.

Os exemplos a seguir representam as definições do filtro prototípico em várias APIs.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2019-05-06

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2019-05-06
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "baseRate lt 150",
            Select = new[] { "hotelId", "description" }
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>Padrões de utilização de filtro

Os exemplos seguintes mostram vários padrões de uso para cenários de filtro. Para obter mais ideias, consulte [sintaxe da expressão OData > exemplos](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples).

+ Autónomo **$filter**, sem uma cadeia de consulta, útil quando a expressão de filtro é capaz de qualificar completamente a documentos de interesse. Sem uma cadeia de consulta, não existe nenhuma análise lexical ou linguística, sem pontuação e nenhuma classificação. Tenha em atenção que a cadeia de pesquisa é apenas um asterisco, que significa "corresponder a todos os documentos".

   ```
   search=*&$filter=(baseRate ge 60 and baseRate lt 300) and accommodation eq 'Hotel' and city eq 'Nogales'
   ```

+ Combinação de cadeia de consulta e **$filter**, em que o filtro cria o subconjunto e a cadeia de consulta fornece as entradas do termo de pesquisa em texto completo sobre o subconjunto filtrado. Utilizar um filtro com uma cadeia de consulta é o padrão de utilização mais comuns.

   ```
   search=hotels ocean$filter=(baseRate ge 60 and baseRate lt 300) and city eq 'Los Angeles'
   ```

+ Componha consultas, separadas por "ou", cada um com seus próprios critérios de filtro (por exemplo, "beagles" em "cachorro") ou "siamese" em "gato". Expressões combinado com `or` são avaliadas individualmente, com a União dos documentos correspondentes a cada expressão enviado de volta na resposta. Este padrão de utilização é obtida por meio do `search.ismatchscoring` função. Também pode utilizar a versão não pontuação, `search.ismatch`.

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'description') or category eq 'Luxury'
   ```

  Também é possível combinar a pesquisa em texto completo através de `search.ismatchscoring` com filtros usando `and` em vez de `or`, mas isto é funcionalmente equivalente a utilizar os `search` e `$filter` parâmetros numa solicitação de pesquisa. Por exemplo, as seguintes duas consultas produzem o mesmo resultado:

  ```
  $filter=search.ismatchscoring('pool') and rating ge 4

  search=pool&$filter=rating ge 4
  ```

Acompanhe os os artigos seguintes para orientações abrangentes em casos de uso específico:

+ [Filtros de facetas](search-filters-facets.md)
+ [Filtros de idioma](search-filters-language.md)
+ [limitação de segurança](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Requisitos do campo para filtragem

Na API do REST, é o filtrável *no* por predefinição para campos simples. Os campos filtráveis aumentam o tamanho do índice; Certifique-se de que definir `"filterable": false` para os campos que não planeja, na verdade, utilize um filtro. Para obter mais informações sobre as definições para definições de campo, veja [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

No SDK do .NET, é o filtrável *desativar* por predefinição. Pode fazer um campo filtrável definindo a [propriedade IsFilterable](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) de correspondente [campo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field?view=azure-dotnet) de objeto para `true`. Pode também fazer isso declarativamente usando a [IsFilterable atributo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). No exemplo abaixo, o atributo é definido no `BaseRate` propriedade de uma classe de modelo que mapeia para a definição do índice.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Fazendo um campo existente filtrável

Não é possível modificar os campos existentes para torná-los filtrável. Em vez disso, terá de adicionar um novo campo, ou reconstrua o índice. Para obter mais informações sobre como recompilar um índice ou preencher novamente os campos, consulte [como reconstruir um índice da Azure Search](search-howto-reindex.md).

## <a name="text-filter-fundamentals"></a>Conceitos básicos do filtro de texto

Filtros de texto correspondem a campos de cadeia em relação a cadeias de caracteres literais que fornecer no filtro. Ao contrário de pesquisa em texto completo, não existe nenhuma análise lexical ou separação de palavras para filtros de texto, para que as comparações destinam-se apenas a correspondências exatas. Por exemplo, suponha que um campo *f* contém "sunny day", `$filter=f eq 'Sunny'` não corresponderem, mas `$filter=f eq 'sunny day'` será. 

Cadeias de texto diferenciam maiúsculas de minúsculas. Não existe nenhum inferior-letras maiúsculas e minúsculas maiúsculo palavras: `$filter=f eq 'Sunny day'` não irá encontrar "sunny day".

### <a name="approaches-for-filtering-on-text"></a>Abordagens para filtrar em texto

| Abordagem | Descrição | Quando utilizar |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | Uma função que corresponde a um campo em relação a uma lista delimitada de cadeias de caracteres. | Recomendado para [filtros de segurança](search-security-trimming-for-azure-search.md) e para quaisquer filtros em que muitos valores de texto não processado têm de ser correspondidos com um campo de cadeia de caracteres. O **search.in** função foi concebida para a velocidade e é muito mais rápida do que explicitamente comparando o campo contra a utilização de cada cadeia de caracteres `eq` e `or`. | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | Uma função que permite combinar as operações de pesquisa em texto completo com operações de filtro estritamente booleano na mesma expressão de filtro. | Uso **search.ismatch** (ou equivalente classificação **search.ismatchscoring**) quando desejar várias combinações de filtro de pesquisa numa solicitação. Também pode usá-lo para um *contém* filtro para filtrar numa cadeia parcial dentro de uma cadeia de caracteres maior. |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | Uma expressão definida pelo utilizador compostas por campos, operadores e valores. | Utilize esta opção quando pretender localizar correspondências exatas entre um campo de cadeia de caracteres e um valor de cadeia de caracteres. |

## <a name="numeric-filter-fundamentals"></a>Conceitos básicos de filtro numérico

Campos numéricos não são `searchable` no contexto de pesquisa em texto completo. Apenas cadeias de caracteres são sujeitos a pesquisa em texto completo. Por exemplo, se introduzir 99,99 como um termo de pesquisa, não obtém itens que o preço de US $99,99. Em vez disso, veria itens que tenham o número de 99 em campos de cadeia de caracteres do documento. Assim, se tiver dados numéricos, o pressuposto é que irá utilizá-los para filtros, incluindo intervalos, facetas, grupos e assim por diante. 

Documentos que contenham campos numéricos (preço, tamanho, SKU, ID) fornecem esses valores nos resultados da pesquisa, se o campo está marcado `retrievable`. O ponto aqui é que a pesquisa em texto completo em si não é aplicável ao tipos de campo numérico.

## <a name="next-steps"></a>Passos Seguintes

Em primeiro lugar, tente **Explorador de pesquisa** no portal para submeter consultas com **$filter** parâmetros. O [índice de exemplo de real estate](search-get-started-portal.md) fornece resultados interessantes para o seguinte filtrado consultas quando colá-los na barra de pesquisa:

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

Para trabalhar com mais exemplos, consulte [sintaxe de expressão de filtro de OData > exemplos](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples).

## <a name="see-also"></a>Consulte também

+ [Completa como funciona a pesquisa de texto no Azure Search](search-lucene-query-architecture.md)
+ [Procurar nos documentos de REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [Sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Sintaxe de consulta Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Tipos de dados suportados](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
