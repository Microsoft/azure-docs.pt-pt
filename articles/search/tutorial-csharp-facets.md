---
title: C#tutorial sobre como utilizar facetas para ajudar a navegação - Azure Search
description: Este tutorial baseia-se no projeto "Paginação - Azure Search de resultados de pesquisa", para adicionar a navegação de faceta. Saiba que facetas podem ser utilizadas para limitar facilmente uma pesquisa.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 06/20/2019
ms.openlocfilehash: 62326ad3bc5f2d740ce744819df559bce8658eb7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443786"
---
# <a name="c-tutorial-use-facets-to-aid-navigation---azure-search"></a>C#Tutorial: Utilizar facetas para ajudar a navegação - Azure Search

Facetas são utilizadas para ajudar a navegação, fornecendo o utilizador com um conjunto de ligações para utilizar para se concentrar sua pesquisa. Facetas são atributos dos dados (por exemplo, a categoria ou uma funcionalidade específica, de um hotel nos nossos dados de exemplo).

Este tutorial baseia-se para o projeto de paginação criado no [ C# Tutorial: Resultados da pesquisa paginação - Azure Search](tutorial-csharp-paging.md) tutorial.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Definir as propriedades de modelo como _IsFacetable_
> * Adicionar a navegação de faceta para a sua aplicação

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

Tem a [ C# Tutorial: Resultados da pesquisa paginação - Azure Search](tutorial-csharp-paging.md) projeto em funcionamento. Este projeto pode ser a sua própria versão ou instalá-lo a partir do GitHub: [Criar a primeira aplicação](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="set-model-properties-as-isfacetable"></a>Definir as propriedades do modelo como IsFacetable

Para uma propriedade de modelo a ser localizado numa pesquisa de faceta efetuada, deve ser etiquetado com **IsFacetable**.

1. Examine os **Hotel** classe. **Categoria** e **etiquetas**, por exemplo, são etiquetados como **IsFacetable**, mas **HotelName** e **Descrição** não são. 

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

2. Vamos não alterar todas as marcas como parte deste tutorial, por isso, feche o ficheiro de hotel.cs inalterado.

    > [!Note]
    > Uma pesquisa de faceta irá gerar um erro, se um campo solicitado na pesquisa não está marcado adequadamente.


## <a name="add-facet-navigation-to-your-app"></a>Adicionar a navegação de faceta para a sua aplicação

Neste exemplo, vamos permitir que o utilizador selecionar uma categoria de hotel ou um amenity, listas de links mostrada à esquerda dos resultados. É iniciado o utilizador ao introduzir alguns pesquisa texto, em seguida, pode restringir os resultados da pesquisa ao selecionar uma categoria e pode restringir ainda mais os resultados ao selecionar um amenity ou podem selecionar o amenity primeiro (a ordem não é importante).

É necessário o controlador para passar as listas de facetas para a exibição. É necessário manter as seleções do utilizador conforme o andamento de pesquisa e, novamente, podemos utilizar o armazenamento temporário como o mecanismo para preservar os dados.

![Utilizar a navegação de faceta para reduzir uma pesquisa de "agrupamento"](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>Adicionar filtro cadeias de caracteres para o modelo de SearchData

1. Abra o ficheiro de SearchData.cs e adicione as propriedades de cadeia de caracteres para o **SearchData** classe, para manter as cadeias de caracteres de filtro de faceta.

    ```cs
        public string categoryFilter { get; set; }
        public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>Adicione o método de ação de faceta

O controlador principal tem uma nova ação **faceta**e atualizações para seus existente **índice** e **página** ações, bem como atualizações para o **RunQueryAsync**  método.

1. Abra o ficheiro de controlador home e adicione a **usando** instrução, para ativar a **lista&lt;cadeia&gt;**  construir.

    ```cs
    using System.Collections.Generic;
    ```

2. Substitua a **índice (modelo de SearchData)** método de ação.

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

3. Substitua a **página (modelo de SearchData)** método de ação.

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

4. Adicionar uma **faceta (modelo de SearchData)** método de ação, a ser ativado quando o usuário clica num link de faceta. O modelo irá conter um filtro de pesquisa de categoria ou um filtro de pesquisa amenity. Talvez adicioná-lo Depois do **página** ação.

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

Quando um usuário seleciona uma faceta determinados, por exemplo, clicarem na **recurso e Spa** categoria, em seguida, hotéis apenas especificadas como esta categoria deve ser devolvida nos resultados. Para reduzir uma pesquisa desta forma, temos de configurar uma _filtro_.

1. Substitua a **RunQueryAsync** método com o código a seguir. Principalmente, ele usa uma cadeia de caracteres de filtro de categoria e uma cadeia de caracteres de filtro de amenity e define a **filtro** parâmetro do **SearchParameters**.

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

    Adicionámos a **categoria** e **etiquetas** propriedades à lista de **selecionar** itens a devolver. Esta adição não é um requisito para a navegação de faceta funcionar, mas estas informações são utilizadas para verificar que podemos está a filtrar corretamente.

### <a name="add-lists-of-facet-links-to-the-view"></a>Adicionar listas de ligações de faceta para a vista

A vista irá exigir algumas mudanças significativas. 

1. Iniciar ao abrir o ficheiro de hotels.css (na pasta wwwroot/css) e adicione as seguintes classes.

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

2. Para a vista, estamos organizar a saída numa tabela, de forma organizada, alinhar a faceta listas à esquerda e os resultados à direita. Abra o ficheiro Index. cshtml. Substitua todo o conteúdo do HTML &lt;corpo&gt; etiquetas, com o código a seguir.

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

    Observe o uso do **ActionLink** chamar. Esta chamada comunica as cadeias de caracteres de filtro válido para o controlador, quando o usuário clica num link de faceta. 

### <a name="run-and-test-the-app"></a>Executar e testar a aplicação

A vantagem de navegação de faceta para o usuário é que eles podem restringir as pesquisas com um único clique, podemos Mostrar o da seguinte sequência.

1. Execute a aplicação, do aeroporto"tipo" como o texto de pesquisa. Certifique-se de que a lista de facetas aparece, de forma organizada, à esquerda. Essas facetas são tudo o que se aplicam a hotéis que tenham "aeroporto" nos seus dados de texto, com uma contagem da frequência com que eles ocorrem.

    ![Utilizar a navegação de faceta para reduzir uma pesquisa de "aeroporto"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport.png)

2. Clique nas **recurso e Spa** categoria. Certifique-se de que todos os resultados estão nesta categoria.

    ![Limitar a procura para "Recurso e Spa"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

3. Clique nas **café da manhã de território continental** amenity. Certifique-se de que todos os resultados estão ainda na categoria "Recurso e Spa", com o amenity selecionado.

    ![Limitar a procura para "território continental café da manhã"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

4. Experimente selecionar qualquer outra categoria, em seguida, um amenity e ver os resultados de restrição. Em seguida, tente o caminho inverso um amenity, em seguida, uma categoria.

    >[!Note]
    > Quando uma seleção é feita numa lista de faceta (por exemplo, a categoria) irá substituir a qualquer seleção anterior dentro da lista de categoria.

## <a name="takeaways"></a>Conclusões

Considere o seguintes das novidades deste projeto:

* É imperativo para cada propriedade como de marcar **IsFacetable**, se estão a ser incluído na navegação de faceta.
* Navegação de faceta fornece um utilizador com uma forma simples e intuitiva, de restrição de uma pesquisa.
* Navegação de faceta melhor está dividida em secções (categorias de hotel), características de um hotel, intervalos de preço, intervalos de classificação, etc., cada seção com um cabeçalho adequado.

## <a name="next-steps"></a>Passos Seguintes

O próximo tutorial, vamos ver resultados de ordenação. Até aqui, os resultados são ordenados simplesmente na ordem que estão localizados na base de dados.

> [!div class="nextstepaction"]
> [C#Tutorial: Ordem de resultados - Azure Search](tutorial-csharp-orders.md)
