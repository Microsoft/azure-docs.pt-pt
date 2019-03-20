---
title: Exemplos de consulta simples - Azure Search
description: Exemplos de consulta simples para pesquisa em texto completo, pesquisa de filtro, pesquisa geográfica, pesquisa por facetas e outras cadeias de caracteres de consulta, usadas para consultar um índice da Azure Search.
author: HeidiSteen
manager: cgronlun
tags: Simple query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/09/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: a975c95af75e9f3e09e5d0142716795ab4b90e28
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58136483"
---
# <a name="simple-syntax-query-examples-for-building-queries-in-azure-search"></a>Exemplos de consulta de sintaxe simples para a criação de consultas na Azure Search

[Sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) invoca o parser de consulta padrão para a execução de consultas de pesquisa de texto completo em relação a um índice da Azure Search. O analisador de consultas simples é rápido e lida com cenários comuns no Azure Search, incluindo a pesquisa em texto completo e pesquisa geográfica pesquisa por facetas e filtrada. Neste artigo, etapa por meio de exemplos que demonstram as operações de consulta disponíveis quando utiliza a sintaxe simple.

A sintaxe de consulta alternativo é [Lucene completa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), que suporta estruturas de consulta mais complexas, tais como difusa e pesquisa com carateres universais, o que pode demorar mais tempo a processar. Para obter mais informações e exemplos que demonstram a sintaxe completa, consulte [exemplos de consulta de sintaxe Lucene](search-query-lucene-examples.md).

## <a name="formulate-requests-in-postman"></a>Formular pedidos no Postman

