---
title: Tipos de consulta e composição
titleSuffix: Azure Cognitive Search
description: Fundamentos para a construção de uma consulta de pesquisa em Pesquisa Cognitiva Azure, utilizando parâmetros para filtrar, selecionar e classificar resultados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 902f3628235cc8a4524ddc4dd8a5327592fe47e7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79282825"
---
# <a name="query-types-and-composition-in-azure-cognitive-search"></a>Tipos de consulta e composição em Pesquisa Cognitiva Azure

Na Pesquisa Cognitiva Azure, uma consulta é uma especificação completa de uma operação de ida e volta. Os parâmetros do pedido fornecem critérios de correspondência para encontrar documentos num índice, quais os campos a incluir ou excluir, instruções de execução passadas ao motor e diretivas para moldar a resposta. Não especificada (`search=*`), uma consulta corre contra todos os campos pesquisáveis como uma operação de pesquisa de texto completo, devolvendo um resultado não marcado definido em ordem arbitrária.

O exemplo seguinte é uma consulta representativa construída na [API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents). Este exemplo visa o índice de demonstração de [hotéis](search-get-started-portal.md) e inclui parâmetros comuns.

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

+ **`queryType`** define o parser, que é ou o [simples parser](search-query-simple-examples.md) de consulta padrão (ideal para pesquisa completa de texto), ou o parser de [consulta lucene completo](search-query-lucene-examples.md) usado para construções de consulta avançada saqueadas como expressões regulares, pesquisa de proximidade, pesquisa de difusão e wildcard, para citar alguns.

+ **`search`** fornece os critérios de correspondência, geralmente texto, mas muitas vezes acompanhado por operadores booleanos. Termos autónomos únicos são consultas *a prazo.* Consultas multipartes fechadas em citações são consultas de *frase chave.* A procura pode ser indefinida, como em **`search=*`** , mas mais provável é composta por termos, frases e operadores semelhantes ao que aparece no exemplo.

+ **`searchFields`** limita a execução de consultas a campos específicos. Qualquer campo que seja atribuído como *pesquisável* no esquema de índice é um candidato a este parâmetro.

As respostas também são moldadas pelos parâmetros que inclui na consulta. No exemplo, o conjunto de resultados consiste em campos enumerados na declaração **`select`.** Apenas os campos marcados como *recuperáveis* podem ser usados numa declaração $select. Além disso, apenas os **`top`** 10 acessos são devolvidos nesta consulta, enquanto **`count`** lhe diz quantos documentos correspondem globalmente, o que pode ser mais do que o que são devolvidos. Nesta consulta, as filas são ordenadas pela Classificação em ordem descendente.

Na Pesquisa Cognitiva Azure, a execução de consulta é sempre contra um índice, autenticado usando uma chave api fornecida no pedido. Em REST, ambos são fornecidos em cabeçalhos de pedido.

### <a name="how-to-run-this-query"></a>Como executar esta consulta

Para executar esta consulta, use o explorador de [pesquisa e o índice de demonstração de hotéis.](search-get-started-portal.md) 

