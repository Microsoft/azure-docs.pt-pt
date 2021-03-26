---
title: Tipos de consultas
titleSuffix: Azure Cognitive Search
description: Conheça os tipos de consultas suportadas na Pesquisa Cognitiva, incluindo texto gratuito, filtro, preconto e sugestões, geo-pesquisa, consultas de sistema e pesquisa de documentos.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 21012848ba3624df6110eaea182beccc4646d234
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609280"
---
# <a name="querying-in-azure-cognitive-search"></a>Consulta em Pesquisa Cognitiva Azure

A Azure Cognitive Search oferece uma rica linguagem de consulta para suportar uma ampla gama de cenários, desde a pesquisa gratuita de texto, até padrões de consulta altamente especificados. Este artigo descreve pedidos de consulta e que tipos de consultas pode criar.

Na Pesquisa Cognitiva, uma consulta é uma especificação completa de uma operação de ida e **`search`** volta, com parâmetros que tanto informam a execução de consultas como moldam a resposta voltando. Parâmetros e parsers determinam o tipo de pedido de consulta. O exemplo de consulta a seguir é uma consulta de texto gratuita com um operador booleano, utilizando os [Documentos de Pesquisa (REST API),](/rest/api/searchservice/search-documents)direcionando a recolha de documentos [de índice de amostras de hotéis.](search-get-started-portal.md)

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "queryType": "simple",
    "searchMode": "all",
    "search": "restaurant +view",
    "searchFields": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "select": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

Os parâmetros utilizados durante a execução de consultas incluem:

+ **`queryType`** define o parser, que é o [parser de consulta simples padrão](search-query-simple-examples.md) (ideal para pesquisa completa de texto), ou o [parser de consulta lucene completo](search-query-lucene-examples.md) usado para construções de consultas avançadas como expressões regulares, pesquisa de proximidade, pesquisa de nádpos e wildcard, para citar alguns.

+ **`searchMode`** especifica se os jogos se baseiam em critérios "todos" ou "quaisquer" na expressão. O padrão é qualquer.

+ **`search`** fornece os critérios de correspondência, geralmente termos ou frases inteiros, com ou sem operadores. Qualquer campo que seja atribuído como *pespável* no esquema de índice é um candidato a este parâmetro.

+ **`searchFields`** limita a execução de consultas a campos pesmáveis específicos. Durante o desenvolvimento, é útil usar a mesma lista de campo para selecionar e pesquisar. Caso contrário, uma correspondência pode basear-se em valores de campo que não pode ver nos resultados, criando incerteza quanto ao motivo pelo qual o documento foi devolvido.

Parâmetros utilizados para moldar a resposta:

+ **`select`** especifica quais os campos a devolver na resposta. Apenas os campos marcados como *recuperáveis* no índice podem ser utilizados numa declaração selecionada.

+ **`top`** devolve o número especificado de documentos mais correspondentes. Neste exemplo, apenas 10 acessos são devolvidos. Pode utilizar a parte superior e saltar (não mostrada) para página dos resultados.

+ **`count`** indica-lhe quantos documentos em toda a correspondência de índice em geral, que pode ser mais do que o que são devolvidos. 

+ **`orderby`** é usado se quiser classificar resultados por um valor, como uma classificação ou localização. Caso contrário, o padrão é utilizar a pontuação de relevância para classificar os resultados. Um campo deve ser atribuído como *um candidato* a este parâmetro.

A lista acima é representativa, mas não exaustiva. Para obter a lista completa dos parâmetros de um pedido de consulta, consulte [Documentos de Busca (REST API)](/rest/api/searchservice/search-documents).

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Tipos de consultas

Com algumas exceções notáveis, um pedido de consulta itera sobre índices invertidos que são estruturados para digitalizações rápidas, onde uma correspondência pode ser encontrada em qualquer campo potencialmente, dentro de qualquer número de documentos de pesquisa. Na Pesquisa Cognitiva, a metodologia principal para encontrar fósforos é a pesquisa completa de texto ou filtros, mas também pode implementar outras experiências de pesquisa bem conhecidas, como a pesquisa automática ou a pesquisa de geolocalização. O resto deste artigo resume consultas na Pesquisa Cognitiva e fornece links para mais informações e exemplos.

## <a name="full-text-search"></a>Pesquisa em texto completo

