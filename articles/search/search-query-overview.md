---
title: Tipos de consulta e composição-Azure Search
description: Noções básicas para criar uma consulta de pesquisa no Azure Search, usando parâmetros para filtrar, selecionar e classificar resultados.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/13/2019
ms.custom: seodec2018
ms.openlocfilehash: c2d40865857bc3ea8367199ed29e0220a0e7c557
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882602"
---
# <a name="how-to-compose-a-query-in-azure-search"></a>Como compor uma consulta no Azure Search

No Azure Search, uma consulta é uma especificação completa de uma operação de viagem de ida e volta. Os parâmetros na solicitação fornecem critérios de correspondência para localizar documentos em um índice, instruções de execução para o mecanismo e diretivas para formatar a resposta. 

Uma solicitação de consulta é uma construção avançada, especificando quais campos estão no escopo, como Pesquisar, quais campos retornar, se deseja classificar ou filtrar e assim por diante. Não especificado, uma consulta é executada em todos os campos pesquisáveis como uma operação de pesquisa de texto completo, retornando um conjunto de resultados não pontuados em ordem arbitrária.

## <a name="apis-and-tools-for-testing"></a>APIs e ferramentas para teste

A tabela a seguir lista as APIs e abordagens baseadas em ferramentas para enviar consultas.