Os exemplos seguintes tirar partido de um índice de pesquisa de empregos em nova IORQUE consistindo em tarefas disponíveis com base num conjunto de dados fornecido pelos [cidade de Nova Iorque OpenData](https://nycopendata.socrata.com/) iniciativa. Estes dados não devem ser considerados atual ou completa. O índice é um serviço de proteção de segurança fornecidas pela Microsoft, o que significa que não é necessário uma subscrição do Azure ou o Azure Search para experimentar estas consultas.

O que é necessário é o Postman ou uma ferramenta equivalente para emitir o pedido HTTP na GET. Para obter mais informações, consulte [explorar com clientes REST](search-fiddler.md).

### <a name="set-the-request-header"></a>Definir o cabeçalho de pedido

1. No cabeçalho do pedido, defina **Content-Type** para `application/json`.

2. Adicionar uma **chave de api**e defina-o como esta cadeia: `252044BE3886FE4A8E3BAA4F595114BB`. Esta é uma chave de consulta para o serviço de pesquisa de área de segurança que aloja o índice de empregos em nova IORQUE.

Depois de especificar o cabeçalho do pedido, pode reutilizá-lo para todas as consultas neste artigo, alternando apenas a **pesquisa =** cadeia de caracteres. 

  ![Cabeçalho de pedido do Postman](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>Definir o URL do pedido

O pedido é um comando GET emparelhado com um URL que contém a cadeia de pesquisa e ponto final do Azure Search.

  ![Cabeçalho de pedido do Postman](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

A composição do URL tem os seguintes elementos:

+ **`https://azs-playground.search.windows.net/`** um serviço de pesquisa de área de segurança é mantido pela equipe de desenvolvimento do Azure Search. 
+ **`indexes/nycjobs/`** é o índice de empregos em nova IORQUE da coleção de índices desse serviço. O nome do serviço e o índice são necessários no pedido.
+ **`docs`** é a coleção de documentos que todo o conteúdo pesquisável. A consulta a chave de api fornecida no cabeçalho do pedido só funciona em operações de leitura visando a coleção de documentos.
+ **`api-version=2017-11-11`** Define a api-version, que é um parâmetro necessário em cada solicitação.
+ **`search=*`** é a cadeia de consulta, que, na consulta inicial, é nulo, retornando os resultados de 50 primeiras (por predefinição).

## <a name="send-your-first-query"></a>Enviar a sua primeira consulta

Como passo de verificação, cole o seguinte pedido GET e clique em **enviar**. Os resultados são retornados como verbosos de documentos JSON. Pode copiar-colar este URL no primeiro exemplo a seguir.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=*
  ```

A cadeia de consulta **`search=*`**, equivale uma pesquisa não especificada para a pesquisa de nula ou estar vazia. Não é especialmente útil, mas é a pesquisa mais simples, que pode fazer.

Opcionalmente, pode adicionar **`$count=true`** para o URL para devolver uma contagem dos documentos correspondentes aos critérios de pesquisa. Numa cadeia de caracteres de pesquisa em branco, trata todos os documentos no índice (aproximadamente 2800 no caso de empregos em nova IORQUE).

## <a name="how-to-invoke-simple-query-parsing"></a>Como invocar a análise de consulta simples

Para consultas interativas, não é necessário especificar nada: a predefinição é simple. No código, se anteriormente invocado **queryType = full** para a sintaxe de consulta completa, foi possível repor a predefinição com **queryType = simples**.

## <a name="example-1-field-scoped-query"></a>Exemplo 1: Consulta com abrangência de campo

Este primeiro exemplo não é específico do analisador, mas vamos levar com ele para apresentar o primeiro conceito de consulta fundamentais: contenção. Neste exemplo examina a execução da consulta e a resposta a apenas alguns campos específicos. É importante saber como estruturar uma resposta JSON legível quando sua ferramenta é o Explorador de pesquisa ou o Postman. 

Para fins de brevidade, a consulta destina-se apenas a *business_title* campo e especifica apenas os títulos de negócios são retornados. A sintaxe é **searchFields** para restringir a execução da consulta para apenas o campo de business_title, e **selecione** para especificar os campos que estão incluídos na resposta.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&search=*
```

A resposta para esta consulta deve ser semelhante à seguinte captura de ecrã.

  ![Resposta de exemplo do postman](media/search-query-lucene-examples/postman-sample-results.png)

Poderá ter reparado a pontuação de pesquisa na resposta. Pontuações uniformes de 1 ocorrerem quando não existe nenhuma classificação, seja porque a pesquisa não era a pesquisa em texto completo ou porque não existem critérios foi aplicada. Para obter pesquisa nulo sem critérios, linhas voltar na ordem arbitrária. Quando inclui critérios reais, verá as pontuações evoluam em valores significativos de pesquisa.

## <a name="example-2-look-up-by-id"></a>Exemplo 2: Procurar por ID

Neste exemplo é um pouco atípica, mas ao avaliar os comportamentos de pesquisa, talvez queira inspecionar todo o conteúdo de um documento específico para compreender por que foram incluído ou excluído dos resultados. Para devolver um único documento em sua totalidade, utilize um [operação de pesquisa de](https://docs.microsoft.com/rest/api/searchservice/lookup-document) para passar no ID do documento.

Todos os documentos têm um identificador exclusivo. Para experimentar a sintaxe para uma consulta de pesquisa, primeiro devolva uma lista de IDs de documento para que pode encontrar uma para utilizar. Para empregos em nova IORQUE, os identificadores são armazenados no `id` campo.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=id&$select=id&search=*
```

O exemplo seguinte é uma consulta de pesquisa devolver um documento específico com base em `id` "9E1E3AF9-0660-4E00-AF51-9B654925A2D5", que apareceu primeiro na resposta anterior. A seguinte consulta devolve o documento inteiro, campos selecionados não apenas. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2017-11-11&$count=true&search=*
```

## <a name="example-3-filter-queries"></a>Exemplo 3: Consultas de filtro

[Sintaxe de filtro](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples) é uma expressão de OData que pode utilizar com **pesquisa** ou por si só. Um filtro de autónoma, sem um parâmetro de pesquisa, é útil quando a expressão de filtro é capaz de qualificar completamente a documentos de interesse. Sem uma cadeia de consulta, significa que não existe nenhuma análise lexical ou linguística, nenhuma classificação (todas as pontuações são 1) e nenhuma classificação. Tenha em atenção que a cadeia de pesquisa está vazia.

```http
POST /indexes/nycjobs/docs/search?api-version=2017-11-11
    {
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "select=job_id, business_title, agency, salary_range_from",
      "count": "true"
    }
```

Utilizados em conjunto, o filtro é aplicado primeiro ao índice completo e, em seguida, a pesquisa é executada nos resultados do filtro. Por conseguinte, os filtros podem ser uma técnica útil para melhorar o desempenho de consultas, uma vez que reduzem o conjunto de documentos necessários à consulta de pesquisa para processamento.

  ![Resposta da consulta de filtro](media/search-query-simple-examples/filtered-query.png)

Se quiser experimentar isso no Postman com o GET, pode colar esta cadeia:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,business_title,agency,salary_range_from&search=&$filter=salary_frequency eq 'Annual' and salary_range_from gt 90000
```

Outra forma poderosa de combinar filtrar e pesquisar é através de **`search.ismatch*()`** numa expressão de filtro, onde pode usar uma consulta de pesquisa dentro do filtro. Esta expressão de filtro utiliza um caráter universal no *plano* para selecionar business_title, incluindo o plano de termo, planner, planejamento e assim por diante.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,business_title,agency&search=&$filter=search.ismatch('plan*', 'business_title', 'full', 'any')
```

Para obter mais informações sobre a função, veja [search.ismatch "Exemplos de filtro"](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples).

## <a name="example-4-range-filters"></a>Exemplo 4: Filtros de intervalo

Filtragem de intervalo é suportada através dos **`$filter`** expressões para qualquer tipo de dados. Os exemplos seguintes de pesquisa em campos numéricos e de cadeia de caracteres. 

Tipos de dados são importantes em filtros de intervalo e funcionam melhor quando dados numéricos estão nos campos numéricos e os dados de cadeia de caracteres em campos de cadeia de caracteres. Dados numéricos em campos de cadeia de caracteres não são adequados para intervalos de uma vez que as cadeias de caracteres numéricas não são comparáveis no Azure Search. 

Os exemplos seguintes estão no formato de mensagem para melhorar a legibilidade (intervalo numérico, seguido de intervalo de texto):

```http
POST /indexes/nycjobs/docs/search?api-version=2017-11-11
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
POST /indexes/nycjobs/docs/search?api-version=2017-11-11
    {
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title",
      "count": "true"
    }
```

  ![Filtro de intervalo para intervalos de texto](media/search-query-simple-examples/rangefiltertext.png)

Pode também experimentar estas no Postman usando GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&search=&$filter=num_of_positions ge 5 and num_of_positions lt 10&$select=job_id, business_title, num_of_positions, agency&$orderby=agency&$count=true
```

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&search=&$filter=business_title ge 'A*' and business_title lt 'C*'&$select=job_id, business_title, agency&$orderby=business_title&$count=true
```

> [!NOTE]
> Facetamento ao longo de intervalos de valores é um requisito de aplicativo de pesquisa comum. Para obter mais informações e exemplos sobre como criar filtros para estruturas de navegação de Faceta, consulte ["Filtro com base num intervalo de" *como implementar a navegação por facetas*](search-faceted-navigation.md#filter-based-on-a-range).

## <a name="example-5-geo-search"></a>Exemplo 5: Pesquisa geográfica

O índice de exemplo inclui um campo de geo_location com coordenadas de latitude e longitude. Este exemplo utiliza a [geo.distance função](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples) que filtros em documentos dentro da circunferência do ponto de partida, fora a uma distância arbitrária (em quilômetros), que fornece. Pode ajustar o último valor na consulta (4) para reduzir ou ampliar a área de superfície da consulta.

O exemplo a seguir está no formato de mensagem para melhorar a legibilidade:

```http
POST /indexes/nycjobs/docs/search?api-version=2017-11-11
    {
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "job_id, business_title, work_location",
      "count": "true"
    }
```
Para obter resultados mais legíveis, os resultados da pesquisa são cortados para incluir um ID da tarefa, cargo e a localização de trabalho. As coordenadas de partida foram obtidas a partir de um documento aleatório no índice (neste caso, para uma localização de trabalho no Staten island.

Pode também experimentar isso no Postman usando GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=&$select=job_id, business_title, work_location&$filter=geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4
```

## <a name="example-6-search-precision"></a>Exemplo 6: Precisão da pesquisa

Consultas de prazo são termos simples, talvez muitos deles, que são avaliados de forma independente. Consultas de frase entre aspas e avaliadas como uma cadeia de caracteres textual. Precisão de correspondência é controlado por operadores e searchMode.

Exemplo 1: **`&search=fire`** devolve 150 resultados, em que todas as correspondências contenham fire o word em algum lugar no documento.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=fire
```

Exemplo 2: **`&search=fire department`** devolve resultados de 2002. Correspondências são devolvidas para documentos que contenham fogo ou departamento.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=fire department
```

Exemplo 3: **`&search="fire department"`** devolve 82 resultados. Colocar a cadeia de caracteres de aspas é uma pesquisa textual em ambos os termos e forem encontradas correspondências em termos com token no índice que consiste dos termos combinados. Isto explica por que uma pesquisa, como **`search=+fire +department`** não é equivalente. Ambos os termos são necessários, mas são analisados de forma independente. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search="fire department"
```

## <a name="example-7-booleans-with-searchmode"></a>Exemplo 7: Booleanos com searchMode

Sintaxe simples suporta operadores booleanos na forma de carateres (`+, -, |`). O parâmetro searchMode informa as compensações entre a precisão e lembre-se, com `searchMode=any` favorável recolhimento (correspondência em critérios qualifica um documento para o conjunto de resultados), e `searchMode=all` favorável precisão (todos os critérios devem corresponder). A predefinição é `searchMode=any`, que pode ser confuso se são empilhadas uma consulta com vários operadores e introdução mais abrangente, em vez de resultados mais estreito. Isso é especialmente verdadeiro com não, onde os resultados incluem todos os documentos "que não contenha" uma condição específica.

Utilizar o searchMode padrão (qualquer), são devolvidos documentos de 2800: aquelas que contém a parte multi prazo "fire departamento", além de todos os documentos que não têm o termo "Metrotech Center".

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```

  ![modo de pesquisa qualquer](media/search-query-simple-examples/searchmodeany.png)

Alterar searchMode para `all` impõe um efeito cumulativo nos critérios e retorna o conjunto de resultados mais pequeno - 21 documentos - consiste em documentos que contêm a frase inteira "fire departamento" menos essas tarefas no endereço Metrotech Center.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```
  ![todos os de modo de pesquisa](media/search-query-simple-examples/searchmodeall.png)

## <a name="example-8-structuring-results"></a>Exemplo 8: Estruturar resultados

Resulta de vários controle de parâmetros que campos são na pesquisa, o número de documentos devolvidos em cada lote e a ordem de classificação. Neste exemplo resurfaces alguns dos exemplos anteriores, limitar os resultados a campos específicos utilizando o **$select** instrução e critérios de pesquisa textuais, 82 correspondências retornadas 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
Anexado no exemplo anterior, pode ordenar por título. Esse tipo funciona porque é civil_service_title *ordenável* no índice.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

Resultados de paginação é implementado com o **$top** parâmetro, neste caso, retornar os documentos de 5 principais:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

Para obter o próximo 5, ignore o primeiro batch:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>Passos Seguintes
Tente especificar consultas no seu código. As ligações seguintes explicam como configurar a consultas de pesquisa para o .NET e a API de REST com a sintaxe simples predefinido.

* [Consultar o índice da Azure Search utilizando o SDK .NET](search-query-dotnet.md)
* [Consultar o índice de pesquisa do Azure com a API REST](search-create-index-rest-api.md)

Referência de sintaxe adicional, a arquitetura de consulta e exemplos podem ser encontrados nas seguintes ligações:

+ [Exemplos de consulta de sintaxe Lucene para a criação de consultas avançadas](search-query-lucene-examples.md)
+ [Completa como funciona a pesquisa de texto no Azure Search](search-lucene-query-architecture.md)
+ [Sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Consultas de Lucene completas](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Sintaxe de filtro e Orderby](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)
