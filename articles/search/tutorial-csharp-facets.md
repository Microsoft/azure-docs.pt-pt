---
title: C#tutorial sobre o uso de facetas para ajudar a navegação
titleSuffix: Azure Cognitive Search
description: Este tutorial baseia-se no projeto "Pesquisa de resultados de paginação - Pesquisa Cognitiva Azure", para adicionar navegação de faceta. Aprenda como as facetas podem ser usadas para reduzir facilmente uma pesquisa.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: d88a9d7efdabd493fd31b961748bb6ad3bd8d738
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121563"
---
# <a name="c-tutorial-use-facets-to-aid-navigation---azure-cognitive-search"></a>C#Tutorial: Use facetas para ajudar na navegação - Pesquisa Cognitiva Azure

As facetas são usadas para ajudar a navegação, fornecendo ao utilizador um conjunto de links para usar para concentrar a sua pesquisa. Facetas são atributos dos dados (como a categoria, ou uma característica específica, de um hotel nos nossos dados de amostra).

Este tutorial baseia-se no projeto de paging criado no [ C# Tutorial: Pesquisa resultados paginação - Tutorial de Pesquisa Cognitiva Azure.](tutorial-csharp-paging.md)

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Definir propriedades do modelo como _IsFacetable_
> * Adicione navegação de facet a sua aplicação

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

Tenha [ C# o Tutorial: Resultados de pesquisa paginação - Projeto](tutorial-csharp-paging.md) de Pesquisa Cognitiva Azure em funcionamento. Este projeto pode ser a sua própria versão, ou instalá-lo a partir do GitHub: [Criar a primeira aplicação](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="set-model-properties-as-isfacetable"></a>Definir propriedades do modelo como IsFacetable

Para que uma propriedade modelo seja localizada numa busca de faceta, deve ser marcada com **IsFacetable**.

1. Examine a classe **hotel.** **Categoria** e **Etiquetas,** por exemplo, são marcados como **IsFacetable,** mas **o Nome** do Hotel e **a Descrição** não são. 

    ```cs
    public partial class Hotel
    {
        [System.ComponentModel.DataAnnotations.Key]
        [IsFilterable]
        public string HotelId { get; set; }

        [IsSearchable, IsSortable]
        public string HotelName { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnLucene)]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrLucene)]
        [JsonProperty("Description_fr")]
        public string DescriptionFr { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string Category { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string[] Tags { get; set; }

        [IsFilterable, IsSortable, IsFacetable]
        public bool? ParkingIncluded { get; set; }

        [IsFilterable, IsSortable, IsFacetable]
        public DateTimeOffset? LastRenovationDate { get; set; }

        [IsFilterable, IsSortable, IsFacetable]
        public double? Rating { get; set; }

        public Address Address { get; set; }

        [IsFilterable, IsSortable]
        public GeographyPoint Location { get; set; }

        public Room[] Rooms { get; set; }

    }
    ```

2. Não vamos alterar nenhuma etiqueta como parte deste tutorial, por isso feche o hotel.cs ficheiro inalterado.

    > [!Note]
    > Uma pesquisa de faceta irá lançar um erro se um campo solicitado na pesquisa não for marcado adequadamente.


## <a name="add-facet-navigation-to-your-app"></a>Adicione navegação de facet a sua aplicação

Para este exemplo, vamos permitir ao utilizador selecionar uma categoria de hotel, ou uma amenidade, a partir de listas de links mostrados à esquerda dos resultados. O utilizador começa por introduzir algum texto de pesquisa, depois pode reduzir os resultados da pesquisa selecionando uma categoria, e pode reduzir ainda mais os resultados selecionando uma amenidade, ou pode selecionar a amenidade primeiro (a ordem não é importante).

Precisamos que o controlador passe as listas de facetas para a vista. Precisamos manter as seleções dos utilizadores à medida que a pesquisa progride, e mais uma vez, usamos o armazenamento temporário como mecanismo de preservação de dados.

![Usando a navegação de faceta para estreitar uma busca de "piscina"](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>Adicione cordas de filtro ao modelo SearchData

1. Abra o ficheiro SearchData.cs e adicione propriedades de cordas à classe **SearchData,** para segurar as cordas do filtro de faceta.

    ```cs
        public string categoryFilter { get; set; }
        public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>Adicione o método de ação facet

O controlador doméstico precisa de uma nova ação, **Facet,** e atualizações para as suas ações de **Index** e **Page** existentes, bem como atualizações para o método **RunQueryAsync.**

1. Abra o ficheiro do controlador doméstico e adicione a declaração **de utilização,** para permitir que a **Lista&lt;&gt;** construção.

    ```cs
    using System.Collections.Generic;
    ```

2. Substitua o método de ação **Index (SearchData).**

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
                await RunQueryAsync(model, 0, 0, "", "");
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

3. Substitua o método de ação **Page (SearchData).**

    ```cs
        public async Task<ActionResult> Page(SearchData model)
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

4. Adicione um método de ação **Facet (SearchData)** a ser ativado quando o utilizador clicar num link facet. O modelo conterá um filtro de pesquisa de categoria ou um filtro de pesquisa de amenidade. Talvez adicioná-lo depois da ação da **Página.**

    ```cs
        public async Task<ActionResult> Facet(SearchData model)
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
                await RunQueryAsync(model, 0, 0, catFilter, ameFilter);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "2" });
            }
            return View("Index", model);
        }
    ```

### <a name="set-up-the-search-filter"></a>Configurar o filtro de pesquisa

Quando um utilizador seleciona uma determinada faceta, por exemplo, clica na categoria **Resort e Spa,** então apenas os hotéis que são especificados como esta categoria devem ser devolvidos nos resultados. Para reduzir uma pesquisa desta forma, precisamos de configurar um _filtro._

1. Substitua o método **RunQueryAsync** pelo seguinte código. Em primeiro lugar, é preciso uma cadeia de filtro de categoria e uma cadeia de filtro de amenidade, e define o parâmetro **filtro** dos **Parâmetros de Busca**.

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

            var parameters = new SearchParameters
            {
                Filter = facetFilter,

                // Return information on the text, and number, of facets in the data.
                Facets = new List<string> { "Category,count:20", "Tags,count:20" },

                // Enter Hotel property names into this list, so only these values will be returned.
                Select = new[] { "HotelName", "Description", "Category", "Tags" },

                SearchMode = SearchMode.All,

                // Skip past results that have already been returned.
                Skip = page * GlobalVariables.ResultsPerPage,

                // Take only the next page worth of results.
                Top = GlobalVariables.ResultsPerPage,

                // Include the total number of results.
                IncludeTotalResultCount = true,
            };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // This variable communicates the total number of pages to the view.
            model.pageCount = ((int)model.resultList.Count + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

            // This variable communicates the page number being displayed to the view.
            model.currentPage = page;

            // Calculate the range of page numbers to display.
            if (page == 0)
            {
                leftMostPage = 0;
            }
            else
               if (page <= leftMostPage)
            {
                // Trigger a switch to a lower page range.
                leftMostPage = Math.Max(page - GlobalVariables.PageRangeDelta, 0);
            }
            else
            if (page >= leftMostPage + GlobalVariables.MaxPageRange - 1)
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

    Adicionámos as propriedades **da Categoria** e **Etiquetas** à lista de itens **Select** para devolver. Esta adição não é um requisito para que a navegação por faceta funcione, mas utilizamos estas informações para verificar se estamos a filtrar corretamente.

### <a name="add-lists-of-facet-links-to-the-view"></a>Adicione listas de links de facet a vista

A vista vai exigir algumas mudanças significativas. 

1. Comece por abrir o ficheiro hotels.css (na pasta wwwroot/css), e adicione as seguintes aulas.

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

2. Para a vista, organizamos a saída numa tabela, para alinhar cuidadosamente as listas de facetas à esquerda, e os resultados à direita. Abra o ficheiro index.cshtml. Substitua todo o conteúdo do corpo HTML &lt;&gt; etiquetas, com o seguinte código.

    ```cs
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
                                            @Html.ActionLink(facetLink, "Facet", "Home", new { categoryFilter = $"Category eq '{categories[c]}'" }, null)
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
                                            @Html.ActionLink(facetLink, "Facet", "Home", new { amenityFilter = $"Tags/any(t: t eq '{tags[c]}')" }, null)
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
                                @Html.DisplayFor(m => m.resultList.Count) Results
                            </p>

                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);

                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nCategory: {Model.resultList.Results[i].Document.Category}";
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel name and description.
                                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
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
                                            @Html.ActionLink("|<", "Page", "Home", new { paging = "0" }, null)
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
                                            @Html.ActionLink("<", "Page", "Home", new { paging = "prev" }, null)
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
                                                @Html.ActionLink((pn + 1).ToString(), "Page", "Home", new { paging = @pn }, null)
                                            </p>
                                        }
                                    </td>
                                }

                                <td class="tdPage">
                                    @if (Model.currentPage < Model.pageCount - 1)
                                    {
                                        <p class="pageButton">
                                            @Html.ActionLink(">", "Page", "Home", new { paging = "next" }, null)
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
                                            @Html.ActionLink(">|", "Page", "Home", new { paging = Model.pageCount - 1 }, null)
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

    Note a utilização da chamada **Html.ActionLink.** Esta chamada comunica cordas de filtro válidas ao controlador, quando o utilizador clica num link de faceta. 

### <a name="run-and-test-the-app"></a>Executar e testar a app

A vantagem da navegação facet a quem o utilizador pode reduzir as pesquisas com um único clique, que podemos mostrar na seguinte sequência.

1. Executar a aplicação, digitar "aeroporto" como texto de pesquisa. Verifique se a lista de facetas aparece perfeitamente à esquerda. Estas facetas são tudo o que se aplica a hotéis que têm "aeroporto" nos seus dados de texto, com uma contagem da frequência com que ocorrem.

    ![Usando a navegação de faceta para estreitar uma busca de "aeroporto"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport.png)

2. Clique na categoria **Resort e Spa.** Verifique se todos os resultados estão nesta categoria.

    ![Reduzindo a procura para "Resort e Spa"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

3. Clique na amenidade do café da **manhã continental.** Verifique se todos os resultados ainda estão na categoria "Resort e Spa", com a amenidade selecionada.

    ![Reduzindo a procura para o "pequeno-almoço continental"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

4. Tente selecionar qualquer outra categoria, depois uma amenidade, e veja os resultados de estreitamento. Então tente ao contrário, uma amenidade, depois uma categoria.

    >[!Note]
    > Quando uma seleção é feita numa lista de facetas (como categoria) irá anular qualquer seleção anterior dentro da lista de categorias.

## <a name="takeaways"></a>Conclusões

Considere os seguintes takeaways deste projeto:

* É imperativo marcar cada propriedade como **IsFacetable,** se forem incluídas na navegação faceta.
* A navegação faceta proporciona a um utilizador uma forma fácil e intuitiva de reduzir uma pesquisa.
* A navegação faceta é melhor dividida em secções (categorias de hotel, comodidades de um hotel, intervalos de preços, gamas de classificação, etc.), cada secção com uma rubrica adequada.

## <a name="next-steps"></a>Passos seguintes

No próximo tutorial, olhamos para os resultados de encomendas. Até este ponto, os resultados são encomendados simplesmente na ordem de que estão localizados na base de dados.

> [!div class="nextstepaction"]
> [C#Tutorial: Encomende os resultados- Pesquisa Cognitiva Azure](tutorial-csharp-orders.md)
