---
title: Exemplos de consulta usando a sintaxe de pesquisa "simples"-Azure Search
description: Exemplos de consulta simples para pesquisa de texto completo, pesquisa de filtro, pesquisa geográfica, pesquisa facetada e outras cadeias de caracteres de consulta usadas para consultar um índice de Azure Search.
author: HeidiSteen
manager: nitinme
tags: Simple query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: df84686e512db90351d5a9815706890bce49848b
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647621"
---
# <a name="query-examples-using-the-simple-search-syntax-in-azure-search"></a>Exemplos de consulta usando a sintaxe de pesquisa "simples" no Azure Search

A [sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) invoca o analisador de consulta padrão para executar consultas de pesquisa de texto completo em um índice de Azure Search. O simples analisador de consultas é rápido e manipula cenários comuns no Azure Search, incluindo pesquisa de texto completo, pesquisa filtrada e facetada e pesquisa geográfica. Neste artigo, percorra os exemplos demonstrando as operações de consulta disponíveis ao usar a sintaxe simples.

A sintaxe de consulta alternativa é o [Lucene completo](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), dando suporte a estruturas de consulta mais complexas, como pesquisa difusa e curinga, o que pode levar mais tempo para ser processado. Para obter mais informações e exemplos que demonstram a sintaxe completa, consulte [exemplos de consulta de sintaxe do Lucene](search-query-lucene-examples.md).

## <a name="formulate-requests-in-postman"></a>Formular solicitações no postmaster

