---
title: Tipos de consulta e composição
titleSuffix: Azure Cognitive Search
description: Básicos para a construção de uma consulta de pesquisa na Pesquisa Cognitiva Azure, utilizando parâmetros para filtrar, selecionar e classificar resultados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 0c05db39e02a6bc2a7fa5d62b8b891626eb0d241
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675804"
---
# <a name="query-types-and-composition-in-azure-cognitive-search"></a>Tipos de consulta e composição em Pesquisa Cognitiva Azure

Em Azure Cognitive Search, uma consulta é uma especificação completa de uma operação de ida e volta. A pedido, existem parâmetros que fornecem instruções de execução para o motor, bem como parâmetros que moldam a resposta voltando. Não especificado ( ), `search=*` sem critérios de correspondência e usando parâmetros nulos ou predefinidos, uma consulta executa contra todos os campos pes pesjáveis como uma operação completa de pesquisa de texto, devolvendo um resultado não marcado definido por ordem arbitrária.

Segue-se uma consulta representativa construída na [API REST](/rest/api/searchservice/search-documents). Este exemplo visa o [índice de demonstração de hotéis](search-get-started-portal.md) e inclui parâmetros comuns para que você possa ter uma ideia de como é uma consulta.

```
{
    "queryType": "simple" 
    "search": "+New York +restaurant",
    "searchFields": "Description, Address/City, Tags",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

+ **`queryType`** define o parser, que é o [parser de consulta simples padrão](search-query-simple-examples.md) (ideal para pesquisa completa de texto), ou o [parser de consulta lucene completo](search-query-lucene-examples.md) usado para construções de consultas avançadas como expressões regulares, pesquisa de proximidade, pesquisa de nádpos e wildcard, para citar alguns.

+ **`search`** fornece os critérios de correspondência, geralmente termos ou frases inteiros, mas muitas vezes acompanhados por operadores booleanos. Termos autónomos únicos são consultas *de termo.* Consultas de várias partes incluídas em cotação são consultas *de frases.* A pesquisa pode ser indefinida, como em **`search=*`** , mas sem critérios para combinar, o conjunto de resultados é composto por documentos arbitrariamente selecionados.

+ **`searchFields`** limita a execução de consultas a campos específicos. Qualquer campo que seja atribuído como *pespável* no esquema de índice é um candidato a este parâmetro.

As respostas também são moldadas pelos parâmetros que inclui na consulta:

+ **`select`** especifica quais os campos a devolver na resposta. Apenas os campos marcados como *recuperáveis* no índice podem ser utilizados numa declaração selecionada.

+ **`top`** devolve o número especificado de documentos mais correspondentes. Neste exemplo, apenas 10 acessos são devolvidos. Pode utilizar a parte superior e saltar (não mostrada) para página dos resultados.

+ **`count`** indica-lhe quantos documentos em toda a correspondência de índice em geral, que pode ser mais do que o que são devolvidos. 

+ **`orderby`** é usado se quiser classificar resultados por um valor, como uma classificação ou localização. Caso contrário, o padrão é utilizar a pontuação de relevância para classificar os resultados.

Na Pesquisa Cognitiva Azure, a execução de consultas é sempre contra um índice, autenticado usando uma chave api fornecida no pedido. Em REST, ambos são fornecidos em cabeçalhos de pedido.

### <a name="how-to-run-this-query"></a>Como executar esta consulta

Antes de escrever qualquer código, pode utilizar ferramentas de consulta para aprender a sintaxe e experimentar com diferentes parâmetros. A abordagem mais rápida é a ferramenta portal incorporada, [Search Explorer](search-explorer.md).

Se você seguiu este [quickstart para criar o índice de demonstração de hotéis,](search-get-started-portal.md)você pode colar esta cadeia de consulta na barra de pesquisa do explorador para executar a sua primeira consulta: `search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

## <a name="how-query-operations-are-enabled-by-the-index"></a>Como as operações de consulta são habilitadas pelo índice

O design de índice e o design de consulta estão fortemente acopdos na Pesquisa Cognitiva Azure. Um facto essencial a saber à frente é que o esquema de *índice,* com atributos em cada campo, determina o tipo de consulta que se pode construir. 

Os atributos de índice num campo definem as operações permitidas - se um campo é *pesmável* no índice, *recuperável* em resultados, *ordenado,* *filtrado* , e assim por diante. Na cadeia de consulta de exemplo, `"$orderby": "Rating"` só funciona porque o campo de Classificação é marcado como classificado como *classificado* no esquema de índice. 

