---
title: Criar uma consulta simples
titleSuffix: Azure Cognitive Search
description: Aprenda por exemplo executando consultas com base na sintaxe simples para pesquisa completa de texto, pesquisa de filtro, pesquisa de geo, pesquisa facial contra um índice de Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 834e4fe8c7b3923f40a07c02c0310200db222308
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94697259"
---
# <a name="create-a-simple-query-in-azure-cognitive-search"></a>Crie uma consulta simples na Pesquisa Cognitiva Azure

Na Pesquisa Cognitiva Azure, a [simples sintaxe de consulta](query-simple-syntax.md) invoca o parser de consulta padrão para executar consultas completas de pesquisa de texto contra um índice. Este parser é rápido e lida com cenários comuns, incluindo pesquisa completa de texto, pesquisa filtrada e frontal, e geo-pesquisa. 

Neste artigo, usamos exemplos para ilustrar a simples sintaxe, povoando o `search=` parâmetro de uma operação de [Documentos de Busca.](/rest/api/searchservice/search-documents)

Uma sintaxe de consulta alternativa é [Full Lucene,](query-lucene-syntax.md)suportando estruturas de consulta mais complexas, como a pesquisa fuzzy e wildcard, que pode levar mais tempo para processar. Para obter mais informações e exemplos que demonstrem a sintaxe completa, consulte [utilizar a sintaxe lucene completa](search-query-lucene-examples.md).

## <a name="formulate-requests-in-postman"></a>Formular pedidos no Carteiro