Pode colar esta cadeia de consulta na barra de pesquisa do explorador: `search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

## <a name="how-query-operations-are-enabled-by-the-index"></a>Como as operações de consulta são ativadas pelo índice

Design de índice e design de consulta estão bem acoplado si na Pesquisa Cognitiva Azure. Um facto essencial para saber na frente é que o *esquema de índice,* com atributos em cada campo, determina o tipo de consulta que você pode construir. 

Os atributos de índice num campo definiram as operações permitidas - se um campo é *pesquisável* no índice, *recuperável* em resultados, *classificativo,* *filtrante*, e assim por diante. Na cadeia de consulta de exemplo, `"$orderby": "Rating"` só funciona porque o campo rating é marcado como classificado como *classificado* no esquema de índice. 

![Definição de índice para a amostra do hotel](./media/search-query-overview/hotel-sample-index-definition.png "Definição de índice para a amostra do hotel")

A imagem acima é uma lista parcial de atributos de índice para a amostra de hotéis. Pode ver todo o esquema do índice no portal. Para obter mais informações sobre os atributos do índice, consulte [Create Index REST API](https://docs.microsoft.com/rest/api/searchservice/create-index).

> [!Note]
> Algumas funcionalidades de consulta são ativadas em todo o índice e não numa base por campo. Estas capacidades incluem: [mapas de sinónimo,](search-synonyms.md) [analisadores](index-add-custom-analyzers.md) [personalizados, construções sugestionantes (para consultas autocompletas e sugeridas)](index-add-suggesters.md), lógica de [pontuação para resultados de ranking](index-add-scoring-profiles.md).

## <a name="elements-of-a-query-request"></a>Elementos de um pedido de consulta

As consultas são sempre dirigidas a um único índice. Não pode aderir a índices ou criar estruturas de dados personalizadas ou temporárias como alvo de consulta. 

Os elementos necessários num pedido de consulta incluem os seguintes componentes:

+ Recolha de documentos de ponto final de serviço e indexados, expressa como URL contendo componentes fixos e definidos pelo utilizador: **`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`** (apenas rest) é necessário porque mais de uma versão da API está sempre disponível. 
+ **`api-key`,** seja uma consulta ou uma chave de api, autentica o pedido ao seu serviço.
+ **`queryType`,** simples ou cheio, que podem ser omitidos se estiver a utilizar a simples sintaxe incorporada.
+ **`search`** ou **`filter`** fornece os critérios de correspondência, que podem não ser especificados se quiser realizar uma pesquisa vazia. Ambos os tipos de consultas são discutidos em termos do simples parser, mas mesmo consultas avançadas requerem o parâmetro de pesquisa para passar expressões de consulta complexas.

Todos os outros parâmetros de pesquisa são opcionais. Para a lista completa de atributos, consulte [Criar Índice (REST)](https://docs.microsoft.com/rest/api/searchservice/create-index). Para ver mais de perto como os parâmetros são usados durante o processamento, consulte como funciona a [pesquisa de texto completo em Azure Cognitive Search](search-lucene-query-architecture.md).

## <a name="choose-apis-and-tools"></a>Escolha APIs e ferramentas

A tabela que se segue lista as APIs e as abordagens baseadas em ferramentas para a apresentação de consultas.

| Metodologia | Descrição |
|-------------|-------------|
| [Explorador de pesquisa (portal)](search-explorer.md) | Fornece uma barra de pesquisa e opções para seleções de indexação e versão api. Os resultados são devolvidos como documentos da JSON. Recomendado para exploração, teste e validação. <br/>[Saiba mais.](search-get-started-portal.md#query-index) | 
| [Carteiro ou outras ferramentas REST](search-get-started-postman.md) | As ferramentas de teste web são uma excelente escolha para formular chamadas REST. A API REST suporta todas as operações possíveis na Pesquisa Cognitiva Azure. Neste artigo, aprenda a configurar um cabeçalho de pedido http e corpo para o envio de pedidos para pesquisa cognitiva Azure.  |
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Cliente que pode ser usado para consultar um índice de Pesquisa Cognitiva Azure.  <br/>[Saiba mais.](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Documentos de pesquisa (REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | Métodos GET ou POST num índice, utilizando parâmetros de consulta para entrada adicional.  |

## <a name="choose-a-parser-simple--full"></a>Escolha um parser: simples ! cheio

A Azure Cognitive Search fica em cima de Apache Lucene e dá-lhe uma escolha entre dois parsers de consulta para lidar com consultas típicas e especializadas. Os pedidos que utilizam o simples parser são formulados utilizando a [simples sintaxe](query-simple-syntax.md)de consulta, selecionada como padrão para a sua velocidade e eficácia em consultas de texto de forma livre. Esta sintaxe apoia vários operadores comuns de pesquisa, incluindo os operadores de e, OU, não, frase, sufixo e precedência.

A [sintaxe de consulta lucene completa,](query-Lucene-syntax.md#bkmk_syntax)ativada quando se adiciona `queryType=full` ao pedido, expõe a linguagem de consulta amplamente adotada e expressiva desenvolvida como parte de [Apache Lucene.](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) A sintaxe completa estende a simples sintaxe. Qualquer consulta que escreva para a simples sintaxe passa por baixo do parser lucene completo. 

Os seguintes exemplos ilustram o ponto: a mesma consulta, mas com diferentes definições de consultaType, produzem resultados diferentes. Na primeira consulta, o `^3` após `historic` é tratado como parte do termo de pesquisa. O resultado mais bem classificado para esta consulta é "Marquis Plaza & Suites", que tem *o oceano* na sua descrição

```
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

