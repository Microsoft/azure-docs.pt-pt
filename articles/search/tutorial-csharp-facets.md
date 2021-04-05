---
title: Tutorial C# sobre o uso de facetas para ajudar na navegação
titleSuffix: Azure Cognitive Search
description: Continue a partir de "Resultados de Paging" para adicionar navegação facetada. Saiba como as facetas podem ser usadas para reduzir facilmente uma pesquisa.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 44a05ed5b4386f2787a1c84dfeb61a5d539c7cb5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98789807"
---
# <a name="tutorial-add-faceted-navigation-using-the-net-sdk"></a>Tutorial: Adicionar navegação facetada usando o .NET SDK

As facetas permitem a navegação auto-dirigida, fornecendo um conjunto de links para os resultados de filtragem. Neste tutorial, uma estrutura de navegação facetada é colocada no lado esquerdo da página, com etiquetas e texto clicável para aparar os resultados.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Definir propriedades do modelo como _IsFacetable_
> * Adicione navegação faceta à sua app

## <a name="overview"></a>Descrição Geral

As facetas são baseadas em campos no seu índice de pesquisa. Um pedido de consulta que inclui facet=[string] fornece o campo para facetar por. É comum incluir múltiplas facetas, tais `&facet=category&facet=amenities` como, cada uma separada por um caráter ampersand (&). A implementação de uma estrutura de navegação facetada requer que especifique ambas as facetas e filtros. O filtro é utilizado num evento de clique para reduzir os resultados. Por exemplo, clicar em "orçamento" filtra os resultados com base nesses critérios.

Este tutorial alarga o projeto de paging criado no [Add paging para pesquisar resultados](tutorial-csharp-paging.md) tutoriais.

Uma versão acabada do código neste tutorial pode ser encontrada no seguinte projeto:

