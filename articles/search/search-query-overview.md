---
title: Tipos de consulta e a composição - Azure Search
description: Noções básicas para a criação de uma consulta de pesquisa no Azure Search, utilizando os parâmetros para filtrar, selecione e ordenar os resultados.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/14/2019
ms.custom: seodec2018
ms.openlocfilehash: a197be06d9c6f4b70b8ffc06712ef315547b4140
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58136517"
---
# <a name="how-to-compose-a-query-in-azure-search"></a>Como compor uma consulta no Azure Search

No Azure Search, uma consulta é uma especificação completa de uma operação de ida e volta. Parâmetros na solicitação fornecem critérios de correspondência para encontrar os documentos num índice, instruções de execução para o mecanismo e as diretivas para a resposta de formatação. 

Um pedido de consulta é uma construção avançada, especificando quais campos estão no âmbito, como pesquisar, os campos a devolver, seja para ordenação ou filtro e assim por diante. Não especificado, é executada uma consulta em relação a todos os campos pesquisáveis como uma operação de pesquisa de texto completo, retornando um resultado sem pontuação definido na ordem arbitrária.

## <a name="apis-and-tools-for-testing"></a>APIs e ferramentas de teste

A tabela seguinte lista as APIs e as abordagens baseadas em ferramenta para o envio de consultas.

