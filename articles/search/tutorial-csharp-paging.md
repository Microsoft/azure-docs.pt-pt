---
title: C#tutorial sobre a paginação de resultados de pesquisa - Azure Search
description: Este tutorial baseia-se no projeto "Criar seu primeiro aplicativo - Azure Search", com a escolha dos dois tipos de paginação. O primeiro usa uma variedade de botões de número de página, bem como em primeiro lugar, em seguida, anterior e última página botões. O sistema de paginação de segundo utiliza deslocamento infinito, acionado por mover uma barra de rolagem vertical para o limite inferior.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 05/01/2019
ms.openlocfilehash: fc2f358921380803e89c7a8ed5c2ef0fc8e1e467
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304352"
---
# <a name="c-tutorial-search-results-pagination---azure-search"></a>C#Tutorial: Paginação de resultados de pesquisa - Azure Search

Aprenda a implementar dois sistemas diferentes de paginação, o primeiro com base nos números de página e a segunda de deslocamento infinito. Ambos os sistemas de paginação são amplamente utilizados e selecionar o correto depende a experiência do usuário que desejar com os resultados. Este tutorial baseia-se os sistemas de paginação no projeto criado no [ C# Tutorial: Crie seu primeiro aplicativo - Azure Search](tutorial-csharp-create-first-app.md) tutorial.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Expandir a sua aplicação com paginação numerada
> * Expandir a sua aplicação com rolagem infinita

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

Tem a [ C# Tutorial: Crie seu primeiro aplicativo - Azure Search](tutorial-csharp-create-first-app.md) projeto em funcionamento. Este projeto pode ser a sua própria versão ou instalá-lo a partir do GitHub: [Criar a primeira aplicação](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="extend-your-app-with-numbered-paging"></a>Expandir a sua aplicação com paginação numerada

Paginação numerada é o sistema de paginação de escolha de mecanismos de pesquisa de internet principal e a maioria dos outros sites de pesquisa. Paginação numerada normalmente inclui uma opção "seguinte" e "anterior", além de um intervalo de números de página real. Também uma opção de "última página" e "primeira página" também poderão estar disponíveis. Estas opções, certamente, dê um controle de usuário ao longo de navegar pelos resultados baseado em página.

Adicionaremos um sistema que inclui o seguinte em primeiro lugar, anterior e opções de pela última vez, juntamente com os números de página que não se iniciam a partir de 1, mas em vez disso, o surround a página atual, o utilizador está num (então, por exemplo, se o usuário está observando a página de 10, talvez números de página 8 9, 10, 11 e 12 são apresentados).

O sistema estará flexível o suficiente para permitir que o número de números de página visível para ser definida numa variável global.

O sistema irão tratar a página mais à esquerda e mais à direita botões de número como especiais, que significa que irão disparar a alterar o intervalo de números de página apresentados. Por exemplo, se são apresentados números de página, 8, 9, 10, 11 e 12 e o usuário clica em 8, o intervalo de números de página apresentado muda para 6, 7, 8, 9 e 10. E há uma mudança semelhante à direita, se tiver selecionado 12.

### <a name="add-paging-fields-to-the-model"></a>Adicione campos de paginação para o modelo

Tem a solução de página de pesquisa básica abrir.

1. Abra o ficheiro de modelo SearchData.cs.

2. Primeiro, adicione algumas variáveis globais. No MVC, as variáveis globais são declaradas na própria classe estática. **ResultsPerPage** define o número de resultados por página. **MaxPageRange** determina o número de números de página visível na exibição. **PageRangeDelta** determina quantas páginas do esquerda ou direita deve ser deslocado o intervalo de páginas, quando o número de página mais à esquerda ou mais à direita está selecionado. Normalmente, este último número é em torno de metade dos **MaxPageRange**. Adicione o seguinte código para o espaço de nomes.

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
    >Se estiver a executar este projeto num dispositivo com uma tela menor, por exemplo, um laptop, em seguida, considere alterar **ResultsPerPage** para 2.

3. Adicionar propriedades de paginação para a **SearchData** de classe, digamos, afinal o **searchText** propriedade.

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

### <a name="add-a-table-of-paging-options-to-the-view"></a>Adicionar uma tabela de opções de paginação para a vista

1. Abra o ficheiro Index. cshtml e adicione o seguinte código logo antes do fechamento &lt;/body&gt; marca. Este novo código apresenta uma tabela de opções de paginação: primeiro, anterior, 1, 2, 3, 4, 5, em seguida, a última.

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

    Utilizamos uma tabela HTML para alinhar as coisas de forma organizada. No entanto, toda a ação é proveniente do @Html.ActionLink instruções, que chama o controlador com um **novos** modelo criado com entradas diferentes para o **paginação** propriedade que adicionamos anteriormente.

    As opções de página de primeiro e último não enviam cadeias de caracteres como "primeiro" e "last", mas em vez disso, enviar os números de página correta.

2. Adicione algumas classes de paginação para a lista de estilos HTML no ficheiro hotels.css. O **pageSelected** classe está lá para identificar a página que o usuário está visualizando atualmente (ao ativar o número em negrito) na lista de números de página.

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

### <a name="add-a-page-action-to-the-controller"></a>Adicionar uma ação de página para o controlador

1. Abra o ficheiro HomeController.cs e adicione a **página** ação. Esta ação responde a qualquer uma das opções de página selecionadas.

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

    O **RunQueryAsync** método mostrará um erro de sintaxe, devido ao terceiro parâmetro, que será entramos para um pouco.

    > [!Note]
    > O **TempData** chamadas de armazenam um valor (uma **objeto**) no armazenamento temporário, porém Este armazenamento persistir durante _apenas_ uma chamada. Se algo que armazenamos em dados temporários, estará disponível para a próxima chamada a uma ação de controlador, mas com certeza será passou pela chamada depois disso! Devido a este tempo de vida curto, armazenamos o texto de pesquisa e propriedades de paginação de volta no armazenamento temporário de cada chamada para **página**.

2. O **Index(model)** necessidades de ação atualizadas para armazenar as variáveis temporárias e adicionar o parâmetro de página mais à esquerda para a **RunQueryAsync** chamar.

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

3. O **RunQueryAsync** necessidades de método atualizadas significativamente. Usamos o **Skip**, **superior**, e **IncludeTotalResultCount** campos do **SearchParameters** classe para solicitar apenas um valor de página de os resultados, começando a **ignorar** definição. Também precisamos calcular as variáveis de paginação para nossa exibição. Substitua o método de todo o código a seguir.

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
                leftMostPage = Math.Min(leftMostPage + GlobalVariables.PageRangeDelta, model.pageCount - GlobalVariables.MaxPageRange);
            }
            model.leftMostPage = leftMostPage;

            // Calculate the number of page numbers to display.
            model.pageRange = Math.Min(model.pageCount - leftMostPage, GlobalVariables.MaxPageRange);

            return View("Index", model);
        }
    ```

4. Por fim, precisamos de faça uma pequena alteração para a vista. A variável **resultsList.Results.Count** agora irá conter o número de resultados retornados numa página (3 no nosso exemplo), não o número total. Porque definimos os **IncludeTotalResultCount** como true, a variável **resultsList.Count** agora contém o número total de resultados. Então, localizar onde o número de resultados é apresentado na vista e alterá-lo para o código a seguir.

    ```cs
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>
    ```

    > [!Note]
    > Há um impacto no desempenho, embora não normalmente grande parte de um, definindo **IncludeTotalResultCount** como true, como este total tem de ser calculado pela Azure Search. Com conjuntos de dados complexos, há um aviso de que o valor devolvido é uma _aproximação_. Para os nossos dados de hotel, será preciso.

### <a name="compile-and-run-the-app"></a>Compilar e executar a aplicação

Agora, selecione **iniciar sem depuração** (ou prima a tecla F5).

1. Procure um texto que vai dar muita resultados (por exemplo, "Wi-Fi"). Pode, página harmoniosamente pelos resultados?

    ![Paginação numerada através de resultados de "agrupamento"](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

2. Experimente clicar no mais à direita e mais tarde, mais à esquerda números de página. Os números de página ajustar adequadamente para o centro da página que estiver a utilizar?

3. São as opções de "primeiros" e "last" útil? Algumas pesquisas na web populares utilizam estas opções, e outros não.

4. Ir para a última página de resultados. A última página é a única página, que pode conter menos de **ResultsPerPage** resultados.

    ![Examinando a última página do "Wi-Fi"](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

5. Escreva "Cidade" e clique em Procurar. Não existem opções de paginação são apresentadas se existirem menos de um valor de página de resultados.

    ![Procurar por "Cidade"](./media/tutorial-csharp-create-first-app/azure-search-town.png)

Agora salve desativar este projeto e vamos tentar uma alternativa para essa forma de paginação.

## <a name="extend-your-app-with-infinite-scrolling"></a>Expandir a sua aplicação com rolagem infinita

Deslocamento infinito é acionado quando um usuário rola uma barra de rolagem vertical para o último dos resultados a serem apresentados. Neste evento, é feita uma chamada para o servidor para a próxima página de resultados. Se não existirem mais resultados, nada é retornado e a barra de deslocamento vertical não é alterada. Se existirem mais resultados, eles são acrescentados para a página atual e as alterações de barra de rolagem para mostrar o que mais resultados estão disponíveis.

O ponto importante aqui é que a página a ser apresentada não é substituída, mas anexado para com os resultados de novo. Cópia de segurança para os primeiros resultados da pesquisa, o utilizador pode sempre navegar.

Para implementar de deslocamento infinito, vamos começar com o projeto antes de qualquer um dos elementos rolagem número da página foram adicionados. Então, se for necessário, fazer outra cópia da página de pesquisa básica do GitHub: [Criar a primeira aplicação](https://github.com/Azure-Samples/azure-search-dotnet-samples).

### <a name="add-paging-fields-to-the-model"></a>Adicione campos de paginação para o modelo

1. Primeiro, adicione uma **paginação** propriedade para o **SearchData** classe (no arquivo de modelo SearchData.cs).

    ```cs
        // Record if the next page is requested.
        public string paging { get; set; }
    ```

    Esta variável é uma cadeia de caracteres, que contém "seguinte" se a página seguinte dos resultados deve ser enviada ou ser nulo para a primeira página de uma pesquisa.

2. No mesmo ficheiro e no espaço de nomes, adicione uma classe de variável global com uma propriedade. No MVC, as variáveis globais são declaradas na própria classe estática. **ResultsPerPage** define o número de resultados por página. 

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

### <a name="add-a-vertical-scroll-bar-to-the-view"></a>Adicionar uma barra de rolagem vertical para o modo de exibição

1. Localize a secção do ficheiro Index. cshtml que apresenta os resultados (ele começa com o  **@if (modelo! = null)** ).

2. Substitua a secção com o código abaixo. A nova **&lt;div&gt;** secção destina-se em torno da área que deve ser rolável e adiciona ambos um **capacidade excedida-y** atributo e uma chamada para um **onscroll**função chamada "scrolled()", da seguinte forma.

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
                    @Html.TextArea("desc", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
                }
            </div>
        }
    ```