Os exemplos a seguir aproveitam um índice de pesquisa de trabalhos do NYC que consiste em trabalhos disponíveis com base em um conjunto de conjuntos fornecido pela [cidade da iniciativa de OpenData de Nova York](https://nycopendata.socrata.com/) . Esses dados não devem ser considerados atuais ou completos. O índice está em um serviço de área restrita fornecido pela Microsoft, o que significa que você não precisa de uma assinatura do Azure ou Azure Search para tentar essas consultas.

O que você precisa é o postmaster ou uma ferramenta equivalente para emitir a solicitação HTTP em GET. Para obter mais informações, consulte [explorar com clientes REST](search-get-started-postman.md).

### <a name="set-the-request-header"></a>Definir o cabeçalho da solicitação

1. No cabeçalho da solicitação, defina **Content-Type** como `application/json`.

2. Adicione uma **chave de API**e defina-a para esta cadeia de `252044BE3886FE4A8E3BAA4F595114BB`caracteres:. Essa é uma chave de consulta para o serviço de pesquisa de área restrita que hospeda o índice de trabalhos do NYC.

Depois de especificar o cabeçalho da solicitação, você pode reutilizá-lo para todas as consultas neste artigo, alternando apenas a cadeia de caracteres **Search =** . 

  ![Cabeçalho de pedido do Postman](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>Definir a URL da solicitação

A solicitação é um comando GET emparelhado com uma URL que contém o ponto de extremidade Azure Search e a cadeia de caracteres de pesquisa.

  ![Cabeçalho de pedido do Postman](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

A composição de URL tem os seguintes elementos:

+ **`https://azs-playground.search.windows.net/`** o é um serviço de pesquisa de área restrita mantido pela equipe de desenvolvimento Azure Search. 
+ **`indexes/nycjobs/`** é o índice de trabalhos do NYC na coleção de índices desse serviço. O nome do serviço e o índice são necessários na solicitação.
+ **`docs`** é a coleção de documentos que contém todo o conteúdo pesquisável. A chave de API de consulta fornecida no cabeçalho da solicitação só funciona em operações de leitura direcionando a coleção de documentos.
+ **`api-version=2019-05-06`** define a versão de API, que é um parâmetro necessário em cada solicitação.
+ **`search=*`** é a cadeia de caracteres de consulta, que na consulta inicial é nula, retornando os primeiros 50 resultados (por padrão).

## <a name="send-your-first-query"></a>Enviar sua primeira consulta

Como uma etapa de verificação, Cole a seguinte solicitação em GET e clique em **Enviar**. Os resultados são retornados como documentos JSON detalhados. Documentos inteiros são retornados, o que permite que você veja todos os campos e todos os valores.

Cole essa URL em um cliente REST como uma etapa de validação e exiba a estrutura do documento.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=*
  ```

A cadeia de caracteres **`search=*`** de consulta,, é uma pesquisa não especificada equivalente a uma pesquisa nula ou vazia. Isso não é especialmente útil, mas é a pesquisa mais simples que você pode fazer.

Opcionalmente, você pode adicionar **`$count=true`** à URL para retornar uma contagem dos documentos que correspondem aos critérios de pesquisa. Em uma cadeia de caracteres de pesquisa vazia, esses são todos os documentos no índice (cerca de 2800 no caso de trabalhos do NYC).

## <a name="how-to-invoke-simple-query-parsing"></a>Como invocar a análise de consulta simples

Para consultas interativas, você não precisa especificar nada: simples é o padrão. No código, se você tiver chamado anteriormente **QueryType = Full** para obter a sintaxe de consulta completa, poderá redefinir o padrão com **QueryType = Simple**.

## <a name="example-1-field-scoped-query"></a>Exemplo 1: Consulta com escopo de campo

Este primeiro exemplo não é específico do analisador, mas nós o conduzimos a apresentar o primeiro conceito fundamental de consulta: confinamento. Este exemplo abrange a execução da consulta e a resposta a apenas alguns campos específicos. Saber como estruturar uma resposta JSON legível é importante quando sua ferramenta é o postmaster ou o Search Explorer. 

Para resumir, a consulta destina-se apenas ao campo *business_title* e especifica que apenas títulos de negócios são retornados. A sintaxe é **searchFields** para restringir a execução da consulta apenas ao campo business_title e **selecione** para especificar quais campos serão incluídos na resposta.

### <a name="partial-query-string"></a>Cadeia de caracteres de consulta parcial

```http
searchFields=business_title&$select=business_title&search=*
```

Aqui está a mesma consulta com vários campos em uma lista delimitada por vírgulas.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

### <a name="full-url"></a>URL completa

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchFields=business_title&$select=business_title&search=*
```

A resposta para essa consulta deve ser semelhante à captura de tela a seguir.

  ![Resposta de exemplo do postmaster](media/search-query-lucene-examples/postman-sample-results.png)

Talvez você tenha notado a pontuação de pesquisa na resposta. Pontuações uniformes de 1 ocorrem quando não há nenhuma classificação, porque a pesquisa não era uma pesquisa de texto completo ou porque nenhum critério foi aplicado. Para pesquisa nula sem critérios, as linhas retornam em ordem arbitrária. Ao incluir os critérios reais, você verá que as pontuações de pesquisa evoluem em valores significativos.

## <a name="example-2-look-up-by-id"></a>Exemplo 2: Pesquisar por ID

Este exemplo é um pouco atípicos, mas ao avaliar os comportamentos de pesquisa, talvez você queira inspecionar todo o conteúdo de um documento específico para entender por que ele foi incluído ou excluído dos resultados. Para retornar um único documento em sua totalidade, use uma [operação de pesquisa](https://docs.microsoft.com/rest/api/searchservice/lookup-document) para passar a ID do documento.

Todos os documentos têm um identificador exclusivo. Para testar a sintaxe de uma consulta de pesquisa, primeiro retorne uma lista de IDs de documento para que você possa encontrar uma para usar. Para trabalhos do NYC, os identificadores são armazenados no `id` campo.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchFields=id&$select=id&search=*
```

O exemplo a seguir é uma consulta de pesquisa que retorna um documento `id` específico com base em "9E1E3AF9-0660-4E00-AF51-9B654925A2D5", que apareceu primeiro na resposta anterior. A consulta a seguir retorna o documento inteiro, não apenas os campos selecionados. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2019-05-06&$count=true&search=*
```

## <a name="example-3-filter-queries"></a>Exemplo 3: Filtrar consultas

A [sintaxe de filtro](https://docs.microsoft.com/azure/search/search-query-odata-filter) é uma expressão OData que você pode usar com a **pesquisa** ou por si só. Um filtro autônomo, sem um parâmetro de pesquisa, é útil quando a expressão de filtro é capaz de qualificar totalmente os documentos de interesse. Sem uma cadeia de caracteres de consulta, não há nenhuma análise léxica ou linguística, sem pontuação (todas as pontuações são 1) e nenhuma classificação. Observe que a cadeia de caracteres de pesquisa está vazia.

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "select=job_id, business_title, agency, salary_range_from",
      "count": "true"
    }
```

Usado em conjunto, o filtro é aplicado primeiro ao índice inteiro e, em seguida, a pesquisa é executada nos resultados do filtro. Por conseguinte, os filtros podem ser uma técnica útil para melhorar o desempenho de consultas, uma vez que reduzem o conjunto de documentos necessários à consulta de pesquisa para processamento.

  ![Filtrar resposta de consulta](media/search-query-simple-examples/filtered-query.png)

Se você quiser experimentar isso no postmaster usando GET, poderá colar nesta cadeia de caracteres:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,business_title,agency,salary_range_from&search=&$filter=salary_frequency eq 'Annual' and salary_range_from gt 90000
```

Outra maneira eficiente de combinar filtro e pesquisa é por **`search.ismatch*()`** meio de uma expressão de filtro, na qual você pode usar uma consulta de pesquisa dentro do filtro. Essa expressão de filtro usa um curinga no *plano* para selecionar business_title, incluindo o termo plano, planejador, planejamento e assim por diante.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,business_title,agency&search=&$filter=search.ismatch('plan*', 'business_title', 'full', 'any')
```

Para obter mais informações sobre a função, consulte [Search. IsMatch em "Filter examples"](https://docs.microsoft.com/azure/search/search-query-odata-full-text-search-functions#examples).

## <a name="example-4-range-filters"></a>Exemplo 4: Filtros de intervalo

A filtragem de intervalo é **`$filter`** suportada por meio de expressões para qualquer tipo de dados. Os exemplos a seguir pesquisam em campos numéricos e de cadeia de caracteres. 

Os tipos de dados são importantes em filtros de intervalo e funcionam melhor quando dados numéricos estão em campos numéricos e dados de cadeia de caracteres em campos de cadeia de caracteres. Dados numéricos em campos de cadeia de caracteres não são adequados para intervalos porque cadeias numéricas não são comparáveis em Azure Search. 

Os exemplos a seguir estão no formato de POSTAgem para legibilidade (intervalo numérico, seguido pelo intervalo de texto):

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency",
      "count": "true"
    }
```
  ![Filtro de intervalo para intervalos numéricos](media/search-query-simple-examples/rangefilternumeric.png)


```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title",
      "count": "true"
    }
```

  ![Filtro de intervalo para intervalos de texto](media/search-query-simple-examples/rangefiltertext.png)

Você também pode experimentá-los no postmaster usando GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&search=&$filter=num_of_positions ge 5 and num_of_positions lt 10&$select=job_id, business_title, num_of_positions, agency&$orderby=agency&$count=true
```

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&search=&$filter=business_title ge 'A*' and business_title lt 'C*'&$select=job_id, business_title, agency&$orderby=business_title&$count=true
```

> [!NOTE]
> A facetação de intervalos de valores é um requisito de aplicativo de pesquisa comum. Para obter mais informações e exemplos sobre a criação de filtros para estruturas de navegação de faceta, consulte ["filtrar com base em um intervalo" em *como implementar a navegação facetada*](search-faceted-navigation.md#filter-based-on-a-range).

## <a name="example-5-geo-search"></a>Exemplo 5: Pesquisa geográfica

O índice de exemplo inclui um campo geo_location com coordenadas de latitude e longitude. Este exemplo usa a [função geográfico. distance](https://docs.microsoft.com/azure/search/search-query-odata-geo-spatial-functions#examples) que filtra em documentos dentro da circunferência de um ponto inicial, até uma distância arbitrária (em quilômetros) que você fornece. Você pode ajustar o último valor na consulta (4) para reduzir ou aumentar a área da superfície da consulta.

O exemplo a seguir está no formato de POSTAgem para facilitar a leitura:

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "job_id, business_title, work_location",
      "count": "true"
    }
```
Para obter resultados mais legíveis, os resultados da pesquisa são cortados para incluir uma ID do trabalho, um cargo e o local de trabalho. As coordenadas iniciais foram obtidas de um documento aleatório no índice (nesse caso, para um local de trabalho na Ilha Staten.

Você também pode experimentar isso no postmaster usando GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=&$select=job_id, business_title, work_location&$filter=geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4
```

## <a name="example-6-search-precision"></a>Exemplo 6: Precisão da pesquisa

As consultas de termo são termos únicos, talvez muitas delas, que são avaliadas de forma independente. As consultas de frase são colocadas entre aspas e avaliadas como uma cadeia de caracteres textual. A precisão da correspondência é controlada por operadores e searchmode.

Exemplo 1: **`&search=fire`** retorna 150 resultados, onde todas as correspondências contêm a palavra Fire em algum lugar no documento.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=fire
```

Exemplo 2: **`&search=fire department`** retorna 2002 resultados. As correspondências são retornadas para documentos que contêm fogo ou departamento.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=fire department
```

Exemplo 3: **`&search="fire department"`** retorna 82 resultados. Colocar a cadeia de caracteres entre aspas é uma pesquisa textual em ambos os termos e as correspondências são encontradas em termos de token no índice que consiste nos termos combinados. Isso explica por que uma pesquisa **`search=+fire +department`** como não é equivalente. Ambos os termos são necessários, mas são verificados de forma independente. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search="fire department"
```

## <a name="example-7-booleans-with-searchmode"></a>Exemplo 7: Boolianos com searchmode

A sintaxe simples dá suporte a operadores boolianos na forma`+, -, |`de caracteres (). O parâmetro searchmode informa as compensações entre Precision e RECALL, `searchMode=any` com favorecer a RECALL (a correspondência em qualquer critério qualifica um documento para o conjunto de `searchMode=all` resultados) e favorecer a precisão (todos os critérios devem ser correspondidos). O padrão é `searchMode=any`, que pode ser confuso se você estiver empilhando uma consulta com vários operadores e obtendo resultados mais amplos em vez de mais estreitos. Isso é particularmente verdadeiro, pois os resultados incluem todos os documentos "não contendo" um termo específico.

Usando o searchmode padrão (any), 2800 documentos são retornados: aqueles que contêm o termo de várias partes "departamento de incêndio", além de todos os documentos que não têm o termo "MetroTech Center".

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```

  ![Pesquisar no modo de pesquisa](media/search-query-simple-examples/searchmodeany.png)

Alterar searchmode para `all` impõe um efeito cumulativo em critérios e retorna um conjunto de resultados menores-21 documentos – consistindo em documentos contendo a frase inteira "departamento de incêndio", menos esses trabalhos no endereço do MetroTech Center.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```
  ![modo de pesquisa tudo](media/search-query-simple-examples/searchmodeall.png)

## <a name="example-8-structuring-results"></a>Exemplo 8: Estruturando resultados

Vários parâmetros controlam quais campos estão nos resultados da pesquisa, o número de documentos retornados em cada lote e a ordem de classificação. Este exemplo retona alguns dos exemplos anteriores, limitando os resultados a campos específicos usando a instrução **$Select** e critérios de pesquisa textual, retornando 82 correspondências 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
Acrescentado no exemplo anterior, você pode classificar por título. Essa classificação funciona porque civil_service_title é *classificável* no índice.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

Os resultados da paginação são implementados usando o parâmetro **$Top** , neste caso, retornando os 5 principais documentos:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

Para obter os próximos 5, ignore o primeiro lote:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>Passos seguintes
Tente especificar consultas em seu código. Os links a seguir explicam como configurar consultas de pesquisa para o .NET e a API REST usando a sintaxe simples padrão.

* [Consultar seu índice de Azure Search usando o SDK do .NET](search-query-dotnet.md)
* [Consultar seu índice de Azure Search usando a API REST](search-create-index-rest-api.md)

Referência de sintaxe adicional, arquitetura de consulta e exemplos podem ser encontrados nos seguintes links:

+ [Exemplos de consulta de sintaxe Lucene para criar consultas avançadas](search-query-lucene-examples.md)
+ [Como funciona a pesquisa de texto completo no Azure Search](search-lucene-query-architecture.md)
+ [Sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Consulta Lucene completa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Sintaxe de Filter e OrderBy](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)