| Metodologia | Descrição |
|-------------|-------------|
| [Explorador de pesquisa (portal)](search-explorer.md) | Fornece uma barra de pesquisa e as opções de seleções de índice e a versão de api. Os resultados são devolvidos como documentos JSON. <br/>[Saiba mais.](search-get-started-portal.md#query-index) | 
| [Postman ou Fiddler](search-fiddler.md) | Ferramentas de teste da Web são uma excelente opção para formular chamadas REST. A API REST oferece suporte a todas as operações possíveis no Azure Search. Neste artigo, saiba como configurar um cabeçalho de pedido HTTP e corpo para enviar pedidos para o Azure Search.  |
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Cliente que pode ser usado para consultar um índice da Azure Search.  <br/>[Saiba mais.](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Pesquisa de documentos (REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | GET ou métodos POST num índice, com parâmetros de consulta de entrada adicional.  |

## <a name="a-first-look-at-query-requests"></a>Uma análise inicial de pedidos de consulta

Os exemplos são úteis para introduzindo novos conceitos. Como uma consulta representativa construídos a [REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents), este destinos de exemplo a [índice de demonstração de imóveis](search-get-started-portal.md) e inclui os parâmetros comuns.

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

+ **`queryType`** Define o analisador, que, no Azure Search, pode ser o [predefinido o analisador de consultas simples](search-query-simple-examples.md) (ideal para pesquisa em texto completo), ou o [completa de analisador de consultas de Lucene](search-query-lucene-examples.md) utilizado para construções de consulta avançada, como as expressões regulares , difusa, pesquisa de proximidade e pesquisa com carateres universais, para citar alguns.

+ **`search`** Fornece a correspondência de critérios, normalmente, texto, mas, muitas vezes, acompanhados por operadores booleanos. Termos de autônomo único são *termo* consultas. Incluído de cotação de consultas com várias partes são *expressões-chave* consultas. Pesquisa pode ser definida, como mostrado na **`search=*`**, mas o mais provável que consiste em termos de expressões e operadores semelhantes para o que é apresentado no exemplo.

+ **`searchFields`** é opcional, usado para restringir a execução da consulta a campos específicos.

As respostas também estão em forma de pelos parâmetros que incluir na consulta. No exemplo, o conjunto de resultados é composta por campos relacionados no **`select`** instrução. Apenas as 10 principais ocorrências são devolvidas nesta consulta, mas **`count`** indica o número de documentos corresponde ao geral. Nesta consulta, as linhas são ordenadas pelo daysOnMarket.

No Azure Search, a execução da consulta é sempre em relação a um índice, autenticado com uma chave de api fornecida no pedido. No RESTANTE, ambos são fornecidas nos cabeçalhos de pedido.

### <a name="how-to-run-this-query"></a>Como executar esta consulta

Para executar essa consulta, utilize [pesquise explorer e o índice de demonstração de imóveis](search-get-started-portal.md). 

Pode colar esta cadeia de consulta na barra de pesquisa do explorer: `search=seattle townhouse +lake&searchFields=description, city&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket`

## <a name="how-query-operations-are-enabled-by-the-index"></a>Como as operações de consulta são habilitadas pelo índice

Estrutura de índice e consulta de design são rigidamente integrados no Azure Search. Um fato essencial saber com antecedência é que o *esquema de índice*, com atributos em cada campo, determina o tipo de consulta que pode criar. 

Atributos de índice num campo de definir as operações permitidas - se um campo é *pesquisável* no índice, *recuperável* nos resultados, *ordenável*,  *filtrável*, e assim por diante. Na cadeia de consulta de exemplo, `"$orderby": "daysOnMarket"` só funciona porque o campo de daysOnMarket está assinalado como *ordenáveis* no esquema do índice. 

![Índice de definição para o exemplo de imóveis](./media/search-query-overview/realestate-sample-index-definition.png "definição para o exemplo de imóveis de índice")

A captura de ecrã acima é uma lista parcial de atributos de índice para o exemplo de imóveis. Pode ver o esquema de índice completo no portal. Para obter mais informações sobre os atributos de índice, consulte [criar API REST do índice](https://docs.microsoft.com/rest/api/searchservice/create-index).

> [!Note]
> Algumas funcionalidades de consulta está ativada em todo o índice em vez de numa base por campo. Esses recursos incluem: [mapeia sinónimo](search-synonyms.md), [analisadores personalizados](index-add-custom-analyzers.md), [sugestor construções (para preenchimento automático e de sugestão automática)](index-add-suggesters.md), [lógica para de classificação resultados de classificação](index-add-scoring-profiles.md).

## <a name="elements-of-a-query-request"></a>Elementos de um pedido de consulta

Consultas sejam sempre direcionadas num único índice. Não é possível associar a índices ou criar estruturas de dados personalizados ou temporário como um destino de consulta. 

Elementos necessários numa solicitação de consulta incluem os seguintes componentes:

+ Ponto final e índice documentos coleção de serviços, expressada como um URL que contém componentes fixos e definido pelo utilizador: **`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`** (Apenas de REST) é necessário porque a mais de uma versão da API está disponível em todos os momentos. 
+ **`api-key`**, uma consulta ou o administrador de chave de api, autentica o pedido para o seu serviço.
+ **`queryType`**, simples ou completo, que pode ser omitido se estiver a utilizar a sintaxe simples predefinidas e incorporadas.
+ **`search`** ou **`filter`** fornece a correspondência de critérios, o que podem ser especificados se deseja realizar uma pesquisa em branco. Ambos os tipos de consulta são discutidos em termos do analisador simple, mas consultas avançadas ainda requerem o parâmetro de pesquisa para a passagem de expressões de consulta complexas.

Todos os outros parâmetros de pesquisa são opcionais. Para obter a lista completa de atributos, consulte [criar índice (REST)](https://docs.microsoft.com/rest/api/searchservice/create-index). Para uma análise detalhada das como parâmetros são utilizados durante o processamento, consulte [como funciona a pesquisa em texto completo no Azure Search](search-lucene-query-architecture.md).

## <a name="choose-a-parser-simple--full"></a>Escolha um analisador: simples | completa

O Azure Search encontra-se com base no Apache Lucene e dá-lhe escolher entre dois analisadores de consulta para processar consultas típicas e especializadas. Pedidos com o analisador simple são formulados usando o [sintaxe de consulta simples](query-simple-syntax.md), selecionado como predefinição para sua velocidade e eficiência em consultas de texto de forma livre. Essa sintaxe suporta um número de operadores de pesquisa comuns, incluindo o e, em alternativa, não, frase, sufixo e operadores de precedência.

O [completa de sintaxe de consulta Lucene](query-Lucene-syntax.md#bkmk_syntax), habilitado quando adiciona `queryType=full` para o pedido, expõe o idioma de consulta amplamente adotado e expressivo, desenvolvido como parte da [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Sintaxe completa estende a sintaxe simples. Qualquer consulta de escrita para a sintaxe simples é executado sob o analisador de Lucene completo. 

Os exemplos a seguir ilustram isso: mesma consulta, mas com configurações de queryType diferentes, produzir resultados diferentes. Na primeira consulta, o `^3` é tratado como parte do termo de pesquisa.

```
queryType=simple&search=mountain beach garden ranch^3&searchFields=description&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket
```

A mesma consulta usando o analisador de Lucene completo interpreta o aumento no campo em "ranch", que aumenta a classificação de pesquisa de resultados com esse termo específico.

```
queryType=full&search=mountain beach garden ranch^3&searchFields=description&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Tipos de consultas

O Azure Search oferece suporte a uma ampla variedade de tipos de consulta. 

| Tipo de consulta | Utilização | Exemplos e obter mais informações |
|------------|--------|-------------------------------|
| Pesquisa de texto de forma livre | Parâmetro de pesquisa e de qualquer analisador| Pesquisa em texto completo verifica a existência de um ou mais termos em todos os *pesquisável* campos no seu índice e funciona da forma que seria de esperar um motor de busca como o Google ou o Bing. O exemplo na introdução é a pesquisa em texto completo.<br/><br/>Pesquisa em texto completo remanescentes passam pela análise de texto usando o analisador de Lucene padrão (por predefinição) para minúsculas todos os termos, palavras de paragem de remover, como "a". Pode substituir a predefinição com [analisadores de inglês](index-add-language-analyzers.md#language-analyzer-list) ou [especializada analisadores de idioma agnóstico](index-add-custom-analyzers.md#AnalyzerTable) que modificar a análise de texto. Um exemplo é [palavra-chave](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) que processa todo o conteúdo de um campo como um único token. Isto é útil para dados, como códigos postais, IDs e alguns nomes de produtos. | 
| Pesquisa filtrada | [Expressão de filtro de OData](query-odata-filter-orderby-syntax.md) e de qualquer analisador | Consultas de filtro avalie uma expressão booleana todos os *filtráveis* campos num índice. Ao contrário de pesquisa, uma consulta de filtro corresponde aos conteúdos exatos de um campo, incluindo sensibilidade em campos de cadeia de caracteres. Outra diferença é que as consultas de filtro são expressas em sintaxe OData. <br/>[Exemplo de expressão de filtro](search-query-simple-examples.md#example-3-filter-queries) |
| Pesquisa geográfica | [Tipo de geographypoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) no campo expressão de filtro e de qualquer analisador | Coordenadas armazenadas num campo de ter um geographypoint são usados para "encontrar perto de mim" ou baseada em mapa procurar controlos. <br/>[Exemplo de pesquisa geográfica](search-query-simple-examples.md#example-5-geo-search)|
| Pesquisa de intervalo | expressão de filtro e analisador simple | No Azure Search, as consultas de intervalo são criadas utilizando o parâmetro de filtro. <br/>[Exemplo de filtro de intervalo](search-query-simple-examples.md#example-4-range-filters) | 
| [Filtragem em campo](query-lucene-syntax.md#bkmk_fields) | Parâmetro de pesquisa e analisador completo | Crie uma expressão de consulta compostos visando um único campo. <br/>[Exemplo de filtro em campo](search-query-lucene-examples.md#example-2-intra-field-filtering) |
| [pesquisa difusa](query-lucene-syntax.md#bkmk_fuzzy) | Parâmetro de pesquisa e analisador completo | Correspondências em termos de ter uma construção semelhante ou ortografia. <br/>[Exemplo de pesquisa difusa](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [pesquisa de proximidade](query-lucene-syntax.md#bkmk_proximity) | Parâmetro de pesquisa e analisador completo | Termos de encontra que estejam perto uns dos outros num documento. <br/>[Exemplo de pesquisa de proximidade](search-query-lucene-examples.md#example-4-proximity-search) |
| [prazo adaptativo](query-lucene-syntax.md#bkmk_termboost) | Parâmetro de pesquisa e analisador completo | Classifica um documento superior se contiver o termo elevado, relativas a outras pessoas que não. <br/>[Exemplo de adaptativo do termo](search-query-lucene-examples.md#example-5-term-boosting) |
| [pesquisa de expressão regular](query-lucene-syntax.md#bkmk_regex) | Parâmetro de pesquisa e analisador completo | Correspondências com base no conteúdo de uma expressão regular. <br/>[Exemplo de expressão regular](search-query-lucene-examples.md#example-6-regex) |
|  [pesquisa com carateres universais ou prefixo](query-lucene-syntax.md#bkmk_wildcard) | Parâmetro de pesquisa e analisador completo | Correspondências com base num prefixo e til (`~`) ou caráter individual (`?`). <br/>[Exemplo de pesquisa com carateres universais](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="manage-search-results"></a>Gerir os resultados da pesquisa 

Os resultados da consulta são transmitidos como documentos JSON da API REST, embora se usar APIs do .NET, serialização baseia-se. Pode moldar os resultados ao definir os parâmetros na consulta, selecionar campos específicos para a resposta.

Parâmetros de consulta podem ser utilizados para estruturar o resultado definido da seguinte maneira:

+ Limitar ou o número de documentos nos resultados (50 por predefinição) de criação de batches
+ Selecionar campos para incluir nos resultados
+ A definição de uma ordem de classificação
+ Prima Adicionar destaca para chamar a atenção para a correspondência de termos no corpo dos resultados da pesquisa

### <a name="tips-for-unexpected-results"></a>Dicas para resultados inesperados

Ocasionalmente, a substância e não a estrutura de resultados são inesperados. Quando os resultados de consulta não que espera ver, pode experimentar estas alterações de consulta para ver se a melhorar os resultados:

+ Alteração **`searchMode=any`** (predefinida) para **`searchMode=all`** para exigir correspondências em todos os critérios em vez de um dos critérios. Isso é especialmente verdadeiro quando operadores booleanos são incluídos a consulta.

+ Se o texto ou análise lexical, é necessário, mas o tipo de consulta o processamento linguístico impede, altere a técnica de consulta. Na pesquisa em texto completo, texto ou análise lexical autocorrects para erros ortográficos, formas de palavras de singular plural e até mesmo irregulares verbos ou substantivos. Para algumas consultas, tais como difusa ou pesquisa com carateres universais, a análise de texto não é parte da consulta pipeline de análise. Em alguns cenários, as expressões regulares foram utilizadas como uma solução alternativa. 

### <a name="paging-results"></a>Resultados de paginação
A Azure Search facilita a implementação da paginação dos resultados da pesquisa. Ao utilizar o **`top`** e **`skip`** parâmetros, sem problemas pode emitir pedidos de pesquisa que permitem receber o conjunto total de resultados da pesquisa no gerenciável, ordenados subconjuntos que Ative facilmente as práticas de interface do Usuário de pesquisa de bom. Ao receber esses subconjuntos de resultados mais pequenos, pode também receber a contagem de documentos no conjunto total de resultados da pesquisa.

Pode saber mais sobre a paginação dos resultados da pesquisa no artigo [Como paginar os resultados da pesquisa na Azure Search](search-pagination-page-layout.md).

### <a name="ordering-results"></a>Ordenar resultados
Ao receber resultados de uma consulta de pesquisa, pode pedir à Azure Search para apresentar os resultados ordenados pelos valores num campo específico. Por predefinição, a Azure Search ordena os resultados da pesquisa com base na classificação de pontuação de pesquisa de cada documento, a qual deriva de [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Se pretender que o Azure Search devolva os resultados ordenados por um valor diferente da pontuação de pesquisa, pode utilizar o **`orderby`** parâmetro de pesquisa. Pode especificar o valor do **`orderby`** parâmetro para incluir os nomes de campos e chamadas para o [  **`geo.distance()` função** ](query-odata-filter-orderby-syntax.md) para valores geoespaciais. Cada expressão pode ser seguido `asc` para indicar que os resultados foram pedidos por ordem ascendente e **`desc`** para indicar que os resultados foram pedidos por ordem descendente. A ordem ascendente de classificação predefinida.


### <a name="hit-highlighting"></a>Detetor de ocorrências
No Azure Search, realce da parte exata dos resultados da pesquisa que correspondem à consulta de pesquisa é facilitado através da **`highlight`**, **`highlightPreTag`**, e **`highlightPostTag`** parâmetros. Pode especificar os campos *pesquisáveis* que devem ter o respetivo texto com correspondência realçado, bem como especificar as etiquetas de cadeias exatas a adicionar no início e no fim do texto com correspondência devolvido pela Azure Search.

## <a name="see-also"></a>Consulte também

+ [Completa como funciona a pesquisa de texto no Azure Search (arquitetura de análise de consulta)](search-lucene-query-architecture.md)
+ [Explorador de pesquisa](search-explorer.md)
+ [Como consultar no .NET](search-query-dotnet.md)
+ [Como consultar no REST](search-create-index-rest-api.md)