Os exemplos a seguir aproveitam um índice de pesquisa nyc jobs composto por empregos disponíveis com base num conjunto de dados fornecido pela iniciativa [City of New York OpenData.](https://nycopendata.socrata.com/) Estes dados não devem ser considerados atuais ou completos. O índice encontra-se num serviço de caixa de areia fornecido pela Microsoft, o que significa que não precisa de uma subscrição Azure ou de Azure Cognitive Search para experimentar estas consultas.

O que precisa é do Carteiro ou de uma ferramenta equivalente para emitir pedido HTTP no GET. Para mais informações, consulte [Quickstart: Explore a API de Pesquisa Cognitiva Azure](search-get-started-rest.md).

### <a name="set-the-request-header"></a>Desa estação o cabeçalho do pedido

1. No cabeçalho do pedido, desa estale o **Tipo de Conteúdo** para `application/json` .

2. Adicione **uma chave api,** e coloque-a nesta cadeia: `252044BE3886FE4A8E3BAA4F595114BB` . Esta é uma chave de consulta para o serviço de pesquisa de caixas de areia que hospeda o índice NYC Jobs.

Depois de especificar o cabeçalho do pedido, pode reutilizá-lo para todas as consultas neste artigo, trocando apenas a **cadeia search=.** 

  :::image type="content" source="media/search-query-lucene-examples/postman-header.png" alt-text="Carteiro solicitam parâmetros definidos" border="false":::

### <a name="set-the-request-url"></a>Definir o URL de pedido

O pedido é um comando GET emparelhado com um URL que contém o ponto final de pesquisa cognitiva do Azure e a cadeia de pesquisa.

  :::image type="content" source="media/search-query-lucene-examples/postman-basic-url-request-elements.png" alt-text="Cabeçalho de pedido do carteiro GET" border="false":::

A composição url tem os seguintes elementos:

+ **`https://azs-playground.search.windows.net/`** é um serviço de pesquisa de caixas de areia mantido pela equipa de desenvolvimento de Pesquisa Cognitiva Azure. 
+ **`indexes/nycjobs/`** é o índice NYC Jobs na coleção de índices desse serviço. Tanto o nome de serviço como o índice são necessários no pedido.
+ **`docs`** é a recolha de documentos que contém todo o conteúdo pesmável. A chave api de consulta fornecida no cabeçalho do pedido apenas funciona em operações de leitura direcionadas para a recolha de documentos.
+ **`api-version=2020-06-30`** define a versão api, que é um parâmetro necessário em cada pedido.
+ **`search=*`** é a cadeia de consulta, que na consulta inicial é nula, devolvendo os primeiros 50 resultados (por padrão).

## <a name="send-your-first-query"></a>Enviar a primeira consulta

Como passo de verificação, cole o seguinte pedido no GET e clique em **Enviar.** Os resultados são devolvidos como documentos verbosos da JSON. São devolvidos documentos inteiros, o que lhe permite ver todos os campos e valores.

Cole este URL num cliente REST como um passo de validação e para ver a estrutura do documento.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*
  ```

A cadeia de consulta, **`search=*`** é uma pesquisa não especificada equivalente a pesquisa nula ou vazia. Não é especialmente útil, mas é a pesquisa mais simples que se pode fazer.

Opcionalmente, pode adicionar **`$count=true`** ao URL para devolver uma contagem dos documentos correspondentes aos critérios de pesquisa. Numa cadeia de pesquisa vazia, estes são todos os documentos do índice (cerca de 2800 no caso de NYC Jobs).

## <a name="how-to-invoke-simple-query-parsing"></a>Como invocar uma simples análise de consulta

Para consultas interativas, não é preciso especificar nada: simples é o padrão. Em código, se já invocasse **consultaType=completa** para sintaxe de consulta completa, poderia redefinir o padrão com **consultaType=simples**.

## <a name="example-1-field-scoped-query"></a>Exemplo 1: Consulta de âmbito de campo

Este primeiro exemplo não é específico da parser, mas conduzimos com ele para introduzir o primeiro conceito de consulta fundamental: a contenção. Este exemplo coloca em vista a execução de consultas e a resposta a apenas alguns campos específicos. Saber estruturar uma resposta JSON legível é importante quando a sua ferramenta é Carteiro ou Explorador de Pesquisa. 

Para a brevidade, a consulta visa apenas o campo *business_title* e especifica apenas os títulos de negócios são devolvidos. A sintaxe é **searchFields** para restringir a execução de consulta apenas ao campo business_title, e **selecione** para especificar quais os campos incluídos na resposta.

### <a name="partial-query-string"></a>Cadeia de consulta parcial

```http
searchFields=business_title&$select=business_title&search=*
```

Aqui está a mesma consulta com vários campos numa lista delimitada por vírgula.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

### <a name="full-url"></a>URL completo

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchFields=business_title&$select=business_title&search=*
```

A resposta a esta consulta deve ser semelhante à seguinte imagem.

  :::image type="content" source="media/search-query-lucene-examples/postman-sample-results.png" alt-text="Resposta da amostra do carteiro" border="false":::

Deve ter reparado na pontuação da pesquisa na resposta. Pontuações uniformes de 1 ocorrem quando não há classificação, quer porque a pesquisa não foi completamente pesquisa de texto, quer porque não foi aplicado nenhum critério. Para uma busca nula sem critérios, as linhas voltam por ordem arbitrária. Quando incluir critérios reais, verá as pontuações de pesquisa evoluir em valores significativos.

## <a name="example-2-look-up-by-id"></a>Exemplo 2: Procure por ID

Este exemplo é um pouco atípico, mas ao avaliar comportamentos de pesquisa, talvez queira inspecionar todo o conteúdo de um documento específico para entender por que foi incluído ou excluído dos resultados. Para devolver um único documento na sua totalidade, utilize uma [operação De procuração](/rest/api/searchservice/lookup-document) para passar no documento ID.

Todos os documentos têm um identificador único. Para experimentar a sintaxe para uma consulta de procura, primeiro devolva uma lista de IDs de documento para que possa encontrar um para usar. Para a NYC Jobs, os identificadores estão armazenados no `id` campo.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchFields=id&$select=id&search=*
```

O exemplo seguinte é uma consulta de procura que devolve um documento específico baseado em `id` "9E1E3AF9-0660-4E00-AF51-9B654925A2D5", que surgiu primeiro na resposta anterior. A seguinte consulta devolve todo o documento, e não apenas campos selecionados. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2020-06-30&$count=true&search=*
```

## <a name="example-3-filter-queries"></a>Exemplo 3: Consultas de filtro

[A sintaxe do filtro](./search-query-odata-filter.md) é uma expressão OData que pode utilizar com **pesquisa** ou por si só. Um filtro autónomo, sem parâmetro de pesquisa, é útil quando a expressão do filtro é capaz de qualificar totalmente documentos de interesse. Sem uma cadeia de consultas, não há análise lexical ou linguística, não há pontuação (todas as pontuações são 1) e nenhuma classificação. Note que o fio de pesquisa está vazio.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "job_id, business_title, agency, salary_range_from",
      "count": "true"
    }