3. Diretamente abaixo do loop, depois do &lt;/div&gt; etiqueta, adicione o **rolado** função.

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

    O **se** instrução no script acima testa para verificar se o utilizador tem rolado na parte inferior da barra de rolagem vertical. Se tiverem, uma chamada para o **home page** controlador é feito para uma ação chamada **próxima**. Nenhuma outra informação é necessária para o controlador, irá devolver a seguinte página de dados. Estes dados, em seguida, são formatados com estilos idênticos de HTML da página original. Se não são devolvidos resultados, nada é acrescentado e mantenha-se as coisas como estão.

### <a name="handle-the-next-action"></a>Lidar com a próxima ação

Existem apenas três ações que têm de ser enviados para o controlador de: a primeira execução da aplicação, que chama **Index()** , a primeira pesquisa pelo usuário, que chama **Index(model)** e, em seguida, os próximos chama-se para obter mais resultados via **Next(model)** .

1. Abra o ficheiro de controlador home e eliminar a **RunQueryAsync** método a partir do original tutorial.

2. Substitua a **Index(model)** ação com o código a seguir. Ela agora trate a **paginação** campo quando é nulo ou definido como "next" e processa a chamada para o Azure Search.

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

    Semelhante ao método numerados de paginação, usamos o **Skip** e **superior** as configurações de pesquisa para solicitar apenas os dados que precisamos é devolvido.