* [4-add-facet-navegação (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/4-add-facet-navigation)

## <a name="prerequisites"></a>Pré-requisitos

* [Solução 2a-add-paging (GitHub).](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2a-add-paging) Este projeto pode ser a sua própria versão construída a partir do tutorial anterior ou uma cópia do GitHub.

Este tutorial foi atualizado para utilizar o pacote [Azure.Search.Documents (versão 11).](https://www.nuget.org/packages/Azure.Search.Documents/) Para obter uma versão anterior do .NET SDK, consulte [a amostra de código Microsoft.Azure.Search (versão 10).](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10)

## <a name="set-model-properties-as-isfacetable"></a>Definir propriedades do modelo como IsFacetable

Para que uma propriedade modelo seja localizada numa pesquisa de facetas, deve ser marcado com **IsFacetable**.

1. Examine a classe **hoteleira.** **As categorias** e tags , por exemplo, são **marcadas** como **IsFacetable,** mas **o HotelName** e **a Description** não são. 

    ```cs
    public partial class Hotel
    {
        [SimpleField(IsFilterable = true, IsKey = true)]
        public string HotelId { get; set; }

        [SearchableField(IsSortable = true)]
        public string HotelName { get; set; }

        [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
        public string Description { get; set; }

        [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
        [JsonPropertyName("Description_fr")]
        public string DescriptionFr { get; set; }

        [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
        public string Category { get; set; }

        [SearchableField(IsFilterable = true, IsFacetable = true)]
        public string[] Tags { get; set; }

        [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
        public bool? ParkingIncluded { get; set; }

        [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
        public DateTimeOffset? LastRenovationDate { get; set; }

        [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
        public double? Rating { get; set; }

        public Address Address { get; set; }

        [SimpleField(IsFilterable = true, IsSortable = true)]
        public GeographyPoint Location { get; set; }

        public Room[] Rooms { get; set; }
    }
    ```

1. Não vamos mudar nenhuma etiqueta como parte deste tutorial, por isso feche o arquivo .cs hotel sem litoral.

    > [!Note]
    > Uma pesquisa de faceta lançará um erro se um campo solicitado na pesquisa não for marcado adequadamente.

## <a name="add-facet-navigation-to-your-app"></a>Adicione navegação faceta à sua app

Para este exemplo, vamos permitir ao utilizador selecionar uma categoria de hotel, ou uma amenidade, a partir de listas de links mostrados à esquerda dos resultados. O utilizador começa por introduzir algum texto de pesquisa e, em seguida, reduz progressivamente os resultados da pesquisa selecionando uma categoria ou amenidade.

É trabalho do controlador passar as listas de facetas para a vista. Para manter as seleções de utilizadores à medida que a pesquisa progride, utilizamos o armazenamento temporário como mecanismo de preservação do estado.

![Usando a navegação faceta para reduzir uma pesquisa de "piscina"](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>Adicione cordas de filtro ao modelo SearchData

1. Abra o ficheiro SearchData.cs e adicione propriedades de cordas à classe **SearchData,** para manter as cordas do filtro faceta.

    ```cs
    public string categoryFilter { get; set; }
    public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>Adicione o método de ação Facet

O controlador doméstico precisa de uma nova ação, **Facet,** e atualiza as ações existentes **de Index** e **Page,** e do método **RunQueryAsync.**

<!-- 1. Open the home controller file, and add the **using** statement, to enable the **List&lt;string&gt;** construct.

    ```cs
    using System.Collections.Generic; 
    ```-->

1. Substitua o método de ação **Index (Modelo SearchData).**

    ```cs
    public async Task<ActionResult> Index(SearchData model)
    {
        try
        {
            // Ensure the search string is valid.
            if (model.searchText == null)
            {
                model.searchText = "";
            }

            // Make the search call for the first page.
            await RunQueryAsync(model, 0, 0, "", "").ConfigureAwait(false);
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }

        return View(model);
    }
    ```

1. Substitua o método de ação **PageAsync (modelo SearchData).**

    ```cs
    public async Task<ActionResult> PageAsync(SearchData model)
    {
        try
        {
            int page;

            // Calculate the page that should be displayed.
            switch (model.paging)
            {
                case "prev":
                    page = (int)TempData["page"] - 1;
                    break;

                case "next":
                    page = (int)TempData["page"] + 1;
                    break;

                default:
                    page = int.Parse(model.paging);
                    break;
            }

            // Recover the leftMostPage.
            int leftMostPage = (int)TempData["leftMostPage"];

            // Recover the filters.
            string catFilter = TempData["categoryFilter"].ToString();
            string ameFilter = TempData["amenityFilter"].ToString();

            // Recover the search text.
            model.searchText = TempData["searchfor"].ToString();

            // Search for the new page.
            await RunQueryAsync(model, page, leftMostPage, catFilter, ameFilter);
        }

        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "2" });
        }
        return View("Index", model);
    }
    ```

1. Adicione um método de ação **FacetAsync (modelo SearchData),** a ser ativado quando o utilizador clicar num link facet. O modelo conterá um filtro de pesquisa de categoria ou de amenidade. Adicione-o depois da ação **do PageAsync.**

    ```cs
    public async Task<ActionResult> FacetAsync(SearchData model)
    {
        try
        {
            // Filters set by the model override those stored in temporary data.
            string catFilter;
            string ameFilter;
            if (model.categoryFilter != null)
            {
                catFilter = model.categoryFilter;
            } else
            {
                catFilter = TempData["categoryFilter"].ToString();
            }

            if (model.amenityFilter != null)
            {
                ameFilter = model.amenityFilter;
            } else
            {
                ameFilter = TempData["amenityFilter"].ToString();
            }

            // Recover the search text.
            model.searchText = TempData["searchfor"].ToString();

            // Initiate a new search.
            await RunQueryAsync(model, 0, 0, catFilter, ameFilter).ConfigureAwait(false);
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "2" });
        }

        return View("Index", model);
    }
    ```

### <a name="set-up-the-search-filter"></a>Configurar o filtro de pesquisa

Quando um utilizador seleciona uma determinada faceta, por exemplo, clica na categoria **Resort e Spa,** então apenas os hotéis especificados como esta categoria devem ser devolvidos nos resultados. Para reduzir uma busca desta forma, precisamos de configurar um _filtro._

1. Substitua o método **RunQueryAsync** pelo seguinte código. Em primeiro lugar, é necessário uma cadeia de filtro de categoria e uma cadeia de filtro de amenidade, e define o parâmetro **filtro** das **Opções de Busca**.

    ```cs
    private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage, string catFilter, string ameFilter)
    {
        InitSearch();

        string facetFilter = "";

        if (catFilter.Length > 0 && ameFilter.Length > 0)
        {
            // Both facets apply.
            facetFilter = $"{catFilter} and {ameFilter}"; 
        } else
        {
            // One, or zero, facets apply.
            facetFilter = $"{catFilter}{ameFilter}";
        }

        var options = new SearchOptions
        {
            Filter = facetFilter,

            SearchMode = SearchMode.All,

            // Skip past results that have already been returned.
            Skip = page * GlobalVariables.ResultsPerPage,

            // Take only the next page worth of results.
            Size = GlobalVariables.ResultsPerPage,

            // Include the total number of results.
            IncludeTotalCount = true,
        };

        // Return information on the text, and number, of facets in the data.
        options.Facets.Add("Category,count:20");
        options.Facets.Add("Tags,count:20");

        // Enter Hotel property names into this list, so only these values will be returned.
        options.Select.Add("HotelName");
        options.Select.Add("Description");
        options.Select.Add("Category");
        options.Select.Add("Tags");

        // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
        model.resultList = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);

        // This variable communicates the total number of pages to the view.
        model.pageCount = ((int)model.resultList.TotalCount + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

        // This variable communicates the page number being displayed to the view.
        model.currentPage = page;

        // Calculate the range of page numbers to display.
        if (page == 0)
        {
            leftMostPage = 0;
        }
        else if (page <= leftMostPage)
        {
            // Trigger a switch to a lower page range.
            leftMostPage = Math.Max(page - GlobalVariables.PageRangeDelta, 0);
        }
        else if (page >= leftMostPage + GlobalVariables.MaxPageRange - 1)
        {
            // Trigger a switch to a higher page range.
            leftMostPage = Math.Min(page - GlobalVariables.PageRangeDelta, model.pageCount - GlobalVariables.MaxPageRange);
        }
        model.leftMostPage = leftMostPage;

        // Calculate the number of page numbers to display.
        model.pageRange = Math.Min(model.pageCount - leftMostPage, GlobalVariables.MaxPageRange);

        // Ensure Temp data is stored for the next call.
        TempData["page"] = page;
        TempData["leftMostPage"] = model.leftMostPage;
        TempData["searchfor"] = model.searchText;
        TempData["categoryFilter"] = catFilter;
        TempData["amenityFilter"] = ameFilter;

        // Return the new view.
        return View("Index", model);
    }
    ```

    Note que as propriedades **de Categoria** e Tags são **adicionadas** à lista de itens **Select** a devolver. Esta adição não é um requisito para que a navegação faceta funcione, mas utilizamos estas informações para verificar se os filtros estão a funcionar corretamente.

### <a name="add-lists-of-facet-links-to-the-view"></a>Adicionar listas de links facetas à vista

A vista vai requerer algumas mudanças significativas. 

1. Comece por abrir os hotéis.css ficheiro (na pasta wwwroot/css) e adicione as seguintes aulas.

    ```html
    .facetlist {
        list-style: none;
    }

    .facetchecks {
        width: 250px;
        display: normal;
        color: #666;
        margin: 10px;
        padding: 5px;
    }

    .facetheader {
        font-size: 10pt;
        font-weight: bold;
        color: darkgreen;
    }
    ```

1. Para a vista, organize a saída numa tabela, para alinhar cuidadosamente as listas de facetas à esquerda, e os resultados à direita. Abra o ficheiro index.cshtml. Substitua todo o conteúdo das &lt; marcas de corpo &gt; HTML, pelo seguinte código.

    ```html
    <body>
        @using (Html.BeginForm("Index", "Home", FormMethod.Post))
        {
            <table>
                <tr>
                    <td></td>
                    <td>
                        <h1 class="sampleTitle">
                            <img src="~/images/azure-logo.png" width="80" />
                            Hotels Search - Facet Navigation
                        </h1>
                    </td>
                </tr>

                <tr>
                    <td></td>
                    <td>
                        <!-- Display the search text box, with the search icon to the right of it.-->
                        <div class="searchBoxForm">
                            @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input value="" class="searchBoxSubmit" type="submit">
                        </div>
                    </td>
                </tr>

                <tr>
                    <td valign="top">
                        <div id="facetplace" class="facetchecks">

                            @if (Model != null && Model.resultList != null)
                            {
                                List<string> categories = Model.resultList.Facets["Category"].Select(x => x.Value.ToString()).ToList();

                                if (categories.Count > 0)
                                {
                                    <h5 class="facetheader">Category:</h5>
                                    <ul class="facetlist">
                                        @for (var c = 0; c < categories.Count; c++)
                                        {
                                            var facetLink = $"{categories[c]} ({Model.resultList.Facets["Category"][c].Count})";
                                            <li>
                                                @Html.ActionLink(facetLink, "FacetAsync", "Home", new { categoryFilter = $"Category eq '{categories[c]}'" }, null)
                                            </li>
                                        }
                                    </ul>
                                }

                                List<string> tags = Model.resultList.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

                                if (tags.Count > 0)
                                {
                                    <h5 class="facetheader">Amenities:</h5>
                                    <ul class="facetlist">
                                        @for (var c = 0; c < tags.Count; c++)
                                        {
                                            var facetLink = $"{tags[c]} ({Model.resultList.Facets["Tags"][c].Count})";
                                            <li>
                                                @Html.ActionLink(facetLink, "FacetAsync", "Home", new { amenityFilter = $"Tags/any(t: t eq '{tags[c]}')" }, null)
                                            </li>
                                        }
                                    </ul>
                                }
                            }
                        </div>
                    </td>
                    <td valign="top">
                        <div id="resultsplace">
                            @if (Model != null && Model.resultList != null)
                            {
                                // Show the result count.
                                <p class="sampleText">
                                    @Model.resultList.TotalCount Results
                                </p>

                                var results = Model.resultList.GetResults().ToList();

                                @for (var i = 0; i < results.Count; i++)
                                {
                                    string amenities = string.Join(", ", results[i].Document.Tags);

                                    string fullDescription = results[i].Document.Description;
                                    fullDescription += $"\nCategory: {results[i].Document.Category}";
                                    fullDescription += $"\nAmenities: {amenities}";


                                    // Display the hotel name and description.
                                    @Html.TextAreaFor(m => results[i].Document.HotelName, new { @class = "box1" })
                                    @Html.TextArea($"desc{i}", fullDescription, new { @class = "box2" })
                                }
                            }
                        </div>
                    </td>
                </tr>

                <tr>
                    <td></td>
                    <td valign="top">
                        @if (Model != null && Model.pageCount > 1)
                        {
                            // If there is more than one page of results, show the paging buttons.
                            <table>
                                <tr>
                                    <td class="tdPage">
                                        @if (Model.currentPage > 0)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink("|<", "PageAsync", "Home", new { paging = "0" }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">|&lt;</p>
                                        }
                                    </td>

                                    <td class="tdPage">
                                        @if (Model.currentPage > 0)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink("<", "PageAsync", "Home", new { paging = "prev" }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">&lt;</p>
                                        }
                                    </td>

                                    @for (var pn = Model.leftMostPage; pn < Model.leftMostPage + Model.pageRange; pn++)
                                    {
                                        <td class="tdPage">
                                            @if (Model.currentPage == pn)
                                            {
                                                // Convert displayed page numbers to 1-based and not 0-based.
                                                <p class="pageSelected">@(pn + 1)</p>
                                            }
                                            else
                                            {
                                                <p class="pageButton">
                                                    @Html.ActionLink((pn + 1).ToString(), "PageAsync", "Home", new { paging = @pn }, null)
                                                </p>
                                            }
                                        </td>
                                    }

                                    <td class="tdPage">
                                        @if (Model.currentPage < Model.pageCount - 1)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink(">", "PageAsync", "Home", new { paging = "next" }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">&gt;</p>
                                        }
                                    </td>

                                    <td class="tdPage">
                                        @if (Model.currentPage < Model.pageCount - 1)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink(">|", "PageAsync", "Home", new { paging = Model.pageCount - 1 }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">&gt;|</p>
                                        }
                                    </td>
                                </tr>
                            </table>
                        }
                    </td>
                </tr>
            </table>
        }
    </body>
    ```

    Note a utilização da chamada **Html.ActionLink.** Esta chamada comunica cadeias de filtro válidas ao controlador, quando o utilizador clica num link facet. 

### <a name="run-and-test-the-app"></a>Executar e testar a aplicação

A vantagem da navegação faceta para o utilizador é que eles podem reduzir as pesquisas com um único clique, que podemos mostrar na seguinte sequência.

1. Executar a aplicação, digitar "aeroporto" como texto de pesquisa. Verifique se a lista de facetas aparece perfeitamente à esquerda. Estas facetas são tudo o que se aplica aos hotéis que têm "aeroporto" nos seus dados de texto, com uma contagem da frequência com que ocorrem.

    ![Usando a navegação faceta para reduzir uma pesquisa de "aeroporto"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport.png)

1. Clique na categoria **Resort e Spa.** Verifique todos os resultados desta categoria.

    ![Estreitando a pesquisa para "Resort e Spa"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

1. Clique na amenidade **do café da manhã continental.** Verifique todos os resultados ainda na categoria "Resort e Spa", com a amenidade selecionada.

    ![Reduzir a procura para "pequeno-almoço continental"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

1. Tente selecionar qualquer outra categoria, em seguida, uma amenidade, e veja os resultados de estreitamento. Então tente o contrário, uma amenidade, depois uma categoria. Envie uma pesquisa vazia para redefinir a página.

    >[!Note]
    > Quando uma seleção é feita numa lista de facetas (como categoria) irá substituir qualquer seleção anterior dentro da lista de categorias.

## <a name="takeaways"></a>Conclusões

Considere os seguintes takeaways deste projeto:

* É imperativo marcar cada campo facetable com a propriedade **IsFacetable** para inclusão na navegação faceta.
* As facetas são combinadas com filtros para reduzir os resultados.
* As facetas são cumulativas, com cada edifício de seleção no anterior para mais resultados mais estreitos.

## <a name="next-steps"></a>Passos seguintes

No próximo tutorial, olhamos para os resultados da encomenda. Até este ponto, os resultados são encomendados simplesmente na ordem em que estão localizados na base de dados.

> [!div class="nextstepaction"]
> [C# tutorial: Encomende os resultados- Pesquisa Cognitiva Azure](tutorial-csharp-orders.md)