![Definição de índice para a amostra do hotel](./media/search-query-overview/hotel-sample-index-definition.png "Definição de índice para a amostra do hotel")

A imagem acima é uma lista parcial de atributos de índice para a amostra de hotéis. Pode ver todo o esquema de índice no portal. Para obter mais informações sobre atributos de índice, consulte [Criar API de Repouso Índice](/rest/api/searchservice/create-index).

> [!Note]
> Algumas funcionalidades de consulta são ativadas em nível de índice e não numa base por campo. Estas capacidades incluem: [mapas de sinónimos,](search-synonyms.md) [analisadores](index-add-custom-analyzers.md) [personalizados, construções sugestivas (para consultas autocompletas e sugeridas)](index-add-suggesters.md), [lógica de pontuação para resultados de classificação](index-add-scoring-profiles.md).

## <a name="elements-of-a-query-request"></a>Elementos de um pedido de consulta

As consultas são sempre direcionadas para um único índice. Não é possível aderir a índices ou criar estruturas de dados personalizadas ou temporárias como alvo de consulta. 

Os elementos necessários num pedido de consulta incluem os seguintes componentes:

+ Recolha de documentos de ponto final e índice de serviço, expressa em URL contendo componentes fixos e definidos pelo utilizador: **`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`** (APENAS REST) é necessário porque mais de uma versão da API está sempre disponível. 
+ **`api-key`** , seja uma consulta ou uma chave api-administrador, autentica o pedido ao seu serviço.
+ **`queryType`** , simples ou cheio, que pode ser omitido se estiver a utilizar a sintaxe simples incorporada.
+ **`search`** ou **`filter`** fornece os critérios de correspondência, que podem não ser especificados se quiser realizar uma pesquisa vazia. Ambos os tipos de consulta são discutidos em termos do simples parser, mas mesmo consultas avançadas requerem o parâmetro de pesquisa para passar expressões de consulta complexas.

Todos os outros parâmetros de pesquisa são opcionais. Para obter a lista completa de atributos, consulte [Criar Índice (REST)](/rest/api/searchservice/create-index). Para ver mais de perto como os parâmetros são usados durante o processamento, consulte [como funciona a pesquisa de texto completo na Pesquisa Cognitiva de Azure](search-lucene-query-architecture.md).

## <a name="choose-apis-and-tools"></a>Escolha APIs e ferramentas

A tabela que se segue lista as APIs e as abordagens baseadas em ferramentas para a apresentação de consultas.

