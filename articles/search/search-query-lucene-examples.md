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
ms.openlocfilehash: ae4dd8b82e40b46da52a1b1f396569fda1dfea2b
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94694631"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>Utilize a sintaxe de pesquisa lucene "completa" (consultas avançadas em Pesquisa Cognitiva Azure)

Ao construir consultas para Azure Cognitive Search, pode substituir o [parser de consulta simples](query-simple-syntax.md) padrão com o mais expansivo [Lucene Query Parser em Azure Cognitive Search](query-lucene-syntax.md) para formular definições de consulta especializadas e avançadas. 

O Lucene parser suporta construções complexas de consultas, tais como consultas de campo, pesquisa de fossa, pesquisa de sfix e sufixo wildcard, pesquisa de proximidade, aumento de termos e pesquisa regular de expressão. A potência adicional vem com requisitos adicionais de processamento, pelo que deverá esperar um tempo de execução um pouco mais longo. Neste artigo, pode passar por exemplos demonstrando operações de consulta disponíveis ao utilizar a sintaxe completa.

> [!Note]
> Muitas das construções de consulta especializadas possibilitadas através da sintaxe de consulta lucene completa não são [analisadas por texto](search-lucene-query-architecture.md#stage-2-lexical-analysis), o que pode ser surpreendente se você espera stemming ou lematização. A análise lexical só é realizada em termos completos (consulta de termo ou consulta de frase). Os tipos de consulta com termos incompletos (consulta de prefixo, consulta wildcard, consulta regex, consulta fuzzy) são adicionados diretamente à árvore de consulta, contornando a fase de análise. A única transformação realizada em termos de consulta parcial é a redução. 
>

## <a name="formulate-requests-in-postman"></a>Formular pedidos no Carteiro

Os exemplos a seguir aproveitam um índice de pesquisa nyc jobs composto por empregos disponíveis com base num conjunto de dados fornecido pela iniciativa [City of New York OpenData.](https://opendata.cityofnewyork.us/) Estes dados não devem ser considerados atuais ou completos. O índice encontra-se num serviço de caixa de areia fornecido pela Microsoft, o que significa que não precisa de uma subscrição Azure ou de Azure Cognitive Search para experimentar estas consultas.

O que precisa é do Carteiro ou de uma ferramenta equivalente para emitir pedido HTTP no GET. Para mais informações, consulte [Explore com clientes REST.](search-get-started-rest.md)

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

A cadeia de consulta, **`search=*`** é uma pesquisa não especificada equivalente a pesquisa nula ou vazia. É a busca mais simples que pode fazer.

Opcionalmente, pode adicionar **`$count=true`** ao URL para devolver uma contagem dos documentos correspondentes aos critérios de pesquisa. Numa cadeia de pesquisa vazia, estes são todos os documentos do índice (cerca de 2800 no caso de NYC Jobs).

## <a name="how-to-invoke-full-lucene-parsing"></a>Como invocar a análise completa de Lucene

Adicione **consultaType=full** para invocar a sintaxe de consulta completa, sobrepondo-se à sintaxe de consulta simples padrão. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&search=*
```

Todos os exemplos deste artigo especificam o parâmetro de pesquisa **de consultaType=full,** indicando que a sintaxe completa é tratada pelo Lucene Query Parser. 

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>Exemplo 1: Consulta a uma lista de campos

Este primeiro exemplo não é específico da Lucene, mas conduzimos com ele para introduzir o primeiro conceito de consulta fundamental: o âmbito de campo. Este exemplo coloca em atenção toda a consulta e a resposta a apenas alguns campos específicos. Saber estruturar uma resposta JSON legível é importante quando a sua ferramenta é Carteiro ou Explorador de Pesquisa. 

Para a brevidade, a consulta visa apenas o campo *business_title* e especifica apenas os títulos de negócios são devolvidos. O parâmetro **searchFields** restringe a execução de consulta apenas ao campo business_title, e **seleciona** especifica quais os campos incluídos na resposta.

### <a name="search-expression"></a>Expressão de pesquisa

```http
&search=*&searchFields=business_title&$select=business_title
```

Aqui está a mesma consulta com vários campos numa lista delimitada por vírgula.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

Os espaços após as vírgulas são opcionais.

> [!Tip]
> Ao utilizar a API REST a partir do seu código de aplicação, não se esqueça de codificar parâmetros como `$select` e `searchFields` .

### <a name="full-url"></a>URL completo

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&search=*&searchFields=business_title&$select=business_title
```

A resposta a esta consulta deve ser semelhante à seguinte imagem.

  ![Resposta da amostra do carteiro com pontuações](media/search-query-lucene-examples/postman-sample-results.png)

Deve ter reparado na pontuação da pesquisa na resposta. Pontuações uniformes de 1 ocorrem quando não há classificação, quer porque a pesquisa não foi completamente pesquisa de texto, quer porque não foi aplicado nenhum critério. Para uma busca nula sem critérios, as linhas voltam por ordem arbitrária. Quando incluir critérios de pesquisa reais, verá as pontuações de pesquisa evoluir em valores significativos.

## <a name="example-2-fielded-search"></a>Exemplo 2: Pesquisa de campo

A sintaxe Lucene completa suporta a deteção de expressões individuais de pesquisa para um campo específico. Este exemplo procura títulos de negócios com o termo sénior neles, mas não júnior.

### <a name="search-expression"></a>Expressão de pesquisa

```http
$select=business_title&search=business_title:(senior NOT junior)
```

Aqui está a mesma consulta com vários campos.

```http
$select=business_title, posting_type&search=business_title:(senior NOT junior) AND posting_type:external
```

### <a name="full-url"></a>URL completo

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&$select=business_title&search=business_title:(senior NOT junior)
```

  :::image type="content" source="media/search-query-lucene-examples/intrafieldfilter.png" alt-text="Expressão de pesquisa de resposta de amostra de carteiro" border="false":::

Pode definir uma operação de pesquisa em campo com o **fieldName:searchX** sintaxe de expressão de pesquisa, onde a expressão de pesquisa pode ser uma única palavra ou uma frase, ou uma expressão mais complexa em parênteses, opcionalmente com operadores Boolean. Alguns exemplos incluem:

- `business_title:(senior NOT junior)`
- `state:("New York" OR "New Jersey")`
- `business_title:(senior NOT junior) AND posting_type:external`

Certifique-se de colocar várias cordas dentro das aspas se quiser que ambas as cordas sejam avaliadas como uma única entidade, como neste caso procurando dois locais distintos no `state` campo. Além disso, certifique-se de que o operador está capitalizado como vê com NÃO E E.

O campo especificado no **fieldName:searchExpression** deve ser um campo pesquisável. Consulte [o Índice de Criação (Azure Cognitive Search REST API)](/rest/api/searchservice/create-index) para obter detalhes sobre como os atributos do índice são usados nas definições de campo.

> [!NOTE]
> No exemplo acima, não precisávamos de utilizar o `searchFields` parâmetro porque cada parte da consulta tem um nome de campo explicitamente especificado. No entanto, ainda pode utilizar o `searchFields` parâmetro se quiser executar uma consulta onde algumas partes são telescópios para um campo específico, e o resto pode aplicar-se a vários campos. Por exemplo, a consulta `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` `senior NOT junior` corresponderia apenas ao `business_title` campo, enquanto que combinaria "externa" com o `posting_type` campo. O nome de campo fornecido no **fieldName:searchExpression** tem sempre precedência sobre o `searchFields` parâmetro, razão pela qual, neste exemplo, não precisamos de incluir `business_title` no `searchFields` parâmetro.

## <a name="example-3-fuzzy-search"></a>Exemplo 3: Pesquisa felpuda

A sintaxe Lucene completa também suporta a pesquisa fuzzy, combinando em termos que têm uma construção semelhante. Para fazer uma pesquisa difusa, apedte o `~` símbolo de azulejos no final de uma única palavra com um parâmetro opcional, um valor entre 0 e 2, que especifica a distância de edição. Por exemplo, `blue~` ou `blue~1` voltaria azul, azul e cola.

### <a name="search-expression"></a>Expressão de pesquisa

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~
```

As frases não são suportadas diretamente, mas pode especificar uma correspondência difusa nas partes componentes de uma frase.

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~ AND comm~ 
```


### <a name="full-url"></a>URL completo

Esta consulta procura empregos com o termo "associado" (deliberadamente mal escrito):

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:asosiate~
```
  ![Resposta de pesquisa fuzzy](media/search-query-lucene-examples/fuzzysearch.png)


> [!Note]
> As consultas fuzzy não são [analisadas.](search-lucene-query-architecture.md#stage-2-lexical-analysis) Os tipos de consulta com termos incompletos (consulta de prefixo, consulta wildcard, consulta regex, consulta fuzzy) são adicionados diretamente à árvore de consulta, contornando a fase de análise. A única transformação realizada em termos de consulta incompleta é a redução.
>

## <a name="example-4-proximity-search"></a>Exemplo 4: Pesquisa de proximidade
As pesquisas de proximidade são usadas para encontrar termos que estão próximos uns dos outros em um documento. Insira um símbolo de azulejos "~" no final de uma frase seguida do número de palavras que criam o limite de proximidade. Por exemplo, "hotel airport"~5 encontrará os termos hotel e aeroporto dentro de 5 palavras um do outro em um documento.

### <a name="search-expression"></a>Expressão de pesquisa

```http
searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```

### <a name="full-url"></a>URL completo

Nesta consulta, para empregos com o termo "analista sénior" onde é separado por não mais do que uma palavra:

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```
  :::image type="content" source="media/search-query-lucene-examples/proximity-before.png" alt-text="Consulta de proximidade" border="false":::

Tente de novo removendo as palavras entre o termo "analista sénior". Note que 8 documentos são devolvidos para esta consulta em oposição a 10 para a consulta anterior.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>Exemplo 5: Aumento de prazos
O aumento de prazos refere-se à classificação de um documento mais elevado se contiver o termo aumentado, em relação a documentos que não contêm o termo. Para impulsionar um termo, use o caret, "^", símbolo com um fator de impulso (um número) no final do termo que está a pesquisar. 

### <a name="full-urls"></a>URLs completos

Nesta consulta "antes", procure empregos com o termo *analista de computadores* e note que não há resultados com ambas as palavras *computador* e *analista*, mas os empregos *informáticos* estão no topo dos resultados.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst
```
  :::image type="content" source="media/search-query-lucene-examples/termboostingbefore.png" alt-text="Aumento de prazos antes" border="false":::

Na consulta "depois", repita a procura, desta vez impulsionando os resultados com o termo *analista* ao longo do termo *computador* se ambas as palavras não existirem. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst%5e2
```
Uma versão mais legível humana da consulta acima é `search=business_title:computer analyst^2` . Para uma consulta viável, `^2` é codificado como `%5E2` , o que é mais difícil de ver.

  :::image type="content" source="media/search-query-lucene-examples/termboostingafter.png" alt-text="Aumento de prazos após" border="false":::

O aumento de prazos difere dos perfis de pontuação em que os perfis de pontuação impulsionam certos campos, em vez de termos específicos. O exemplo a seguir ajuda a ilustrar as diferenças.

Considere um perfil de pontuação que impulsione os jogos em determinado campo, como o **género** no exemplo musicstoreindex. O aumento de prazos poderia ser usado para aumentar ainda mais certos termos de pesquisa mais elevados do que outros. Por exemplo, "rock^2 electronic" irá impulsionar documentos que contenham os termos de pesquisa no campo do **género** mais elevados do que outros campos pes pesjáveis no índice. Além disso, os documentos que contenham o termo de pesquisa "rock" serão classificados acima do outro termo de pesquisa "eletrónico" como resultado do valor de aumento do termo (2).

Ao definir o nível de fator, quanto maior for o fator de impulso, mais relevante será o termo relativo a outros termos de pesquisa. Por padrão, o fator de impulso é 1. Embora o fator de impulso tenha de ser positivo, pode ser inferior a 1 (por exemplo, 0,2).


## <a name="example-6-regex"></a>Exemplo 6: Regex

Uma pesquisa regular de expressão encontra uma correspondência com base no conteúdo entre barras para a frente "/", como documentado na [classe RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).

### <a name="search-expression"></a>Expressão de pesquisa

```http
searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

### <a name="full-url"></a>URL completo

Nesta consulta, procure empregos com o termo Senior ou Júnior: `search=business_title:/(Sen|Jun)ior/` .

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

  :::image type="content" source="media/search-query-lucene-examples/regex.png" alt-text="Consulta regex" border="false":::

> [!Note]
> As consultas de Regex não são [analisadas.](./search-lucene-query-architecture.md#stage-2-lexical-analysis) A única transformação realizada em termos de consulta incompleta é a redução.
>

## <a name="example-7-wildcard-search"></a>Exemplo 7: Pesquisa de Wildcard
Pode utilizar sintaxe geralmente reconhecida para pesquisas de caracteres múltiplos \* () ou simples (?) de caracteres. Note que o parser de consulta Lucene suporta o uso destes símbolos com um único termo, e não uma frase.

### <a name="search-expression"></a>Expressão de pesquisa

```http
searchFields=business_title&$select=business_title&search=business_title:prog*
```

### <a name="full-url"></a>URL completo

Nesta consulta, procure empregos que contenham o prefixo 'prog' que incluiria títulos empresariais com os termos de programação e programador nele. Não pode usar um ou? símbolo como o primeiro caráter de uma pesquisa.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:prog*
```
  :::image type="content" source="media/search-query-lucene-examples/wildcard.png" alt-text="Consulta wildcard" border="false":::

> [!Note]
> As consultas wildcard não são [analisadas.](./search-lucene-query-architecture.md#stage-2-lexical-analysis) A única transformação realizada em termos de consulta incompleta é a redução.
>

## <a name="next-steps"></a>Passos seguintes
Tente especificar o Lucene Query Parser no seu código. Os seguintes links explicam como configurar consultas de pesquisa tanto para .NET como para a API REST. As ligações utilizam a sintaxe simples padrão, pelo que terá de aplicar o que aprendeu com este artigo para especificar a **consultaType**.

* [Consultar o seu índice utilizando o .NET SDK](./search-get-started-dotnet.md)
* [Consultar o seu índice utilizando a API REST](./search-get-started-powershell.md)

Referência de sintaxe adicional, arquitetura de consulta e exemplos podem ser encontrados nos seguintes links:

+ [Exemplos simples de consulta de sintaxe](search-query-simple-examples.md)
+ [Como funciona a pesquisa em texto completo no Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Sintaxe de consulta simples](/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Sintaxe de consulta lucene completa](/rest/api/searchservice/lucene-query-syntax-in-azure-search)