---
title: Use sintaxe de consulta Lucene completa
titleSuffix: Azure Cognitive Search
description: Sintaxe lucene consulta para pesquisa fuzzy, pesquisa de proximidade, aumento de termo, pesquisa de expressão regular, e pesquisas de wildcard em um serviço de Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: Lucene query analyzer syntax
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 745be21c2a7a09a09fdbbfd57a305d09a4fac3ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793433"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>Utilize a sintaxe de pesquisa "completa" lucene (consultas avançadas em Pesquisa Cognitiva Azure)

Ao construir consultas para pesquisa cognitiva Azure, você pode substituir o [simples parser](query-simple-syntax.md) de consulta padrão por um [parser lucene](query-lucene-syntax.md) mais expansivo em Pesquisa Cognitiva Azure para formular definições de consulta especializadas e avançadas. 

O parser Lucene suporta construções complexas de consultas, tais como consultas de âmbito de campo, pesquisa de wildcard fuzzy e prefixo, pesquisa de proximidade, aumento de termo e pesquisa regular de expressão. A potência adicional vem com requisitos adicionais de processamento, pelo que deverá esperar um tempo de execução um pouco mais longo. Neste artigo, pode passar por exemplos que demonstram operações de consulta disponíveis ao utilizar a sintaxe completa.

