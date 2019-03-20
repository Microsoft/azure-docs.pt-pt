---
title: Filtros para controlar o âmbito resultados da pesquisa de um índice - Azure Search
description: Filtrar por identidade de segurança do utilizador, idioma, localização geográfica ou valores numéricos para reduzir os resultados da pesquisa em consultas no Azure Search, um serviço de pesquisa de nuvem alojada no Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: a9e8d2cbc067fd92208fac778ba17c58bdc7a5e4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58079150"
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

+ `$select` parâmetro é utilizado para especificar quais campos serão incluídos num resultado de definir, com eficiência cortar a resposta antes de os enviar ao aplicativo de chamada. Este parâmetro não refinar a consulta ou reduzir a coleção de documentos, mas se uma resposta granular é o seu objetivo, este parâmetro é uma opção a considerar. 

Para obter mais informações sobre qualquer parâmetro, consulte [documentos sobre pesquisa > Pedir > parâmetros de consulta](https://docs.microsoft.com/rest/api/searchservice/search-documents#request).


## <a name="filters-in-the-query-pipeline"></a>Filtros no pipeline de consulta

No momento da consulta, um analisador de filtro aceita critérios como entrada, converte a expressão em expressões booleanas atômicas e cria uma árvore de filtros, em seguida, é avaliada através de campos filtráveis num índice.  

Filtragem ocorre antes de pesquisa, qualificar quais documentos para incluir no processamento a jusante para obtenção de documento e a pontuação de relevância. Quando combinado com uma cadeia de caracteres de pesquisa, o filtro reduz efetivamente a área de superfície da operação de pesquisa subsequentes. Quando usada sozinha (por exemplo, quando a cadeia de consulta está vazia onde `search=*`), os critérios de filtro é a única entrada. 

## <a name="filter-definition"></a>Definição do filtro

Os filtros são expressões de OData, articuladas usando um [subconjunto da sintaxe de OData V4 suportado no Azure Search](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

Pode especificar um filtro para cada **pesquisa** operação, mas o filtro em si pode incluir vários campos, vários critérios, e se usar um **ismatch** função, várias expressões. Numa expressão de filtro com várias partes, pode especificar predicados por qualquer ordem. Não há nenhum ganho considerável no desempenho se tentar reorganizar predicados numa sequência específica.

O limite do disco rígido uma expressão de filtro é o limite máximo na solicitação. O pedido completo, que inclui o filtro, pode ter um máximo de 16 MB para o POST ou 8 KB para o GET. Correlacionam os limites flexíveis para o número de cláusulas na sua expressão de filtro. Uma boa regra prática é que se tiver centenas de cláusulas, está em risco de em execução para o limite. Recomendamos a criação do seu aplicativo de tal forma que ele não gera filtros de tamanho não vinculado.

Os exemplos a seguir representam as definições do filtro prototípico em várias APIs.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2017-11-11

# Option 2: Use filter for POST and pass it in the header
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2017-11-11
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

```

## <a name="filter-design-patterns"></a>Padrões de design de filtro

Os exemplos seguintes mostram vários padrões de design para cenários de filtro. Para obter mais ideias, consulte [sintaxe da expressão OData > exemplos](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples).

+ Autónomo **$filter**, sem uma cadeia de consulta, útil quando a expressão de filtro é capaz de qualificar completamente a documentos de interesse. Sem uma cadeia de consulta, não existe nenhuma análise lexical ou linguística, sem pontuação e nenhuma classificação. Tenha em atenção que a cadeia de pesquisa está vazia.

   ```
   search=*&$filter=(baseRate ge 60 and baseRate lt 300) and accommodation eq 'Hotel' and city eq 'Nogales'
   ```

+ Combinação de cadeia de consulta e **$filter**, em que o filtro cria o subconjunto e a cadeia de consulta fornece as entradas do termo de pesquisa em texto completo sobre o subconjunto filtrado. Utilizar um filtro com uma cadeia de consulta é o padrão de código mais comuns.

   ```
   search=hotels ocean$filter=(baseRate ge 60 and baseRate lt 300) and city eq 'Los Angeles'
   ```

+ Componha consultas, separadas por "ou", cada um com seus próprios critérios de filtro (por exemplo, "beagles" em "cachorro") ou "siamese" em "gato". OU tinha as expressões são avaliadas individualmente, com as respostas de cada um combinados numa resposta enviada de volta ao aplicativo de chamada. Este padrão de design é obtido por meio da função de search.ismatch. Pode utilizar a versão de não de classificação de mensagens em fila (search.ismatch) ou a versão de classificação (search.ismatchscoring).

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'description') or category eq 'Luxury'
   ```

Acompanhe os os artigos seguintes para orientações abrangentes em casos de uso específico:

+ [Filtros de facetas](search-filters-facets.md)
+ [Filtros de idioma](search-filters-language.md)
+ [limitação de segurança](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Requisitos do campo para filtragem

Na API do REST, é o filtrável *no* por predefinição. Os campos filtráveis aumentam o tamanho do índice; Certifique-se de que definir `filterable=FALSE` para os campos que não planeja, na verdade, utilize um filtro. Para obter mais informações sobre as definições para definições de campo, veja [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

No SDK do .NET, é o filtrável *desativar* por predefinição. É a API para definir a propriedade filtrável [IsFilterable](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). No exemplo abaixo, seu conjunto sobre a definição de campo BaseRate.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="reindexing-requirements"></a>Requisitos de reindexação

Se um campo é não filtradas e quiser torná-lo filtráveis, terá de adicionar um novo campo, ou recriar o campo existente. Alterar uma definição de campo altera a estrutura física do índice. No Azure Search, todos os caminhos de acesso permitidos são indexados para a velocidade de consulta rápida, que exige uma reconstrução das estruturas de dados quando alterar definições de campo. 

A reconstrução de campos individuais pode ser uma operação de baixo impacto, exigindo apenas uma operação de intercalação que envia a chave de documento existente e valores associados para o índice, deixando intactos os o resto de cada documento. Se tiver um requisito de recompilação, veja [indexação ações (carregar, intercalar, mergeOrUpload, eliminar)](search-what-is-data-import.md#indexing-actions) para obter uma lista de opções.


## <a name="text-filter-fundamentals"></a>Conceitos básicos do filtro de texto

Filtros de texto são válidos para campos de cadeia de caracteres, a partir do qual pretende extrair alguns coleção arbitrária de documentos com base nos valores dentro do corpo.

Para filtros de texto compostos por cadeias de caracteres, não existe nenhum análise lexical ou separação de palavras, para que as comparações destinam-se apenas a correspondências exatas. Por exemplo, suponha que um campo *f* contém "sunny day", `$filter=f eq 'Sunny'`não corresponderem, mas `$filter=f eq 'Sunny day'` será. 

Cadeias de texto diferenciam maiúsculas de minúsculas. Não existe nenhum inferior-letras maiúsculas e minúsculas maiúsculo palavras: `$filter=f eq 'Sunny day'` não irá encontrar "dia ensolarado '.


| Abordagem | Descrição | 
|----------|-------------|
| [search.in()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Uma função fornece a lista delimitada por vírgulas de cadeias de caracteres para um determinado campo. As cadeias de caracteres compreendem os critérios de filtro, que são aplicados a todos os campos no âmbito da consulta. <br/><br/>`search.in(f, ‘a, b, c’)` é semanticamente equivalente a `f eq ‘a’ or f eq ‘b’ or f eq ‘c’`, exceto que ele seja executado muito mais rapidamente quando a lista de valores é grande.<br/><br/>Recomendamos que o **search.in** funcionar para [filtros de segurança](search-security-trimming-for-azure-search.md) e para todos os filtros compostos de texto não processado para corresponder nos valores num determinado campo. Esta abordagem destina-se a velocidade. Pode esperar subsecond o tempo de resposta para centenas ou milhares de valores. Embora não haja nenhum limite explícito no número de itens que pode passar para a função, os aumentos de latência forma proporcional, segundo o número de cadeias de caracteres que fornecer. | 
| [search.ismatch()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Uma função que permite combinar as operações de pesquisa em texto completo com operações de filtro estritamente booleano na mesma expressão de filtro. Permite que várias combinações de filtro de consulta numa solicitação. Também pode usá-lo para um *contém* filtro para filtrar numa cadeia parcial dentro de uma cadeia de caracteres maior. |  
| [$filter = a cadeia de caracteres de operador de campo](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Uma expressão definida pelo utilizador compostas por campos, operadores e valores. | 

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
