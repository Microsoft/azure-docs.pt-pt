---
title: Create a search index in the Azure portal
titleSuffix: Azure Cognitive Search
description: In this portal quickstart, learn how to use the Import Data wizard to create, load, and query your first search index in Azure Cognitive Search.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: be2425d97573e7990ea7f0dfd4c2d999e85fe922
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74407003"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-the-azure-portal"></a>Quickstart: Create an Azure Cognitive Search index in the Azure portal
> [!div class="op_single_selector"]
> * [Portal](search-get-started-portal.md)
> * [C#](search-get-started-dotnet.md)
> * [Java](search-get-started-java.md)
> * [Node.js](search-get-started-nodejs.md)
> * [PowerShell](search-get-started-powershell.md)
> * [Postman](search-get-started-postman.md)
> * [Python](search-get-started-python.md)

Use the portal's **Import data** wizard and **Search explorer** tools to quickly ramp up on concepts, and write interesting queries against an index within minutes.

If the tools are too limiting, you can consider a [code-based introduction to programming Azure Cognitive Search in .NET](search-howto-dotnet-sdk.md) or use [Postman for making REST API calls](search-get-started-postman.md). 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

[Create an Azure Cognitive Search service](search-create-service-portal.md) or [find an existing service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under your current subscription. You can use a free service for this quickstart. 

### <a name="check-for-space"></a>Verificar o espaço

Muitos clientes começam com o serviço gratuito. Esta versão está limitada a três índices, três origens de dados e três indexadores. Certifique-se de que tem espaço para itens adicionais antes de começar. Este tutorial cria um objeto de cada.

Sections on the service dashboard show how many indexes, indexers, and data sources you already have. 

![Lists of indexes, indexers, and datasources](media/search-get-started-portal/tiles-indexers-datasources.png)

## <a name="create-index"></a> Criar um índice e carregar dados

As consultas de pesquisa iteram num [*índice*](search-what-is-an-index.md) que contém dados pesquisáveis, metadados e construções adicionais que otimizam determinados comportamentos de pesquisa.

For this tutorial, we use a built-in sample dataset that can be crawled using an [*indexer*](search-indexer-overview.md) via the [**Import data** wizard](search-import-data-portal.md). Um indexador é um crawler específico da origem que pode ler metadados e conteúdo de origens de dados suportadas pelo Azure. Normally, indexers are used programmatically, but in the portal, you can access them through the **Import data** wizard. 

### <a name="step-1---start-the-import-data-wizard-and-create-a-data-source"></a>Step 1 - Start the Import data wizard and create a data source

1. On the Azure Cognitive Search service dashboard, click **Import data** on the command bar to create and populate a search index.

   ![Comando de importação de dados](media/search-get-started-portal/import-data-cmd.png)

2. In the wizard, click **Connect to your data** > **Samples** > **hotels-sample**. This data source is built-in. If you were creating your own data source, you would need to specify a name, type, and connection information. Depois de criada, torna-se uma “origem de dados existente”, que pode ser reutilizada noutras operações de importação.

   ![Selecionar o conjunto de dados de exemplo](media/search-get-started-portal/import-datasource-sample.png)

3. Continue to the next page.

### <a name="step-2---skip-the-enrich-content-page"></a>Step 2 - Skip the "Enrich content" page

The wizard supports the creation of an [AI enrichment pipeline](cognitive-search-concept-intro.md) for incorporating the Cognitive Services AI algorithms into indexing. 

We'll skip this step for now, and move directly on to **Customize target index**.

   ![Ignorar o passo de capacidades cognitivas](media/search-get-started-portal/skip-cog-skill-step.png)

> [!TIP]
> You can step through an AI-indexing example in a [quickstart](cognitive-search-quickstart-blob.md) or [tutorial](cognitive-search-tutorial-blob.md).

### <a name="step-3---configure-index"></a>Step 3 - Configure index

Typically, index creation is a code-based exercise, completed prior to loading data. However, as this tutorial indicates, the wizard can generate a basic index for any data source it can crawl. No mínimo, os índices precisam de um nome e de uma coleção de campos; um dos campos deve ser marcado como a chave do documento para identificar exclusivamente cada documento. Additionally, you can specify language analyzers or suggesters if you want autocomplete or suggested queries.

Os campos têm tipos de dados e atributos. As caixas de verificação na parte superior são *atributos de índice* que controlam a forma como o campo é utilizado.

* **Recuperável** significa que aparece na lista de resultados da pesquisa. You can mark individual fields as off limits for search results by clearing this checkbox, for example for fields used only in filter expressions.
* **Key** is the unique document identifier. It's always a string, and it is required.
* **Filterable**, **Sortable**, and **Facetable** determine whether fields are used in a filter, sort, or faceted navigation structure.
* **Pesquisável** significa que um campo está incluído na pesquisa de texto completo. As cadeias são pesquisáveis. Os campos numéricos e booleanos são frequentemente marcados como não pesquisáveis.

Storage requirements do not vary as a result of your selection. For example, if you set the **Retrievable** attribute on multiple fields, storage requirements do not go up.

Por predefinição, o assistente verifica a origem de dados relativamente a identificadores exclusivos como a base do campo de chaves. *Strings* are attributed as **Retrievable** and **Searchable**. *Integers* are attributed as **Retrievable**, **Filterable**, **Sortable**, and **Facetable**.

1. Aceite as predefinições. 

   If you rerun the wizard a second time using an existing hotels data source, the index won't be configured with default attributes. You'll have to manually select attributes on future imports. 

   ![Generated hotels index](media/search-get-started-portal/hotelsindex.png)

2. Continue to the next page.


### <a name="step-4---configure-indexer"></a>Step 4 - Configure indexer

Ainda no assistente para **Importar dados**, clique em **Indexador** > **Nome** e escreva um nome para o indexador.

Este objeto define um processo executável. You could put it on recurring schedule, but for now use the default option to run the indexer once, immediately.

Click **Submit** to create and simultaneously run the indexer.

  ![hotels indexer](media/search-get-started-portal/hotels-indexer.png)

## <a name="monitor-progress"></a>Monitor progress

The wizard should take you to the Indexers list where you can monitor progress. For self-navigation, go to the Overview page and click **Indexers**.

It can take a few minutes for the portal to update the page, but you should see the newly created indexer in the list, with status indicating "in progress" or success, along with the number of documents indexed.

   ![Mensagem de indexador em curso](media/search-get-started-portal/indexers-inprogress.png)

## <a name="view-the-index"></a>Ver o índice

The main service page provides links to the resources created in your Azure Cognitive Search service.  To view the index you just created, click **Indexes** from the list of links. 

Wait for the portal page to refresh. After a few minutes, you should see the index with a document count and storage size.

   ![Indexes list on the service dashboard](media/search-get-started-portal/indexes-list.png)

From this list, you can click on the *hotels-sample* index that you just created, view the index schema. and optionally add new fields. 

The **Fields** tab shows the index schema. Scroll to the bottom of the list to enter a new field. In most cases, you cannot change existing fields. Existing fields have a physical representation in Azure Cognitive Search and are thus non-modifiable, not even in code. To fundamentally change an existing field, create a new index, dropping the original.

   ![definição de índice de exemplo](media/search-get-started-portal/sample-index-def.png)

Outras construções, como a classificação de perfis e as opções de CORS, podem ser adicionadas a qualquer momento.

Para compreender claramente o que pode e não pode editar durante o design do índice, dispense um minuto para ver as opções de definição do índice. As opções desativadas são um indicador de que um valor não pode ser alterado nem eliminado. 

## <a name="query-index"></a> Query using Search explorer

Mais adiante, já deve ter um índice de pesquisa pronto para consultar com a página de consulta [**Explorador de procura**](search-explorer.md) incorporada. Fornece uma caixa de pesquisa para que possa testar as cadeias de consulta arbitrária.

**Search explorer** is only equipped to handle [REST API requests](https://docs.microsoft.com/rest/api/searchservice/search-documents), but it accepts syntax for both [simple query syntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) and [full Lucene query parser](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), plus all the search parameters available in [Search Document REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) operations.

> [!TIP]
> The following steps are demonstrated at 6m08s into the [Azure Cognitive Search Overview video](https://channel9.msdn.com/Events/Connect/2016/138).
>

1. Clique em **Explorador de pesquisa** na barra de comando.

   ![Comando do Explorador de pesquisa](media/search-get-started-portal/search-explorer-cmd.png)

2. From the **Index** dropdown, choose  *hotels-sample-index*. Click the **API Version** dropdown, to see which REST APIs are available. For the queries below, use the generally available version (2019-05-06).

   ![Comandos de índice e de API](media/search-get-started-portal/search-explorer-changeindex.png)

3. In the search bar, paste in the query strings below and click **Search**.

   ![Query string and search button](media/search-get-started-portal/search-explorer-query-string-example.png)

## <a name="example-queries"></a>Example queries

You can enter terms and phrases, similar to what you might do in a Bing or Google search, or fully-specified query expressions. Results are returned as verbose JSON documents.

### <a name="simple-query-with-top-n-results"></a>Consulta simples com N principais resultados

#### <a name="example-string-query-searchspa"></a>Example (string query): `search=spa`

* The **search** parameter is used to input a keyword search for full text search, in this case, returning hotel data for those containing *spa* in any searchable field in the document.

* O **Explorador de pesquisa** devolve resultados em JSON, que é verboso e difícil de ler se os documentos tiverem uma estrutura densa. This is intentional; visibility into the entire document is important for development purposes, especially during testing. Para uma melhor experiência de utilizador, terá de escrever código que [processa resultados de pesquisa](search-pagination-page-layout.md) para reproduzir elementos importantes.

* Os documentos são compostos por todos os campos marcados como "recuperáveis" no índice. To view index attributes in the portal, click *hotels-sample* in the **Indexes** list.

#### <a name="example-parameterized-query-searchspacounttruetop10"></a>Example (parameterized query): `search=spa&$count=true&$top=10`

* O símbolo **&** é utilizado para acrescentar os parâmetros da pesquisa, que podem ser especificados por qualquer ordem.

* The **$count=true** parameter returns the total count of all documents returned. Este valor é apresentado junto à parte superior dos resultados da pesquisa. Pode verificar as consultas de filtro através da monitorização das alterações comunicadas por **$count=true**. As contagens mais pequenas indicam que o filtro está a funcionar.

* The **$top=10** returns the highest ranked 10 documents out of the total. By default, Azure Cognitive Search returns the first 50 best matches. Pode aumentar ou diminuir a quantidade através de **$top**.

### <a name="filter-query"></a>Filtrar a consulta

Os filtros são incluídos nos pedidos de pesquisa se acrescentar o parâmetro **$filter**. 

#### <a name="example-filtered-searchbeachfilterrating-gt-4"></a>Exemplo (filtrado): `search=beach&$filter=Rating gt 4`

* O parâmetro **$filter** devolve resultados que correspondem aos critérios que indicou. In this case, ratings greater than 4.

* A sintaxe do filtro é uma construção OData. Para obter mais informações, veja [Filter OData syntax (Sintaxe de Filtros OData)](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

### <a name="facet-query"></a> Facetar a consulta

Os filtros de faceta são incluídos nos resultados da pesquisa. Pode utilizar o parâmetro de faceta para devolver uma contagem agregada dos documentos que correspondam a um valor de faceta que indicar.

#### <a name="example-faceted-with-scope-reduction-searchfacetcategorytop2"></a>Exemplo (por facetas com redução de âmbito): `search=*&facet=Category&$top=2`

* **search=** * é uma consulta vazia. As pesquisas em branco pesquisam em tudo. Uma das razões para submeter uma consulta em branco é para filtrar ou especificar facetas no conjunto completo de documentos. For example, you want a faceting navigation structure to consist of all hotels in the index.
* **facet** devolve uma estrutura de navegação que pode ser transmitida a um controlo de IU. Devolve categorias e uma contagem. In this case, categories are based on a field conveniently called *Category*. There is no aggregation in Azure Cognitive Search, but you can approximate aggregation via `facet`, which gives a count of documents in each category.

* **$top=2** devolve dois documentos, o que exemplifica que pode utilizar `top` para reduzir ou aumentar os resultados.

#### <a name="example-facet-on-numeric-values-searchspafacetrating"></a>Example (facet on numeric values): `search=spa&facet=Rating`

* This query is facet for rating, on a text search for *spa*. The term *Rating* can be specified as a facet because the field is marked as retrievable, filterable, and facetable in the index, and the values it contains (numeric, 1 through 5), are suitable for categorizing listings into groups.

* Apenas os campos filtráveis podem ser facetados. Apenas os campos recuperáveis podem ser devolvidos nos resultados.

* The *Rating* field is double-precision floating point and the grouping will be by precise value. For more information on grouping by interval (for instance, "3 star ratings," "4 star ratings," etc.), see [How to implement faceted navigation in Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-faceted-navigation#filter-based-on-a-range).


### <a name="highlight-query"></a> Realçar os resultados de pesquisa

O detetor de ocorrências refere-se à formatação no texto que corresponde à palavra-chave, tendo em conta que as correspondências são encontradas num determinado campo. Se o termo da sua pesquisa estiver embrenhado numa descrição, pode adicionar o detetor de ocorrências para que seja mais fácil encontrá-lo.

#### <a name="example-highlighter-searchbeachhighlightdescription"></a>Exemplo (marcador): `search=beach&highlight=Description`

* In this example, the formatted word *beach* is easier to spot in the description field.

#### <a name="example-linguistic-analysis-searchbeacheshighlightdescription"></a>Exemplo (análise linguística): `search=beaches&highlight=Description`

* Full text search recognizes basic variations in word forms. In this case, search results contain highlighted text for "beach", for hotels that have that word in their searchable fields, in response to a keyword search on "beaches". Podem aparecer nos resultados diferentes formas da mesma palavra, devido à análise linguística. 

* Azure Cognitive Search supports 56 analyzers from both Lucene and Microsoft. The default used by Azure Cognitive Search is the standard Lucene analyzer.

### <a name="fuzzy-search"></a> Experimentar a pesquisa difusa

By default, misspelled query terms, like *seatle* for "Seattle", fail to return matches in typical search. O exemplo seguinte não devolve nenhum resultado.

#### <a name="example-misspelled-term-unhandled-searchseatle"></a>Exemplo (termo com erros ortográficos, não processado): `search=seatle`

Para processar erros ortográficos, pode utilizar a pesquisa difusa. A pesquisa difusa é ativada quando utiliza a sintaxe de consulta Lucene completa, que ocorre quando faz duas coisas: define **queryType=full** na consulta e anexa **~** à cadeia de pesquisa.

#### <a name="example-misspelled-term-handled-searchseatlequerytypefull"></a>Exemplo (termo com erros ortográficos, processado): `search=seatle~&queryType=full`

This example now returns documents that include matches on "Seattle".

Quando **queryType** não é especificado, é utilizado o analisador de consultas simples predefinido. O analisador de consultas simples é mais rápido, mas se precisar de pesquisa difusa, de expressões regulares, da pesquisa de proximidade ou de outros tipos de consultas avançadas, será necessária a sintaxe completa.

A pesquisa difusa e a pesquisa com carateres universais têm implicações no resultado da pesquisa. A análise linguística não é realizada nestes formatos de consulta. Before using fuzzy and wildcard search, review [How full text search works in Azure Cognitive Search](search-lucene-query-architecture.md#stage-2-lexical-analysis) and look for the section about exceptions to lexical analysis.

For more information about query scenarios enabled by the full query parser, see [Lucene query syntax in Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search).

### <a name="geo-search"></a> Experimentar a pesquisa geoespacial

A pesquisa geoespacial é suportada através do [tipo de dados edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) em campos que contenham coordenadas. A pesquisa geográfica é um tipo de filtros especificado em [Filter OData syntax (Sintaxe de Filtros OData)](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-12212-4767-le-5"></a>Exemplo (filtros de coordenadas geográficas): `search=*&$count=true&$filter=geo.distance(Location,geography'POINT(-122.12 47.67)') le 5`

A consulta de exemplo filtra todos os resultados relativamente a dados posicionais, em que os resultados estejam a menos de cinco quilómetros de distância de um determinado ponto (especificado como coordenadas de latitude e longitude). Ao adicionar **$count**, pode ver quantos resultados são devolvidos quando alterar a distância ou as coordenadas.

A pesquisa geoespacial é útil se a sua aplicação de pesquisa tiver uma funcionalidade “encontrar perto de mim” ou se utilizar a navegação por mapa. Contudo, não é uma pesquisa em texto completo. If you have user requirements for searching on a city or country/region by name, add fields containing city or country/region names, in addition to coordinates.

## <a name="takeaways"></a>Conclusões

This tutorial provided a quick introduction to Azure Cognitive Search using the Azure portal.

Aprendeu a criar um índice de pesquisa com o assistente para **Importar dados**. Aprendeu sobre [indexadores](search-indexer-overview.md), bem como o fluxo de trabalho básico para o design de índices, incluindo [modificações suportadas num índice publicado](https://docs.microsoft.com/rest/api/searchservice/update-index).

Ao utilizar o **explorador do Search** no portal do Azure, aprendeu a sintaxe básica das consultas através de exemplos práticos que demonstram as capacidades principais, como filtros, detetor de ocorrências, pesquisa difusa e pesquisa geográfica.

You also learned how to find indexes, indexers, and data sources in the portal. Utilizando uma origem de dados nova no futuro, pode utilizar o portal para verificar rapidamente as definições ou as coleções de campos da mesma com um esforço mínimo.

## <a name="clean-up-resources"></a>Limpar recursos

When you're working in your own subscription, it's a good idea at the end of a project to identify whether you still need the resources you created. Resources left running can cost you money. You can delete resources individually or delete the resource group to delete the entire set of resources.

You can find and manage resources in the portal, using the **All resources** or **Resource groups** link in the left-navigation pane.

If you are using a free service, remember that you are limited to three indexes, indexers, and data sources. You can delete individual items in the portal to stay under the limit. 

## <a name="next-steps"></a>Passos seguintes

While the portal is useful for initial exploration and small tasks, reviewing the APIs early on will help you understand the concepts and workflow on a deeper level:

> [!div class="nextstepaction"]
> [Create an index using .NET SDK](https://docs.microsoft.com/azure/search/search-create-index-dotnet)