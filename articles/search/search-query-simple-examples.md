---
title: Criar uma consulta simples
titleSuffix: Azure Cognitive Search
description: Aprenda por exemplo executando consultas baseadas na simples sintaxe para pesquisa completa de texto, pesquisa de filtros, pesquisa de geo-pesquisa, pesquisa enfrentada contra um índice de Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3a801af7b97954510139a009a6d1344b281cf056
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81261810"
---
# <a name="create-a-simple-query-in-azure-cognitive-search"></a>Criar uma consulta simples na Pesquisa Cognitiva Azure

Na Pesquisa Cognitiva Azure, a [simples sintaxe](query-simple-syntax.md) de consulta invoca o parser de consulta padrão para executar consultas completas de pesquisa de texto contra um índice. Este parser é rápido e lida com cenários comuns, incluindo pesquisa completa de texto, pesquisa filtrada e facetada, e geo-pesquisa. 

Neste artigo, utilizamos exemplos para ilustrar a simples `search=` sintaxe, povoando o parâmetro de uma operação de Documentos de [Busca.](https://docs.microsoft.com/rest/api/searchservice/search-documents)

Uma sintaxe de consulta alternativa é [Full Lucene,](query-lucene-syntax.md)suportando estruturas de consulta mais complexas, como a pesquisa fuzzy e wildcard, que pode levar mais tempo para processar. Para mais informações e exemplos que demonstrem a completa [sintaxe, consulte Use a sintaxe lucene completa](search-query-lucene-examples.md).

## <a name="formulate-requests-in-postman"></a>Formular pedidos no Carteiro

Os exemplos seguintes alavancam um índice de pesquisa de NYC Jobs composto por empregos disponíveis com base num conjunto de dados fornecido pela iniciativa [City of New York OpenData.](https://nycopendata.socrata.com/) Estes dados não devem ser considerados atuais ou completos. O índice encontra-se num serviço de caixa de areia fornecido pela Microsoft, o que significa que não precisa de uma subscrição Azure ou de Pesquisa Cognitiva Azure para experimentar estas consultas.

O que precisa é do Carteiro ou de uma ferramenta equivalente para emitir o pedido http no GET. Para mais informações, consulte [Quickstart: Explore a API de pesquisa cognitiva azure usando o Carteiro](search-get-started-postman.md).

### <a name="set-the-request-header"></a>Detete o cabeçalho de pedido

1. No cabeçalho de pedido, `application/json`coloque o Tipo de **Conteúdo** para .

2. Adicione uma **tecla api,** e coloque-a nesta corda: `252044BE3886FE4A8E3BAA4F595114BB`. Esta é uma chave de consulta para o serviço de pesquisa de caixas de areia que acolhe o índice NYC Jobs.

Depois de especificar o cabeçalho de pedido, pode reutilizá-lo para todas as consultas neste artigo, trocando apenas a **cadeia search=** string. 

  ![Cabeçalho de pedido do Postman](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>Delineie o URL de pedido

O pedido é um comando GET emparelhado com um URL contendo o ponto final de pesquisa cognitiva Azure e a cadeia de pesquisa.

  ![Cabeçalho de pedido do Postman](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

A composição do URL tem os seguintes elementos:

+ **`https://azs-playground.search.windows.net/`** é um serviço de pesquisa de caixa de areia mantido pela equipa de desenvolvimento da Pesquisa Cognitiva Azure. 
+ **`indexes/nycjobs/`** é o índice NYC Jobs na coleção de índices desse serviço. Tanto o nome do serviço como o índice são exigidos no pedido.
+ **`docs`** é a coleção de documentos que contém todos os conteúdos pesquisáveis. A chave api-chave de consulta fornecida no cabeçalho de pedido apenas funciona em operações de leitura direcionadas para a recolha de documentos.
+ **`api-version=2019-05-06`** define a versão api, que é um parâmetro necessário em cada pedido.
+ **`search=*`** é a cadeia de consulta, que na consulta inicial é nula, devolvendo os primeiros 50 resultados (por padrão).

## <a name="send-your-first-query"></a>Enviar a primeira consulta

Como passo de verificação, colhe o seguinte pedido no GET e clique **em Enviar**. Os resultados são devolvidos como documentos verbosos da JSON. Documentos inteiros são devolvidos, o que lhe permite ver todos os campos e todos os valores.

Colá-lo a um cliente REST como um passo de validação e para visualizar a estrutura do documento.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=*
  ```

A corda de **`search=*`** consulta, é uma pesquisa não especificada equivalente a pesquisa nula ou vazia. Não é especialmente útil, mas é a pesquisa mais simples que se pode fazer.

Opcionalmente, pode **`$count=true`** adicionar ao URL para devolver uma contagem dos documentos correspondentes aos critérios de pesquisa. Numa cadeia de pesquisa vazia, estes são todos os documentos do índice (cerca de 2800 no caso de NYC Jobs).

## <a name="how-to-invoke-simple-query-parsing"></a>Como invocar simples consulta parsing

Para consultas interativas, não é preciso especificar nada: simples é o padrão. Em código, se invocou previamente **consultaType=full** para sintaxe de consulta completa, pode redefinir o padrão com **consultaType=simples**.

## <a name="example-1-field-scoped-query"></a>Exemplo 1: Consulta com âmbito de campo

Este primeiro exemplo não é específico da parser, mas conduzimo-lo a introduzir o primeiro conceito de consulta fundamental: a contenção. Este exemplo aborda a execução de consultas e a resposta a apenas alguns campos específicos. Saber estruturar uma resposta legível da JSON é importante quando a sua ferramenta é carteiro ou explorador de pesquisa. 

Para a brevidade, a consulta visa apenas o campo *business_title* e especifica que apenas os títulos de negócio são devolvidos. A sintaxe é **pesquisar Fields** para restringir a execução de consulta apenas ao campo business_title, e **selecionar** para especificar quais os campos incluídos na resposta.

### <a name="partial-query-string"></a>Corda de consulta parcial

```http
searchFields=business_title&$select=business_title&search=*
```

Aqui está a mesma consulta com vários campos numa lista de comma-delimitada.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

### <a name="full-url"></a>URL completo

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchFields=business_title&$select=business_title&search=*
```

A resposta para esta consulta deve ser semelhante à seguinte imagem.

  ![Resposta da amostra do carteiro](media/search-query-lucene-examples/postman-sample-results.png)

Deve ter reparado na pontuação da pesquisa na resposta. Pontuações uniformes de 1 ocorrem quando não há classificação, seja porque a pesquisa não foi pesquisa completa de texto, ou porque não foi aplicado nenhum critério. Para pesquisa nula sem critérios, as filas voltam em ordem arbitrária. Quando incluir critérios reais, verá que as pontuações de pesquisa evoluem para valores significativos.

## <a name="example-2-look-up-by-id"></a>Exemplo 2: Procure por ID

Este exemplo é um pouco atípico, mas ao avaliar comportamentos de pesquisa, você pode querer inspecionar todo o conteúdo de um documento específico para entender por que foi incluído ou excluído dos resultados. Para devolver um único documento na sua totalidade, utilize uma [operação de procuração](https://docs.microsoft.com/rest/api/searchservice/lookup-document) para passar o DOCUMENTO de documento.

Todos os documentos têm um identificador único. Para experimentar a sintaxe para uma consulta de procura, em primeiro lugar, devolva uma lista de IDs de documentos para que possa encontrar uma para usar. Para a NYC Jobs, os identificadores estão guardados no `id` terreno.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchFields=id&$select=id&search=*
```

O exemplo seguinte é uma consulta de procura `id` que devolve um documento específico baseado em "9E1E3AF9-0660-4E00-AF51-9B654925A2D5", que apareceu primeiro na resposta anterior. A seguinte consulta devolve todo o documento, não apenas campos selecionados. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2019-05-06&$count=true&search=*
```

## <a name="example-3-filter-queries"></a>Exemplo 3: Consultas de filtro

[A sintaxe](https://docs.microsoft.com/azure/search/search-query-odata-filter) do filtro é uma expressão OData que pode utilizar com **pesquisa** ou por si só. Um filtro autónomo, sem parâmetro de pesquisa, é útil quando a expressão do filtro é capaz de qualificar totalmente documentos de interesse. Sem uma corda de consulta, não há análise léxica ou linguística, não há pontuação (todas as pontuações são 1), e nenhum ranking. Reparem que a cadeia de pesquisa está vazia.

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "job_id, business_title, agency, salary_range_from",
      "count": "true"
    }
```

Utilizado em conjunto, o filtro é aplicado primeiro a todo o índice e, em seguida, a pesquisa é realizada nos resultados do filtro. Por conseguinte, os filtros podem ser uma técnica útil para melhorar o desempenho de consultas, uma vez que reduzem o conjunto de documentos necessários à consulta de pesquisa para processamento.

  ![Resposta de consulta de filtro](media/search-query-simple-examples/filtered-query.png)

Se quiser experimentar isto no Carteiro usando get, pode colar nesta cadeia:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,business_title,agency,salary_range_from&search=&$filter=salary_frequency eq 'Annual' and salary_range_from gt 90000
```

Outra forma poderosa de combinar **`search.ismatch*()`** filtro e pesquisa é através de uma expressão de filtro, onde você pode usar uma consulta de pesquisa dentro do filtro. Esta expressão de filtro usa um wildcard no *plano* para selecionar business_title incluindo o plano de termo, planejador, planeamento, e assim por diante.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,business_title,agency&search=&$filter=search.ismatch('plan*', 'business_title', 'full', 'any')
```

Para obter mais informações sobre a função, consulte [search.ismatch em "Exemplos de filtro".](https://docs.microsoft.com/azure/search/search-query-odata-full-text-search-functions#examples)

## <a name="example-4-range-filters"></a>Exemplo 4: Filtros de gama

A filtragem de **`$filter`** alcance é suportada através de expressões para qualquer tipo de dados. Os seguintes exemplos procuram por campos numéricos e de cordas. 

Os tipos de dados são importantes nos filtros de alcance e funcionam melhor quando os dados numéricos estão em campos numéricos e dados de cordas em campos de cordas. Os dados numéricos nos campos de cordas não são adequados para intervalos porque as cordas numéricas não são comparáveis na Pesquisa Cognitiva Azure. 

Os seguintes exemplos estão no formato POST para a legibilidade (gama numérica, seguido da gama de texto):

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
  ![Filtro de gama para gamas numéricas](media/search-query-simple-examples/rangefilternumeric.png)


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

  ![Filtro de gama para gamas de texto](media/search-query-simple-examples/rangefiltertext.png)

Também pode experimentá-las no Carteiro usando get:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&search=&$filter=num_of_positions ge 5 and num_of_positions lt 10&$select=job_id, business_title, num_of_positions, agency&$orderby=agency&$count=true
```

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&search=&$filter=business_title ge 'A*' and business_title lt 'C*'&$select=job_id, business_title, agency&$orderby=business_title&$count=true
```

> [!NOTE]
> Enfrentar uma série de valores é um requisito comum de aplicação de pesquisa. Para obter mais informações e exemplos sobre a construção de filtros para estruturas de navegação de faceta, consulte ["Filter based on a range" in *How to implement ed faceed navigation*](search-faceted-navigation.md#filter-based-on-a-range).

## <a name="example-5-geo-search"></a>Exemplo 5: Geo-pesquisa

O índice da amostra inclui um campo geo_location com coordenadas de latitude e longitude. Este exemplo utiliza a [função geo.distance](https://docs.microsoft.com/azure/search/search-query-odata-geo-spatial-functions#examples) que filtra em documentos dentro da circunferência de um ponto de partida, até uma distância arbitrária (em quilómetros) que fornece. Pode ajustar o último valor na consulta (4) para reduzir ou ampliar a área de superfície da consulta.

O exemplo a seguir está no formato POST para a legibilidade:

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "job_id, business_title, work_location",
      "count": "true"
    }
```
Para resultados mais legíveis, os resultados da pesquisa são aparados para incluir uma identificação de trabalho, um cargo e o local de trabalho. As coordenadas de partida foram obtidas a partir de um documento aleatório no índice (neste caso, para um local de trabalho em Staten Island.

Também pode experimentar isto no Carteiro usando get:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=&$select=job_id, business_title, work_location&$filter=geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4
```

## <a name="example-6-search-precision"></a>Exemplo 6: Precisão de pesquisa

As consultas a prazo são termos únicos, talvez muitos deles, que são avaliados de forma independente. As consultas de frase são fechadas em aspas e avaliadas como uma corda verbatim. A precisão da partida é controlada pelos operadores e pelo modo de busca.

Exemplo 1: **`&search=fire`** devolve 150 resultados, onde todos os fósforos contêm a palavra fogo algures no documento.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=fire
```

Exemplo 2: **`&search=fire department`** devoluções resultados de 2002. Os fósforos são devolvidos para documentos que contenham fogo ou departamento.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=fire department
```

Exemplo 3: **`&search="fire department"`** devolve 82 resultados. A constituição da cadeia em aspas é uma pesquisa verbatim em ambos os termos, e os fósforos são encontrados em termos tokenizados no índice composto pelos termos combinados. Isto explica porque uma **`search=+fire +department`** pesquisa como não é equivalente. Ambos os termos são necessários, mas são digitalizados de forma independente. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search="fire department"
```

## <a name="example-7-booleans-with-searchmode"></a>Exemplo 7: Booleans com modo de pesquisa

A sintaxe simples suporta os operadores`+, -, |`booleanos sob a forma de caracteres ( ). O parâmetro searchMode informa as trocas entre precisão e recordação, favorecendo `searchMode=any` a recordação (correspondendo `searchMode=all` a quaisquer critérios qualifica um documento para o conjunto de resultados), e favorecendo a precisão (todos os critérios devem ser combinados). O padrão `searchMode=any`é , o que pode ser confuso se estiver a empilhar uma consulta com vários operadores e a ficar mais amplo em vez de resultados mais estreitos. Isto é particularmente verdade com o NOT, onde os resultados incluem todos os documentos "não conter" um termo específico.

Utilizando o modo de busca padrão (qualquer), são devolvidos 2800 documentos: os que contêm o termo multi-parte "corpo de bombeiros", além de todos os documentos que não tenham o termo "Metrotech Center".

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```

  ![modo de pesquisa qualquer](media/search-query-simple-examples/searchmodeany.png)

Alterar o `all` modo de busca para impor um efeito cumulativo nos critérios e devolver um conjunto de resultados menores - 21 documentos - composto por documentos que contenham toda a frase "bombeiros", menos os empregos no endereço do Centro Metrotech.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```
  ![modo de pesquisa todos](media/search-query-simple-examples/searchmodeall.png)

## <a name="example-8-structuring-results"></a>Exemplo 8: Resultados estruturantes

Vários parâmetros controlam quais os campos nos resultados da pesquisa, o número de documentos devolvidos em cada lote, e ordenar a ordem. Este exemplo reaparece alguns dos exemplos anteriores, limitando os resultados a campos específicos utilizando a declaração **de $select** e critérios de pesquisa verbatim, devolvendo 82 fósforos 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
Anexado ao exemplo anterior, pode classificar por título. Este tipo funciona porque civil_service_title é *classificado* no índice.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

Os resultados de paging são implementados utilizando o parâmetro **$top,** neste caso devolvendo os 5 principais documentos:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

Para obter os próximos 5, salte o primeiro lote:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>Passos seguintes
Tente especificar consultas no seu código. Os seguintes links explicam como configurar consultas de pesquisa tanto para a .NET como para a API REST utilizando a simples sintaxe predefinida.

* [Consulta do seu índice usando o .NET SDK](search-query-dotnet.md)
* [Consulta do seu índice usando a API REST](search-create-index-rest-api.md)

Referência adicional da sintaxe, arquitetura de consulta e exemplos podem ser encontrados nos seguintes links:

+ [Exemplos de consultas de sintaxe lucene para construir consultas avançadas](search-query-lucene-examples.md)
+ [Como funciona a pesquisa em texto completo no Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Consulta lucene completa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Filtragem e sintaxe Orderby](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)