A mesma consulta usando o parser Lucene completo interpreta `^3` como um impulsionador de termo no campo. Trocar de parsers muda a classificação, com resultados que contêm o termo *histórico* movendo-se para o topo.

```
queryType=full&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Tipos de consultas

A Pesquisa Cognitiva Azure suporta uma ampla gama de tipos de consultas. 

| Tipo de consulta | Utilização | Exemplos e mais informações |
|------------|--------|-------------------------------|
| Pesquisa de texto de formulário gratuito | Parâmetro de pesquisa e parser| A pesquisa completa de texto procura um ou mais termos em todos os campos *pesquisáveis* do seu índice, e funciona da forma que se espera que um motor de busca como o Google ou o Bing funcione. O exemplo na introdução é a pesquisa completa de texto.<br/><br/>A pesquisa completa de texto passa pela análise de texto utilizando o analisador lucene padrão (por padrão) para reduzir todos os termos, removendo palavras de paragem como "o". Pode anular o padrão com [analisadores não ingleses](index-add-language-analyzers.md#language-analyzer-list) ou [analisadores agnósticos](index-add-custom-analyzers.md#AnalyzerTable) especializados que modificam a análise de texto. Um exemplo é a [palavra-chave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) que trata todo o conteúdo de um campo como um único símbolo. Isto é útil para dados como códigos postais, iDs e alguns nomes de produtos. | 
| Pesquisa filtrada | Expressão de [filtro OData](query-odata-filter-orderby-syntax.md) e parser | As consultas de filtro avaliam uma expressão booleana sobre todos os campos *filtrados* num índice. Ao contrário da pesquisa, uma consulta de filtro corresponde ao conteúdo exato de um campo, incluindo sensibilidade de caso em campos de cordas. Outra diferença é que as consultas de filtro são expressas na sintaxe OData. <br/>[Exemplo de expressão de filtro](search-query-simple-examples.md#example-3-filter-queries) |
| Pesquisa geográfica | [Tipo Edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) no campo, expressão de filtro, e ou parser | As coordenadas armazenadas num campo com um Edm.GeographyPoint são usadas para "encontrar perto de mim" ou controlos de pesquisa baseados em mapas. <br/>[Exemplo de geopesquisa](search-query-simple-examples.md#example-5-geo-search)|
| Pesquisa de alcance | expressão de filtro e simples parser | Na Pesquisa Cognitiva Azure, as consultas de alcance são construídas utilizando o parâmetro do filtro. <br/>[Exemplo de filtro de gama](search-query-simple-examples.md#example-4-range-filters) | 
| [Pesquisa no terreno](query-lucene-syntax.md#bkmk_fields) | Parâmetro de pesquisa e parser completo | Construa uma expressão de consulta composta visando um único campo. <br/>[Exemplo de pesquisa em campo](search-query-lucene-examples.md#example-2-fielded-search) |
| [pesquisa fuzzy](query-lucene-syntax.md#bkmk_fuzzy) | Parâmetro de pesquisa e parser completo | Corresponde em termos de construção ou ortografia semelhantes. <br/>[Exemplo de pesquisa fuzzy](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [pesquisa de proximidade](query-lucene-syntax.md#bkmk_proximity) | Parâmetro de pesquisa e parser completo | Encontra termos que estão próximos um do outro num documento. <br/>[Exemplo de pesquisa de proximidade](search-query-lucene-examples.md#example-4-proximity-search) |
| [aumento de termo](query-lucene-syntax.md#bkmk_termboost) | Parâmetro de pesquisa e parser completo | Classifica um documento mais alto se contiver o termo reforçado, em relação a outros que não o fizerem. <br/>[Exemplo de reforço de termo](search-query-lucene-examples.md#example-5-term-boosting) |
| [pesquisa regular de expressão](query-lucene-syntax.md#bkmk_regex) | Parâmetro de pesquisa e parser completo | Fósforos baseados no conteúdo de uma expressão regular. <br/>[Exemplo de expressão regular](search-query-lucene-examples.md#example-6-regex) |
|  [pesquisa wildcard ou prefixo](query-lucene-syntax.md#bkmk_wildcard) | Parâmetro de pesquisa e parser completo | Fósforos baseados num prefixo e tilde (`~`) ou num único carácter (`?`). <br/>[Exemplo de pesquisa de Wildcard](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="manage-search-results"></a>Gerir resultados de pesquisa 

Os resultados da consulta são transmitidos como documentos JSON na API REST, embora se utilizar .NET APIs, a serialização é incorporada. Pode moldar os resultados definindo parâmetros na consulta, selecionando campos específicos para a resposta.

Os parâmetros da consulta podem ser utilizados para estruturar o resultado definido das seguintes formas:

+ Limitação ou loteamento do número de documentos nos resultados (50 por defeito)
+ Selecionar campos para incluir nos resultados
+ Definição de uma ordem de classificação
+ Adicionar destaques de sucesso para chamar a atenção para termos correspondentes no corpo dos resultados da pesquisa

### <a name="tips-for-unexpected-results"></a>Dicas para resultados inesperados

Ocasionalmente, a substância e não a estrutura dos resultados são inesperadas. Quando os resultados da consulta não são o que espera ver, pode experimentar estas modificações de consulta para ver se os resultados melhoram:

+ Alterar **`searchMode=any`** (padrão) para **`searchMode=all`** exigir correspondências em todos os critérios em vez de qualquer um dos critérios. Isto é especialmente verdade quando os operadores booleanos são incluídos na consulta.

+ Altere a técnica de consulta se o texto ou a análise lexical forem necessárias, mas o tipo de consulta impede o processamento linguístico. Em plena pesquisa de texto, texto ou análise lexical corrige automaticamente erros ortográficos, formas de palavras singulares e até verbos irregulares ou substantivos. Para algumas consultas, como pesquisa fuzzy ou wildcard, a análise de texto não faz parte do pipeline de consulta. Para alguns cenários, expressões regulares têm sido usadas como uma suposições. 

### <a name="paging-results"></a>Resultados de paginação
A Pesquisa Cognitiva Azure facilita a implementação dos resultados da pesquisa. Ao utilizar os parâmetros **`top`** e **`skip`,** pode emitir sem problemas pedidos de pesquisa que lhe permitem receber o conjunto total de resultados de pesquisa em subconjuntos manejáveis e ordenados que facilmente permitem boas práticas de Pesquisa de UI. Ao receber esses subconjuntos de resultados mais pequenos, pode também receber a contagem de documentos no conjunto total de resultados da pesquisa.

Pode saber mais sobre os resultados da pesquisa de paging no artigo Como página resultados de [pesquisa em Pesquisa Cognitiva Azure](search-pagination-page-layout.md).

### <a name="ordering-results"></a>Ordenar resultados
Ao receber resultados para uma consulta de pesquisa, pode solicitar que a Pesquisa Cognitiva Azure sirva os resultados encomendados por valores num campo específico. Por padrão, a Pesquisa Cognitiva Azure ordena os resultados de pesquisa com base na classificação da pontuação de pesquisa de cada documento, que é derivada da [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Se quiser que a Pesquisa Cognitiva Azure devolva os seus resultados encomendados por um valor diferente da pontuação de pesquisa, pode utilizar o parâmetro de pesquisa **`orderby`.** Pode especificar o valor do parâmetro **`orderby`** para incluir nomes de campo e chamadas para a [**função`geo.distance()`** ](query-odata-filter-orderby-syntax.md) para valores geoespaciais. Cada expressão pode ser seguida por `asc` para indicar que os resultados são solicitados por ordem ascendente, e **`desc`** para indicar que os resultados são solicitados por ordem descendente. A ordem ascendente de classificação predefinida.


### <a name="hit-highlighting"></a>Detetor de ocorrências
Na Pesquisa Cognitiva Azure, enfatizar a parte exata dos resultados da pesquisa que correspondem à consulta de pesquisa é facilitada usando os **parâmetros`highlight`** , **`highlightPreTag`** e **`highlightPostTag`.** Pode especificar quais os campos *pesquisáveis* que devem ter o seu texto compatível enfatizado, bem como especificar as etiquetas de corda exatas para anexar ao início e ao fim do texto combinado que a Pesquisa Cognitiva Azure retorna.

## <a name="see-also"></a>Consulte também

+ [Como funciona a pesquisa completa de texto em Azure Cognitive Search (consulta de arquitetura de análise)](search-lucene-query-architecture.md)
+ [Explorador de pesquisa](search-explorer.md)
+ [Como consultar em .NET](search-query-dotnet.md)
+ [Como consultar em REST](search-create-index-rest-api.md)