```

Usado em conjunto, o filtro é aplicado primeiro a todo o índice, e depois a pesquisa é realizada nos resultados do filtro. Por conseguinte, os filtros podem ser uma técnica útil para melhorar o desempenho de consultas, uma vez que reduzem o conjunto de documentos necessários à consulta de pesquisa para processamento.

  :::image type="content" source="media/search-query-simple-examples/filtered-query.png" alt-text="Resposta de consulta de filtro" border="false":::

Se quiser experimentar isto no Carteiro usando GET, pode colar nesta cadeia:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,business_title,agency,salary_range_from&search=&$filter=salary_frequency eq 'Annual' and salary_range_from gt 90000
```

Outra forma poderosa de combinar filtro e pesquisa é através de **`search.ismatch*()`** uma expressão de filtro, onde você pode usar uma consulta de pesquisa dentro do filtro. Esta expressão de filtro usa um wildcard no *plano* para selecionar business_title incluindo o plano de prazo, planejador, planeamento, e assim por diante.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,business_title,agency&search=&$filter=search.ismatch('plan*', 'business_title', 'full', 'any')
```

Para obter mais informações sobre a função, consulte [search.ismatch em "Exemplos de filtro".](./search-query-odata-full-text-search-functions.md#examples)

## <a name="example-4-range-filters"></a>Exemplo 4: Filtros de alcance

A filtragem de gama é suportada através **`$filter`** de expressões para qualquer tipo de dado. Os exemplos seguintes pesquisam sobre campos numéricos e de cordas. 

Os tipos de dados são importantes nos filtros de alcance e funcionam melhor quando os dados numéricos estão em campos numéricos e dados de cordas em campos de cordas. Os dados numéricos nos campos de cordas não são adequados para intervalos porque as cordas numéricas não são comparáveis na Pesquisa Cognitiva Azure. 

Os exemplos a seguir são no formato POST para a legibilidade (gama numérica, seguido de gama de texto):

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency",
      "count": "true"
    }
```
  :::image type="content" source="media/search-query-simple-examples/rangefilternumeric.png" alt-text="Filtro de gama para gamas numéricas" border="false":::


```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title",
      "count": "true"
    }
```

  :::image type="content" source="media/search-query-simple-examples/rangefiltertext.png" alt-text="Filtro de gama para intervalos de texto" border="false":::

Também pode experimentá-las no Carteiro usando GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&search=&$filter=num_of_positions ge 5 and num_of_positions lt 10&$select=job_id, business_title, num_of_positions, agency&$orderby=agency&$count=true
```

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&search=&$filter=business_title ge 'A*' and business_title lt 'C*'&$select=job_id, business_title, agency&$orderby=business_title&$count=true
```