> [!Note]
> Muitas das construções especializadas de consulta habilitadas através da sintaxe de consulta lucene completa não são [analisadas](search-lucene-query-architecture.md#stage-2-lexical-analysis)por texto , o que pode ser surpreendente se você espera de stemming ou lemmatization. A análise lexical só é realizada em termos completos (um termo consulta ou consulta de frase). Os tipos de consulta com termos incompletos (consulta prefixo, consulta de wildcard, consulta de regex, consulta fuzzy) são adicionados diretamente à árvore de consulta, contornando a fase de análise. A única transformação realizada em termos de consulta incompleta é a redução das casinos. 
>

## <a name="formulate-requests-in-postman"></a>Formular pedidos no Carteiro

Os exemplos seguintes alavancam um índice de pesquisa de NYC Jobs composto por empregos disponíveis com base num conjunto de dados fornecido pela iniciativa [City of New York OpenData.](https://opendata.cityofnewyork.us/) Estes dados não devem ser considerados atuais ou completos. O índice encontra-se num serviço de caixa de areia fornecido pela Microsoft, o que significa que não precisa de uma subscrição Azure ou de Pesquisa Cognitiva Azure para experimentar estas consultas.

O que precisa é do Carteiro ou de uma ferramenta equivalente para emitir o pedido http no GET. Para mais informações, consulte [Explore com clientes REST](search-get-started-postman.md).

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

A corda de **`search=*`** consulta, é uma pesquisa não especificada equivalente a pesquisa nula ou vazia. É a busca mais simples que se pode fazer.

Opcionalmente, pode **`$count=true`** adicionar ao URL para devolver uma contagem dos documentos correspondentes aos critérios de pesquisa. Numa cadeia de pesquisa vazia, estes são todos os documentos do índice (cerca de 2800 no caso de NYC Jobs).

## <a name="how-to-invoke-full-lucene-parsing"></a>Como invocar a análise total de Lucene

Adicione **consultaType=full** para invocar a sintaxe de consulta completa, sobrepondo-se à sintaxe de consulta simples padrão. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&search=*
```

Todos os exemplos deste artigo especificam o parâmetro de pesquisa **completo consultaType=full** search, indicando que a sintaxe completa é tratada pelo Lucene Query Parser. 

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>Exemplo 1: Consulta consulta a uma lista de campos

Este primeiro exemplo não é específico de Lucene, mas conduzimo-lo para introduzir o primeiro conceito de consulta fundamental: o âmbito de campo. Este exemplo aborda toda a consulta e a resposta a apenas alguns campos específicos. Saber estruturar uma resposta legível da JSON é importante quando a sua ferramenta é carteiro ou explorador de pesquisa. 

Para a brevidade, a consulta visa apenas o campo *business_title* e especifica que apenas os títulos de negócio são devolvidos. O parâmetro **searchFields** restringe a execução da consulta apenas ao campo business_title, e **selecione** quais os campos incluídos na resposta.

### <a name="partial-query-string"></a>Corda de consulta parcial

```http
&search=*&searchFields=business_title&$select=business_title
```

Aqui está a mesma consulta com vários campos numa lista de comma-delimitada.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

Os espaços após as vírgulas são opcionais.

> [!Tip]
> Ao utilizar a API REST do seu código de aplicação, `$select` não `searchFields`se esqueça de codificar parâmetros de código URL como e .

### <a name="full-url"></a>URL completo

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&search=*&searchFields=business_title&$select=business_title
```

A resposta para esta consulta deve ser semelhante à seguinte imagem.

  ![Resposta da amostra do carteiro](media/search-query-lucene-examples/postman-sample-results.png)

Deve ter reparado na pontuação da pesquisa na resposta. Pontuações uniformes de 1 ocorrem quando não há classificação, seja porque a pesquisa não foi pesquisa completa de texto, ou porque não foi aplicado nenhum critério. Para pesquisa nula sem critérios, as filas voltam em ordem arbitrária. Quando incluir critérios de pesquisa reais, verá que as pontuações de pesquisa evoluem para valores significativos.

## <a name="example-2-fielded-search"></a>Exemplo 2: Pesquisa no terreno

A sintaxe lucene completa suporta a deteção de expressões individuais de pesquisa para um campo específico. Este exemplo procura títulos de negóciocom o termo sénior neles, mas não júnior.

### <a name="partial-query-string"></a>Corda de consulta parcial

```http
$select=business_title&search=business_title:(senior NOT junior)
```

Aqui está a mesma consulta com vários campos.

```http
$select=business_title, posting_type&search=business_title:(senior NOT junior) AND posting_type:external
```

### <a name="full-url"></a>URL completo

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&$select=business_title&search=business_title:(senior NOT junior)
```

  ![Resposta da amostra do carteiro](media/search-query-lucene-examples/intrafieldfilter.png)

Pode definir uma operação de pesquisa em campo com o **fieldName:searchExpression** sintax, onde a expressão de pesquisa pode ser uma única palavra ou uma frase, ou uma expressão mais complexa em parênteses, opcionalmente com os operadores booleanos. Alguns exemplos incluem o seguinte:

- `business_title:(senior NOT junior)`
- `state:("New York" OR "New Jersey")`
- `business_title:(senior NOT junior) AND posting_type:external`

Certifique-se de colocar várias cordas dentro das aspas se quiser que ambas as cordas sejam avaliadas como `state` uma única entidade, como neste caso procurando por dois locais distintos no campo. Além disso, certifique-se de que o operador está capitalizado como vê com NÃO e E.

O campo especificado no **fieldName:searchExpression** deve ser um campo pesquisável. Consulte o [Create Index (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) para obter detalhes sobre como os atributos de índice são usados nas definições de campo.

> [!NOTE]
> No exemplo acima, não precisamos `searchFields` de utilizar o parâmetro porque cada parte da consulta tem um nome de campo explicitamente especificado. No entanto, ainda `searchFields` pode utilizar o parâmetro se quiser fazer uma consulta onde algumas partes são traçadas para um campo específico, e o resto pode aplicar-se a vários campos. Por exemplo, a `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` consulta `senior NOT junior` corresponderia apenas ao `business_title` campo, enquanto combinaria "externa" com o `posting_type` campo. O nome de campo fornecido no **fieldName:searchExpression** tem sempre precedência sobre o `searchFields` parâmetro, `business_title` razão `searchFields` pela qual neste exemplo, não precisamos incluir no parâmetro.

## <a name="example-3-fuzzy-search"></a>Exemplo 3: Pesquisa difusa

A sintaxe lucene completa também suporta a pesquisa fuzzy, combinando em termos que têm uma construção semelhante. Para fazer uma pesquisa difusa, `~` ajuste o símbolo de tilo no final de uma única palavra com um parâmetro opcional, um valor entre 0 e 2, que especifica a distância de edição. Por `blue~` exemplo, `blue~1` ou devolveria azul, azul e cola.

### <a name="partial-query-string"></a>Corda de consulta parcial

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~
```

As frases não são suportadas diretamente, mas pode especificar uma correspondência difusa em partes componentes de uma frase.

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~ AND comm~ 
```


### <a name="full-url"></a>URL completo

Esta consulta procura empregos com o termo "associado" (deliberadamente mal escrito):

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:asosiate~
```
  ![Resposta de pesquisa fuzzy](media/search-query-lucene-examples/fuzzysearch.png)


> [!Note]
> As consultas [difusas](search-lucene-query-architecture.md#stage-2-lexical-analysis)não são analisadas. Os tipos de consulta com termos incompletos (consulta prefixo, consulta de wildcard, consulta de regex, consulta fuzzy) são adicionados diretamente à árvore de consulta, contornando a fase de análise. A única transformação realizada em termos de consulta incompleta é a redução das casinos.
>

## <a name="example-4-proximity-search"></a>Exemplo 4: Pesquisa de proximidade
As pesquisas de proximidade são usadas para encontrar termos que se aproximam uns dos outros num documento. Insira um símbolo de tilde "~" no final de uma frase seguida pelo número de palavras que criam o limite de proximidade. Por exemplo, o "aeroporto do hotel"~5 encontrará os termos hotel e aeroporto dentro de 5 palavras um do outro em um documento.

### <a name="partial-query-string"></a>Corda de consulta parcial

```http
searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```

### <a name="full-url"></a>URL completo

Nesta consulta, para empregos com o termo "analista sénior" onde é separado por não mais do que uma palavra:

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```
  ![Consulta de proximidade](media/search-query-lucene-examples/proximity-before.png)

Tente novamente removendo as palavras entre o termo "analista sénior". Note que 8 documentos são devolvidos para esta consulta em oposição a 10 para a consulta anterior.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>Exemplo 5: Aumento de prazos
O aumento de prazo refere-se ao ranking de um documento mais elevado se contiver o termo reforçado, em relação a documentos que não contêm o termo. Para impulsionar um termo, use o cuidador, "^", símbolo com um fator de impulso (um número) no final do período que você está procurando. 

### <a name="full-urls"></a>URLs completos

Nesta consulta "antes", procure empregos com o termo *analista de computadores* e note que não há resultados com palavras *computador* e *analista*– mas os trabalhos *informáticos* estão no topo dos resultados.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst
```
  ![Aumento de prazos antes](media/search-query-lucene-examples/termboostingbefore.png)

Na consulta "depois", repita a pesquisa, desta vez aumentando os resultados com o termo *analista* ao longo do termo *computador* se ambas as palavras não existirem. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst%5e2
```
Uma versão mais legível humana da `search=business_title:computer analyst^2`consulta acima é . Para uma consulta viável, `^2` é codificado como `%5E2`, o que é mais difícil de ver.

  ![Aumento de prazos após](media/search-query-lucene-examples/termboostingafter.png)

O aumento de prazos difere de marcar perfis em que os perfis de pontuação impulsionam determinados campos, em vez de termos específicos. O exemplo que se segue ajuda a ilustrar as diferenças.

Considere um perfil de pontuação que impulsione os fósforos num determinado campo, como o **género** no exemplo do musicstoreindex. O aumento de prazos poderia ser usado para aumentar ainda mais certos termos de pesquisa superiores aos outros. Por exemplo, o "rock^2 electronic" irá impulsionar documentos que contenham os termos de pesquisa no campo de **género** superior espetados do que outros campos pesquisáveis no índice. Além disso, os documentos que contenham o termo de pesquisa "rock" serão classificados acima do outro termo de pesquisa "eletrónico" como resultado do valor de aumento do termo (2).

Ao definir o nível de fator, quanto maior for o fator de impulso, mais relevante será o termo em relação a outros termos de pesquisa. Por padrão, o fator de impulso é 1. Embora o fator de impulso tenha de ser positivo, pode ser inferior a 1 (por exemplo, 0,2).


## <a name="example-6-regex"></a>Exemplo 6: Regex

Uma pesquisa de expressão regular encontra uma correspondência baseada no conteúdo entre as barras dianteiras "/", conforme documentado na [classe RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).

### <a name="partial-query-string"></a>Corda de consulta parcial

```http
searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

### <a name="full-url"></a>URL completo

Nesta consulta, procure empregos com o termo `search=business_title:/(Sen|Jun)ior/`Sénior ou Júnior: .

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

  ![Consulta regex](media/search-query-lucene-examples/regex.png)

> [!Note]
> As consultas regex não são [analisadas.](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis) A única transformação realizada em termos de consulta incompleta é a redução das casinos.
>

## <a name="example-7-wildcard-search"></a>Exemplo 7: Pesquisa de Wildcard
Você pode usar sintaxe geralmente reconhecida para múltiplas ()\*ou únicas (?) pesquisas de caracteres wildcard. Note que o parser lucene da consulta suporta o uso destes símbolos com um único termo, e não uma frase.

### <a name="partial-query-string"></a>Corda de consulta parcial

```http
searchFields=business_title&$select=business_title&search=business_title:prog*
```

### <a name="full-url"></a>URL completo

Nesta consulta, procura empregos que contenham o prefixo 'prog' que inclua títulos empresariais com os termos de programação e programador nele. Não pode usar um ou? símbolo como o primeiro personagem de uma pesquisa.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:prog*
```
  ![Consulta wildcard](media/search-query-lucene-examples/wildcard.png)

> [!Note]
> As consultas wildcard não são [analisadas.](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis) A única transformação realizada em termos de consulta incompleta é a redução das casinos.
>

## <a name="next-steps"></a>Passos seguintes
Tente especificar o Lucene Query Parser no seu código. Os seguintes links explicam como configurar consultas de pesquisa tanto para a .NET como para a API REST. Os links utilizam a sintaxe simples por defeito, pelo que terá de aplicar o que aprendeu com este artigo para especificar a **consultaType**.

* [Consulta do seu índice usando o .NET SDK](search-query-dotnet.md)
* [Consulta do seu índice usando a API REST](search-create-index-rest-api.md)

Referência adicional da sintaxe, arquitetura de consulta e exemplos podem ser encontrados nos seguintes links:

+ [Exemplos simples de consulta de sintaxe](search-query-simple-examples.md)
+ [Como funciona a pesquisa em texto completo no Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Sintaxe de consulta Lucene completo](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)