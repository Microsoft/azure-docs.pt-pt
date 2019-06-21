---
title: C#tutorial sobre como utilizar facetas para uma eficiência de navegação e rede - Azure Search
description: Este tutorial baseia-se no projeto "Paginação - Azure Search de resultados de pesquisa", para adicionar as pesquisas de faceta. Saiba que facetas podem ser utilizadas na navegação e preenchimento automático.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 06/20/2019
ms.openlocfilehash: a81042869564533050fef42a983f2f8fb9bc7b23
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304672"
---
# <a name="c-tutorial-use-facets-for-navigation-and-network-efficiency---azure-search"></a>C#Tutorial: Utilizar facetas para uma eficiência de navegação e rede - Azure Search

Facetas tem dois usos diferentes no Azure Search. Facetas podem ser utilizadas para ajudar a navegação, fornecendo o utilizador com um conjunto de caixas de verificação para utilizar para se concentrar sua pesquisa. Além disso, eles podem ser usados para melhorar a eficiência de rede, quando usado no preenchimento automático. Pesquisas de faceta são eficientes porque eles são executados apenas uma vez para cada carregamento de página, em vez de uma vez para cada pressionamento de tecla. 

Facetas são atributos dos dados (por exemplo, a categoria de um hotel nos nossos dados de exemplo) e se manter relevantes para o tempo de vida de uma pesquisa.

