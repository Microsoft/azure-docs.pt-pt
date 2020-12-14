---
title: Use sintaxe de consulta lucene simples
titleSuffix: Azure Cognitive Search
description: Aprenda por exemplo executando consultas com base na sintaxe simples para pesquisa completa de texto, pesquisa de filtro, pesquisa de geo, pesquisa facial contra um índice de Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/12/2020
ms.openlocfilehash: ff9495e37a499b5502d8f8ced79b69608fa9552a
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401751"
---
# <a name="use-the-simple-search-syntax-in-azure-cognitive-search"></a>Use a sintaxe de pesquisa "simples" na Pesquisa Cognitiva Azure

Na Pesquisa Cognitiva Azure, a [simples sintaxe de consulta](query-simple-syntax.md) invoca o parser de consulta padrão para pesquisa completa de texto. O analisador é rápido e lida com cenários comuns, incluindo pesquisa completa de texto, pesquisa filtrada e frontal, e pesquisa de prefixos. Este artigo utiliza exemplos para ilustrar o uso simples da sintaxe num pedido [de Documentos de Busca (REST API).](/rest/api/searchservice/search-documents)

> [!NOTE]
> Uma sintaxe de consulta alternativa é [Full Lucene,](query-lucene-syntax.md)suportando estruturas de consulta mais complexas, tais como pesquisa de fuzzy e wildcard. Para obter mais informações e exemplos, consulte [utilizar a sintaxe lucene completa.](search-query-lucene-examples.md)

## <a name="nyc-jobs-examples"></a>Exemplos de empregos de NYC

Os exemplos a seguir aproveitam o índice de [pesquisa nyc jobs,](https://azjobsdemo.azurewebsites.net/) composto por empregos disponíveis com base num conjunto de dados fornecido pela [City of New York OpenData Initiative](https://nycopendata.socrata.com/). Estes dados não devem ser considerados atuais ou completos. O índice encontra-se num serviço de caixa de areia fornecido pela Microsoft, o que significa que não precisa de uma subscrição Azure ou de Azure Cognitive Search para experimentar estas consultas.

O que precisa é do Carteiro ou de uma ferramenta equivalente para emitir pedido HTTP no GET ou POST. Se não estiver familiarizado com estas ferramentas, consulte [Quickstart: Explore a AZure Cognitive Search REST API](search-get-started-rest.md).

## <a name="set-up-the-request"></a>Configurar o pedido

1. Os cabeçalhos de pedido devem ter os seguintes valores:

   | Chave | Valor |
   |-----|-------|
   | Content-Type | `application/json`|
   | api-chave  | `252044BE3886FE4A8E3BAA4F595114BB` </br> (esta é a chave API de consulta real para o serviço de pesquisa de caixas de areia que acolhe o índice NYC Jobs) |

1. Deslome o verbo para **`GET`** .

1. Desa estação o URL para **`https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&search=*&$count=true`** . 

   + A recolha de documentos no índice contém todo o conteúdo pesmável. A chave api de consulta fornecida no cabeçalho do pedido apenas funciona para operações de leitura direcionadas para a recolha de documentos.

   + **`$count=true`** devolve uma contagem dos documentos correspondentes aos critérios de pesquisa. Numa cadeia de pesquisa vazia, a contagem será de todos os documentos do índice (cerca de 2558 no caso de NYC Jobs).

   + **`search=*`** é uma consulta não especificada, equivalente a uma busca nula ou vazia. Não é especialmente útil, mas é a pesquisa mais simples que pode fazer, e mostra todos os campos recuperáveis no índice, com todos os valores.

1. Como passo de verificação, cole o seguinte pedido no GET e clique em **Enviar.** Os resultados são devolvidos como documentos verbosos da JSON.

   ```http
   https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*&queryType=full
   ```

### <a name="how-to-invoke-simple-query-parsing"></a>Como invocar uma simples análise de consulta

Para consultas interativas, não é preciso especificar nada: simples é o padrão. Em código, se for previamente **`queryType=full`** invocado, pode redefinir o padrão com **`queryType=simple`** .

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "queryType": "simple"
}
```

## <a name="example-1-full-text-search-on-specific-fields"></a>Exemplo 1: Pesquisa completa de texto em campos específicos

Este primeiro exemplo não é específico da parser, mas conduzimos com ele para introduzir o primeiro conceito de consulta fundamental: a contenção. Este exemplo limita tanto a execução de consultas como a resposta a apenas alguns campos específicos. Saber estruturar uma resposta JSON legível é importante quando a sua ferramenta é Carteiro ou Explorador de Pesquisa. 

Esta consulta visa apenas *business_title* em **`searchFields`** , especificando através do parâmetro o mesmo **`select`** campo na resposta.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

A resposta a esta consulta deve ser semelhante à seguinte imagem.

  :::image type="content" source="media/search-query-lucene-examples/postman-sample-results.png" alt-text="Resposta da amostra do carteiro" border="false":::

Deve ter reparado na pontuação da pesquisa na resposta. Pontuações uniformes de **1** ocorrem quando não há classificação, quer porque a pesquisa não foi completamente pesquisa de texto, quer porque não foram fornecidos critérios. Para uma busca vazia, as filas voltam por ordem arbitrária. Quando incluir critérios reais, verá as pontuações de pesquisa evoluir em valores significativos.

## <a name="example-2-look-up-by-id"></a>Exemplo 2: Procure por ID

Quando devolve os resultados da pesquisa numa consulta, um passo lógico seguinte é fornecer uma página de detalhes que inclua mais campos do documento. Este exemplo mostra-lhe como devolver um único documento utilizando uma [operação De procuração](/rest/api/searchservice/lookup-document) para passar no documento ID.

Todos os documentos têm um identificador único. Para experimentar a sintaxe para uma consulta de procura, primeiro devolva uma lista de IDs de documento para que possa encontrar um para usar. Para a NYC Jobs, os identificadores estão armazenados no `id` campo.

```http
GET /indexes/nycjobs/docs?api-version=2020-06-30&search=*&$select=id&$count=true
```

Em seguida, recupere um documento da coleção com base no `id` "9E1E3AF9-0660-4E00-AF51-9B654925A2D5", que surgiu primeiro na resposta anterior. A seguinte consulta devolve todos os campos recuperáveis para todo o documento.

```http
GET /indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2020-06-30
```

## <a name="example-3-filter-queries"></a>Exemplo 3: Consultas de filtro

[A sintaxe do filtro](./search-query-odata-filter.md) é uma expressão OData que pode utilizar por si só ou com **`search`** . Um filtro autónomo, sem parâmetro de pesquisa, é útil quando a expressão do filtro é capaz de qualificar totalmente documentos de interesse. Sem uma cadeia de consultas, não há análise lexical ou linguística, não há pontuação (todas as pontuações são 1) e nenhuma classificação. Note que o fio de pesquisa está vazio.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "job_id, business_title, agency, salary_range_from"
    }
```

