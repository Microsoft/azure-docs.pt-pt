---
title: C# tutorial sobre paginação de resultados de pesquisa
titleSuffix: Azure Cognitive Search
description: Adicione botões de paginação e navegação aos resultados da pesquisa, baseando-se num projeto de hotéis existentes para adicionar primeiro, próximo, último, último e numerados botões. Um segundo sistema de paging usa um deslocamento infinito, desencadeado movendo uma barra de deslocamento vertical para o seu limite inferior.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 06/20/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: edae1edc076c99c025ff70c9b2493bc15e44102b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91280748"
---
# <a name="tutorial-add-paging-to-search-results-using-the-net-sdk"></a>Tutorial: Adicione paging aos resultados da pesquisa usando o .NET SDK

Aprenda a implementar dois sistemas de paging diferentes, o primeiro baseado em números de página e o segundo em scrolling infinito. Ambos os sistemas de paging são amplamente utilizados, e selecionar o certo depende da experiência do utilizador que você gostaria com os resultados. Este tutorial constrói os sistemas de paging no projeto criado no [Tutorial C#: Crie a sua primeira app - Tutorial de Pesquisa Cognitiva Azure.](tutorial-csharp-create-first-app.md)

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Estenda a sua app com paging numerado
> * Estenda a sua app com scrolling infinito

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

Tenha o [Tutorial C#: Crie a sua primeira app - Projeto Azure Cognitive Search](tutorial-csharp-create-first-app.md) em funcionamento. Este projeto pode ser a sua própria versão ou instalá-lo a partir do GitHub: [Criar a primeira aplicação.](https://github.com/Azure-Samples/azure-search-dotnet-samples)

## <a name="extend-your-app-with-numbered-paging"></a>Estenda a sua app com paging numerado

A paging numerada é o sistema de paging de eleição dos principais motores de pesquisa de internet e a maioria dos outros sites de pesquisa. A paging numerada normalmente inclui uma opção "seguinte" e "anterior", além de uma gama de números reais de página. Também pode estar disponível uma opção de "primeira página" e "última página". Estas opções certamente dão a um utilizador o controlo sobre a navegação através de resultados baseados em página.

Vamos adicionar um sistema que inclui primeira, anterior, próxima e últimas opções, juntamente com números de página que não começam a partir de 1, mas em vez disso rodeiam a página atual em que o utilizador está (por exemplo, se o utilizador estiver a olhar para a página 10, talvez os números de página 8, 9, 10, 11 e 12 são apresentados).

O sistema será suficientemente flexível para permitir que o número de números de página visíveis seja definido numa variável global.

O sistema tratará os botões de número de página mais à esquerda e à direita como especiais, o que significa que irão desencadear alterações na gama de números de página apresentados. Por exemplo, se os números de página 8, 9, 10, 11 e 12 forem apresentados e o utilizador clicar em 8, então o intervalo de números de página apresentados muda para 6, 7, 8, 9 e 10. E há uma mudança semelhante à direita se escolherem 12.

### <a name="add-paging-fields-to-the-model"></a>Adicione campos de paging ao modelo

Tenha a solução básica de página de pesquisa aberta.

1. Abra o ficheiro SearchData.cs modelo.

2. Primeiro adicione algumas variáveis globais. Em MVC, as variáveis globais são declaradas na sua própria classe estática. **ResultsPerPage** define o número de resultados por página. **MaxPageRange** determina o número de números de página visíveis na vista. **O PageRangeDelta** determina quantas páginas à esquerda ou à direita o intervalo de página deve ser deslocado, quando o número de página mais à esquerda ou à direita é selecionado. Normalmente, este último número é de cerca de metade do **MaxPageRange.** Adicione o seguinte código no espaço de nomes.

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
    >Se estiver a executar este projeto num dispositivo com um ecrã mais pequeno, como um portátil, considere alterar **o ResultsPerPage** para 2.

3. Adicione propriedades de paging à classe **SearchData,** por exemplo, após a **propriedade searchText.**

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

### <a name="add-a-table-of-paging-options-to-the-view"></a>Adicione uma tabela de opções de paging à vista

1. Abra o ficheiro index.cshtml e adicione o seguinte código imediatamente antes do fecho &lt; /etiqueta do &gt; corpo. Este novo código apresenta uma tabela de opções de paging: primeiro, anterior, 1, 2, 3, 4, 5, seguinte, último.

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

    Usamos uma tabela HTML para alinhar as coisas cuidadosamente. No entanto, toda a ação provém das @Html.ActionLink declarações, cada um chamando o controlador com um **novo** modelo criado com diferentes entradas para a propriedade **de paging** que adicionámos anteriormente.

    As opções de primeira e última página não enviam cadeias como "primeiro" e "último", mas enviam os números de página corretos.

2. Adicione algumas aulas de paging à lista de estilos HTML no arquivo hotels.css. A **páginaSelected** class is there para identificar a página que o utilizador está atualmente a visualizar (rodando o número em negrito) na lista de números de página.

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

### <a name="add-a-page-action-to-the-controller"></a>Adicione uma ação de página ao controlador

1. Abra o ficheiro HomeController.cs e adicione a ação **page.** Esta ação responde a qualquer uma das opções de página selecionadas.

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

    O método **RunQueryAsync** irá agora mostrar um erro de sintaxe, devido ao terceiro parâmetro, ao qual chegaremos daqui a pouco.

    > [!Note]
    > As chamadas **TempData** armazenam um valor (um **objeto)** em armazenamento temporário, embora este armazenamento persista por _apenas_ uma chamada. Se armazenarmos algo em dados temporários, estará disponível para a próxima chamada para uma ação de controlador, mas será definitivamente ido embora pela chamada depois disso! Devido a este curto período de vida, armazenamos o texto de pesquisa e as propriedades de paging de volta em armazenamento temporário cada uma das chamadas para **a Página.**

2. A ação **Index(modelo)** precisa de ser atualizada para armazenar as variáveis temporárias e para adicionar o parâmetro de página mais à esquerda à chamada **RunQueryAsync.**

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

3. O método **RunQueryAsync** precisa de ser atualizado de forma significativa. Utilizamos os campos **Skip**, **Top**e **IncludeTotalResultCount** da classe **SearchParameters** para solicitar apenas uma página de resultados, a partir da definição **Skip.** Também precisamos calcular as variáveis de paging para a nossa visão. Substitua todo o método pelo seguinte código.

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

4. Finalmente, temos de fazer uma pequena alteração ao ponto de vista. Os **resultados variáveisList.Results.Count** irá agora conter o número de resultados devolvidos numa página (3 no nosso exemplo), e não o número total. Como definimos o **IncludeTotalResultCount** verdadeiro, o **resultado variávelList.Count** agora contém o número total de resultados. Por isso, localize onde o número de resultados é apresentado na vista e altere-o para o seguinte código.

    ```cs
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>
    ```

    > [!Note]
    > Há um sucesso de desempenho, embora geralmente não muito de um, ao definir **IncludeTotalResultCount** para verdade, uma vez que este total precisa ser calculado pela Azure Cognitive Search. Com conjuntos de dados complexos, há um aviso de que o valor devolvido é uma _aproximação._ Para os dados do nosso hotel, será preciso.

### <a name="compile-and-run-the-app"></a>Compilar e executar a app

Agora **selecione Iniciar Sem Depurar** (ou prima a tecla F5).

1. Procure em alguns textos que dêem muitos resultados (como "wifi"). Pode páginar cuidadosamente os resultados?

    ![Paging numerado através dos resultados da "piscina"](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

2. Tente clicar nos números de página mais à direita e, mais tarde, à esquerda. Os números de página ajustam-se adequadamente para centrar a página em que se encontram?

3. As opções "primeira" e "última" são úteis? Algumas pesquisas populares na web usam estas opções, e outras não.

4. Vá à última página dos resultados. A última página é a única página que pode conter menos do que os resultados do **ResultsPerPage.**

    ![Examinando a última página de "wifi"](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

5. Digite em "cidade", e clique em pesquisa. Não são apresentadas opções de paging se houver menos de uma página de resultados.

    ![À procura de "cidade"](./media/tutorial-csharp-create-first-app/azure-search-town.png)

Agora, poupe este projeto e vamos tentar uma alternativa a esta forma de paging.

## <a name="extend-your-app-with-infinite-scrolling"></a>Estenda a sua app com scrolling infinito

O deslocamento infinito é acionado quando um utilizador desloca uma barra de deslocamento vertical até ao último dos resultados apresentados. Neste evento, é feita uma chamada para o servidor para a próxima página de resultados. Se não houver mais resultados, nada é devolvido e a barra de deslocação vertical não muda. Se houver mais resultados, são anexados à página atual, e a barra de deslocamento muda para mostrar que há mais resultados disponíveis.

O ponto importante aqui é que a página que está a ser exibida não é substituída, mas anexada aos novos resultados. Um utilizador pode sempre voltar a rolar até aos primeiros resultados da pesquisa.

Para implementar o scrolling infinito, vamos começar com o projeto antes que qualquer um dos elementos de deslocamento do número de página foram adicionados. Portanto, se precisar, faça outra cópia da página de pesquisa básica do GitHub: [Crie a primeira aplicação](https://github.com/Azure-Samples/azure-search-dotnet-samples).

### <a name="add-paging-fields-to-the-model"></a>Adicione campos de paging ao modelo

1. Em primeiro lugar, adicione uma propriedade **de paging** à classe **SearchData** (no ficheiro modelo SearchData.cs).

    ```cs
        // Record if the next page is requested.
        public string paging { get; set; }
    ```

    Esta variável é uma cadeia, que contém "seguinte" se a próxima página de resultados deve ser enviada, ou ser nula para a primeira página de uma pesquisa.

2. No mesmo ficheiro, e dentro do espaço de nome, adicione uma classe variável global com uma propriedade. Em MVC, as variáveis globais são declaradas na sua própria classe estática. **ResultsPerPage** define o número de resultados por página. 

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

### <a name="add-a-vertical-scroll-bar-to-the-view"></a>Adicione uma barra de deslocação vertical à vista

1. Localizar a secção do ficheiro index.cshtml que apresenta os resultados (começa com o ** @if (Modelo!= nulo)**).

2. Substitua a secção pelo código abaixo. A nova secção ** &lt; de div &gt; ** é em torno da área que deve ser percorrível, e adiciona tanto um atributo **transbordante como** uma chamada para uma função **onscroll** chamada "scrolled()", assim.

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

3. Diretamente por baixo do laço, depois da &lt; etiqueta &gt; /div, adicione a função de **deslocamento.**

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

    A **if** declaração no script acima testa para ver se o utilizador se desloca na parte inferior da barra de deslocação vertical. Se o fizerem, é feita uma chamada para o controlador **Home** para uma ação chamada **Next**. Nenhuma outra informação é necessária pelo controlador, irá devolver a próxima página de dados. Estes dados são então formatados utilizando estilos HTML idênticos como a página original. Se não forem devolvidos resultados, nada é anexado e as coisas ficam como estão.

### <a name="handle-the-next-action"></a>Lidar com a próxima ação

Há apenas três ações que precisam de ser enviadas para o controlador: a primeira execução da app, que chama **Index()**, a primeira pesquisa pelo utilizador, que chama **Índice(modelo)**, e depois as chamadas subsequentes para mais resultados através do **seguinte(modelo)**.

1. Abra o ficheiro do controlador doméstico e elimine o método **RunQueryAsync** do tutorial original.

2. Substitua a ação **(modelo)** do Índice pelo seguinte código. Agora lida com o campo **de paging** quando é nulo, ou definido para "seguinte", e lida com a chamada para Azure Cognitive Search.

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

    Semelhante ao método de paging numerado, usamos as definições de pesquisa **Skip** e **Top** para solicitar apenas os dados de que precisamos são devolvidos.

3. Adicione a **próxima** ação ao controlador doméstico. Note como devolve uma lista, cada hotel adicionando dois elementos à lista: um nome de hotel e uma descrição do hotel. Este formato está definido para corresponder à utilização dos dados devolvidos da função **desloque** na vista.

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

4. Se estiver a obter um erro de sintaxe na ** &lt; &gt; cadeia List,** adicione a seguinte diretiva de **utilização** à cabeça do ficheiro do controlador.

    ```cs
    using System.Collections.Generic;
    ```

### <a name="compile-and-run-your-project"></a>Compilar e executar o seu projeto

Agora **selecione Iniciar Sem Depurar** (ou prima a tecla F5).

1. Insira um termo que dará muitos resultados (como "pool") e, em seguida, teste a barra de deslocação vertical. Desencadeia uma nova página de resultados?

    ![Scrolling infinito através dos resultados da "piscina"](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

    > [!Tip]
    > Para garantir que uma barra de deslocação aparece na primeira página, a primeira página de resultados deve exceder ligeiramente a altura da área em que estão a ser apresentadas. No nosso exemplo **.box1** tem uma altura de 30 pixels, **.box2** tem uma altura de 100 pixels _e_ uma margem inferior de 24 pixels. Assim, cada entrada usa 154 pixels. Três entradas ocuparão 3 x 154 = 462 pixels. Para garantir a localização de uma barra de deslocamento vertical, deve ser definida uma altura para a área de visualização inferior a 462 pixels, mesmo 461 trabalhos. Este problema ocorre apenas na primeira página, depois disso uma barra de deslocação certamente aparecerá. A linha a atualizar é: ** &lt; div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()" &gt; **.

2. Percorra todo o caminho até ao fundo dos resultados. Note como todas as informações estão agora na página de uma vista. Pode deslocar-se até ao topo sem desencadear chamadas de servidor.

Sistemas de deslocação infinitos mais sofisticados podem usar a roda do rato, ou outro mecanismo semelhante, para desencadear o carregamento de uma nova página de resultados. Não vamos levar o scroll infinito mais longe nestes tutoriais, mas tem um certo charme, pois evita cliques extras de rato, e você pode querer investigar outras opções mais adiante!

## <a name="takeaways"></a>Conclusões

Considere os seguintes takeaways deste projeto:

* A chamada numerada é boa para pesquisas onde a ordem dos resultados é um pouco arbitrária, o que significa que pode muito bem haver algo de interessante para os seus utilizadores nas páginas posteriores.
* O scrolling infinito é bom quando a ordem dos resultados é particularmente importante. Por exemplo, se os resultados forem encomendados à distância do centro de uma cidade de destino.
* A paging numerada permite uma melhor navegação. Por exemplo, um utilizador pode lembrar-se que um resultado interessante estava na página 6, enquanto que não existe tal referência fácil no scrolling infinito.
* O scrolling infinito tem um apelo fácil, rolando para cima e para baixo sem números de página exigentes para clicar.
* Uma característica chave do scrolling infinito é que os resultados são anexados a uma página existente, não substituindo essa página, que é eficiente.
* O armazenamento temporário persiste por apenas uma chamada, e precisa ser reposto para sobreviver a chamadas adicionais.


## <a name="next-steps"></a>Passos seguintes

A chamada é fundamental para as pesquisas na Internet. Com a paging bem coberta, o próximo passo é melhorar ainda mais a experiência do utilizador, adicionando pesquisas de tipo à frente.

> [!div class="nextstepaction"]
> [C# Tutorial: Adicionar auto-completação e sugestões - Azure Cognitive Search](tutorial-csharp-type-ahead-and-suggestions.md)