Este tutorial baseia-se dois projetos, uma para a navegação de faceta e outra para preenchimento automático de faceta. Ambos os projetos criar para o projeto de paginação criado no [ C# Tutorial: Resultados da pesquisa paginação - Azure Search](tutorial-csharp-paging.md) tutorial.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Definir as propriedades de modelo como _IsFacetable_
> * Adicionar a navegação de faceta para a sua aplicação
> * Adicione preenchimento automático de faceta à sua aplicação
> * Decida quando usar o preenchimento automático de faceta

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

Tem a [ C# Tutorial: Resultados da pesquisa paginação - Azure Search](tutorial-csharp-paging.md) projeto em funcionamento. Isso pode ser a sua própria versão ou instalá-lo a partir do GitHub: [Criar a primeira aplicação](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="set-model-fields-as-isfacetable"></a>Campos do modelo de conjunto conforme IsFacetable

Por ordem para uma propriedade de modelo a ser localizado numa pesquisa de faceta (navegação ou autocompletar), tem de ser etiquetado com **IsFacetable**.

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

2. Vamos não alterar quaisquer etiquetas neste tutorial. Uma pesquisa de faceta irá gerar um erro, se um campo solicitado na pesquisa não está marcado adequadamente.


## <a name="add-facet-navigation-to-your-app"></a>Adicionar a navegação de faceta para a sua aplicação

Neste exemplo, vamos permitir que o utilizador selecionar uma ou mais categorias de hotel, numa lista mostrada à esquerda dos resultados. É necessário o controlador saber a lista de categorias, quando a aplicação é executada pela primeira vez e para passar esta lista para a exibição a apresentar quando o primeiro ecrã é processado. Cada página é renderizada, é necessário para se certificar de que mantivemos a lista de facetas e as seleções do utilizador atual, para ser transmitido à páginas subsequentes. Novamente, podemos usar um armazenamento temporário como o mecanismo para preservar os dados.

![Utilizar a navegação de faceta para reduzir uma pesquisa de "agrupamento"](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="modify-the-searchdata-model"></a>Modificar o modelo de SearchData

1. Abra o ficheiro de SearchData.cs e adicione neste adicionais **usando** instrução. É necessário ativar a **lista&lt;cadeia&gt;**  construir.

    ```cs
    using System.Collections.Generic;
    ```

2. No mesmo ficheiro, adicione as seguintes linhas para o **SearchData** classe. Não elimine qualquer uma das propriedades de classe existente, mas adicionar os seguintes métodos de construtor e as matrizes de propriedades.

    ```cs
        public SearchData()
        {
        }

        // Constructor to initialize the list of facets sent from the controller.
        public SearchData(List<string> facets)
        {
            facetText = new string[facets.Count];

            for (int i = 0; i < facets.Count; i++)
            {
                facetText[i] = facets[i];
            }
        }

        // Array to hold the text for each facet.
        public string[] facetText { get; set; }

        // Array to hold the check box setting.
        public bool[] facetOn { get; set; }
    ```


### <a name="search-for-facets-on-the-first-index-call"></a>Procurar por facetas na primeira chamada de índice

O controlador principal tem uma mudança significativa. A primeira chamada para **Index()** deixasse de devolver uma exibição com nenhum outro tipo de processamento. Queremos fornecer a vista com uma lista completa das facetas, e a primeira chamada é a certa para essa finalidade.

1. Abra o ficheiro de controlador home e adicione dois **usando** instruções.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

2. Agora substitua as linhas da atual **Index()** método com um método que realiza uma pesquisa de faceta para categorias de hotel. Como a pesquisa deve ser executada assincronicamente, tem de declarar o **índice** método como **async**.

    ```cs
        public async Task<ActionResult> Index()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchParameters sp = new SearchParameters()
            {
                // Search for up to 20 categories.
                // Field names specified here must be marked as "IsFacetable" in the model, or the search call will throw an exception.
                Facets = new List<string> { "Category,count:20" },
            };

            DocumentSearchResult<Hotel> searchResult = await _indexClient.Documents.SearchAsync<Hotel>("*", sp);

            // Convert the results to a list that can be displayed in the client.
            List<string> categories = searchResult.Facets["Category"].Select(x => x.Value.ToString()).ToList();

            // Initiate a model with a list of facets for the first view.
            SearchData model = new SearchData(categories);

            // Save the facet text for the next view.
            SaveFacets(model);

            // Render the view including the facets.
            return View(model);
        }
    ```

    Alguns pontos a serem observados aqui. Iremos converter os resultados da chamada de pesquisa a uma lista de cadeias de caracteres, em seguida, essas cadeias de caracteres de faceta são adicionadas a uma **SearchData** modelos para a comunicação para a vista. Também vamos salvar essas cadeias de caracteres para armazenamento temporário antes de finalmente processar a vista. Essa economia é feita para que esta lista está disponível para a próxima chamada a uma ação de controlador.

3. Vamos adicionar os dois métodos privados para salvar e restaurar facetas para o modelo de e para armazenamento temporário.

    ```cs
        // Save the facet text to temporary storage, optionally saving the state of the check boxes.
        private void SaveFacets(SearchData model, bool saveChecks = false)
        {
            for (int i = 0; i < model.facetText.Length; i++)
            {
                TempData["facet" + i.ToString()] = model.facetText[i];
                if (saveChecks)
                {
                    TempData["faceton" + i.ToString()] = model.facetOn[i];
                }
            }
            TempData["facetcount"] = model.facetText.Length;
        }

        // Recover the facet text to a model, optionally recoving the state of the check boxes.
        private void RecoverFacets(SearchData model, bool recoverChecks = false)
        {
            // Create arrays of the appropriate length.
            model.facetText = new string[(int)TempData["facetcount"]];
            if (recoverChecks)
            {
                model.facetOn = new bool[(int)TempData["facetcount"]];
            }

            for (int i = 0; i < (int)TempData["facetcount"]; i++)
            {
                model.facetText[i] = TempData["facet" + i.ToString()].ToString();
                if (recoverChecks)
                {
                    model.facetOn[i] = (bool)TempData["faceton" + i.ToString()];
                }
            }
        }
    ```

### <a name="save-and-restore-facet-text-on-all-calls"></a>Guardar e restaurar o texto de faceta em todas as chamadas

1. As outras duas ações do controlador de casa, **índice (SearchData modelo)** e **página (modelo de SearchData)** , ambos precisam recuperar as facetas antes da chamada de pesquisa e salvá-los novamente após a chamada de pesquisa. Alteração da **índice (modelo de SearchData)** para tornar essas duas chamadas.

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

                // Recover the facet text.
                RecoverFacets(model);

                // Make the search call for the first page.
                await RunQueryAsync(model, 0, 0);

                // Ensure temporary data is stored for the next call.
                TempData["page"] = 0;
                TempData["leftMostPage"] = 0;
                TempData["searchfor"] = model.searchText;

                // Facets
                SaveFacets(model, true);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

2. Agora fazer o mesmo o **página (modelo de SearchData)** método. Listámos apenas o código relevante abaixo. Adicionar a **RecoverFacets** e **SaveFacets** chama em todo o **RunQueryAsync** chamar.

    ```cs
                // Recover facet text and check marks.
                RecoverFacets(model, true);

                await RunQueryAsync(model, page, leftMostPage);

                // Save facets and check marks.
                SaveFacets(model, true);
    ```

### <a name="set-up-a-search-filter"></a>Configurar um filtro de pesquisa

Quando um utilizador seleciona determinadas facetas, por exemplo, digamos que clicarem na **orçamento** e **recurso e Spa** categorias, em seguida, apenas os hotéis especificadas como uma destas duas categorias deve ser devolvida o resultados. Para otimizar uma pesquisa desta forma, é necessário configurar um _filtro_.

1. Na **RunQueryAsync** método, adicione o código para fazer o loop por meio de configurações de faceta o modelo específico, para criar uma cadeia de filtro. E adicione o filtro para o **SearchParameters**, conforme mostrado no código a seguir.

    ```cs
            // Create a filter for selected facets.
            string selectedFacets = "";

            for (int f = 0; f < model.facetText.Length; f++)
            {
                if (model.facetOn[f])
                {
                    if (selectedFacets.Length > 0)
                    {
                        // If there is more than one selected facet, logically OR them together.
                        selectedFacets += " or ";
                    }
                    selectedFacets += "(Category eq \'" + model.facetText[f] + "\')";
                }
            }

            var parameters = new SearchParameters
            {
                // Facets: add the filter.
                Filter = selectedFacets,

                // Enter Hotel property names into this list so only these values will be returned.
                // If Select is empty, all values will be returned, which can be inefficient.
                Select = new[] { "HotelName", "Description", "Category" },
                SearchMode = SearchMode.All,

                // Skip past results that have already been returned.
                Skip = page * GlobalVariables.ResultsPerPage,

                // Take only the next page worth of results.
                Top = GlobalVariables.ResultsPerPage,

                // Include the total number of results.
                IncludeTotalResultCount = true,
            };
    ```

    Adicionámos a **categoria** propriedade à lista de **selecione** itens a devolver. Adicionar esta propriedade não é um requisito, mas, desta forma podemos verificar que podemos está a filtrar corretamente.

### <a name="define-a-few-additional-html-styles"></a>Definir alguns estilos adicionais de HTML

A vista irá exigir algumas mudanças significativas. 

1. Iniciar ao abrir o ficheiro de hotels.css (na pasta wwwroot/css) e adicione as seguintes classes.

    ```html
    .facetlist {
        list-style: none;
    }

    .facetchecks {
        width: 200px;
        background-color: lightgoldenrodyellow;
        display: normal;
        color: #666;
        margin: 10px;
    }
    ```

### <a name="add-a-list-of-facet-checkboxes-to-the-view"></a>Adicionar uma lista de caixas de seleção de faceta para a vista

Para a vista, estamos organizar a saída numa tabela, de forma organizada alinhar as facetas no lado esquerdo e os resultados à direita. Abra o ficheiro Index. cshtml.

1. Substitua todo o conteúdo do HTML &lt;corpo&gt; etiquetas, com o código a seguir.

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
                        <h5>Filter by Category:</h5>
                        <ul class="facetlist">
                            @for (var i = 0; i < Model.facetText.Length; i++)
                            {
                                <li> @Html.CheckBoxFor(m => m.facetOn[i], new { @id = "check" + i.ToString() }) @Model.facetText[i] </li>
                            }
                        </ul>
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
                                // Display the hotel name and description.
                                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                                @Html.TextArea("desc", Model.resultList.Results[i].Document.Description + "\nCategory:  " +  Model.resultList.Results[i].Document.Category, new { @class = "box2" })
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

    Observe o uso do **CheckBoxFor** chamada para preencher o **facetOn** matriz com as seleções do utilizador. Além disso, adicionámos a categoria de hotel ao final da descrição do hotel. Este texto é simplesmente confirmar que a nossa pesquisa está a funcionar corretamente. Não muito mais foi alterado de tutoriais anteriores, exceto pelo fato de ter organizamos a saída numa tabela.

### <a name="run-and-test-the-app"></a>Executar e testar a aplicação

1. Executar a aplicação e certifique-se de que a lista de facetas é claramente apresentado à esquerda.

2. Tente selecionar um, dois, três ou mais caixas de verificação e verifique se os resultados.

    ![Utilizar a navegação de faceta para reduzir uma pesquisa de "Wi-Fi"](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

3. Existe uma ligeira complicação com navegação de faceta. O que deve acontecer se um utilizador altera a seleção de faceta (selecionar ou anule a seleção as caixas de verificação), mas, em seguida, clica em uma das opções de paginação e não uma barra de pesquisa? Na verdade, alterar a seleção de deve iniciar uma nova pesquisa, como as páginas atuais já não estará corretas. Em alternativa, podem ser ignoradas as alterações de utilizador, e a próxima página de resultados fornecido, com base nas seleções de faceta original. Podemos ter escolhido à última solução neste exemplo, mas talvez considere como pode implementar a solução anterior. Talvez acionar uma nova pesquisa se a seleção mais recente de facetas escolhidas exatamente não coincide com a seleção no armazenamento temporário?

Isso conclui o nosso exemplo de navegação de faceta. Mas talvez precisa também considerar como esta aplicação pode ser estendida. A lista de faceta pode ser expandida para incluir outros campos capaz de faceta (Digamos **etiquetas**), para que um utilizador pode selecionar uma variedade de opções, como um conjunto, Wi-Fi, barra, estacionamento gratuito e assim por diante. 

A vantagem de navegação de faceta para o usuário é que eles não têm de manter a introduzir o mesmo texto, suas opções de faceta são preservadas para o tempo de vida da sessão atual com a aplicação. Podem selecionar categorias e potencialmente outros atributos, com um único clique, em seguida, pesquise no outro texto específico.

Agora vamos examinar uma utilização diferente facetas.

## <a name="add-facet-autocompletion-to-your-app"></a>Adicione preenchimento automático de faceta à sua aplicação

Preenchimento automático de faceta funciona fazendo uma pesquisa inicial quando a aplicação é executada pela primeira vez. Esta pesquisa recolhe uma lista de facetas para utilizar como sugestões quando o usuário está digitando.

![Digitação "re" revela três facetas](./media/tutorial-csharp-create-first-app/azure-search-facet-type-re.png)

Vamos utilizar a aplicação de paginação numerado que poderá concluir o tutorial segundo como base para este exemplo.

Para implementar o preenchimento automático de Faceta, não precisamos alterar qualquer um dos modelos (as classes de dados). Precisamos adicionar um script para a exibição e uma ação para o controlador.

### <a name="add-an-autocomplete-script-to-the-view"></a>Adicionar um script de conclusão automática para a vista

Para iniciar uma pesquisa de Faceta, precisamos de enviar uma consulta. O seguinte JavaScript adicionado ao ficheiro Index. cshtml fornece a lógica de consulta e a apresentação que é necessário.

1. Localize a **@Html.TextBoxFor(m = > m.searchText,...)** instrução e adicione um ID exclusivo, semelhante ao seguinte.

    ```cs
    <div class="searchBoxForm">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresearchfacets" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

2. Agora, adicione o seguinte JavaScript (após o fecho **&lt;/div&gt;** mostrado acima funciona bem).

    ```JavaScript
     <script>
            $(function () {
                $.getJSON("/Home/Facets", function (data) {

                    $("#azuresearchfacets").autocomplete({
                        source: data,
                        minLength: 2,
                        position: {
                            my: "left top",
                            at: "left-23 bottom+10"
                        }
                    });
                });
            });
        </script>
    ```

    Tenha em atenção que o script chama o **facetas** ação no controlador de casa, sem quaisquer outros parâmetros, quando for atingido um comprimento mínimo de dois caracteres digitados.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Adicionar referências aos scripts de jquery para a vista

A função de conclusão automática chamada no script acima não é algo que temos de criar como ele está disponível na biblioteca do jquery. 

1. Para acessar a biblioteca jquery, substitua a &lt;head&gt; secção do ficheiro de vista com o código a seguir.

    ```cs
    <head>
        <meta charset="utf-8">
        <title>Facets demo</title>
        <link href="https://code.jquery.com/ui/1.10.4/themes/ui-lightness/jquery-ui.css"
              rel="stylesheet">
        <script src="https://code.jquery.com/jquery-1.10.2.js"></script>
        <script src="https://code.jquery.com/ui/1.10.4/jquery-ui.js"></script>

        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

2. Também é necessário remover ou comentar, uma linha de referência a jquery no ficheiro Layout. cshtml (no **Views/Shared** pasta). Localize as seguintes linhas e comente a primeira linha do script, conforme mostrado. Ao remover esta linha, podemos evitar referências ambíguas a jquery.

    ```html
     <environment include="Development">
            <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
            <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
            <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

Agora podemos usar as funções do jquery de conclusão automática predefinidas.

### <a name="add-a-facet-action-to-the-controller"></a>Adicionar uma ação de faceta para o controlador

1. Abra o controlador principal e adicione as seguintes duas **usando** instruções ao cabeçalho do ficheiro.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

2. O JavaScript nos acionadores de vista a **facetas** ação no controlador, então, vamos adicionar essa ação para o controlador principal (Digamos, abaixo da **página** ação).

    ```cs
        public async Task<ActionResult> Facets()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchParameters sp = new SearchParameters()
            {
                // Search all Tags, but limit the total number to 100, and add up to 20 categories.
                // Field names specified here must be marked as "IsFacetable" in the model, or the search call will throw an exception.
                Facets = new List<string> { "Tags,count:100", "Category,count:20" },
            };

            DocumentSearchResult<Hotel> searchResult = await _indexClient.Documents.SearchAsync<Hotel>("*", sp);

            // Convert the results to two lists that can be displayed in the client.
            List<string> facets = searchResult.Facets["Tags"].Select(x => x.Value.ToString()).ToList();
            List<string> categories = searchResult.Facets["Category"].Select(x => x.Value.ToString()).ToList();

            // Combine and return the lists.
            facets.AddRange(categories);
            return new JsonResult(facets);
        }
    ```

    Tenha em atenção está a solicitar até 100 facetas do **etiquetas** campos e até 20 da **categoria** campos. O **contagem** entradas são opcionais, não se estiver definida nenhuma contagem a predefinição é 10.

    Temos duas listas, que, em seguida, são combinadas em um, uma vez que solicitamos dois campos devem ser pesquisadas (**etiquetas** e **categoria**). Se tinha solicitamos três campos deve ser pesquisada, teríamos de combinar três listas em um e assim por diante.

    > [!NOTE]
    > É possível definir uma ou mais dos seguintes parâmetros para cada campo numa pesquisa de faceta: **contagem**, **ordenar**, **intervalo**, e **valores**. Para obter mais informações, consulte [como implementar a navegação por facetas no Azure Search](https://docs.microsoft.com/azure/search/search-faceted-navigation).

### <a name="compile-and-run-your-project"></a>Compilar e executar o seu projeto

O programa de teste agora.

1. Experimente escrever "fr" na caixa de pesquisa, o que deve mostrar vários resultados.

    ![Escrever "fr" revela três facetas](./media/tutorial-csharp-create-first-app/azure-search-facet-type-fr.png)

2. Agora, adicione um "o" fazer "para" e observar que a variedade de opções é reduzida para um.

3. Escreva a outras combinações de duas letras e ver o que é apresentada. Tenha em atenção que, quando digita o servidor está *não* a ser chamado. As facetas são colocadas em cache localmente quando a aplicação é iniciada e, agora é feita uma chamada apenas para o servidor quando o usuário solicita uma pesquisa.

## <a name="decide-when-to-use-a-facet-autocompletion-search"></a>Decida quando usar uma pesquisa de preenchimento automático de faceta

A diferença clara entre as pesquisas de faceta e outras pesquisas como sugestões e preenchimento automático, é que a pesquisa de faceta _projetado_ para ser apenas levadas a cabo uma vez quando uma página for carregada. As pesquisas de preenchimento automático são _projetado_ a ser chamado após cada caractere é digitado. Desta forma potencialmente usando facetas guarda várias chamadas para o servidor. 

No entanto, quando deve preenchimento automático de faceta ser utilizado?

Preenchimento automático de faceta é melhor usada quando:
* A principal razão é que o desempenho das outras pesquisas que chamar o servidor cada pressionamento de tecla é um problema.
* As facetas devolvidas fornecem ao usuário uma lista de opções de razoável quando escreve na alguns caracteres.
* As facetas devolvidas fornecem uma forma rápida de aceder a mais ou o ideal é que todos, os dados disponíveis.
* As contagens máximas permitem que a maioria dos aspectos a serem incluídos. No nosso código, definimos um máximo de 100 facetas para **etiquetas** e 20 facetas para **categoria**. O conjunto de valores máximos deve trabalhar bem com o tamanho do conjunto de dados. Se estão a ser cortar muitas facetas potenciais, em seguida, talvez a pesquisa não é tão útil quanto deve ser.

> [!NOTE]
> Apesar de pesquisas de faceta são projetadas para ser chamado uma vez por carregamento da página, eles podem certamente ser chamados com muito mais freqüência, depende do JavaScript. Igualmente true é que as pesquisas de preenchimento automático/sugestão podem ser executadas com menos frequência do que uma vez por pressionamentos de tecla. Novamente tal é determinado pelo seu JavaScript, não a Azure Search. No entanto, a pesquisa de faceta foi concebida para ser chamado apenas uma vez por página como facetas são construídas pelo Azure Search a partir os documentos pesquisados com isso em mente. É recomendável considerar pesquisas de preenchimento automático de faceta como uma forma um pouco menos flexível, mais rede eficiente, mas de assistência ao usuário.

## <a name="takeaways"></a>Conclusões

Considere o seguintes das novidades deste projeto:

* É imperativo para cada campo como de marcar **IsFacetable**, se estão a ser incluído na navegação de faceta ou preenchimento automático.
* Navegação de faceta fornece um utilizador com uma forma simples e intuitiva, de restrição de uma pesquisa.
* Navegação de faceta melhor está dividida em secções (categorias de hotel), funcionalidades de um hotel, intervalos de preços, etc., cada seção com um cabeçalho adequado.
* Preenchimento automático de faceta é uma forma eficaz de obter uma experiência de utilizador útil sem as chamadas de servidor repetidas de outras pesquisas de preenchimento automático.
* Preenchimento automático de faceta é um _alternativo_ para preenchimento automático/sugestões, não é uma adição.

## <a name="next-steps"></a>Passos Seguintes

Concluiu esta série de C# tutoriais - deve obtido valioso conhecimento das APIs de pesquisa do Azure.

Para referência mais e tutoriais, considere a navegação [Microsoft Learn](https://docs.microsoft.com/learn/browse/?products=azure), ou os outros tutoriais a [documentação do Azure Search](https://docs.microsoft.com/azure/search/).