Usado em conjunto, o filtro é aplicado primeiro a todo o índice, e depois a pesquisa é realizada nos resultados do filtro. Por conseguinte, os filtros podem ser uma técnica útil para melhorar o desempenho de consultas, uma vez que reduzem o conjunto de documentos necessários à consulta de pesquisa para processamento.

  :::image type="content" source="media/search-query-simple-examples/filtered-query.png" alt-text="Resposta de consulta de filtro" border="false":::

Outra forma poderosa de combinar filtro e pesquisa é através de **`search.ismatch*()`** uma expressão de filtro, onde você pode usar uma consulta de pesquisa dentro do filtro. Esta expressão de filtro usa um wildcard no *plano* para selecionar business_title incluindo o plano de prazo, planejador, planeamento, e assim por diante.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "search.ismatch('plan*', 'business_title', 'full', 'any')",
      "select": "job_id, business_title, agency, salary_range_from"
    }
```

Para obter mais informações sobre a função, consulte [search.ismatch em "Exemplos de filtro".](./search-query-odata-full-text-search-functions.md#examples)

## <a name="example-4-range-filters"></a>Exemplo 4: Filtros de alcance

A filtragem de gama é suportada através **`$filter`** de expressões para qualquer tipo de dado. Os exemplos seguintes pesquisam sobre campos numéricos e de cordas. 

Os tipos de dados são importantes nos filtros de alcance e funcionam melhor quando os dados numéricos estão em campos numéricos e dados de cordas em campos de cordas. Os dados numéricos nos campos de cordas não são adequados para intervalos porque as cordas numéricas não são comparáveis na Pesquisa Cognitiva Azure.

A seguinte consulta é uma gama numérica:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency"
    }
```
A resposta a esta consulta deve ser semelhante à seguinte imagem.

  :::image type="content" source="media/search-query-simple-examples/rangefilternumeric.png" alt-text="Filtro de gama para gamas numéricas" border="false":::

Nesta consulta, o alcance é sobre um campo de cordas (business_title):

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title"
    }
```

A resposta a esta consulta deve ser semelhante à seguinte imagem.

  :::image type="content" source="media/search-query-simple-examples/rangefiltertext.png" alt-text="Filtro de gama para intervalos de texto" border="false":::

> [!NOTE]
> Encarar os intervalos de valores é um requisito comum de aplicação de pesquisa. Para obter mais informações e exemplos, consulte [Como construir um filtro de faceta.](search-filters-facets.md)

## <a name="example-5-geo-search"></a>Exemplo 5: Geo-pesquisa

O índice de amostra inclui um campo geo_location com coordenadas de latitude e longitude. Este exemplo utiliza a [função geo.distância](search-query-odata-geo-spatial-functions.md#examples) que filtra em documentos dentro da circunferência de um ponto de partida, para uma distância arbitrária (em quilómetros) que fornece. Pode ajustar o último valor da consulta (4) para reduzir ou ampliar a área de superfície da consulta.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "business_title, work_location"
    }
```

