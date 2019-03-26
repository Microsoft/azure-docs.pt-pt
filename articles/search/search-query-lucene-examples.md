---
title: Exemplos de consulta Lucene - Azure Search
description: Sintaxe de consulta Lucene para a pesquisa difusa, pesquisa de proximidade, aumentos de termos, pesquisa de expressão regular e pesquisas com carateres universais num serviço Azure Search.
author: HeidiSteen
manager: cgronlun
tags: Lucene query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: c7c120b9bac33f71df72650d8a9d9a72e819d227
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439206"
---
# <a name="query-examples-using-full-lucene-search-syntax-advanced-queries-in-azure-search"></a>Exemplos de consulta usando a sintaxe de pesquisa do "completo" Lucene (consultas avançadas no Azure Search)

Ao construir consultas para o Azure Search, pode substituir a predefinição [analisador de consultas simples](query-simple-syntax.md) com o abrangente [analisador de consultas do Lucene no Azure Search](query-lucene-syntax.md) para formular a consulta especializada e avançada definições. 

O analisador de Lucene suporta as construções de consulta complexas, como consultas de âmbito de campo, difusas e pesquisa de curinga de prefixo, pesquisa de proximidade, aumentos de termos e pesquisa de expressão regular. O poder adicional vem com requisitos de processamento adicional para que deve esperar um pouco mais tempo de execução. Neste artigo, pode percorrer exemplos que demonstram as operações de consulta disponíveis quando utiliza a sintaxe completa.

