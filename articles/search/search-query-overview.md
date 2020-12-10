---
title: Tipos de consultas
titleSuffix: Azure Cognitive Search
description: Conheça os tipos de consultas suportadas na Pesquisa Cognitiva, incluindo texto gratuito, filtro, preconto e sugestões, geo-pesquisa, consultas de sistema e pesquisa de documentos.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: d1ea2d0ba8ed5850e5d4cd9c06a0b016c4059ca7
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007862"
---
# <a name="query-types-in-azure-cognitive-search"></a>Tipos de consulta na Pesquisa Cognitiva Azure

Em Azure Cognitive Search, uma consulta é uma especificação completa de uma operação de ida e volta, com parâmetros que regem a execução de consultas, e parâmetros que moldam a resposta voltando.

## <a name="elements-of-a-request"></a>Elementos de um pedido

O exemplo a seguir é uma consulta representativa construída utilizando [documentos de pesquisa REST API.](/rest/api/searchservice/search-documents) Este exemplo visa o [índice de demonstração de hotéis](search-get-started-portal.md) e inclui parâmetros comuns para que você possa ter uma ideia de como é uma consulta.

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=[api-version]
{
    "queryType": "simple" 
    "search": "`New York` +restaurant",
    "searchFields": "Description, Address/City, Tags",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

As consultas são sempre direcionadas para a recolha de documentos de um único índice. Não é possível aderir a índices ou criar estruturas de dados personalizadas ou temporárias como alvo de consulta.

+ **`queryType`** define o parser, que é o [parser de consulta simples padrão](search-query-simple-examples.md) (ideal para pesquisa completa de texto), ou o [parser de consulta lucene completo](search-query-lucene-examples.md) usado para construções de consultas avançadas como expressões regulares, pesquisa de proximidade, pesquisa de nádpos e wildcard, para citar alguns.

+ **`search`** fornece os critérios de correspondência, geralmente termos ou frases inteiros, mas muitas vezes acompanhados por operadores booleanos. Termos autónomos únicos são consultas *de termo.* Consultas de várias partes incluídas em cotação são consultas *de frases.* A pesquisa pode ser indefinida, como em **`search=*`** , mas sem critérios para combinar, o conjunto de resultados é composto por documentos arbitrariamente selecionados.

+ **`searchFields`** limita a execução de consultas a campos específicos. Qualquer campo que seja atribuído como *pespável* no esquema de índice é um candidato a este parâmetro.

As respostas também são moldadas pelos parâmetros que inclui na consulta:

+ **`select`** especifica quais os campos a devolver na resposta. Apenas os campos marcados como *recuperáveis* no índice podem ser utilizados numa declaração selecionada.

+ **`top`** devolve o número especificado de documentos mais correspondentes. Neste exemplo, apenas 10 acessos são devolvidos. Pode utilizar a parte superior e saltar (não mostrada) para página dos resultados.

+ **`count`** indica-lhe quantos documentos em toda a correspondência de índice em geral, que pode ser mais do que o que são devolvidos. 

+ **`orderby`** é usado se quiser classificar resultados por um valor, como uma classificação ou localização. Caso contrário, o padrão é utilizar a pontuação de relevância para classificar os resultados.

> [!Tip]
> Antes de escrever qualquer código, pode utilizar ferramentas de consulta para aprender a sintaxe e experimentar com diferentes parâmetros. A abordagem mais rápida é a ferramenta portal incorporada, [Search Explorer](search-explorer.md).
>
> Se você seguiu este [quickstart para criar o índice de demonstração de hotéis,](search-get-started-portal.md)você pode colar esta cadeia de consulta na barra de pesquisa do explorador para executar a sua primeira consulta: `search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

### <a name="how-field-attributes-in-an-index-determine-query-behaviors"></a>Como os atributos de campo num índice determinam comportamentos de consulta

O design de índice e o design de consulta estão fortemente acopdos na Pesquisa Cognitiva Azure. Um facto essencial a saber à frente é que o esquema de *índice,* com atributos em cada campo, determina o tipo de consulta que se pode construir. 

Os atributos de índice num campo definem as operações permitidas - se um campo é *pesmável* no índice, *recuperável* em resultados, *ordenado,* *filtrado*, e assim por diante. Na cadeia de consulta de exemplo, `"$orderby": "Rating"` só funciona porque o campo de Classificação é marcado como classificado como *classificado* no esquema de índice. 

![Definição de índice para a amostra do hotel](./media/search-query-overview/hotel-sample-index-definition.png "Definição de índice para a amostra do hotel")

A imagem acima é uma lista parcial de atributos de índice para a amostra de hotéis. Pode ver todo o esquema de índice no portal. Para obter mais informações sobre atributos de índice, consulte [Criar API de Repouso Índice](/rest/api/searchservice/create-index).

> [!Note]
> Algumas funcionalidades de consulta são ativadas em nível de índice e não numa base por campo. Estas capacidades incluem: [mapas de sinónimos,](search-synonyms.md) [analisadores](index-add-custom-analyzers.md) [personalizados, construções sugestivas (para consultas autocompletas e sugeridas)](index-add-suggesters.md), [lógica de pontuação para resultados de classificação](index-add-scoring-profiles.md).

## <a name="choose-a-parser-simple--full"></a>Escolha um parser: simples / cheio

A Azure Cognitive Search dá-lhe uma escolha entre dois parsers de consulta para lidar com consultas típicas e especializadas. Os pedidos de utilização do parser simples são formulados utilizando a [sintaxe de consulta simples,](query-simple-syntax.md)selecionada como padrão para a sua velocidade e eficácia em consultas de texto de forma livre. Esta sintaxe suporta uma série de operadores de pesquisa comuns, incluindo os operadores E, OU, NÃO, frase, sufixo e precedência.

A [sintaxe de consulta lucene completa,](query-Lucene-syntax.md#bkmk_syntax)ativada quando adiciona `queryType=full` ao pedido, expõe a linguagem de consulta amplamente adotada e expressiva desenvolvida como parte do [Apache Lucene.](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) A sintaxe completa estende a sintaxe simples. Qualquer consulta que escreva para a simples sintaxe passa por baixo do parser lucene completo. 

Os exemplos a seguir ilustram o ponto: a mesma consulta, mas com diferentes configurações de consultas, produzem resultados diferentes. Na primeira consulta, o `^3` seguinte é tratado como parte do termo de `historic` pesquisa. O resultado mais bem classificado para esta consulta é o "Marquês Plaza & Suites", que tem *oceano* na sua descrição.

```http
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

A mesma consulta utilizando o parser lucene completo interpreta `^3` como um impulsionador de prazos em campo. A troca de parsers muda de posição, com os resultados que contêm o termo *histórico* movendo-se para o topo.

```http
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
| [Resultados autocompletos ou sugeridos](search-autocomplete-tutorial.md) | Parâmetro de autocompleto ou sugestão | Um formulário de consulta alternativo que executa com base em cordas parciais numa experiência de pesquisa como você. Pode utilizar as sugestões e as sugestões automáticas em conjunto ou separadamente. |
| [pesquisa fuzzy](query-lucene-syntax.md#bkmk_fuzzy) | Parâmetro de pesquisa e parser completo | Corresponde em termos de construção ou ortografia semelhantes. <br/>[Exemplo de pesquisa difusa](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [pesquisa de proximidade](query-lucene-syntax.md#bkmk_proximity) | Parâmetro de pesquisa e parser completo | Encontra termos que estão perto um do outro num documento. <br/>[Exemplo de pesquisa de proximidade](search-query-lucene-examples.md#example-4-proximity-search) |
| [aumento de prazos](query-lucene-syntax.md#bkmk_termboost) | Parâmetro de pesquisa e parser completo | Classifica um documento mais alto se contiver o termo aumentado, em relação a outros que não. <br/>[Exemplo de reforço de prazos](search-query-lucene-examples.md#example-5-term-boosting) |
| [pesquisa regular de expressão](query-lucene-syntax.md#bkmk_regex) | Parâmetro de pesquisa e parser completo | Fósforos baseados no conteúdo de uma expressão regular. <br/>[Exemplo de expressão regular](search-query-lucene-examples.md#example-6-regex) |
|  [pesquisa de wildcard ou prefixo](query-lucene-syntax.md#bkmk_wildcard) | Parâmetro de pesquisa e parser completo | Corresponde com base num prefixo e num único `~` carácter (). `?` <br/>[Exemplo de pesquisa wildcard](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="next-steps"></a>Passos seguintes

Use o portal, ou outra ferramenta, como o Postman ou o Visual Studio Code, ou um dos SDKs para explorar as consultas em maior profundidade. Os seguintes links vão começar.

+ [Explorador de pesquisa](search-explorer.md)
+ [Como consultar em .NET](./search-get-started-dotnet.md)
+ [Como consultar em REST](./search-get-started-powershell.md)