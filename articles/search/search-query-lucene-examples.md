---
title: Usar sintaxe de consulta Lucene completa-Azure Search
description: Sintaxe de consulta Lucene para pesquisa difusa, pesquisa de proximidade, aumento de termo, pesquisa de expressão regular e pesquisas de curinga em um serviço de Azure Search.
author: HeidiSteen
manager: nitinme
tags: Lucene query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: fcfc668022d0d8fc74258657bb93642aec49bd08
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178152"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-search"></a>Use a sintaxe de pesquisa "completa" do Lucene (consultas avançadas no Azure Search)

Ao construir consultas para Azure Search, você pode substituir o [analisador de consulta simples](query-simple-syntax.md) padrão pelo [analisador de consulta Lucene mais avançado no Azure Search](query-lucene-syntax.md) para formular definições de consulta especializadas e avançadas. 

O analisador Lucene dá suporte a construções de consultas complexas, como consultas com escopo de campo, pesquisa de curingas difusas e de prefixo, pesquisa por proximidade, aumento de termo e pesquisa de expressão regular. O poder adicional é fornecido com requisitos de processamento adicionais, portanto, você deve esperar um tempo de execução um pouco mais longo. Neste artigo, você pode percorrer exemplos demonstrando as operações de consulta disponíveis ao usar a sintaxe completa.