> [!Note]
> Muitos das construções de consulta especializadas ativadas através da sintaxe de consulta Lucene completa não são [analisados de texto](search-lucene-query-architecture.md#stage-2-lexical-analysis), que pode ser uma surpresa, se esperar Lematização ou à Lematização. Análise lexical só é executada em termos de conclusão (uma consulta de termo ou a consulta de frase). Tipos de consulta com os termos incompletos (consulta de prefixo, consulta de caráter universal, consultas de regex, consulta difusa) são adicionados diretamente à árvore de consulta, ignorando a fase de análise. A transformação de apenas executada nos termos de consulta incompleta é minúsculas. 
>

## <a name="formulate-requests-in-postman"></a>Formular pedidos no Postman

Os exemplos seguintes tirar partido de um índice de pesquisa de empregos em nova IORQUE consistindo em tarefas disponíveis com base num conjunto de dados fornecido pelos [cidade de Nova Iorque OpenData](https://opendata.cityofnewyork.us/) iniciativa. Estes dados não devem ser considerados atual ou completa. O índice é um serviço de proteção de segurança fornecidas pela Microsoft, o que significa que não é necessário uma subscrição do Azure ou o Azure Search para experimentar estas consultas.

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

Como passo de verificação, cole o seguinte pedido GET e clique em **enviar**. Os resultados são retornados como verbosos de documentos JSON. Todos os documentos são devolvidos, que permite-lhe ver todos os campos e todos os valores.

Cole este URL para um cliente REST como uma etapa de validação e para ver a estrutura do documento.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=*
  ```

A cadeia de consulta **`search=*`**, equivale uma pesquisa não especificada para a pesquisa de nula ou estar vazia. É a pesquisa mais simples, que pode fazer.

Opcionalmente, pode adicionar **`$count=true`** para o URL para devolver uma contagem dos documentos correspondentes aos critérios de pesquisa. Numa cadeia de caracteres de pesquisa em branco, trata todos os documentos no índice (aproximadamente 2800 no caso de empregos em nova IORQUE).

## <a name="how-to-invoke-full-lucene-parsing"></a>Como invocar a análise de Lucene completo

Adicione **queryType = full** para invocar a sintaxe de consulta completa, substituindo a sintaxe de consulta simples predefinido. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&search=*
```

Todos os exemplos neste artigo de especificar o **queryType = full** parâmetro, que indica que a sintaxe completa é processada pelo Parser de consulta Lucene de pesquisa. 

## <a name="example-1-field-scoped-query"></a>Exemplo 1: Consulta com abrangência de campo

Este primeiro exemplo não é específico do Lucene, mas vamos levar com ele para apresentar o primeiro conceito de consulta fundamentais: contenção. Neste exemplo examina a execução da consulta e a resposta a apenas alguns campos específicos. É importante saber como estruturar uma resposta JSON legível quando sua ferramenta é o Explorador de pesquisa ou o Postman. 

Para fins de brevidade, a consulta destina-se apenas a *business_title* campo e especifica apenas os títulos de negócios são retornados. A sintaxe é **searchFields** para restringir a execução da consulta para apenas o campo de business_title, e **selecione** para especificar os campos que estão incluídos na resposta.

### <a name="partial-query-string"></a>Cadeia de consulta parcial

```http
&search=*&searchFields=business_title&$select=business_title
```

Eis a mesma consulta com vários campos de uma lista delimitada por vírgulas.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

### <a name="full-url"></a>URL completo

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&$count=true&search=*&searchFields=business_title&$select=business_title
```

A resposta para esta consulta deve ser semelhante à seguinte captura de ecrã.

  ![Resposta de exemplo do postman](media/search-query-lucene-examples/postman-sample-results.png)

Poderá ter reparado a pontuação de pesquisa na resposta. Pontuações uniformes de 1 ocorrerem quando não existe nenhuma classificação, seja porque a pesquisa não era a pesquisa em texto completo ou porque não existem critérios foi aplicada. Para obter pesquisa nulo sem critérios, linhas voltar na ordem arbitrária. Quando inclui critérios reais, verá as pontuações evoluam em valores significativos de pesquisa.

## <a name="example-2-intra-field-filtering"></a>Exemplo 2: Filtragem em campo

Sintaxe de Lucene completa oferece suporte a expressões num campo. Neste exemplo procura títulos de negócios com o sênior do termo em-los, mas não júnior.

### <a name="partial-query-string"></a>Cadeia de consulta parcial

```http
searchFields=business_title&$select=business_title&search=business_title:senior+NOT+junior
```

Eis a mesma consulta com vários campos.

```http
searchFields=business_title, posting_type&$select=business_title, posting_type&search=business_title:senior+NOT+junior AND posting_type:external
```

### <a name="full-url"></a>URL completo

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:senior+NOT+junior
```

  ![Resposta de exemplo do postman](media/search-query-lucene-examples/intrafieldfilter.png)

Ao especificar uma **fieldname:searchterm** construção, pode definir uma operação de consulta respondidas, onde o campo é uma única palavra, e o termo de pesquisa também é uma única palavra ou frase, opcionalmente, com operadores booleanos. Alguns exemplos incluem o seguinte:

* business_title:(senior NOT junior)
* Estado: ("New York" e "Nova Jérsia")

Certifique-se de que colocar várias cadeias de caracteres entre aspas, se pretender que ambas as cadeias de caracteres a ser avaliada como uma única entidade, como nesse caso a procurar duas cidades diferentes no campo localização. Além disso, certifique-se o operador está em maiúsculas como pode ver com não e and.

O campo especificado no **fieldname:searchterm** tem de ser um campo pesquisável. Ver [criar índice (API do REST de serviço de pesquisa do Azure)](https://docs.microsoft.com/rest/api/searchservice/create-index) para obter detalhes sobre como os atributos de índice são usados nas definições de campo.

## <a name="example-3-fuzzy-search"></a>Exemplo 3: pesquisa difusa

Sintaxe de Lucene completa também suporta a pesquisa difusa, que correspondem à medida que têm uma construção semelhante. Para fazer uma pesquisa difusa, acrescente o til `~` símbolo no final de uma única palavra com um parâmetro opcional, um valor entre 0 e 2, que especifica a distância de edição. Por exemplo, `blue~` ou `blue~1` retornaria azul, blues e cola.

### <a name="partial-query-string"></a>Cadeia de consulta parcial

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~
```

Expressões não são suportados diretamente, mas pode especificar uma correspondência por semelhantes em componentes de uma frase.

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~ AND comm~ 
```


### <a name="full-url"></a>URL completo

Esta consulta pesquisa para as tarefas com o termo "associar" (deliberadamente mal escrito):

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:asosiate~
```
  ![Resposta de pesquisa difusa](media/search-query-lucene-examples/fuzzysearch.png)


> [!Note]
> As consultas difusas não são [analisados](search-lucene-query-architecture.md#stage-2-lexical-analysis). Tipos de consulta com os termos incompletos (consulta de prefixo, consulta de caráter universal, consultas de regex, consulta difusa) são adicionados diretamente à árvore de consulta, ignorando a fase de análise. A transformação de apenas executada nos termos de consulta incompleta é minúsculas.
>

## <a name="example-4-proximity-search"></a>Exemplo 4: pesquisa de proximidade
Pesquisas de proximidades são usadas para encontrar os termos que estejam perto uns dos outros num documento. Inserir um til "~" símbolo no final de uma frase seguido do número de palavras que criar o limite de proximidade. Por exemplo, "aeroporto de hotel" ~ 5 encontrará o hotel de termos e aeroporto dentro de 5 palavras da outra num documento.

### <a name="partial-query-string"></a>Cadeia de consulta parcial

```http
searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```

### <a name="full-url"></a>URL completo

Nesta consulta, para as tarefas com o termo "analista sênior", onde ele é separado por mais do que uma palavra:

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```
  ![Consulta de proximidade](media/search-query-lucene-examples/proximity-before.png)

Experimente remover novamente as palavras entre o termo "analista sênior". Tenha em atenção que 8 documentos são devolvidos para esta consulta em vez de 10 para a consulta anterior.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>Exemplo 5: prazo adaptativo
Aumentos de termos refere-se a classificação de um documento superior se contiver o termo elevado, em relação ao documentos que contenham o termo. Para aumentar um termo, utilize o sinal de interpolação, "^", símbolo com um fator de aumento (um número) no final do período de vigência esteja a pesquisar. 

### <a name="full-urls"></a>URLs completos

Nesta "consulta antes", procure tarefas com o termo *analista de computador* e repare que não há resultados com ambas as palavras *computador* e *analista*, ainda  *computador* tarefas estão na parte superior dos resultados.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst
```
  ![Prazo adaptativo antes de](media/search-query-lucene-examples/termboostingbefore.png)

Na consulta "depois", repetir a pesquisa, desta vez adaptativo resultados com o termo *analista* sobre o termo *computador* se não existirem ambas as palavras. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst%5e2
```
Uma versão mais humana legível da consulta acima é `search=business_title:computer analyst^2`. Para uma consulta viável, `^2` é codificado como `%5E2`, que é mais difícil de ver.

  ![Prazo adaptativo após](media/search-query-lucene-examples/termboostingafter.png)

Aumentos de termos difere de perfis de classificação em que aumentam a perfis de classificação determinados campos, em vez de termos específicos. O exemplo a seguir ajuda a ilustrar as diferenças.

Considere corresponde a um perfil de classificação que aumenta num determinado campo, como **gênero** no exemplo musicstoreindex. Aumentos de termos poderiam ser usado para melhorar ainda mais determinada pesquisa termos mais do que outras pessoas. Por exemplo, "rock ^ 2 eletrônicos" irá aumentar a documentos que contêm os termos de pesquisa na **gênero** campo maior do que outros campos pesquisáveis no índice. Além disso, documentos que contenham o termo de pesquisa "rock" serão classificados superior do que o outro termo de pesquisa "eletrónico" como resultado o valor de aumento de termo (2).

Ao definir o nível de fatores, quanto maior for o fator de aumento, o mais relevante o termo serão relativas a outros termos de pesquisa. Por predefinição, o fator de aumento é 1. Embora o fator de boost tem de ser positivo, ele pode ser inferior a 1 (por exemplo, 0,2).


## <a name="example-6-regex"></a>Exemplo 6: RegEx

Uma pesquisa de expressão regular encontra uma correspondência com base no conteúdo entre barras "/", como documentado na [RegExp classe](https://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

### <a name="partial-query-string"></a>Cadeia de consulta parcial

```http
searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

### <a name="full-url"></a>URL completo

Nesta consulta de pesquisa para as tarefas com o termo Sênior ou Junior: `search=business_title:/(Sen|Jun)ior/`.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

  ![Consulta de RegEx](media/search-query-lucene-examples/regex.png)

> [!Note]
> As consultas de RegEx não são [analisados](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). A transformação de apenas executada nos termos de consulta incompleta é minúsculas.
>

## <a name="example-7-wildcard-search"></a>Exemplo 7: pesquisa com carateres universais
Pode usar sintaxe geralmente reconhecido para vários (\*) ou único pesquisas de com carateres universais do caractere (?). Tenha em atenção de que o analisador de consultas de Lucene suporta a utilização desses símbolos com um único termo e não uma frase.

### <a name="partial-query-string"></a>Cadeia de consulta parcial

```http
searchFields=business_title&$select=business_title&search=business_title:prog*
```

### <a name="full-url"></a>URL completo

Nesta consulta, procure as tarefas que contêm o prefixo "programa", que inclui títulos de negócios com os termos de programação e o programador nela. Não é possível utilizar um * ou? símbolo de como o primeiro caráter de uma pesquisa.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:prog*
```
  ![Consulta de caráter universal](media/search-query-lucene-examples/wildcard.png)

> [!Note]
> As consultas de carateres universais não são [analisados](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). A transformação de apenas executada nos termos de consulta incompleta é minúsculas.
>

## <a name="next-steps"></a>Passos Seguintes
Experimente a especificar o analisador de consultas do Lucene no seu código. As ligações seguintes explicam como configurar a consultas de pesquisa para o .NET e a API REST. As ligações utilizam a sintaxe simples padrão, por isso terá de aplicar o que aprendeu deste artigo para especificar a **queryType**.

* [Consultar o índice da Azure Search utilizando o SDK .NET](search-query-dotnet.md)
* [Consultar o índice de pesquisa do Azure com a API REST](search-create-index-rest-api.md)

Referência de sintaxe adicional, a arquitetura de consulta e exemplos podem ser encontrados nas seguintes ligações:

+ [Exemplos de consulta de sintaxe simples](search-query-simple-examples.md)
+ [Completa como funciona a pesquisa de texto no Azure Search](search-lucene-query-architecture.md)
+ [Sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Sintaxe de consulta Lucene completa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)