Para resultados mais legíveis, os resultados da pesquisa são aparados para incluir o título de emprego e a localização do trabalho. As coordenadas iniciais foram obtidas a partir de um documento aleatório no índice (neste caso, para um local de trabalho em Staten Island.

  :::image type="content" source="media/search-query-simple-examples/geo-search.png" alt-text="Mapa da ilha de Staten" border="false":::

## <a name="example-6-search-precision"></a>Exemplo 6: Precisão de pesquisa

Consultas a prazo são termos únicos, talvez muitos deles, que são avaliados de forma independente. As consultas de frase são incluídas em aspas e avaliadas como uma cadeia verbatim. A precisão da partida é controlada pelos operadores e pesquisaMode.

Exemplo 1: `search=fire`  corresponde a 140 resultados, onde todos os jogos contêm a palavra fogo algures no documento.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "fire"
    }
```

Exemplo 2: `search=fire department` devolve resultados de 2002. Os fósforos são devolvidos para documentos que contenham bombeiros ou departamentos.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "fire department"
    }
```

Exemplo 3: `search="fire department"` devolve 77 resultados. A cobertura da cadeia em aspas cria uma procura de frases composta por ambos os termos, e os fósforos são encontrados em termos simbólicos no índice constituído pelos termos combinados. Isto explica porque uma pesquisa como `search=+fire +department` a não é equivalente. Ambos os termos são necessários, mas são digitalizados de forma independente. 

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
    "count": true,
    "search": "\"fire department\""
    }
```

> [!Note]
> Como uma consulta de frase é especificada através de aspas, este exemplo adiciona um personagem de fuga `\` () para preservar a sintaxe.

## <a name="example-7-booleans-with-searchmode"></a>Exemplo 7: Booleans com searchMode

A sintaxe simples suporta os operadores booleanos sob a forma de caracteres `+, -, |` (). O parâmetro searchMode informa as trocas entre precisão e **`searchMode=any`** recordação, com a recuperação favorável (correspondente a qualquer critério qualifica um documento para o conjunto de resultados), e **`searchMode=all`** favorecendo a precisão (todos os critérios devem ser correspondidos). 

O padrão é **`searchMode=any`** , o que pode ser confuso se estiver a empilhar uma consulta com vários operadores e a obter resultados mais amplos em vez de resultados mais estreitos. Isto é particularmente verdade com o NÃO, onde os resultados incluem todos os documentos "não contendo" um termo específico.

Utilizando a pesquisa padrãoMode (qualquer), são devolvidos 2800 documentos: os que contêm a frase "corpo de bombeiros", além de todos os documentos que não têm a frase "Metrotech Center".

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"-\"Metrotech Center\"",
      "searchMode": "any"
    }
```

A resposta a esta consulta deve ser semelhante à seguinte imagem.

  :::image type="content" source="media/search-query-simple-examples/searchmodeany.png" alt-text="modo de pesquisa qualquer" border="false":::

A alteração para **`searchMode=all`** impor um efeito cumulativo nos critérios e a devolução de um conjunto de resultados menores - 21 documentos - consiste em documentos que contenham toda a frase "corpo de bombeiros", menos os postos de trabalho no endereço do Metrotech Center.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"-\"Metrotech Center\"",
      "searchMode": "all"
    }
```

  :::image type="content" source="media/search-query-simple-examples/searchmodeall.png" alt-text="modo de pesquisa todos" border="false":::

## <a name="example-8-structuring-results"></a>Exemplo 8: Resultados estruturantes

Vários parâmetros controlam quais os campos que estão nos resultados da pesquisa, o número de documentos devolvidos em cada lote e ordenar a ordem. Este exemplo reaparece em alguns dos exemplos anteriores, limitando os resultados a campos específicos utilizando os critérios de pesquisa de **`$select`** declaração e verbatim, devolvendo 82 partidas.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description"
    }
```

Anexado ao exemplo anterior, pode classificar por título. Este tipo funciona porque civil_service_title é *ordenada* no índice.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title"
    }
```

Os resultados da paging são implementados usando o **`$top`** parâmetro, neste caso devolvendo os 5 principais documentos:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title",
      "top": "5"
    }
```

Para obter os próximos 5, salte o primeiro lote:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title",
      "top": "5",
      "skip": "5"
    }
```

## <a name="next-steps"></a>Passos seguintes

Tente especificar consultas em código. Os seguintes links explicam como configurar consultas de pesquisa usando os Azure SDKs.

+ [Consultar o seu índice utilizando o .NET SDK](search-get-started-dotnet.md)
+ [Consultar o seu índice usando o Python SDK](search-get-started-python.md)
+ [Consultar o seu índice utilizando o JavaScript SDK](search-get-started-javascript.md)

Referência de sintaxe adicional, arquitetura de consulta e exemplos podem ser encontrados nos seguintes links:

+ [Exemplos de consulta de sintaxe lucene para a construção de consultas avançadas](search-query-lucene-examples.md)
+ [Como funciona a pesquisa em texto completo no Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Sintaxe de consulta simples](query-simple-syntax.md)
+ [Sintaxe de consulta Lucene completo](query-lucene-syntax.md)
+ [Sintaxe de filtro](search-query-odata-filter.md)