Se a sua aplicação de pesquisa incluir uma caixa de pesquisa que recolhe entradas de termo, então a pesquisa completa por texto é provavelmente a operação de consulta que apoia essa experiência. A pesquisa por texto completo aceita termos ou frases passadas num **`search`** parâmetro em todos os campos *pesjáveis* do seu índice. Os operadores de boolean opcionais na cadeia de consulta podem especificar critérios de inclusão ou exclusão. Tanto o parser simples como o parser completo suportam a pesquisa completa de texto.

Na Pesquisa Cognitiva, a pesquisa completa de texto é construída no motor de consulta Apache Lucene. As cadeias de consulta em toda a pesquisa por texto são submetidas a análises lexicais para tornar as sondagens mais eficientes. A análise inclui o invólucro inferior de todos os termos, removendo palavras de paragem como "o", e reduzindo termos a formas de raiz primitivas. O analisador padrão é Standard Lucene.

Quando os termos correspondentes são encontrados, o motor de consulta reconstitui um documento de pesquisa contendo a correspondência utilizando a chave de documento ou ID para montar valores de campo, classifica os documentos por ordem de relevância, e devolve o top 50 (por defeito) na resposta ou um número diferente se especificado **`top`** .

Se estiver a implementar uma pesquisa completa de texto, compreender como o seu conteúdo é tokenized irá ajudá-lo a depurar quaisquer anomalias de consulta. Consultas sobre cordas hifenizadas ou caracteres especiais poderiam necessitar de usar um analisador diferente do padrão Lucene para garantir que o índice contém as fichas certas. Pode anular o padrão com [analisadores de linguagem](index-add-language-analyzers.md#language-analyzer-list) ou [analisadores especializados](index-add-custom-analyzers.md#built-in-analyzers) que modificam a análise lexical. Um exemplo é [a palavra-chave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) que trata todo o conteúdo de um campo como um único símbolo. Isto é útil para dados como códigos postais, IDs e alguns nomes de produtos. Para obter mais informações, consulte [a pesquisa parcial e padrões com caracteres especiais.](search-query-partial-matching.md)

Se antecipar o uso pesado dos operadores booleanos, que é mais provável em índices que contenham grandes blocos de texto (um campo de conteúdo ou descrições longas), certifique-se de testar consultas com o **`searchMode=Any|All`** parâmetro para avaliar o impacto dessa definição na pesquisa booleana.

## <a name="autocomplete-and-suggested-queries"></a>Consultas autocompletas e sugeridas

[Os resultados autocompletos ou sugeridos](search-add-autocomplete-suggestions.md) são alternativas a **`search`** esses pedidos de consulta sucessivas de fogo baseados em entradas parciais de cordas (após cada personagem) numa experiência de pesquisa como você tipo. Pode utilizar **`autocomplete`** e **`suggestions`** parâmetros juntos ou separados, como descrito [neste tutorial,](tutorial-csharp-type-ahead-and-suggestions.md)mas não pode usá-los com **`search`** . Ambos os termos preenchidos e consultas sugeridas são derivados de conteúdos de índice. O motor nunca devolverá uma corda ou sugestão que não exista no seu índice. Para obter mais informações, consulte [Autocomplete (REST API)](/rest/api/searchservice/autocomplete) e [Sugestões (REST API)](/rest/api/searchservice/suggestions).

## <a name="filter-search"></a>Pesquisa de filtros

Os filtros são amplamente utilizados em apps que incluem a Pesquisa Cognitiva. Nas páginas de aplicações, os filtros são frequentemente visualizados como facetas nas estruturas de navegação de ligação para filtragem dirigida pelo utilizador. Os filtros também são usados internamente para expor fatias de conteúdo indexado. Por exemplo, pode inicializar uma página de pesquisa usando um filtro numa categoria de produto, ou um idioma se um índice contiver campos em inglês e francês.

Também pode precisar de filtros para invocar um formulário de consulta especializado, conforme descrito na tabela seguinte. Pode utilizar um filtro com uma pesquisa não especificada **`search=*`** () ou com uma cadeia de consulta que inclua termos, frases, operadores e padrões.

| Cenário de filtro | Description |
|-----------------|-------------|
| Filtros de gama | Na Pesquisa Cognitiva Azure, as consultas de alcance são construídas usando o parâmetro do filtro. Para obter mais informações e exemplos, consulte [o exemplo do filtro Range](search-query-simple-examples.md#example-5-range-filters). |
| Pesquisa de geolocalização | Se um campo pescatório for do [tipo Edm.GeographyPoint,](/rest/api/searchservice/supported-data-types)pode criar uma expressão de filtro para "encontrar perto de mim" ou controlos de pesquisa baseados em mapas. Os campos que impulsionam a geo-pesquisa contêm coordenadas. Para obter mais informações e um exemplo, consulte [o exemplo de Geo-pesquisa.](search-query-simple-examples.md#example-6-geo-search) |
| Navegação por facetas | Uma estrutura de navegação faceta torna-se fundamental na navegação dirigida pelo utilizador quando invoca um filtro em resposta a `onclick` um evento numa faceta. Como tal, as facetas e os filtros andam de mãos dadas. Se adicionar navegação faceta, precisará de filtros para completar a experiência. Para obter mais informações, consulte [Como construir um filtro de faceta.](search-filters-facets.md) |

> [!NOTE]
> O texto que é usado numa expressão de filtro não é analisado durante o processamento de consulta. Presume-se que a entrada de texto seja um padrão de caracteres sensível ao caso verbatim que ou sucede ou falha na partida. As expressões de filtro são construídas com recurso [à sintaxe OData](query-odata-filter-orderby-syntax.md) e passadas num **`filter`** parâmetro em todos os campos *filtrados* do seu índice. Para obter mais informações, consulte [Filtros em Pesquisa Cognitiva Azure](search-filters.md).

## <a name="document-look-up"></a>Análise documental

Em contraste com os formulários de consulta previamente descritos, este recupera um único [documento de pesquisa por ID,](/rest/api/searchservice/lookup-document)sem pesquisa ou digitalização de índice correspondente. Apenas o documento é solicitado e devolvido. Quando um utilizador seleciona um item nos resultados da pesquisa, recuperar o documento e povoar uma página de detalhes com campos é uma resposta típica, e um look-up de documento é a operação que o suporta.

## <a name="advanced-search-fuzzy-wildcard-proximity-regex"></a>Pesquisa avançada: fuzzy, wildcard, proximidade, regex

Um formulário de consulta avançado depende do parser e operadores de Lucene completo que desencadeiam um comportamento específico de consulta.

| Tipo de consulta | Utilização | Exemplos e mais informações |
|------------|--------|------------------------------|
| [Pesquisa de campo](query-lucene-syntax.md#bkmk_fields) | **`search`**  parâmetro, **`queryType=full`**  | Construa uma expressão de consulta composta dirigida a um único campo. <br/>[Exemplo de pesquisa em campo](search-query-lucene-examples.md#example-1-fielded-search) |
| [pesquisa fuzzy](query-lucene-syntax.md#bkmk_fuzzy) | **`search`** parâmetro, **`queryType=full`** | Corresponde em termos de construção ou ortografia semelhantes. <br/>[Exemplo de pesquisa difusa](search-query-lucene-examples.md#example-2-fuzzy-search) |
| [pesquisa de proximidade](query-lucene-syntax.md#bkmk_proximity) | **`search`** parâmetro, **`queryType=full`** | Encontra termos que estão perto um do outro num documento. <br/>[Exemplo de pesquisa de proximidade](search-query-lucene-examples.md#example-3-proximity-search) |
| [aumento de prazos](query-lucene-syntax.md#bkmk_termboost) | **`search`** parâmetro, **`queryType=full`** | Classifica um documento mais alto se contiver o termo aumentado, em relação a outros que não. <br/>[Exemplo de reforço de prazos](search-query-lucene-examples.md#example-4-term-boosting) |
| [pesquisa regular de expressão](query-lucene-syntax.md#bkmk_regex) | **`search`** parâmetro, **`queryType=full`** | Fósforos baseados no conteúdo de uma expressão regular. <br/>[Exemplo de expressão regular](search-query-lucene-examples.md#example-5-regex) |
|  [pesquisa de wildcard ou prefixo](query-lucene-syntax.md#bkmk_wildcard) | **`search`** parâmetro com * *_`~`_* **`?`** ou, **`queryType=full`**| Corresponde com base num prefixo e num único `~` carácter (). `?` <br/>[Exemplo de pesquisa wildcard](search-query-lucene-examples.md#example-6-wildcard-search) |

## <a name="next-steps"></a>Passos seguintes

Para uma análise mais atenta da implementação da consulta, reveja os exemplos de cada sintaxe. Se você é novo na pesquisa completa de texto, uma olhada mais atenta ao que o motor de consulta faz pode ser uma escolha igualmente boa.

+ [Exemplos de consultas simples](search-query-simple-examples.md)
+ [Exemplos de consulta de sintaxe lucene para a construção de consultas avançadas](search-query-lucene-examples.md)
+ [Como funciona a pesquisa completa de texto em Azure Cognitive Search](search-lucene-query-architecture.md)git