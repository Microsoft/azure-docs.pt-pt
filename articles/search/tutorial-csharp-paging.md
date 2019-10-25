---
title: C#tutorial sobre a paginação de resultados da pesquisa
titleSuffix: Azure Cognitive Search
description: Este tutorial se baseia no projeto "criar seu primeiro aplicativo-Azure Pesquisa Cognitiva", com a opção de dois tipos de paginação. O primeiro usa um intervalo de botões de número de página, bem como os botões primeiro, próximo, anterior e última página. O segundo sistema de paginação usa a rolagem infinita, disparada movendo uma barra de rolagem vertical para seu limite inferior.
manager: nitinme
author: PeterTurcan
ms.author: v-pettur
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 935e6d43cf77d94b485d55eb4bc5eb517bf802a0
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794006"
---
# <a name="c-tutorial-search-results-pagination---azure-cognitive-search"></a>C#Tutorial: paginação dos resultados da pesquisa-Azure Pesquisa Cognitiva

Saiba como implementar dois sistemas de paginação diferentes, o primeiro com base em números de página e o segundo na rolagem infinita. Ambos os sistemas de paginação são amplamente usados e a seleção da correta depende da experiência do usuário que você gostaria com os resultados. Este tutorial cria os sistemas de paginação no projeto criado no [ C# tutorial: criar seu primeiro aplicativo-tutorial de pesquisa cognitiva do Azure](tutorial-csharp-create-first-app.md) .

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Estenda seu aplicativo com paginação numerada
> * Estenda seu aplicativo com rolagem infinita

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

Faça o [ C# tutorial: criar seu primeiro aplicativo-Azure pesquisa cognitiva](tutorial-csharp-create-first-app.md) projeto em funcionamento. Este projeto pode ser sua própria versão ou instalá-lo do GitHub: [criar primeiro aplicativo](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="extend-your-app-with-numbered-paging"></a>Estenda seu aplicativo com paginação numerada

A paginação numerada é o sistema de paginação de sua escolha dos principais mecanismos de pesquisa da Internet e a maioria dos outros sites de pesquisa. A paginação numerada normalmente inclui uma opção "Next" e "Previous", além de um intervalo de números de página reais. Também uma "primeira página" e a opção "última página" também podem estar disponíveis. Essas opções certamente dão a um controle de usuário sobre a navegação por meio de resultados baseados em página.

Adicionaremos um sistema que inclui as opções primeiro, anterior, avançar e último, juntamente com os números de página que não iniciam de 1, mas, em vez disso, envolvem a página atual em que o usuário está (por exemplo, se o usuário estiver olhando para a página 10, talvez números de página 8 , 9, 10, 11 e 12 são exibidos).

O sistema será flexível o suficiente para permitir que o número de números de página visíveis seja definido em uma variável global.

O sistema tratará os botões de número de página mais à esquerda e à direita como especiais, o que significa que eles irão disparar a alteração do intervalo de números de página exibidos. Por exemplo, se os números de página 8, 9, 10, 11 e 12 forem exibidos e o usuário clicar em 8, o intervalo de números de página exibido será alterado para 6, 7, 8, 9 e 10. E haverá uma mudança semelhante à direita se elas tiverem selecionado 12.

### <a name="add-paging-fields-to-the-model"></a>Adicionar campos de paginação ao modelo

Tenha a solução de página de pesquisa básica aberta.

1. Abra o arquivo de modelo SearchData.cs.

2. Primeiro, adicione algumas variáveis globais. No MVC, as variáveis globais são declaradas em sua própria classe estática. **ResultsPerPage** define o número de resultados por página. **MaxPageRange** determina o número de números de página visíveis na exibição. **PageRangeDelta** determina quantas páginas esquerda ou direita o intervalo de páginas deve ser deslocado, quando o número de página mais à esquerda ou à direita é selecionado. Normalmente, esse último número é cerca de metade de **MaxPageRange**. Adicione o código a seguir ao namespace.

    ```cs
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
        public static int MaxPageRange
        {
            get
            {
                return 5;
            }
        }

        public static int PageRangeDelta
        {
            get
            {
                return 2;
            }
        }
    }
    ```

    >[!Tip]
    >Se você estiver executando esse projeto em um dispositivo com uma tela menor, como um laptop, considere alterar **ResultsPerPage** para 2.

3. Adicione Propriedades de paginação à classe **SearchData** , digamos, após a propriedade **ProcurarTexto** .

    ```cs
        // The current page being displayed.
        public int currentPage { get; set; }

        // The total number of pages of results.
        public int pageCount { get; set; }

        // The left-most page number to display.
        public int leftMostPage { get; set; }

        // The number of page numbers to display - which can be less than MaxPageRange towards the end of the results.
        public int pageRange { get; set; }

        // Used when page numbers, or next or prev buttons, have been selected.
        public string paging { get; set; }
    ```

### <a name="add-a-table-of-paging-options-to-the-view"></a>Adicionar uma tabela de opções de paginação à exibição

1. Abra o arquivo index. cshtml e adicione o código a seguir logo antes de fechar &lt;marca de&gt; de/Body. Esse novo código apresenta uma tabela de opções de paginação: primeiro, anterior, 1, 2, 3, 4, 5, avançar, último.

    ```cs
    @if (Model != null && Model.pageCount > 1)
    {
    // If there is more than one page of results, show the paging buttons.
    <table>
        <tr>
            <td>
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

            <td>
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
                <td>
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

            <td>
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

            <td>
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
    ```

    Usamos uma tabela HTML para alinhar as coisas de maneiras organizadas. No entanto, toda a ação é proveniente das instruções @Html.ActionLink, cada uma chamando o controlador com um **novo** modelo criado com entradas diferentes para a propriedade de **paginação** que adicionamos anteriormente.

    A primeira e a última opção de página não enviam cadeias de caracteres como "First" e "Last", mas sim enviar os números de página corretos.

2. Adicione algumas classes de paginação à lista de estilos HTML no arquivo Hotéis. css. A classe **pageSelected** está lá para identificar a página que o usuário está exibindo no momento (virando o número em negrito) na lista de números de página.

    ```html
        .pageButton {
            border: none;
            color: darkblue;
            font-weight: normal;
            width: 50px;
        }

        .pageSelected {
            border: none;
            color: black;
            font-weight: bold;
            width: 50px;
        }

        .pageButtonDisabled {
            border: none;
            color: lightgray;
            font-weight: bold;
            width: 50px;
        }
    ```

### <a name="add-a-page-action-to-the-controller"></a>Adicionar uma ação de página ao controlador

1. Abra o arquivo HomeController.cs e adicione a ação da **página** . Essa ação responde a qualquer uma das opções de página selecionadas.

    ```cs
        public async Task<ActionResult> Page(SearchData model)
        {
            try
            {
                int page;

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

                // Recover the search text and search for the data for the new page.
                model.searchText = TempData["searchfor"].ToString();

                await RunQueryAsync(model, page, leftMostPage);

                // Ensure Temp data is stored for next call, as TempData only stored for one call.
                TempData["page"] = (object)page;
                TempData["searchfor"] = model.searchText;
                TempData["leftMostPage"] = model.leftMostPage;
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "2" });
            }
            return View("Index", model);
        }
    ```

    O método **RunQueryAsync** agora mostrará um erro de sintaxe, devido ao terceiro parâmetro, que entraremos em um bit.

    > [!Note]
    > As chamadas **TempData** armazenam um valor (um **objeto**) no armazenamento temporário, embora esse armazenamento persista _apenas_ em uma chamada. Se armazenarmos algo em dados temporários, ele estará disponível para a próxima chamada a uma ação de controlador, mas, na maioria das chamadas, será indefinida depois disso! Devido a esse breve período de vida, armazenamos as propriedades de texto de pesquisa e de paginação de volta no armazenamento temporário cada um e cada chamada à **página**.

2. A ação de **índice (modelo)** precisa ser atualizada para armazenar as variáveis temporárias e adicionar o parâmetro de página mais à esquerda à chamada **RunQueryAsync** .

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
                await RunQueryAsync(model, 0, 0);

                // Ensure temporary data is stored for the next call.
                TempData["page"] = 0;
                TempData["leftMostPage"] = 0;
                TempData["searchfor"] = model.searchText;
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

3. O método **RunQueryAsync** precisa ser atualizado significativamente. Usamos os campos **Skip**, **Top**e **IncludeTotalResultCount** da classe **SearchParameters** para solicitar apenas uma página de resultados, começando pela configuração **Skip** . Também precisamos calcular as variáveis de paginação para nossa exibição. Substitua o método inteiro pelo código a seguir.

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage)
        {
            InitSearch();

            var parameters = new SearchParameters
            {
                   // Enter Hotel property names into this list so only these values will be returned.
                   // If Select is empty, all values will be returned, which can be inefficient.
                   Select = new[] { "HotelName", "Description" },
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
            model.pageCount = ( (int)model.resultList.Count + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

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

            return View("Index", model);
        }
    ```

4. Por fim, precisamos fazer uma pequena alteração na exibição. A variável **resultList. Results. Count** agora conterá o número de resultados retornados em uma página (3 em nosso exemplo), não o número total. Como definimos o **IncludeTotalResultCount** como true, a variável **resultList. Count** agora contém o número total de resultados. Portanto, localize onde o número de resultados é exibido na exibição e altere-o para o código a seguir.

    ```cs
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>
    ```

    > [!Note]
    > Há um impacto no desempenho, embora geralmente não seja muito de um, definindo **IncludeTotalResultCount** como true, pois esse total precisa ser calculado pelo Azure pesquisa cognitiva. Com conjuntos de dados complexos, há um aviso de que o valor retornado é uma _aproximação_. Para nossos dados de Hotel, será preciso.

### <a name="compile-and-run-the-app"></a>Compilar e executar o aplicativo

Agora, selecione **Iniciar sem depuração** (ou pressione a tecla F5).

1. Pesquise um texto que fornecerá muitos resultados (como "WiFi"). Você pode obter uma página organizada dos resultados?

    ![Paginação numerada por meio de resultados de "pool"](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

2. Tente clicar na mais à direita e, mais tarde, os números de página mais à esquerda. Os números de página se ajustam adequadamente para centralizar a página em que você está?

3. As opções "primeiro" e "última" são úteis? Algumas pesquisas populares da Web usam essas opções, e outras não.

4. Vá para a última página de resultados. A última página é a única página que pode conter menos de **ResultsPerPage** resultados.

    ![Examinando a última página de "WiFi"](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

5. Digite "cidade" e clique em Pesquisar. Nenhuma opção de paginação será exibida se houver menos de uma página de resultados.

    ![Pesquisando por "cidade"](./media/tutorial-csharp-create-first-app/azure-search-town.png)

Agora, salve este projeto e vamos tentar uma alternativa para essa forma de paginação.

## <a name="extend-your-app-with-infinite-scrolling"></a>Estenda seu aplicativo com rolagem infinita

A rolagem infinita é disparada quando um usuário rola uma barra de rolagem vertical para o último dos resultados exibidos. Nesse evento, uma chamada para o servidor é feita para a próxima página de resultados. Se não houver mais resultados, nada será retornado e a barra de rolagem vertical não mudará. Se houver mais resultados, eles serão anexados à página atual e a barra de rolagem será alterada para mostrar que mais resultados estão disponíveis.

O ponto importante aqui é que a página que está sendo exibida não é substituída, mas acrescentada ao com os novos resultados. Um usuário sempre pode rolar para o backup até os primeiros resultados da pesquisa.

Para implementar a rolagem infinita, vamos começar com o projeto antes que qualquer um dos elementos de rolagem de número de página fosse adicionado. Portanto, se necessário, faça outra cópia da página de pesquisa básica do GitHub: [criar primeiro aplicativo](https://github.com/Azure-Samples/azure-search-dotnet-samples).

### <a name="add-paging-fields-to-the-model"></a>Adicionar campos de paginação ao modelo

1. Primeiro, adicione uma propriedade de **paginação** à classe **SearchData** (no arquivo de modelo SearchData.cs).

    ```cs
        // Record if the next page is requested.
        public string paging { get; set; }
    ```

    Essa variável é uma cadeia de caracteres que mantém "Avançar" se a próxima página de resultados deve ser enviada ou ser nula para a primeira página de uma pesquisa.

2. No mesmo arquivo e no namespace, adicione uma classe de variável global com uma propriedade. No MVC, as variáveis globais são declaradas em sua própria classe estática. **ResultsPerPage** define o número de resultados por página. 

    ```cs
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
    }
    ```

### <a name="add-a-vertical-scroll-bar-to-the-view"></a>Adicionar uma barra de rolagem vertical à exibição

1. Localize a seção do arquivo index. cshtml que exibe os resultados (ele começa com o **@if (Model! = NULL)** ).

2. Substitua a seção pelo código abaixo. A nova seção **&lt;div&gt;** está em volta da área que deve ser rolável e adiciona um atributo **overflow-y** e uma chamada a uma função **onrolation** chamada "rolada ()", desta forma.

    ```cs
        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>

            <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    // Display the hotel name and description.
                    @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
                }
            </div>
        }
    ```

3. Diretamente abaixo do loop, após a marca &lt;/div&gt;, adicione a função **rolada** .

    ```javascript
        <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 2) {
                                div.innerHTML += '\n<textarea class="box1">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2">' + data[i + 1] + '</textarea>';
                            }
                        });
                    }
                }
        </script>
    ```

    A instrução **If** no script acima testa para ver se o usuário foi rolado para a parte inferior da barra de rolagem vertical. Se houver, será feita uma chamada para o controlador **doméstico** para uma ação chamada **Next**. Nenhuma outra informação é necessária para o controlador; ela retornará a próxima página de dados. Esses dados são formatados usando estilos HTML idênticos à página original. Se nenhum resultado for retornado, nada será acrescentado e as coisas permanecerão como estão.

### <a name="handle-the-next-action"></a>Manipular a próxima ação

Há apenas três ações que precisam ser enviadas ao controlador: a primeira execução do aplicativo, que chama **Index ()** , a primeira pesquisa pelo usuário, que chama **Index (Model)** e, em seguida, as chamadas subsequentes para obter mais resultados por meio do **próximo (Model)** .

1. Abra o arquivo do controlador Home e exclua o método **RunQueryAsync** do tutorial original.

2. Substitua a ação de **índice (modelo)** pelo código a seguir. Agora ele manipula o campo de **paginação** quando é nulo, ou definido como "Next", e manipula a chamada para o Azure pesquisa cognitiva.

    ```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                InitSearch();

                int page;

                if (model.paging != null && model.paging == "next")
                {
                    // Increment the page.
                    page = (int)TempData["page"] + 1;

                    // Recover the search text.
                    model.searchText = TempData["searchfor"].ToString();
                }
                else
                {
                    // First call. Check for valid text input.
                    if (model.searchText == null)
                    {
                        model.searchText = "";
                    }
                    page = 0;
                }

                // Setup the search parameters.
                var parameters = new SearchParameters
                {
                    // Enter Hotel property names into this list so only these values will be returned.
                    // If Select is empty, all values will be returned, which can be inefficient.
                    Select = new[] { "HotelName", "Description" },
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

                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;
            }
            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View("Index", model);
        }
    ```

    Semelhante ao método de paginação numerada, usamos as configurações de pesquisa **Skip** e **Top** para solicitar apenas os dados que precisamos que sejam retornados.

3. Adicione a **próxima** ação ao controlador Home. Observe como ele retorna uma lista, cada hotel adicionando dois elementos à lista: um nome de Hotel e uma descrição de Hotel. Esse formato é definido para corresponder ao uso da função **rolada** dos dados retornados na exibição.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel name, then description, to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

4. Se você estiver recebendo um erro de sintaxe na **lista&lt;cadeia de caracteres&gt;** , adicione o seguinte **usando** a diretiva ao cabeçalho do arquivo do controlador.

    ```cs
    using System.Collections.Generic;
    ```

### <a name="compile-and-run-your-project"></a>Compilar e executar seu projeto

Agora, selecione **Iniciar sem depuração** (ou pressione a tecla F5).

1. Insira um termo que fornecerá muitos resultados (como "pool") e, em seguida, teste a barra de rolagem vertical. Ele dispara uma nova página de resultados?

    ![Rolagem infinita por meio de resultados de "pool"](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

    > [!Tip]
    > Para garantir que uma barra de rolagem apareça na primeira página, a primeira página de resultados deve exceder ligeiramente a altura da área em que estão sendo exibidas. Em nosso exemplo **. box1** tem uma altura de 30 pixels, **. box2** tem uma altura de 100 pixels _e_ uma margem inferior de 24 pixels. Portanto, cada entrada usa 154 pixels. Três entradas terão 3 x 154 = 462 pixels. Para garantir que uma barra de rolagem vertical seja exibida, uma altura para a área de exibição deve ser definida com menos de 462 pixels, até 461 Works. Esse problema ocorre apenas na primeira página, depois que uma barra de rolagem tiver certeza de que ela aparecerá. A linha a ser atualizada é: **&lt;div id = "myDiv" Style = "largura: 800px; altura: 450px; overflow-y: rolagem;" onrolal = "rolado ()"&gt;** .

2. Role para baixo até a parte inferior dos resultados. Observe como todas as informações agora estão na página uma exibição. Você pode rolar todo o caminho de volta para a parte superior sem disparar nenhuma chamada de servidor.

Sistemas de rolagem infinitas mais sofisticados podem usar a roda do mouse ou outro mecanismo semelhante para disparar o carregamento de uma nova página de resultados. Não vamos fazer mais nenhuma rolagem infinita nesses tutoriais, mas ele tem um certo botão para ele, pois evita cliques adicionais do mouse, e talvez você queira investigar outras opções ainda mais!

## <a name="takeaways"></a>Conclusões

Considere as seguintes alternativas deste projeto:

* A paginação numerada é boa para pesquisas em que a ordem dos resultados é um pouco arbitrária, o que significa que pode haver algo de interesse para seus usuários nas páginas posteriores.
* A rolagem infinita é boa quando a ordem dos resultados é particularmente importante. Por exemplo, se os resultados forem ordenados na distância do centro de uma cidade de destino.
* A paginação numerada permite uma melhor navegação. Por exemplo, um usuário pode se lembrar de que um resultado interessante estava na página 6, embora não exista nenhuma referência fácil na rolagem infinita.
* A rolagem infinita tem um apelo fácil, rolando para cima e para baixo sem números de página minuciosos para clicar.
* Um recurso importante de rolagem infinita é que os resultados são anexados a uma página existente, não substituindo essa página, o que é eficiente.
* O armazenamento temporário persiste para apenas uma chamada e precisa ser redefinido para sobreviver a chamadas adicionais.


## <a name="next-steps"></a>Passos seguintes

A paginação é fundamental para pesquisas na Internet. Com a paginação bem coberta, a próxima etapa é melhorar ainda mais a experiência do usuário, adicionando pesquisas de tipo antecipado.

> [!div class="nextstepaction"]
> [C#Tutorial: Adicionar AutoCompletar e sugestões-Azure Pesquisa Cognitiva](tutorial-csharp-type-ahead-and-suggestions.md)
