---
title: Use a sintaxe de consulta lucene completa
titleSuffix: Azure Cognitive Search
description: Lucene consulta sintaxe para pesquisa de fuzzy, pesquisa de proximidade, aumento de termo, pesquisa regular de expressão e pesquisas de wildcard em um serviço de Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: Lucene query analyzer syntax
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 406233fd93ca76a683cf9f9a9e857de9099705ef
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368550"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>Utilize a sintaxe de pesquisa lucene "completa" (consultas avançadas em Pesquisa Cognitiva Azure)

Ao construir consultas para Azure Cognitive Search, pode substituir o [parser de consulta simples](query-simple-syntax.md) padrão pelo mais poderoso [Lucene Query Parser em Azure Cognitive Search](query-lucene-syntax.md) para formular definições de consulta especializadas e avançadas. 

O Lucene parser suporta construções complexas de consultas, tais como consultas de campo, pesquisa de fossa, pesquisa de sfix e sufixo wildcard, pesquisa de proximidade, aumento de termos e pesquisa regular de expressão. A potência adicional vem com requisitos adicionais de processamento, pelo que deverá esperar um tempo de execução um pouco mais longo. Neste artigo, pode passar por exemplos demonstrando operações de consulta com base em sintaxe completa.

> [!Note]
> Muitas das construções de consulta especializadas possibilitadas através da sintaxe de consulta lucene completa não são [analisadas por texto](search-lucene-query-architecture.md#stage-2-lexical-analysis), o que pode ser surpreendente se você espera stemming ou lematização. A análise lexical só é realizada em termos completos (consulta de termo ou consulta de frase). Os tipos de consulta com termos incompletos (consulta de prefixo, consulta wildcard, consulta regex, consulta fuzzy) são adicionados diretamente à árvore de consulta, contornando a fase de análise. A única transformação realizada em termos de consulta parcial é a redução. 
>

## <a name="nyc-jobs-examples"></a>Exemplos de empregos de NYC

Os exemplos a seguir aproveitam o índice de [pesquisa nyc jobs,](https://azjobsdemo.azurewebsites.net/)  composto por empregos disponíveis com base num conjunto de dados fornecido pela [City of New York OpenData Initiative](https://nycopendata.socrata.com/). Estes dados não devem ser considerados atuais ou completos. O índice encontra-se num serviço de caixa de areia fornecido pela Microsoft, o que significa que não precisa de uma subscrição Azure ou de Azure Cognitive Search para experimentar estas consultas.

O que precisa é do Carteiro ou de uma ferramenta equivalente para emitir pedido HTTP no GET ou POST. Se não estiver familiarizado com estas ferramentas, consulte [Quickstart: Explore a AZure Cognitive Search REST API](search-get-started-rest.md).

## <a name="set-up-the-request"></a>Configurar o pedido

1. Os cabeçalhos de pedido devem ter os seguintes valores:

   | Chave | Valor |
   |-----|-------|
   | Content-Type | `application/json`|
   | api-chave  | `252044BE3886FE4A8E3BAA4F595114BB` </br> (esta é a chave API de consulta real para o serviço de pesquisa de caixas de areia que acolhe o índice NYC Jobs) |

1. Deslome o verbo para **`GET`** .

1. Desa estação a URL **`https://azs-playground.search.windows.net/indexes/nycjobs/docs/search=*&api-version=2020-06-30&queryType=full`**

   + A recolha de documentos no índice contém todo o conteúdo pesmável. Uma chave API de consulta fornecida no cabeçalho de pedido apenas funciona para operações de leitura direcionadas para a recolha de documentos.

   + **`$count=true`** devolve uma contagem dos documentos correspondentes aos critérios de pesquisa. Numa cadeia de pesquisa vazia, a contagem será de todos os documentos do índice (cerca de 2558 no caso de NYC Jobs).

   + **`search=*`** é uma consulta não especificada, equivalente a uma busca nula ou vazia. Não é especialmente útil, mas é a pesquisa mais simples que pode fazer, e mostra todos os campos recuperáveis no índice, com todos os valores.

   + **`queryType=full`** invoca o analisador lucene completo.

1. Como passo de verificação, cole o seguinte pedido no GET e clique em **Enviar.** Os resultados são devolvidos como documentos verbosos da JSON.

   ```http
   https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*&queryType=full
   ```

### <a name="how-to-invoke-full-lucene-parsing"></a>Como invocar a análise completa de Lucene

Adicione **`queryType=full`** para invocar a sintaxe de consulta completa, sobreprimando a sintaxe de consulta simples padrão. Todos os exemplos deste artigo especificam o **`queryType=full`** parâmetro de pesquisa, indicando que a sintaxe completa é tratada pelo Lucene Query Parser. 

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "*"
}
```

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>Exemplo 1: Consulta a uma lista de campos

Este primeiro exemplo não é específico da parser, mas conduzimos com ele para introduzir o primeiro conceito de consulta fundamental: a contenção. Este exemplo limita tanto a execução de consultas como a resposta a apenas alguns campos específicos. Saber estruturar uma resposta JSON legível é importante quando a sua ferramenta é Carteiro ou Explorador de Pesquisa. 

Esta consulta visa apenas *business_title* em **`searchFields`** , especificando através do parâmetro o mesmo **`select`** campo na resposta.

```http
POST https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

A resposta a esta consulta deve ser semelhante à seguinte imagem.

  ![Resposta da amostra do carteiro com pontuações](media/search-query-lucene-examples/postman-sample-results.png)

Deve ter reparado na pontuação da pesquisa na resposta. Pontuações uniformes de **1** ocorrem quando não há classificação, quer porque a pesquisa não foi completamente pesquisa de texto, quer porque não foram fornecidos critérios. Para uma busca vazia, as filas voltam por ordem arbitrária. Quando incluir critérios reais, verá as pontuações de pesquisa evoluir em valores significativos.

## <a name="example-2-fielded-search"></a>Exemplo 2: Pesquisa de campo

A sintaxe Lucene completa suporta a deteção de expressões individuais de pesquisa para um campo específico. Este exemplo procura títulos de negócios com o termo sénior neles, mas não júnior. Pode especificar vários campos utilizando E.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:(senior NOT junior) AND posting_type:external",
    "searchFields": "business_title, posting_type",
    "select": "business_title, posting_type"
}
```

A resposta a esta consulta deve ser semelhante à seguinte imagem (não é mostrada posting_type).

  :::image type="content" source="media/search-query-lucene-examples/intrafieldfilter.png" alt-text="Expressão de pesquisa de resposta de amostra de carteiro" border="false":::

A expressão de pesquisa pode ser uma única palavra ou uma frase, ou uma expressão mais complexa em parênteses, opcionalmente com operadores Boolean. Alguns exemplos incluem:

+ `business_title:(senior NOT junior)`
+ `state:("New York" OR "New Jersey")`
+ `business_title:(senior NOT junior) AND posting_type:external`

Certifique-se de colocar várias cordas dentro das aspas se quiser que ambas as cordas sejam avaliadas como uma única entidade, como neste caso procurando dois locais distintos no `state` campo. Dependendo da ferramenta, poderá ter de escapar `\` () as aspas. 

O campo especificado no **fieldName:searchExpression** deve ser um campo pesquisável. Consulte [o Índice de Criação (Azure Cognitive Search REST API)](/rest/api/searchservice/create-index) para obter detalhes sobre como os atributos do índice são usados nas definições de campo.

> [!NOTE]
> No exemplo acima, o **`searchFields`** parâmetro é omitido porque cada parte da consulta tem um nome de campo explicitamente especificado. No entanto, ainda pode utilizar **`searchFields`** se a consulta tiver várias partes (utilizando declarações E). Por exemplo, a consulta `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` `senior NOT junior` corresponderia apenas ao `business_title` campo, enquanto que combinaria "externa" com o `posting_type` campo. O nome de campo fornecido `fieldName:searchExpression` sempre tem precedência sobre **`searchFields`** , e é por isso que, neste exemplo, podemos omitir `business_title` de **`searchFields`** .

## <a name="example-3-fuzzy-search"></a>Exemplo 3: Pesquisa felpuda

A sintaxe Lucene completa também suporta a pesquisa fuzzy, combinando em termos que têm uma construção semelhante. Para fazer uma pesquisa difusa, apedte o `~` símbolo de azulejos no final de uma única palavra com um parâmetro opcional, um valor entre 0 e 2, que especifica a distância de edição. Por exemplo, `blue~` ou `blue~1` voltaria azul, azul e cola.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:asosiate~",
    "searchFields": "business_title",
    "select": "business_title"
}
```

