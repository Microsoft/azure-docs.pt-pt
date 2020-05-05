---
title: C# tutorial sobre paginação de resultados de pesquisa
titleSuffix: Azure Cognitive Search
description: Adicione botões de paginação e navegação aos resultados de pesquisa, baseando-se num projeto de hotéis existente para adicionar os primeiros, próximos, anteriores, últimos e numerados botões. Um segundo sistema de paging usa deslocamento infinito, desencadeado movendo uma barra de pergaminho vertical para o seu limite inferior.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 0dce3852d2b0489b373162fe754d745b01bd3074
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780577"
---
# <a name="tutorial-add-paging-to-search-results-using-the-net-sdk"></a>Tutorial: Adicione a paging aos resultados de pesquisa utilizando o .NET SDK

Aprenda a implementar dois diferentes sistemas de paging, o primeiro baseado em números de página e o segundo em deslocamentoinfinito. Ambos os sistemas de paging são amplamente utilizados, e a seleção certa depende da experiência do utilizador que deseja com os resultados. Este tutorial constrói os sistemas de paging no projeto criado no [C# Tutorial: Crie](tutorial-csharp-create-first-app.md) a sua primeira app - Tutorial de Pesquisa Cognitiva Azure.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Estenda a sua aplicação com paging numerado
> * Estenda a sua aplicação com infinitascrolling

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

Tenha o [C# Tutorial: Crie a sua primeira app - Projeto de Pesquisa Cognitiva Azure](tutorial-csharp-create-first-app.md) em funcionamento. Este projeto pode ser a sua própria versão, ou instalá-lo a partir do GitHub: [Criar a primeira aplicação](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="extend-your-app-with-numbered-paging"></a>Estenda a sua aplicação com paging numerado

A paging numerada é o sistema de paging de eleição dos principais motores de pesquisa de internet e a maioria dos outros sites de pesquisa. A pagagem numerada inclui tipicamente uma opção "seguinte" e "anterior", além de uma gama de números reais de página. Também pode estar disponível uma opção de "primeira página" e "última página". Estas opções certamente dão a um utilizador o controlo sobre a navegação através de resultados baseados em página.

Vamos adicionar um sistema que inclui as primeiras, anteriores, próximas e últimas opções, juntamente com números de página que não começam a partir de 1, mas em vez disso rodeiam a página atual em que o utilizador está (por exemplo, se o utilizador estiver a olhar para a página 10, talvez os números da página 8, 9, 10, 11 e 12 são apresentados).

O sistema será suficientemente flexível para permitir que o número de números de página visíveis seja definido numa variável global.

O sistema tratará os botões de número de página mais à esquerda e à direita como especiais, o que significa que irão desencadear a alteração da gama de números de página apresentados. Por exemplo, se os números da página 8, 9, 10, 11 e 12 forem apresentados, e o utilizador clicar em 8, então o intervalo de números de página apresentado muda para 6, 7, 8, 9 e 10. E há uma mudança semelhante para a direita se escolherem 12.

### <a name="add-paging-fields-to-the-model"></a>Adicione campos de paging ao modelo

Tenha a solução básica da página de pesquisa aberta.

1. Abra o ficheiro modelo SearchData.cs.

2. Primeiro adicione algumas variáveis globais. Em MVC, as variáveis globais são declaradas na sua própria classe estática. **ResultsPerPage** define o número de resultados por página. **MaxPageRange** determina o número de números de página visíveis na vista. **PageRangeDelta** determina quantas páginas à esquerda ou à direita a gama de páginas deve ser deslocada, quando o número de página mais à esquerda ou à direita é selecionado. Tipicamente este último número é de cerca de metade do **MaxPageRange**. Adicione o seguinte código no espaço de nome.

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
    >Se estiver a executar este projeto num dispositivo com um ecrã mais pequeno, como um portátil, considere mudar o **ResultsPerPage** para 2.

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

1. Abra o ficheiro index.cshtml e adicione o &lt;seguinte&gt; código imediatamente antes da etiqueta de fecho/corpo. Este novo código apresenta uma tabela de opções de paging: primeiro, anterior, 1, 2, 3, 4, 5, seguinte, último.

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

    Usamos uma tabela HTML para alinhar as coisas cuidadosamente. No entanto, toda a ação provém das @Html.ActionLink declarações, cada uma chamando o controlador com um **novo** modelo criado com diferentes entradas para a propriedade **paging** que adicionamos anteriormente.

    As opções de primeira e última página não enviam cordas como "primeiro" e "último", mas sim enviar os números de página corretos.

2. Adicione algumas aulas de paging à lista de estilos HTML no ficheiro hotels.css. A **página A** classe Selecionada está lá para identificar a página que o utilizador está atualmente a visualizar (virando o número ousado) na lista de números da página.

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

1. Abra o ficheiro HomeController.cs e adicione a ação **da Página.** Esta ação responde a qualquer uma das opções de página selecionadas.

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
    > As chamadas **TempData** armazenam um valor (um **objeto)** em armazenamento temporário, embora este armazenamento persista _apenas_ por uma chamada. Se armazenarmos algo em dados temporários, estará disponível para a próxima chamada para uma ação do controlador, mas definitivamente será extraída pela chamada depois disso! Devido a este curto tempo de vida, armazenamos o texto de pesquisa e propriedades paging de volta em armazenamento temporário cada chamada para **página**.

2. A ação **indexada (modelo)** precisa de ser atualizada para armazenar as variáveis temporárias e adicionar o parâmetro mais à esquerda à chamada **RunQueryAsync.**

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

3. O método **RunQueryAsync** precisa de ser atualizado significativamente. Utilizamos os campos **Skip,** **Top**e **IncludeTotalResultCount** da classe **SearchParameters** para solicitar apenas uma página de resultados, a partir da definição **Skip.** Precisamos também de calcular as variáveis pagantes para a nossa visão. Substitua todo o método pelo seguinte código.

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

4. Por último, temos de fazer uma pequena alteração à vista. Os **resultados variáveisList.Results.Count** agora contêm o número de resultados devolvidos numa página (3 no nosso exemplo), e não o número total. Como definimos o **Incluir TotalResultCount** como verdadeiro, os **resultados variáveisList.Count** agora contém o número total de resultados. Por isso, localize onde o número de resultados é apresentado na vista e altere-o para o seguinte código.

    ```cs
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>
    ```

    > [!Note]
    > Há um sucesso de desempenho, embora geralmente não muito de um, definindo **Incluir TotalResultCount** para verdade, uma vez que este total precisa ser calculado pela Pesquisa Cognitiva Azure. Com conjuntos de dados complexos, há um aviso de que o valor devolvido é uma _aproximação._ Para os dados do nosso hotel, será preciso.

### <a name="compile-and-run-the-app"></a>Compilar e executar a app

Selecione agora **Start Without Debugging** (ou prima a tecla F5).

1. Pesquise em algum texto que dará muitos resultados (como "wifi"). Pode ver bem os resultados?

    ![Paginação numerada através dos resultados da "piscina"](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

2. Tente clicar nos números mais à direita e, mais tarde, à esquerda. Os números da página ajustam-se adequadamente para centrar a página em que está?

3. As opções "primeira" e "última" são úteis? Algumas pesquisas populares na web usam estas opções, e outras não.

4. Vá à última página dos resultados. A última página é a única página que pode conter menos do que os resultados do **ResultsPerPage.**

    ![Examinando a última página de "wifi"](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

5. Digite em "cidade", e clique em pesquisar. Não são apresentadas opções de paging se houver menos de uma página de resultados.

    ![À procura de "cidade"](./media/tutorial-csharp-create-first-app/azure-search-town.png)

Agora, salve este projeto e vamos tentar uma alternativa a esta forma de paging.

## <a name="extend-your-app-with-infinite-scrolling"></a>Estenda a sua aplicação com infinitascrolling

O deslocamento infinito é acionado quando um utilizador percorre uma barra de pergaminho vertical até ao último dos resultados que estão a ser apresentados. Neste caso, é feita uma chamada para o servidor para a próxima página dos resultados. Se não houver mais resultados, nada é devolvido e a barra de pergaminho vertical não muda. Se houver mais resultados, estão anexados à página atual, e a barra de pergaminho muda para mostrar que há mais resultados disponíveis.

O ponto importante aqui é que a página que está a ser exibida não é substituída, mas anexada aos novos resultados. Um utilizador pode sempre deslocar-se de volta até aos primeiros resultados da pesquisa.

Para implementar um deslocamento infinito, vamos começar com o projeto antes de qualquer um dos elementos de pergaminho do número da página ser adicionado. Por isso, se for preciso, faça outra cópia da página de pesquisa básica do GitHub: Crie a [primeira aplicação](https://github.com/Azure-Samples/azure-search-dotnet-samples).

### <a name="add-paging-fields-to-the-model"></a>Adicione campos de paging ao modelo

1. Em primeiro lugar, adicione uma propriedade **de paging** à classe **SearchData** (no ficheiro modelo SearchData.cs).

    ```cs
        // Record if the next page is requested.
        public string paging { get; set; }
    ```

    Esta variável é uma cadeia, que se mantém "a seguir" se a próxima página de resultados deve ser enviada, ou ser nula para a primeira página de uma pesquisa.

2. No mesmo arquivo, e dentro do espaço de nome, adicione uma classe variável global com uma propriedade. Em MVC, as variáveis globais são declaradas na sua própria classe estática. **ResultsPerPage** define o número de resultados por página. 

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

### <a name="add-a-vertical-scroll-bar-to-the-view"></a>Adicione uma barra de pergaminho vertical à vista

1. Localize a secção do ficheiro index.cshtml que apresenta os resultados (começa com o ** @if (Modelo!= nulo)**).

2. Substitua a secção pelo código abaixo. A ** &lt;nova&gt; ** secção de mergulho é em torno da área que deve ser perlocada, e adiciona tanto um atributo de **transbordamento e** uma chamada para uma função **onscroll** chamada "scrolled()", como assim.

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

3. Diretamente por baixo do &lt;laço,&gt; após a etiqueta /div, adicione a função **deslocada.**

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

    A **if** declaração se no script acima testa para ver se o utilizador rolou para a parte inferior da barra de pergaminho vertical. Se o fizerem, é feita uma chamada para o controlador **doméstico** para uma ação chamada **Next**. Nenhuma outra informação é necessária pelo controlador, irá devolver a próxima página de dados. Estes dados são então formatados utilizando estilos HTML idênticos como a página original. Se não forem devolvidos resultados, nada é anexado e as coisas ficam como estão.

### <a name="handle-the-next-action"></a>Manuseie a próxima ação

Há apenas três ações que precisam de ser enviadas para o controlador: a primeira execução da app, que chama **Index()**- a primeira pesquisa pelo utilizador, que chama **Index(modelo)** e, em seguida, as chamadas subsequentes para mais resultados via **Next(modelo)**.

1. Abra o ficheiro do controlador doméstico e elimine o método **RunQueryAsync** do tutorial original.

2. Substitua a ação **Index(modelo)** pelo seguinte código. Trata agora do campo de **paginação** quando é nulo, ou definido para "seguinte", e trata da chamada para a Pesquisa Cognitiva Azure.

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

    Semelhante ao método de paging numerado, utilizamos as definições de pesquisa **Skip** e **Top** para solicitar apenas os dados que precisamos é devolvido.

3. Adicione a **próxima** ação ao controlador doméstico. Note como devolve uma lista, cada hotel adicionando dois elementos à lista: um nome de hotel e uma descrição do hotel. Este formato é definido para corresponder à utilização dos dados devolvidos pela função **scrolled.**

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

4. Se estiver a ter um erro de sintaxe na **&lt;&gt;cadeia da Lista,** adicione a seguinte diretiva utilizando a seguinte diretiva de **utilização** ao chefe do ficheiro do controlador.

    ```cs
    using System.Collections.Generic;
    ```

### <a name="compile-and-run-your-project"></a>Compile e execute o seu projeto

Selecione agora **Start Without Debugging** (ou prima a tecla F5).

1. Introduza um termo que dará muitos resultados (como "piscina") e, em seguida, teste a barra de pergaminho vertical. Desencadeia uma nova página de resultados?

    ![Infinita scrolling através dos resultados da "piscina"](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

    > [!Tip]
    > Para garantir que uma barra de pergaminho aparece na primeira página, a primeira página dos resultados deve exceder ligeiramente a altura da área em que estão a ser apresentadas. No nosso exemplo **.box1** tem uma altura de 30 pixels, **.box2** tem uma altura de 100 pixels _e_ uma margem inferior de 24 pixels. Assim, cada entrada usa 154 pixels. Três entradas ocuparão 3 x 154 = 462 pixels. Para garantir que aparece uma barra de pergaminho vertical, deve ser definida uma altura na área de visualização inferior a 462 pixels, mesmo 461 obras. Este problema só ocorre na primeira página, depois disso uma barra de pergaminho certamente aparecerá. A linha de atualização é: ** &lt;div id="myDiv" style="width: 800px; height: 450px; overflow-y:&gt;scroll;" onscroll="scroll()".**

2. Desça até ao fundo dos resultados. Note como toda a informação está agora na página de uma vista. Pode deslocar-se até ao topo sem acionar chamadas do servidor.

Sistemas de deslocação infinitos mais sofisticados podem usar a roda do rato, ou outro mecanismo semelhante, para desencadear o carregamento de uma nova página de resultados. Não vamos levar o infinito scrolling mais longe nestes tutoriais, mas tem um certo charme, pois evita cliques extra de rato, e você pode querer investigar outras opções mais adiante!

## <a name="takeaways"></a>Conclusões

Considere os seguintes takeaways deste projeto:

* A pagagem numerada é boa para pesquisas onde a ordem dos resultados é um pouco arbitrária, o que significa que pode muito bem haver algo de interesse para os seus utilizadores nas páginas posteriores.
* Deslocamento infinito é bom quando a ordem dos resultados é particularmente importante. Por exemplo, se os resultados forem encomendados à distância do centro de uma cidade de destino.
* A pagagem numerada permite uma melhor navegação. Por exemplo, um utilizador pode lembrar-se que um resultado interessante estava na página 6, enquanto que não existe uma referência tão fácil no deslocamento infinito.
* O scrolling infinito tem um apelo fácil, percorrendo para cima e para baixo sem números de página exigentes para clicar.
* Uma característica chave do scrolling infinito é que os resultados são anexados a uma página existente, não substituindo essa página, que é eficiente.
* O armazenamento temporário persiste para apenas uma chamada, e precisa de ser reiniciado para sobreviver a chamadas adicionais.


## <a name="next-steps"></a>Passos seguintes

Paging é fundamental para pesquisas na Internet. Com a paging bem coberta, o próximo passo é melhorar ainda mais a experiência do utilizador, adicionando pesquisas tipo-ahead.

> [!div class="nextstepaction"]
> [C# Tutorial: Adicionar auto-conclusão e sugestões - Pesquisa Cognitiva Azure](tutorial-csharp-type-ahead-and-suggestions.md)