3. Adicionar a **seguinte** ação para o controlador home. Observe como ele retorna uma lista, a cada dois elementos a adicionar à lista de hotel: um nome de hotel e uma descrição de hotel. Este formato é definido de acordo com o **rolado** uso de função dos dados retornados na vista.

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

4. Se estiver recebendo um erro de sintaxe **lista&lt;cadeia&gt;** , em seguida, adicione o seguinte **usando** diretiva ao cabeçalho do ficheiro de controlador.

    ```cs
    using System.Collections.Generic;
    ```

### <a name="compile-and-run-your-project"></a>Compilar e executar o seu projeto

Agora, selecione **iniciar sem depuração** (ou prima a tecla F5).

1. Introduza um termo que forneceram muita resultados (como "conjunto") e, em seguida, testar a barra de deslocamento vertical. Acionar uma nova página de resultados?

    ![Infinito de rolagem por meio de resultados de "agrupamento"](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

    > [!Tip]
    > Para garantir que uma barra de deslocamento aparece na primeira página, a primeira página de resultados tem de exceder um pouco a altura da área que estão a ser apresentados no. No nosso exemplo **.box1** tem uma altura de 30 pixels, **.box2** tem uma altura de 100 pixels _e_ uma margem inferior de 24 pixels. Portanto, cada entrada utiliza 154 pixels. Três entradas irão demorar até 3 x 154 = 462 pixels. Para garantir que uma barra de rolagem vertical aparece, uma altura para a área de exibição deve ser definida que é menor do que 462 pixels, até mesmo 461 funciona. Este problema ocorre apenas na primeira página, depois disso, uma barra de rolagem é-se de que são apresentados. A linha a atualizar é:  **&lt;div id = "myDiv" style = "width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()"&gt;** .

2. Role para baixo até à parte inferior dos resultados. Observe que como todas as informações estão agora na página de uma vista. Pode navegar de volta à parte superior sem disparar quaisquer chamadas do servidor.

Sistemas de deslocamento infinitos mais sofisticados, poderão utilizar a roda do mouse, ou outro mecanismo, para acionar o carregamento de uma nova página de resultados. Não extrairemos algum ainda mais nestes tutoriais de deslocamento infinito, mas tem um determinado atalho para o mesmo à medida que evita cliques do extra mouse e, é aconselhável investigar outras opções ainda mais!

## <a name="takeaways"></a>Conclusões

Considere o seguintes das novidades deste projeto:

* Paginação numerada é boa para pesquisas onde a ordem dos resultados é um tanto arbitrário, o que significa Maio há também ser algo de interesse para seus usuários nas páginas posteriores.
* Deslocamento infinito é bom quando a ordem de resultados é particularmente importante. Por exemplo, se os resultados são ordenados na distância do Centro de uma cidade de destino.
* Paginação numerada permite navegação melhor. Por exemplo, um utilizador pode não se esqueça de que um resultado interessante foi na página 6, ao passo que nenhuma dessa referência fácil existe no deslocamento infinito.
* Deslocamento infinito tem um apelo fácil, a rolagem e reduzir verticalmente com nenhum número de página meticuloso clicar em.
* Um recurso chave do deslocamento infinito é que os resultados são acrescentados a uma página existente, não substituir nessa página, que é eficiente.
* Armazenamento temporário persistir para apenas uma chamada e tem de ser reposto para resistir a chamadas adicionais.


## <a name="next-steps"></a>Passos Seguintes

Paginação é fundamental para pesquisas da internet. Com a paginação cobertas bem, a próxima etapa é melhorar a experiência do usuário ainda mais, adicionando pesquisas antecipada.

> [!div class="nextstepaction"]
> [C#Tutorial: Adicionar preenchimento automático e sugestões - Azure Search](tutorial-csharp-type-ahead-and-suggestions.md)