As frases não são suportadas diretamente, mas pode especificar uma correspondência difusa em cada termo de uma frase multi-partes, como `search=business_title:asosiate~ AND comm~` .  Na imagem abaixo, a resposta inclui uma correspondência no *associado comunitário.*

  :::image type="content" source="media/search-query-lucene-examples/fuzzysearch.png" alt-text="Resposta de pesquisa fuzzy" border="false":::

> [!Note]
> As consultas fuzzy não são [analisadas.](search-lucene-query-architecture.md#stage-2-lexical-analysis) Os tipos de consulta com termos incompletos (consulta de prefixo, consulta wildcard, consulta regex, consulta fuzzy) são adicionados diretamente à árvore de consulta, contornando a fase de análise. A única transformação realizada em termos de consulta parcial é a redução.
>

## <a name="example-4-proximity-search"></a>Exemplo 4: Pesquisa de proximidade

As pesquisas de proximidade são usadas para encontrar termos que estão próximos uns dos outros em um documento. Insira um símbolo de azulejos "~" no final de uma frase seguida do número de palavras que criam o limite de proximidade. Por exemplo, "hotel airport"~5 encontrará os termos hotel e aeroporto dentro de 5 palavras um do outro em um documento.

Esta consulta procura os termos "senior" e "analista", onde cada termo é separado por não mais do que uma palavra, e as aspas são escapadas para `\"` preservar a frase:

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:\"senior analyst\"~1",
    "searchFields": "business_title",
    "select": "business_title"
}
```

A resposta para esta consulta deve ser semelhante à seguinte screenshot 

  :::image type="content" source="media/search-query-lucene-examples/proximity-before.png" alt-text="Consulta de proximidade" border="false":::

Tente novamente, eliminando qualquer distância `~0` entre os termos "analista sénior". Note que 8 documentos são devolvidos para esta consulta em oposição a 10 para a consulta anterior.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:\"senior analyst\"~0",
    "searchFields": "business_title",
    "select": "business_title"
}
```