> [!NOTE]
> Encarar os intervalos de valores é um requisito comum de aplicação de pesquisa. Para obter mais informações e exemplos sobre a construção de filtros para estruturas de navegação faceta, consulte ["Filtro baseado numa gama" em *Como implementar a navegação facetada*](search-faceted-navigation.md#filter-based-on-a-range).

## <a name="example-5-geo-search"></a>Exemplo 5: Geo-pesquisa

O índice de amostra inclui um campo geo_location com coordenadas de latitude e longitude. Este exemplo utiliza a [função geo.distância](./search-query-odata-geo-spatial-functions.md#examples) que filtra em documentos dentro da circunferência de um ponto de partida, para uma distância arbitrária (em quilómetros) que fornece. Pode ajustar o último valor da consulta (4) para reduzir ou ampliar a área de superfície da consulta.

O exemplo a seguir está no formato POST para a legibilidade:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "job_id, business_title, work_location",
      "count": "true"
    }
```
Para resultados mais legíveis, os resultados da pesquisa são aparados para incluir uma identificação de trabalho, o título de emprego e o local de trabalho. As coordenadas iniciais foram obtidas a partir de um documento aleatório no índice (neste caso, para um local de trabalho em Staten Island.

Também pode experimentar isso no Carteiro usando GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=&$select=job_id, business_title, work_location&$filter=geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4
```

## <a name="example-6-search-precision"></a>Exemplo 6: Precisão de pesquisa

Consultas a prazo são termos únicos, talvez muitos deles, que são avaliados de forma independente. As consultas de frase são incluídas em aspas e avaliadas como uma cadeia verbatim. A precisão da partida é controlada pelos operadores e pesquisaMode.

Exemplo 1: **`&search=fire`**  devolve 150 resultados, onde todos os jogos contêm a palavra fogo algures no documento.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=fire
```

Exemplo 2: **`&search=fire department`** devolve resultados de 2002. Os fósforos são devolvidos para documentos que contenham bombeiros ou departamentos.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=fire department
```

Exemplo 3: **`&search="fire department"`** devolve 82 resultados. A cobertura da cadeia em aspas é uma pesquisa verbatim em ambos os termos, e os jogos são encontrados em termos simbólicos no índice constituído pelos termos combinados. Isto explica porque uma pesquisa como **`search=+fire +department`** a não é equivalente. Ambos os termos são necessários, mas são digitalizados de forma independente. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search="fire department"
```

## <a name="example-7-booleans-with-searchmode"></a>Exemplo 7: Booleans com searchMode

A sintaxe simples suporta os operadores booleanos sob a forma de caracteres `+, -, |` (). O parâmetro searchMode informa as trocas entre precisão e `searchMode=any` recordação, com a recuperação favorável (correspondente a qualquer critério qualifica um documento para o conjunto de resultados), e `searchMode=all` favorecendo a precisão (todos os critérios devem ser correspondidos). O padrão é `searchMode=any` , o que pode ser confuso se estiver a empilhar uma consulta com vários operadores e a obter resultados mais amplos em vez de resultados mais estreitos. Isto é particularmente verdade com o NÃO, onde os resultados incluem todos os documentos "não contendo" um termo específico.

Utilizando a pesquisa padrãoMode (qualquer), são devolvidos 2800 documentos: os que contêm o termo multi-partes "corpo de bombeiros", além de todos os documentos que não têm o termo "Metrotech Center".

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```

  :::image type="content" source="media/search-query-simple-examples/searchmodeany.png" alt-text="modo de pesquisa qualquer" border="false":::

Alteração da pesquisaMode para `all` impor um efeito cumulativo em critérios e devolve um conjunto de resultados menor - 21 documentos - composto por documentos que contenham toda a frase "corpo de bombeiros", menos os empregos no endereço do Metrotech Center.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```
  :::image type="content" source="media/search-query-simple-examples/searchmodeall.png" alt-text="modo de pesquisa todos" border="false":::

## <a name="example-8-structuring-results"></a>Exemplo 8: Resultados estruturantes

Vários parâmetros controlam quais os campos que estão nos resultados da pesquisa, o número de documentos devolvidos em cada lote e ordenar a ordem. Este exemplo reaparece em alguns dos exemplos anteriores, limitando os resultados a campos específicos utilizando a **declaração de $select** e critérios de pesquisa verbatim, devolvendo 82 jogos 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
Anexado ao exemplo anterior, pode classificar por título. Este tipo funciona porque civil_service_title é *ordenada* no índice.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

Os resultados da paging são implementados utilizando o parâmetro **$top,** neste caso devolvendo os 5 principais documentos:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

Para obter os próximos 5, salte o primeiro lote:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>Passos seguintes
Tente especificar consultas no seu código. Os seguintes links explicam como configurar consultas de pesquisa tanto para .NET como para a API REST utilizando a sintaxe simples padrão.

* [Consultar o seu índice utilizando o .NET SDK](./search-get-started-dotnet.md)
* [Consultar o seu índice utilizando a API REST](./search-get-started-powershell.md)

Referência de sintaxe adicional, arquitetura de consulta e exemplos podem ser encontrados nos seguintes links:

+ [Exemplos de consulta de sintaxe lucene para a construção de consultas avançadas](search-query-lucene-examples.md)
+ [Como funciona a pesquisa em texto completo no Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Sintaxe de consulta simples](/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Consulta lucene completa](/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Sintaxe filtro e ordem](/rest/api/searchservice/odata-expression-syntax-for-azure-search)