| Metodologia | Descrição |
|-------------|-------------|
| [Explorador de procura (portal)](search-explorer.md) | Fornece uma barra de pesquisa e opções para seleções de versão index e api. Os resultados são devolvidos como documentos JSON. Recomendado para exploração, teste e validação. <br/>[Saiba mais.](search-get-started-portal.md#query-index) | 
| [Carteiro ou outras ferramentas REST](search-get-started-postman.md) | As ferramentas de teste web são uma excelente escolha para formular chamadas REST. A API REST suporta todas as operações possíveis na Pesquisa Cognitiva Azure. Neste artigo, aprenda a configurar um cabeçalho e corpo de pedidos HTTP para o envio de pedidos para a Azure Cognitive Search.  |
| [SearchClient (.NET)](/dotnet/api/azure.search.documents.searchclient) | Cliente que pode ser usado para consultar um índice de Pesquisa Cognitiva Azure.  <br/>[Saiba mais.](search-howto-dotnet-sdk.md)  |
| [Documentos de pesquisa (REST API)](/rest/api/searchservice/search-documents) | Métodos GET ou POST num índice, utilizando parâmetros de consulta para entrada adicional.  |

## <a name="choose-a-parser-simple--full"></a>Escolha um parser: simples / cheio

A Azure Cognitive Search fica em cima de Apache Lucene e dá-lhe uma escolha entre dois parsers de consulta para lidar com consultas típicas e especializadas. Os pedidos de utilização do parser simples são formulados utilizando a [sintaxe de consulta simples,](query-simple-syntax.md)selecionada como padrão para a sua velocidade e eficácia em consultas de texto de forma livre. Esta sintaxe suporta uma série de operadores de pesquisa comuns, incluindo os operadores E, OU, NÃO, frase, sufixo e precedência.

A [sintaxe de consulta lucene completa,](query-Lucene-syntax.md#bkmk_syntax)ativada quando adiciona `queryType=full` ao pedido, expõe a linguagem de consulta amplamente adotada e expressiva desenvolvida como parte do [Apache Lucene.](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) A sintaxe completa estende a sintaxe simples. Qualquer consulta que escreva para a simples sintaxe passa por baixo do parser lucene completo. 

Os exemplos a seguir ilustram o ponto: a mesma consulta, mas com diferentes configurações de consultas, produzem resultados diferentes. Na primeira consulta, o `^3` seguinte é tratado como parte do termo de `historic` pesquisa. O resultado mais bem classificado para esta consulta é "Marquês Plaza & Suites", que tem *oceano* na sua descrição

```
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

A mesma consulta utilizando o parser lucene completo interpreta `^3` como um impulsionador de prazos em campo. A troca de parsers muda de posição, com os resultados que contêm o termo *histórico* movendo-se para o topo.

```
queryType=full&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Tipos de consultas

A Azure Cognitive Search suporta uma ampla gama de tipos de consultas. 

| Tipo de consulta | Utilização | Exemplos e mais informações |
|------------|--------|-------------------------------|
| Pesquisa de texto gratuita | Parâmetro de pesquisa e qualquer parser| A pesquisa completa de texto procura um ou mais termos em todos os campos *pesquisáveis* no seu índice, e funciona da forma que esperaria que um motor de busca como o Google ou o Bing funcionassem. O exemplo da introdução é a pesquisa completa por texto.<br/><br/>A pesquisa completa de texto passa por uma análise lexical utilizando o analisador padrão Lucene (por padrão) para reduzir todos os termos, remover palavras de paragem como "o". Pode anular o padrão com [analisadores não ingleses](index-add-language-analyzers.md#language-analyzer-list) ou [analisadores agnósticos de linguagem especializada que](index-add-custom-analyzers.md#AnalyzerTable) modificam a análise lexical. Um exemplo é [a palavra-chave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) que trata todo o conteúdo de um campo como um único símbolo. Isto é útil para dados como códigos postais, IDs e alguns nomes de produtos. | 
| Pesquisa filtrada | [Expressão de filtro OData](query-odata-filter-orderby-syntax.md) e qualquer parser | As consultas de filtro avaliam uma expressão booleana em todos os campos *filtrados* num índice. Ao contrário da pesquisa, uma consulta de filtro corresponde ao conteúdo exato de um campo, incluindo sensibilidade a casos nos campos de cordas. Outra diferença é que as consultas de filtro são expressas na sintaxe OData. <br/>[Exemplo de expressão de filtro](search-query-simple-examples.md#example-3-filter-queries) |
| Pesquisa geográfica | [Edm.GeographyPoint tipo](/rest/api/searchservice/supported-data-types) no campo, expressão de filtro e qualquer parser | As coordenadas armazenadas num campo com um Edm.GeographyPoint são usadas para "encontrar perto de mim" ou controlos de pesquisa baseados em mapas. <br/>[Exemplo de geo-pesquisa](search-query-simple-examples.md#example-5-geo-search)|
| Pesquisa de alcance | expressão de filtro e parser simples | Na Pesquisa Cognitiva Azure, as consultas de alcance são construídas usando o parâmetro do filtro. <br/>[Exemplo de filtro de gama](search-query-simple-examples.md#example-4-range-filters) | 
| [Pesquisa de campo](query-lucene-syntax.md#bkmk_fields) | Parâmetro de pesquisa e parser completo | Construa uma expressão de consulta composta dirigida a um único campo. <br/>[Exemplo de pesquisa em campo](search-query-lucene-examples.md#example-2-fielded-search) |
| [pesquisa fuzzy](query-lucene-syntax.md#bkmk_fuzzy) | Parâmetro de pesquisa e parser completo | Corresponde em termos de construção ou ortografia semelhantes. <br/>[Exemplo de pesquisa difusa](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [pesquisa de proximidade](query-lucene-syntax.md#bkmk_proximity) | Parâmetro de pesquisa e parser completo | Encontra termos que estão perto um do outro num documento. <br/>[Exemplo de pesquisa de proximidade](search-query-lucene-examples.md#example-4-proximity-search) |
| [aumento de prazos](query-lucene-syntax.md#bkmk_termboost) | Parâmetro de pesquisa e parser completo | Classifica um documento mais alto se contiver o termo aumentado, em relação a outros que não. <br/>[Exemplo de reforço de prazos](search-query-lucene-examples.md#example-5-term-boosting) |
| [pesquisa regular de expressão](query-lucene-syntax.md#bkmk_regex) | Parâmetro de pesquisa e parser completo | Fósforos baseados no conteúdo de uma expressão regular. <br/>[Exemplo de expressão regular](search-query-lucene-examples.md#example-6-regex) |
|  [pesquisa de wildcard ou prefixo](query-lucene-syntax.md#bkmk_wildcard) | Parâmetro de pesquisa e parser completo | Corresponde com base num prefixo e num único `~` carácter (). `?` <br/>[Exemplo de pesquisa wildcard](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="manage-search-results"></a>Gerir os resultados da pesquisa 

Os resultados da consulta são transmitidos como documentos JSON na API REST, embora se utilizar .NET APIs, a serialização é incorporada. Pode moldar os resultados definindo parâmetros na consulta, selecionando campos específicos para a resposta.

Os parâmetros da consulta podem ser utilizados para estruturar o resultado definido de forma a seguir:

+ Limitação ou loteamento do número de documentos nos resultados (50 por defeito)
+ Selecionando campos para incluir nos resultados
+ Definição de uma ordem de classificação
+ Adicionar destaques de sucesso para chamar a atenção para termos correspondentes no corpo dos resultados da pesquisa

### <a name="tips-for-unexpected-results"></a>Dicas para resultados inesperados

Ocasionalmente, a substância e não a estrutura dos resultados são inesperadas. Quando os resultados da consulta não são o que espera ver, pode experimentar estas modificações de consulta para ver se os resultados melhoram:

+ Alterar **`searchMode=any`** (predefinição) **`searchMode=all`** para exigir correspondências em todos os critérios em vez de qualquer um dos critérios. Isto é especialmente verdade quando os operadores booleans são incluídos na consulta.

+ Altere a técnica de consulta se o texto ou a análise lexical forem necessários, mas o tipo de consulta impede o processamento linguístico. Na pesquisa completa de texto, texto ou análise lexical, os erros ortográficos, formas de palavras singulares-plural e até verbos ou substantivos irregulares. Para algumas consultas, tais como pesquisa de fofinhos ou wildcard, a análise lexical não faz parte do pipeline de análise de consulta. Para alguns cenários, expressões regulares têm sido usadas como uma solução alternativa. 

### <a name="paging-results"></a>Resultados de paginação
A Azure Cognitive Search facilita a implementação de paging de resultados de pesquisa. Ao utilizar os **`top`** parâmetros e **`skip`** parâmetros, pode emitir suavemente pedidos de pesquisa que lhe permitam receber o conjunto total de resultados de pesquisa em subconjuntos manejáveis e ordenados que facilmente permitem boas práticas de UI de pesquisa. Ao receber esses subconjuntos de resultados mais pequenos, pode também receber a contagem de documentos no conjunto total de resultados da pesquisa.

Pode saber mais sobre os resultados da pesquisa de paging no artigo [Como página os resultados da pesquisa na Azure Cognitive Search](search-pagination-page-layout.md).

### <a name="ordering-results"></a>Ordenar resultados
Ao receber resultados de uma consulta de pesquisa, pode solicitar que a Azure Cognitive Search sirva os resultados ordenados por valores num campo específico. Por predefinição, a Azure Cognitive Search ordena os resultados de pesquisa com base no grau de pontuação de pesquisa de cada documento, que é derivado da [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Se quiser que a Azure Cognitive Search retorna os seus resultados encomendados por um valor diferente da pontuação de pesquisa, pode utilizar o **`orderby`** parâmetro de pesquisa. Pode especificar o valor do parâmetro para incluir nomes de **`orderby`** campo e chamadas para a [**`geo.distance()` função**](query-odata-filter-orderby-syntax.md) para valores geoespacial. Cada expressão pode ser seguida por `asc` indicar que os resultados são solicitados por ordem ascendente, e **`desc`** para indicar que os resultados são solicitados por ordem descendente. A ordem ascendente de classificação predefinida.


### <a name="hit-highlighting"></a>Detetor de ocorrências
Na Pesquisa Cognitiva Azure, enfatizar a parte exata dos resultados de pesquisa que correspondem à consulta de pesquisa é facilitada através da utilização de **`highlight`** **`highlightPreTag`** , e **`highlightPostTag`** parâmetros. Pode especificar quais os campos *pes pesjáveis* que devem ter o seu texto combinado realçado, bem como especificar as etiquetas de corda exatas para anexar ao início e fim do texto combinado que a Azure Cognitive Search retorna.

## <a name="see-also"></a>Ver também

+ [Como funciona a pesquisa completa de texto na Azure Cognitive Search (arquitetura de análise de consulta)](search-lucene-query-architecture.md)
+ [Explorador de pesquisa](search-explorer.md)
+ [Como consultar em .NET](./search-get-started-dotnet.md)
+ [Como consultar em REST](./search-get-started-powershell.md)