## <a name="example-5-term-boosting"></a>Exemplo 5: Aumento de prazos

O aumento de prazos refere-se à classificação de um documento mais elevado se contiver o termo aumentado, em relação a documentos que não contêm o termo. Para impulsionar um termo, use o caret, `^` símbolo com um fator de impulso (um número) no final do termo que você está procurando.

Nesta consulta "antes", procure empregos com o termo *analista de computadores* e note que não há resultados com ambas as palavras *computador* e *analista*, mas os empregos *informáticos* estão no topo dos resultados.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:computer analyst",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Na consulta "depois", repita a procura, desta vez impulsionando os resultados com o termo *analista* ao longo do termo *computador* se ambas as palavras não existirem. Uma versão legível humana da consulta é `search=business_title:computer analyst^2` . Para uma consulta viável no Carteiro, `^2` é codificado como `%5E2` .

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:computer analyst%5e2",
    "searchFields": "business_title",
    "select": "business_title"
}
```

A resposta a esta consulta deve ser semelhante à seguinte imagem.

  :::image type="content" source="media/search-query-lucene-examples/termboostingafter.png" alt-text="Aumento de prazos após" border="false":::

O aumento de prazos difere dos perfis de pontuação em que os perfis de pontuação impulsionam certos campos, em vez de termos específicos. O exemplo a seguir ajuda a ilustrar as diferenças.

Considere um perfil de pontuação que impulsione os jogos em determinado campo, como o **género** no exemplo musicstoreindex. O aumento de prazos poderia ser usado para aumentar ainda mais certos termos de pesquisa mais elevados do que outros. Por exemplo, "rock^2 electronic" irá impulsionar documentos que contenham os termos de pesquisa no campo do **género** mais elevados do que outros campos pes pesjáveis no índice. Além disso, os documentos que contenham o termo de pesquisa "rock" serão classificados acima do outro termo de pesquisa "eletrónico" como resultado do valor de aumento do termo (2).

Ao definir o nível de fator, quanto maior for o fator de impulso, mais relevante será o termo relativo a outros termos de pesquisa. Por padrão, o fator de impulso é 1. Embora o fator de impulso tenha de ser positivo, pode ser inferior a 1 (por exemplo, 0,2).

## <a name="example-6-regex"></a>Exemplo 6: Regex

Uma pesquisa regular de expressão encontra uma correspondência com base no conteúdo entre barras para a frente "/", como documentado na [classe RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:/(Sen|Jun)ior/",
    "searchFields": "business_title",
    "select": "business_title"
}
```