> [!Note]
> Muitas das construções de consulta especializadas habilitadas por meio da sintaxe de consulta Lucene completa não são [analisadas por texto](search-lucene-query-architecture.md#stage-2-lexical-analysis), o que pode ser surpreendente se você esperar lematização ou lematização. A análise lexical só é realizada em termos completos (uma consulta de termo ou consulta de frase). Tipos de consulta com termos incompletos (consulta de prefixo, consulta de caractere curinga, consulta de Regex, consulta difusa) são adicionados diretamente à árvore de consulta, ignorando o estágio de análise. A única transformação executada em termos de consulta incompletos está em letras minúsculas. 
>

## <a name="formulate-requests-in-postman"></a>Formular solicitações no postmaster

Os exemplos a seguir aproveitam um índice de pesquisa de trabalhos do NYC que consiste em trabalhos disponíveis com base em um conjunto de conjuntos fornecido pela [cidade da iniciativa de OpenData de Nova York](https://opendata.cityofnewyork.us/) . Esses dados não devem ser considerados atuais ou completos. O índice está em um serviço de área restrita fornecido pela Microsoft, o que significa que você não precisa de uma assinatura do Azure ou Azure Search para tentar essas consultas.

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

A cadeia de caracteres **`search=*`** de consulta,, é uma pesquisa não especificada equivalente a uma pesquisa nula ou vazia. É a pesquisa mais simples que você pode fazer.

Opcionalmente, você pode adicionar **`$count=true`** à URL para retornar uma contagem dos documentos que correspondem aos critérios de pesquisa. Em uma cadeia de caracteres de pesquisa vazia, esses são todos os documentos no índice (cerca de 2800 no caso de trabalhos do NYC).

## <a name="how-to-invoke-full-lucene-parsing"></a>Como invocar a análise de Lucene completa

Adicione **QueryType = Full** para invocar a sintaxe de consulta completa, substituindo a sintaxe de consulta simples padrão. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&search=*
```

Todos os exemplos neste artigo especificam o parâmetro de pesquisa **QueryType = Full** , indicando que a sintaxe completa é tratada pelo analisador de consulta Lucene. 

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>Exemplo 1: Consulta com escopo para uma lista de campos

Este primeiro exemplo não é específico do Lucene, mas nós o conduzimos a apresentar o primeiro conceito fundamental de consulta: escopo do campo. Este exemplo abrange toda a consulta e a resposta a apenas alguns campos específicos. Saber como estruturar uma resposta JSON legível é importante quando sua ferramenta é o postmaster ou o Search Explorer. 

Para resumir, a consulta destina-se apenas ao campo *business_title* e especifica que apenas títulos de negócios são retornados. O parâmetro **searchFields** restringe a execução da consulta apenas ao campo business_title, e **Select** especifica quais campos são incluídos na resposta.

### <a name="partial-query-string"></a>Cadeia de caracteres de consulta parcial

```http
&search=*&searchFields=business_title&$select=business_title
```

Aqui está a mesma consulta com vários campos em uma lista delimitada por vírgulas.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

Os espaços após as vírgulas são opcionais.

> [!Tip]
> Ao usar a API REST do código do aplicativo, não se esqueça de parâmetros de codificação de `$select` URL `searchFields`como e.

### <a name="full-url"></a>URL completa

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&search=*&searchFields=business_title&$select=business_title
```

A resposta para essa consulta deve ser semelhante à captura de tela a seguir.

  ![Resposta de exemplo do postmaster](media/search-query-lucene-examples/postman-sample-results.png)

Talvez você tenha notado a pontuação de pesquisa na resposta. Pontuações uniformes de 1 ocorrem quando não há nenhuma classificação, porque a pesquisa não era uma pesquisa de texto completo ou porque nenhum critério foi aplicado. Para pesquisa nula sem critérios, as linhas retornam em ordem arbitrária. Ao incluir os critérios de pesquisa reais, você verá que as pontuações de pesquisa evoluem em valores significativos.

## <a name="example-2-fielded-search"></a>Exemplo 2: Pesquisa em campo

A sintaxe Lucene completa dá suporte a expressões de pesquisa individuais de escopo para um campo específico. Este exemplo pesquisa títulos de negócios com o termo sênior neles, mas não Júnior.

### <a name="partial-query-string"></a>Cadeia de caracteres de consulta parcial

```http
$select=business_title&search=business_title:(senior NOT junior)
```

Aqui está a mesma consulta com vários campos.

```http
$select=business_title, posting_type&search=business_title:(senior NOT junior) AND posting_type:external
```

### <a name="full-url"></a>URL completa

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&$select=business_title&search=business_title:(senior NOT junior)
```

  ![Resposta de exemplo do postmaster](media/search-query-lucene-examples/intrafieldfilter.png)

Você pode definir uma operação de pesquisa em campo com a sintaxe **FieldName: searchion** , em que a expressão de pesquisa pode ser uma única palavra ou frase, ou uma expressão mais complexa entre parênteses, opcionalmente com operadores boolianos. Alguns exemplos incluem o seguinte:

- `business_title:(senior NOT junior)`
- `state:("New York" OR "New Jersey")`
- `business_title:(senior NOT junior) AND posting_type:external`

Certifique-se de colocar várias cadeias de caracteres entre aspas se desejar que ambas as cadeias de caracteres sejam avaliadas como uma única entidade, como nesse caso procurando `state` por dois locais distintos no campo. Além disso, verifique se o operador está em letras maiúsculas, como você vê com NOT e e.

O campo especificado em **FieldName: searchId** deve ser um campo pesquisável. Consulte [criar índice (API REST do serviço de Azure Search)](https://docs.microsoft.com/rest/api/searchservice/create-index) para obter detalhes sobre como os atributos de índice são usados em definições de campo.

> [!NOTE]
> No exemplo acima, não precisamos usar o `searchFields` parâmetro porque cada parte da consulta tem um nome de campo especificado explicitamente. No entanto, você ainda poderá `searchFields` usar o parâmetro se quiser executar uma consulta em que algumas partes têm o escopo de um campo específico, e o restante pode se aplicar a vários campos. Por exemplo, a consulta `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` `senior NOT junior` corresponderia apenas ao `business_title` campo, enquanto ele corresponderia a "external" `posting_type` com o campo. O nome do campo fornecido em **FieldName: searchexception** sempre tem precedência `searchFields` sobre o parâmetro, que é o motivo neste exemplo, não precisamos incluir `business_title` no `searchFields` parâmetro.

## <a name="example-3-fuzzy-search"></a>Exemplo 3: Pesquisa difusa

A sintaxe Lucene completa também dá suporte à pesquisa difusa, correspondendo a termos que têm uma construção semelhante. Para fazer uma pesquisa difusa, acrescente o símbolo de `~` til no final de uma única palavra com um parâmetro opcional, um valor entre 0 e 2, que especifica a distância de edição. Por exemplo, `blue~` ou `blue~1` retornariam azul, azuis e Glue.

### <a name="partial-query-string"></a>Cadeia de caracteres de consulta parcial

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~
```

Não há suporte diretamente para frases, mas você pode especificar uma correspondência difusa em partes de componente de uma frase.

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~ AND comm~ 
```


### <a name="full-url"></a>URL completa

Essa consulta pesquisa trabalhos com o termo "associar" (deliberadamente digitado incorretamente):

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:asosiate~
```
  ![Resposta de pesquisa difusa](media/search-query-lucene-examples/fuzzysearch.png)


> [!Note]
> As consultas difusas não são [analisadas](search-lucene-query-architecture.md#stage-2-lexical-analysis). Tipos de consulta com termos incompletos (consulta de prefixo, consulta de caractere curinga, consulta de Regex, consulta difusa) são adicionados diretamente à árvore de consulta, ignorando o estágio de análise. A única transformação executada em termos de consulta incompletos está em letras minúsculas.
>

## <a name="example-4-proximity-search"></a>Exemplo 4: Pesquisa de proximidade
As pesquisas de proximidade são usadas para encontrar os termos próximos uns dos outros em um documento. Insere um símbolo de til "~" no final de uma frase, seguido pelo número de palavras que criam o limite de proximidade. Por exemplo, "Hotel Airport" ~ 5 encontrará os termos Hotel e aeroporto dentro de 5 palavras entre si em um documento.

### <a name="partial-query-string"></a>Cadeia de caracteres de consulta parcial

```http
searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```

### <a name="full-url"></a>URL completa

Nesta consulta, para trabalhos com o termo "analista sênior", em que ele é separado por não mais de uma palavra:

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```
  ![Consulta de proximidade](media/search-query-lucene-examples/proximity-before.png)

Tente novamente removendo as palavras entre o termo "analista sênior". Observe que 8 documentos são retornados para essa consulta, em oposição a 10 para a consulta anterior.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>Exemplo 5: Aumento de termo
O aumento do termo refere-se à classificação de um documento mais alto se ele contiver o termo aumentado, em relação a documentos que não contêm o termo. Para aumentar um termo, use o sinal de interpolação, "^", símbolo com um fator de aumento (um número) no final do termo que você está pesquisando. 

### <a name="full-urls"></a>URLs completas

Nesta consulta "antes", pesquise por trabalhos com o termo *analista de computadores* e observe que não há resultados com as duas palavras *computador* e *analista*, enquanto os trabalhos do *computador* estão na parte superior dos resultados.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst
```
  ![Aumento de termo antes](media/search-query-lucene-examples/termboostingbefore.png)

Na consulta "After", repita a pesquisa, desta vez aumentando os resultados com o termo *analista* sobre o termo *computador* se ambas as palavras não existirem. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst%5e2
```
Uma versão legível mais humana da consulta acima é `search=business_title:computer analyst^2`. Para uma consulta viável, `^2` é codificado como `%5E2`, o que é mais difícil de ver.

  ![Aumento de termo após](media/search-query-lucene-examples/termboostingafter.png)

O aumento de termos difere dos perfis de pontuação que os perfis de Pontuação impulsionam determinados campos, em vez de termos específicos. O exemplo a seguir ajuda a ilustrar as diferenças.

Considere um perfil de pontuação que aumenta as correspondências em um determinado campo, como **gênero** no exemplo musicstoreindex. O aumento de termo pode ser usado para aumentar ainda mais alguns termos de pesquisa mais altos do que outros. Por exemplo, "Rock ^ 2 eletrônico" aumentará os documentos que contêm os termos de pesquisa no campo **gênero** mais alto do que outros campos pesquisáveis no índice. Além disso, os documentos que contêm o termo de pesquisa "rock" serão classificados como mais altos do que o outro termo de pesquisa "eletrônico" como resultado do valor de aumento de termo (2).

Ao definir o nível de fator, quanto maior o fator de aumento, mais relevante será o termo em relação a outros termos de pesquisa. Por padrão, o fator de aumento é 1. Embora o fator de aumento deva ser positivo, ele pode ser menor que 1 (por exemplo, 0,2).


## <a name="example-6-regex"></a>Exemplo 6: Regex

Uma pesquisa de expressão regular localiza uma correspondência com base no conteúdo entre barras "/", conforme documentado na [classe RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).

### <a name="partial-query-string"></a>Cadeia de caracteres de consulta parcial

```http
searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

### <a name="full-url"></a>URL completa

Nesta consulta, pesquise trabalhos com o termo sênior ou Júnior: `search=business_title:/(Sen|Jun)ior/`.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

  ![Consulta Regex](media/search-query-lucene-examples/regex.png)

> [!Note]
> As consultas Regex não são [analisadas](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). A única transformação executada em termos de consulta incompletos está em letras minúsculas.
>

## <a name="example-7-wildcard-search"></a>Exemplo 7: Pesquisa de curinga
Você pode usar a sintaxe geralmente reconhecida para\*várias pesquisas de curinga de caractere () ou única (?). Observe que o analisador de consulta Lucene dá suporte ao uso desses símbolos com um único termo, e não uma frase.

### <a name="partial-query-string"></a>Cadeia de caracteres de consulta parcial

```http
searchFields=business_title&$select=business_title&search=business_title:prog*
```

### <a name="full-url"></a>URL completa

Nesta consulta, pesquise trabalhos que contenham o prefixo "PROG", que incluirá títulos de negócios com os termos programação e programador. Você não pode usar um * ou? símbolo como o primeiro caractere de uma pesquisa.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:prog*
```
  ![Consulta curinga](media/search-query-lucene-examples/wildcard.png)

> [!Note]
> As consultas curinga não são [analisadas](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). A única transformação executada em termos de consulta incompletos está em letras minúsculas.
>

## <a name="next-steps"></a>Passos seguintes
Tente especificar o analisador de consulta Lucene em seu código. Os links a seguir explicam como configurar consultas de pesquisa para o .NET e a API REST. Os links usam a sintaxe simples padrão, portanto, será necessário aplicar o que você aprendeu deste artigo para especificar o **QueryType**.

* [Consultar seu índice de Azure Search usando o SDK do .NET](search-query-dotnet.md)
* [Consultar seu índice de Azure Search usando a API REST](search-create-index-rest-api.md)

Referência de sintaxe adicional, arquitetura de consulta e exemplos podem ser encontrados nos seguintes links:

+ [Exemplos de consulta de sintaxe simples](search-query-simple-examples.md)
+ [Como funciona a pesquisa de texto completo no Azure Search](search-lucene-query-architecture.md)
+ [Sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Sintaxe de consulta Lucene completa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)