| Metodologia | Descrição |
|-------------|-------------|
| [Gerenciador de pesquisa (Portal)](search-explorer.md) | Fornece uma barra de pesquisa e opções para as seleções de índice e versão de API. Os resultados são retornados como documentos JSON. <br/>[Saiba mais.](search-get-started-portal.md#query-index) | 
| [Postmaster ou Fiddler](search-get-started-postman.md) | As ferramentas de teste na Web são uma excelente opção para chamadas REST formular. A API REST dá suporte a todas as operações possíveis no Azure Search. Neste artigo, saiba como configurar um cabeçalho de solicitação HTTP e o corpo para enviar solicitações para Azure Search.  |
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Cliente que pode ser usado para consultar um índice de Azure Search.  <br/>[Saiba mais.](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Pesquisar documentos (API REST)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | Métodos GET ou POST em um índice, usando parâmetros de consulta para entrada adicional.  |

## <a name="a-first-look-at-query-requests"></a>Uma primeira olhada nas solicitações de consulta

Exemplos são úteis para introduzir novos conceitos. Como uma consulta representativa construída na [API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents), este exemplo tem como alvo o [índice de demonstração de imóveis](search-get-started-portal.md) e inclui parâmetros comuns.

```
{
    "queryType": "simple" 
    "search": "seattle townhouse* +\"lake\"",
    "searchFields": "description, city",
    "count": "true",
    "select": "listingId, street, status, daysOnMarket, description",
    "top": "10",
    "orderby": "daysOnMarket"
}
```

+ **`queryType`** define o analisador, que em Azure Search pode ser o [analisador de consulta simples padrão](search-query-simple-examples.md) (ideal para pesquisa de texto completo) ou o analisador de [consulta Lucene completo](search-query-lucene-examples.md) usado para construções de consulta avançada, como expressões regulares, pesquisa de proximidade, difusa e curinga Pesquise, para citar alguns.

+ **`search`** fornece os critérios de correspondência, geralmente texto, mas geralmente acompanhados por operadores boolianos. Termos autônomos únicos são consultas de *termo* . As consultas de várias partes entre aspas são consultas de *frase-chave* . A pesquisa pode ser indefinida, como **`search=*`** em, mas é mais provável que ela seja composta por termos, frases e operadores semelhantes ao que aparece no exemplo.

+ **`searchFields`** é opcional, usado para restringir a execução da consulta a campos específicos.

As respostas também são moldadas pelos parâmetros que você inclui na consulta. No exemplo, o conjunto de resultados consiste em campos listados na **`select`** instrução. Somente os 10 principais acertos são retornados nessa consulta, mas **`count`** informa quantos documentos correspondem ao geral. Nessa consulta, as linhas são classificadas por daysOnMarket.

No Azure Search, a execução da consulta é sempre em relação a um índice, autenticado usando uma chave de API fornecida na solicitação. Em repouso, ambos são fornecidos nos cabeçalhos de solicitação.

### <a name="how-to-run-this-query"></a>Como executar esta consulta

Para executar essa consulta, use [o Gerenciador de pesquisa e o índice de demonstração de imóveis](search-get-started-portal.md). 

Você pode colar essa cadeia de caracteres de consulta na barra de pesquisa do Gerenciador:`search=seattle townhouse +lake&searchFields=description, city&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket`

## <a name="how-query-operations-are-enabled-by-the-index"></a>Como as operações de consulta são habilitadas pelo índice

Design de índice e design de consulta são rigidamente acoplados em Azure Search. Um fato essencial para saber antecipadamente é que o *esquema de índice*, com atributos em cada campo, determina o tipo de consulta que você pode criar. 

Atributos de índice em um campo definem as operações permitidas-se um campo é pesquisável no índice, *recuperável* em Results, *classificável*, *filtrável*e assim por diante. Na cadeia de caracteres de consulta `"$orderby": "daysOnMarket"` de exemplo, só funciona porque o campo daysOnMarket está marcado como *classificável* no esquema de índice. 

![Definição de índice para o exemplo de espaço real](./media/search-query-overview/realestate-sample-index-definition.png "Definição de índice para o exemplo de espaço real")

A captura de tela acima é uma lista parcial de atributos de índice para o exemplo de espaço real. Você pode exibir o esquema de índice inteiro no Portal. Para obter mais informações sobre atributos de índice, consulte [criar API REST de índice](https://docs.microsoft.com/rest/api/searchservice/create-index).

> [!Note]
> Algumas funcionalidades de consulta são habilitadas para índice em vez de em uma base por campo. Esses recursos incluem: [mapas](search-synonyms.md)de sinônimos, [analisadores personalizados](index-add-custom-analyzers.md), construções de sugestores [(para consultas de preenchimento automático e sugerido)](index-add-suggesters.md), [lógica de Pontuação para resultados de classificação](index-add-scoring-profiles.md).

## <a name="elements-of-a-query-request"></a>Elementos de uma solicitação de consulta

As consultas são sempre direcionadas a um único índice. Você não pode unir índices ou criar estruturas de dados personalizadas ou temporárias como um destino de consulta. 

Os elementos necessários em uma solicitação de consulta incluem os seguintes componentes:

+ Coleção de pontos de extremidade de serviço e de índice, expressa como uma URL que contém componentes fixos e definidos pelo usuário: **`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`** (Somente REST) é necessário porque mais de uma versão da API está disponível o tempo todo. 
+ **`api-key`** , uma consulta ou uma chave de API de administração, autentica a solicitação para seu serviço.
+ **`queryType`** , simples ou completo, que pode ser omitido se você estiver usando a sintaxe simples padrão interna.
+ **`search`** ou **`filter`** fornece os critérios de correspondência, que podem ser não especificados se você quiser executar uma pesquisa vazia. Ambos os tipos de consulta são discutidos em termos do analisador simples, mas até mesmo consultas avançadas exigem o parâmetro de pesquisa para passar expressões de consulta complexas.

Todos os outros parâmetros de pesquisa são opcionais. Para obter a lista completa de atributos, consulte [criar índice (REST)](https://docs.microsoft.com/rest/api/searchservice/create-index). Para uma análise mais detalhada de como os parâmetros são usados durante o processamento, consulte [como funciona a pesquisa de texto completo no Azure Search](search-lucene-query-architecture.md).

## <a name="choose-a-parser-simple--full"></a>Escolha um analisador: simples | completo

Azure Search se encontra na parte superior do Apache Lucene e oferece a você uma opção entre dois analisadores de consulta para lidar com consultas típicas e especializadas. As solicitações que usam o analisador simples são formuladas usando a [sintaxe de consulta simples](query-simple-syntax.md), selecionada como o padrão para sua velocidade e eficácia em consultas de texto de forma livre. Essa sintaxe oferece suporte a vários operadores de pesquisa comuns, incluindo os operadores e, ou, não, frase, sufixo e precedência.

A [sintaxe de consulta Lucene completa](query-Lucene-syntax.md#bkmk_syntax), habilitada quando `queryType=full` você adiciona à solicitação, expõe a linguagem de consulta amplamente adotada e expressiva desenvolvida como parte do [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). A sintaxe completa estende a sintaxe simples. Qualquer consulta que você escreve para a sintaxe simples é executada no analisador Lucene completo. 

Os exemplos a seguir ilustram o ponto: a mesma consulta, mas com configurações de QueryType diferentes, produzem resultados diferentes. Na primeira consulta, o `^3` é tratado como parte do termo de pesquisa.

```
queryType=simple&search=mountain beach garden ranch^3&searchFields=description&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket
```

A mesma consulta que usa o analisador Lucene completo interpreta o aumento no campo em "Ranch", que aumenta a classificação de pesquisa de resultados que contêm esse termo específico.

```
queryType=full&search=mountain beach garden ranch^3&searchFields=description&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Tipos de consultas

O Azure Search dá suporte a uma ampla variedade de tipos de consulta. 

| Tipo de consulta | Utilização | Exemplos e mais informações |
|------------|--------|-------------------------------|
| Pesquisa de texto de forma livre | Parâmetro de pesquisa e o analisador| A pesquisa de texto completo procura um ou mais termos em todos os campos pesquisáveis em seu índice e funciona da forma como você esperaria que um mecanismo de pesquisa como o Google ou o Bing funcionasse. O exemplo na introdução é pesquisa de texto completo.<br/><br/>A pesquisa de texto completo passa por uma análise de texto usando o analisador Lucene padrão (por padrão) para reduzir todos os termos, remover palavras de parada como "a". Você pode substituir o padrão por analisadores [não-inglês](index-add-language-analyzers.md#language-analyzer-list) ou [analisadores independentes de linguagem especializada](index-add-custom-analyzers.md#AnalyzerTable) que modificam a análise de texto. Um exemplo é a [palavra-chave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) que trata todo o conteúdo de um campo como um único token. Isso é útil para dados como CEPs, IDs e alguns nomes de produtos. | 
| Pesquisa filtrada | [Expressão de filtro OData](query-odata-filter-orderby-syntax.md) e um analisador | As consultas de filtro avaliam uma expressão booliana em todos os campos filtráveis em um índice. Ao contrário da pesquisa, uma consulta de filtro corresponde ao conteúdo exato de um campo, incluindo a diferenciação de maiúsculas e minúsculas nos campos de cadeia de caracteres. Outra diferença é que as consultas de filtro são expressas na sintaxe do OData. <br/>[Exemplo de expressão de filtro](search-query-simple-examples.md#example-3-filter-queries) |
| Pesquisa geográfica | [EDM. GeographyPoint tipo](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) no campo, expressão de filtro e qualquer analisador | As coordenadas armazenadas em um campo com um EDM. GeographyPoint são usadas para "localizar perto de mim" ou controles de pesquisa baseados em mapa. <br/>[Exemplo de pesquisa geográfica](search-query-simple-examples.md#example-5-geo-search)|
| Pesquisa de intervalo | expressão de filtro e analisador simples | Em Azure Search, as consultas de intervalo são criadas usando o parâmetro de filtro. <br/>[Exemplo de filtro de intervalo](search-query-simple-examples.md#example-4-range-filters) | 
| [Pesquisa em campo](query-lucene-syntax.md#bkmk_fields) | Parâmetro de pesquisa e analisador completo | Crie uma expressão de consulta composta direcionada a um único campo. <br/>[Exemplo de pesquisa em campo](search-query-lucene-examples.md#example-2-fielded-search) |
| [pesquisa difusa](query-lucene-syntax.md#bkmk_fuzzy) | Parâmetro de pesquisa e analisador completo | Corresponde a termos com uma construção ou grafia semelhante. <br/>[Exemplo de pesquisa difusa](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [pesquisa de proximidade](query-lucene-syntax.md#bkmk_proximity) | Parâmetro de pesquisa e analisador completo | Localiza os termos que estão próximos um do outro em um documento. <br/>[Exemplo de pesquisa de proximidade](search-query-lucene-examples.md#example-4-proximity-search) |
| [aumento de termo](query-lucene-syntax.md#bkmk_termboost) | Parâmetro de pesquisa e analisador completo | Classifica um documento mais alto se ele contiver o termo aumentado, em relação a outros que não. <br/>[Exemplo de aumento de termo](search-query-lucene-examples.md#example-5-term-boosting) |
| [pesquisa de expressão regular](query-lucene-syntax.md#bkmk_regex) | Parâmetro de pesquisa e analisador completo | Corresponde com base no conteúdo de uma expressão regular. <br/>[Exemplo de expressão regular](search-query-lucene-examples.md#example-6-regex) |
|  [pesquisa de prefixo ou curinga](query-lucene-syntax.md#bkmk_wildcard) | Parâmetro de pesquisa e analisador completo | Corresponde com base em um prefixo e til`~`() ou caractere único`?`(). <br/>[Exemplo de pesquisa curinga](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="manage-search-results"></a>Gerenciar resultados da pesquisa 

Os resultados da consulta são transmitidos como documentos JSON na API REST, embora se você usar as APIs do .NET, a serialização será interna. Você pode formatar os resultados definindo parâmetros na consulta, selecionando campos específicos para a resposta.

Os parâmetros na consulta podem ser usados para estruturar o conjunto de resultados das seguintes maneiras:

+ Limitando ou enviando em lote o número de documentos nos resultados (50 por padrão)
+ Selecionando campos para incluir nos resultados
+ Definindo uma ordem de classificação
+ Adicionando realces de ocorrências para chamar a atenção para os termos correspondentes no corpo dos resultados da pesquisa

### <a name="tips-for-unexpected-results"></a>Dicas para resultados inesperados

Ocasionalmente, a substância e não a estrutura dos resultados são inesperados. Quando os resultados da consulta não são o que você espera ver, você pode tentar essas modificações de consulta para ver se o resultado melhora:

+ Altere **`searchMode=any`** (padrão) para **`searchMode=all`** para exigir correspondências em todos os critérios em vez de qualquer um dos critérios. Isso é especialmente verdadeiro quando operadores boolianos são incluídos na consulta.

+ Altere a técnica de consulta se a análise de texto ou lexical for necessária, mas o tipo de consulta impede o processamento linguístico. Na pesquisa de texto completo, a análise de texto ou lexical corrige os erros de ortografia, as formas de palavra singular e até mesmo verbos ou substantivos irregulares. Para algumas consultas como pesquisa difusa ou curinga, a análise de texto não faz parte do pipeline de análise de consulta. Para alguns cenários, as expressões regulares foram usadas como uma solução alternativa. 

### <a name="paging-results"></a>Resultados de paginação
A Azure Search facilita a implementação da paginação dos resultados da pesquisa. Usando os **`top`** parâmetros e **`skip`** , você pode emitir rapidamente solicitações de pesquisa que permitem receber o conjunto total de resultados da pesquisa em subconjuntos gerenciáveis e ordenados que habilitam facilmente boas práticas de interface do usuário de pesquisa. Ao receber esses subconjuntos de resultados mais pequenos, pode também receber a contagem de documentos no conjunto total de resultados da pesquisa.

Pode saber mais sobre a paginação dos resultados da pesquisa no artigo [Como paginar os resultados da pesquisa na Azure Search](search-pagination-page-layout.md).

### <a name="ordering-results"></a>Ordenar resultados
Ao receber resultados de uma consulta de pesquisa, pode pedir à Azure Search para apresentar os resultados ordenados pelos valores num campo específico. Por predefinição, a Azure Search ordena os resultados da pesquisa com base na classificação de pontuação de pesquisa de cada documento, a qual deriva de [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Se desejar que Azure Search retorne os resultados ordenados por um valor diferente da Pontuação de pesquisa, você poderá usar **`orderby`** o parâmetro de pesquisa. Você pode especificar o valor do **`orderby`** parâmetro para incluir nomes de campo e chamadas para a [ **`geo.distance()` função**](query-odata-filter-orderby-syntax.md) para valores geoespaciais. Cada expressão pode ser seguida por `asc` para indicar que os resultados são solicitados em ordem crescente e **`desc`** para indicar que os resultados são solicitados em ordem decrescente. A ordem ascendente de classificação predefinida.


### <a name="hit-highlighting"></a>Detetor de ocorrências
No Azure Search, enfatizando a parte exata dos resultados da pesquisa que correspondem à consulta de pesquisa é facilitada usando **`highlight`** os **`highlightPreTag`** parâmetros, **`highlightPostTag`** e. Pode especificar os campos *pesquisáveis* que devem ter o respetivo texto com correspondência realçado, bem como especificar as etiquetas de cadeias exatas a adicionar no início e no fim do texto com correspondência devolvido pela Azure Search.

## <a name="see-also"></a>Consulte também

+ [Como a pesquisa de texto completo funciona no Azure Search (arquitetura de análise de consulta)](search-lucene-query-architecture.md)
+ [Gerenciador de pesquisa](search-explorer.md)
+ [Como consultar no .NET](search-query-dotnet.md)
+ [Como consultar em REST](search-create-index-rest-api.md)