A resposta a esta consulta deve ser semelhante à seguinte imagem.

  :::image type="content" source="media/search-query-lucene-examples/regex.png" alt-text="Consulta regex" border="false":::

> [!Note]
> As consultas de Regex não são [analisadas.](./search-lucene-query-architecture.md#stage-2-lexical-analysis) A única transformação realizada em termos de consulta parcial é a redução.
>

## <a name="example-7-wildcard-search"></a>Exemplo 7: Pesquisa de Wildcard

Pode utilizar sintaxe geralmente reconhecida para pesquisas de caracteres múltiplos \* () ou simples (?) de caracteres. Note que o parser de consulta Lucene suporta o uso destes símbolos com um único termo, e não uma frase.

Nesta consulta, procure empregos que contenham o prefixo 'prog' que incluiria títulos empresariais com os termos de programação e programador nele. Não é possível utilizar um `*` ou símbolo como o primeiro `?` personagem de uma pesquisa.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:prog*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

A resposta a esta consulta deve ser semelhante à seguinte imagem.

  :::image type="content" source="media/search-query-lucene-examples/wildcard.png" alt-text="Consulta wildcard" border="false":::

> [!Note]
> As consultas wildcard não são [analisadas.](./search-lucene-query-architecture.md#stage-2-lexical-analysis) A única transformação realizada em termos de consulta parcial é a redução.
>

## <a name="next-steps"></a>Passos seguintes

Tente especificar consultas em código. Os seguintes links explicam como configurar consultas de pesquisa usando os Azure SDKs.

+ [Consultar o seu índice utilizando o .NET SDK](search-get-started-dotnet.md)
+ [Consultar o seu índice usando o Python SDK](search-get-started-python.md)
+ [Consultar o seu índice utilizando o JavaScript SDK](search-get-started-javascript.md)

Referência de sintaxe adicional, arquitetura de consulta e exemplos podem ser encontrados nos seguintes links:

+ [Exemplos de consulta de sintaxe lucene para a construção de consultas avançadas](search-query-lucene-examples.md)
+ [Como funciona a pesquisa em texto completo no Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Sintaxe de consulta simples](query-simple-syntax.md)
+ [Sintaxe de consulta lucene completa](query-lucene-syntax.md)
+ [Sintaxe de filtro](search-query-odata-filter.md)