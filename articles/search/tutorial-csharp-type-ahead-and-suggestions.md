---
title: Autocompleta e sugestões
titleSuffix: Azure Cognitive Search
description: Este tutorial demonstra a completação automática e sugestões como uma forma de recolher a entrada de prazos de pesquisa dos utilizadores usando a lista de dropdown. Baseia-se num projeto de hotéis existente.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: 6b74c3bbb811c122950fd969a8797e87f8f77f86
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81641080"
---
# <a name="c-tutorial-add-autocomplete-and-suggestions---azure-cognitive-search"></a>C# tutorial: Adicionar autocomplete e sugestões - Pesquisa Cognitiva Azure

Aprenda a implementar automaticamente (consultas de tipo grafite e documentos sugeridos) quando um utilizador começa a digitar numa caixa de pesquisa. Neste tutorial, mostraremos consultas e resultados de sugestão auto-completas separadamente, e depois juntos. Um utilizador só pode ter de escrever dois ou três caracteres para localizar todos os resultados disponíveis.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Adicionar sugestões
> * Adicione destaque às sugestões
> * Adicionar autocompleto
> * Combine auto-conclusão e sugestões

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial faz parte de uma série e baseia-se no projeto de paging criado no [C# Tutorial: Search results pagination - Azure Cognitive Search](tutorial-csharp-paging.md).

Em alternativa, pode descarregar e executar a solução para este tutorial específico: [3 add-typeahead](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/3-add-typeahead).

## <a name="add-suggestions"></a>Adicionar sugestões

Comecemos pelo caso mais simples de oferecer alternativas ao utilizador: uma lista de sugestões.

1. No ficheiro index.cshtml, `@id` alteração da declaração **textBoxFor** para **azureautosuggest**.

    ```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

2. Seguindo esta declaração, após o ** &lt;fecho/div,&gt;** insira este script. Este script aproveita o [widget Autocomplete](https://api.jqueryui.com/autocomplete/) da biblioteca jQuery UI de código aberto para apresentar a lista de resultados sugeridos. 

    ```javascript
    <script>
        $("#azureautosuggest").autocomplete({
            source: "/Home/Suggest?highlights=false&fuzzy=false",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

    O ID "azureautosuggest" liga o script acima à caixa de pesquisa. A opção de origem do widget é definida para um método Sugerir que chama a API sugerir com dois parâmetros de consulta: **destaques** e **difusor,** ambos definidos como falsos neste caso. Além disso, um mínimo de dois caracteres é necessário para desencadear a pesquisa.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Adicione referências aos scripts jQuery à vista

1. Para aceder à biblioteca jQuery, altere a &lt;secção principal&gt; do ficheiro de visualização para o seguinte código:

    ```cs
    <head>
        <meta charset="utf-8">
        <title>Typeahead</title>
        <link href="https://code.jquery.com/ui/1.12.1/themes/start/jquery-ui.css"
              rel="stylesheet">
        <script src="https://code.jquery.com/jquery-1.10.2.js"></script>
        <script src="https://code.jquery.com/ui/1.12.1/jquery-ui.js"></script>

        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

2. Como estamos a introduzir uma nova referência jQuery, também precisamos de remover, ou comentar, a referência padrão jQuery no ficheiro _Layout.cshtml (na pasta **Views/Shared).** Localize as seguintes linhas e comente a primeira linha de script, como mostrado. Esta alteração evita o embate de referências ao jQuery.

    ```html
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

    Agora podemos usar as funções de jQuery autocompleto pré-definido.

### <a name="add-the-suggest-action-to-the-controller"></a>Adicione a ação Sugerir ao controlador

1. No controlador doméstico, adicione a ação **Sugerir** (por exemplo, após a ação **da Página).**

    ```cs
        public async Task<ActionResult> Suggest(bool highlights, bool fuzzy, string term)
        {
            InitSearch();

            // Setup the suggest parameters.
            var parameters = new SuggestParameters()
            {
                UseFuzzyMatching = fuzzy,
                Top = 8,
            };

            if (highlights)
            {
                parameters.HighlightPreTag = "<b>";
                parameters.HighlightPostTag = "</b>";
            }

            // Only one suggester can be specified per index. It is defined in the index schema.
            // The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and simply searches the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", parameters);

            // Convert the suggest query results to a list that can be displayed in the client.
            List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();

            // Return the list of suggestions.
            return new JsonResult(suggestions);
        }
    ```

    O parâmetro **Superior** especifica quantos resultados deve devolver (se não especificado, o padrão é 5). É especificado um _sugestor_ no índice Azure, que é feito quando os dados são configurados, e não por uma aplicação de cliente como este tutorial. Neste caso, o sugestor chama-se "sg", e procura no campo **HotelName** - nada mais. 

    A correspondência fuzzy permite que "quase falhas" sejam incluídas na saída, até uma distância de edição. Se o parâmetro de **destaque** estiver definido como verdadeiro, as etiquetas HTML arrojadas são adicionadas à saída. Vamos definir estes dois parâmetros verdadeiros na próxima secção.

2. Pode ter alguns erros de sintaxe. Em caso afirmativo, adicione as duas seguintes **utilizando** declarações no topo do ficheiro.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

3. Execute a aplicação. Obtém um leque de opções quando entra em "po", por exemplo? Agora tente "pa".

    ![Escrever "po" revela duas sugestões](./media/tutorial-csharp-create-first-app/azure-search-suggest-po.png)

    Note que as letras que insere _devem_ começar uma palavra, e não ser incluídas simplesmente dentro da palavra.

4. No roteiro de visualização, desloque **&de** forma verdadeira e volte a executar a aplicação. Agora entra "po". Reparem que a busca pressupõe que se enganou numa carta!
 
    ![Digitando "pa" com conjunto felpudo para verdade](./media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png)

    Se estiver interessado, a [sintaxe lucene de consulta em Pesquisa Cognitiva Azure](https://docs.microsoft.com/azure/search/query-lucene-syntax) descreve a lógica usada em pesquisas difusas em detalhe.

## <a name="add-highlighting-to-the-suggestions"></a>Adicione destaque às sugestões

Podemos melhorar a aparência das sugestões ao utilizador, definindo o parâmetro de **destaques** como verdadeiro. No entanto, primeiro, precisamos de adicionar algum código à vista para exibir o texto arrojado.

1. Na vista (index.cshtml), adicione o seguinte script após o script **azureautosuggest** que inseriu acima.

    ```javascript
    <script>
        var updateTextbox = function (event, ui) {
            var result = ui.item.value.replace(/<\/?[^>]+(>|$)/g, "");
            $("#azuresuggesthighlights").val(result);
            return false;
        };

        $("#azuresuggesthighlights").autocomplete({
            html: true,
            source: "/home/suggest?highlights=true&fuzzy=false&",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },
            select: updateTextbox,
            focus: updateTextbox
        }).data("ui-autocomplete")._renderItem = function (ul, item) {
            return $("<li></li>")
                .data("item.autocomplete", item)
                .append("<a>" + item.label + "</a>")
                .appendTo(ul);
        };
    </script>
    ```

2. Agora mude a identificação da caixa de texto para que leia o seguinte.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. Volte a executar a aplicação e deverá ver o texto introduzido asermado nas sugestões. Tente escrever "pa".
 
    ![Digitando "pa" com destaque](./media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png)

4. A lógica usada no guião de destaque acima não é à prova de falhas. Se entrar num termo que aparece duas vezes com o mesmo nome, os resultados arrojados não são bem o que desejaria. Tente escrever "mo".

    Uma das perguntas que um desenvolvedor precisa de responder é, quando é que um guião funciona "bem o suficiente", e quando devem ser abordados os seus caprichos. Não vamos levar mais destaque neste tutorial, mas encontrar um algoritmo preciso é algo a considerar se o destaque não é eficaz para os seus dados. Para mais informações, consulte [hit highlighting](search-pagination-page-layout.md#hit-highlighting).

## <a name="add-autocomplete"></a>Adicionar autocompleto

Outra variação, que é ligeiramente diferente das sugestões, é a auto-conclusão (por vezes chamada de "tipo à frente") que completa um termo de consulta. Mais uma vez, começaremos com a implementação mais simples, antes de melhorar a experiência do utilizador.

1. Introduza o seguinte script na vista, seguindo os scripts anteriores.

    ```javascript
    <script>
        $("#azureautocompletebasic").autocomplete({
            source: "/Home/Autocomplete",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

2. Agora mude a identificação da caixa de texto, para que leia o seguinte.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. No controlador doméstico precisamos entrar na ação **Autocomplete,** por exemplo, abaixo da ação **Sugerir.**

    ```cs
        public async Task<ActionResult> AutoComplete(string term)
        {
            InitSearch();

            // Setup the autocomplete parameters.
            var ap = new AutocompleteParameters()
            {
                AutocompleteMode = AutocompleteMode.OneTermWithContext,
                Top = 6
            };
            AutocompleteResult autocompleteResult = await _indexClient.Documents.AutocompleteAsync(term, "sg", ap);

            // Convert the results to a list that can be displayed in the client.
            List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();

            // Return the list.
            return new JsonResult(autocomplete);
        }
    ```

    Note-se que estamos a usar a mesma função *de sugestionista,* chamada "sg", na pesquisa autocompleta como fizemos para sugestões (por isso estamos apenas a tentar completar automaticamente os nomes do hotel).

    Existem uma gama de definições **de Modo Autocomplete,** e estamos a usar o **OneTermWithContext**. Consulte a [API autocompleta](https://docs.microsoft.com/rest/api/searchservice/autocomplete) para obter uma descrição de opções adicionais.

4. Execute a aplicação. Note como o leque de opções apresentadas na lista de lançamentos são palavras individuais. Tente escrever palavras começando com "re". Note como o número de opções reduz à medida que mais letras são dactilografadas.

    ![Dactilografia com auto-conclusão básica](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png)

    Tal como está, o guião de sugestões que executou anteriormente é provavelmente mais útil do que este roteiro de auto-conclusão. Para tornar a auto-conclusão mais fácil de utilizar, é melhor adicionar à pesquisa de sugestões.

## <a name="combine-autocompletion-and-suggestions"></a>Combine auto-conclusão e sugestões

Combinar auto-conclusão e sugestões é a mais complexa das nossas opções, e provavelmente proporciona a melhor experiência do utilizador. O que queremos é mostrar, em linha com o texto que está a ser dactilografado, é a primeira escolha da Pesquisa Cognitiva Azure para completar automaticamente o texto. Além disso, queremos uma série de sugestões como uma lista de abandono.

Existem bibliotecas que oferecem esta funcionalidade - muitas vezes chamada de "auto-conclusão inline" ou um nome semelhante. No entanto, vamos implementar de forma nativa esta funcionalidade, para que possa ver o que se passa. Vamos começar a trabalhar no controlador primeiro neste exemplo.

1. Precisamos adicionar uma ação ao controlador que devolve apenas um resultado de auto-conclusão, juntamente com um número especificado de sugestões. Chamaremos a esta ação **AutocompleteAndSuggest**. No controlador doméstico, adicione as seguintes ações, seguindo as suas outras novas ações.

    ```cs
        public async Task<ActionResult> AutocompleteAndSuggest(string term)
        {
            InitSearch();

            // Setup the type-ahead search parameters.
            var ap = new AutocompleteParameters()
            {
                AutocompleteMode = AutocompleteMode.OneTermWithContext,
                Top = 1,
            };
            AutocompleteResult autocompleteResult = await _indexClient.Documents.AutocompleteAsync(term, "sg", ap);

            // Setup the suggest search parameters.
            var sp = new SuggestParameters()
            {
                Top = 8,
            };

            // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and it searches only the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", sp);

            // Create an empty list.
            var results = new List<string>();

            if (autocompleteResult.Results.Count > 0)
            {
                // Add the top result for type-ahead.
                results.Add(autocompleteResult.Results[0].Text);
            }
            else
            {
                // There were no type-ahead suggestions, so add an empty string.
                results.Add("");
            }
            for (int n = 0; n < suggestResult.Results.Count; n++)
            {
                // Now add the suggestions.
                results.Add(suggestResult.Results[n].Text);
            }

            // Return the list.
            return new JsonResult(results);
        }
    ```

    Uma opção de auto-conclusão é devolvida no topo da lista de **resultados,** seguida de todas as sugestões.

2. Na opinião, primeiro implementamos um truque para que uma palavra de auto-conclusão cinza clara seja entregue corretamente sob texto mais ousado sendo introduzido pelo utilizador. HTML inclui posicionamento relativo para este fim. Altere a declaração **TextBoxFor** &gt; (e as suas declarações de mergulho circundantes) &lt;para o seguinte, observando que uma segunda caixa de pesquisa identificada como por **baixo** está mesmo debaixo da nossa caixa de pesquisa normal, puxando esta caixa de pesquisa 39 pixels para fora da sua localização padrão!

    ```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

    Note que estamos mudando o ID novamente, para **azureautocomplete** neste caso.

3. Também na vista, insira o seguinte script, depois de todos os scripts que inseriu até agora. Há muito a fazer.

    ```javascript
    <script>
        $('#azureautocomplete').autocomplete({
            delay: 500,
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },

            // Use Ajax to set up a "success" function.
            source: function (request, response) {
                var controllerUrl = "/Home/AutoCompleteAndSuggest?term=" + $("#azureautocomplete").val();
                $.ajax({
                    url: controllerUrl,
                    dataType: "json",
                    success: function (data) {
                        if (data && data.length > 0) {

                            // Show the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = data[0];

                            // Remove the top suggestion as it is used for inline autocomplete.
                            var array = new Array();
                            for (var n = 1; n < data.length; n++) {
                                array[n - 1] = data[n];
                            }

                            // Show the drop-down list of suggestions.
                            response(array);
                        } else {

                            // Nothing is returned, so clear the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }
                });
            }
        });

        // Complete on TAB.
        // Clear on ESC.
        // Clear if backspace to less than 2 characters.
        // Clear if any arrow key hit as user is navigating the suggestions.
        $("#azureautocomplete").keydown(function (evt) {

            var suggestedText = document.getElementById("underneath").innerHTML;
            if (evt.keyCode === 9 /* TAB */ && suggestedText.length > 0) {
                $("#azureautocomplete").val(suggestedText);
                return false;
            } else if (evt.keyCode === 27 /* ESC */) {
                document.getElementById("underneath").innerHTML = "";
                $("#azureautocomplete").val("");
            } else if (evt.keyCode === 8 /* Backspace */) {
                if ($("#azureautocomplete").val().length < 2) {
                    document.getElementById("underneath").innerHTML = "";
                }
            } else if (evt.keyCode >= 37 && evt.keyCode <= 40 /* Any arrow key */) {
                document.getElementById("underneath").innerHTML = "";
            }
        });

        // Character replace function.
        function setCharAt(str, index, chr) {
            if (index > str.length - 1) return str;
            return str.substr(0, index) + chr + str.substr(index + 1);
        }

        // This function is needed to clear the "underneath" text when the user clicks on a suggestion, and to
        // correct the case of the autocomplete option when it does not match the case of the user input.
        // The interval function is activated with the input, blur, change, or focus events.
        $("#azureautocomplete").on("input blur change focus", function (e) {

            // Set a 2 second interval duration.
            var intervalDuration = 2000, 
                interval = setInterval(function () {

                    // Compare the autocorrect suggestion with the actual typed string.
                    var inputText = document.getElementById("azureautocomplete").value;
                    var autoText = document.getElementById("underneath").innerHTML;

                    // If the typed string is longer than the suggestion, then clear the suggestion.
                    if (inputText.length > autoText.length) {
                        document.getElementById("underneath").innerHTML = "";
                    } else {

                        // If the strings match, change the case of the suggestion to match the case of the typed input.
                        if (autoText.toLowerCase().startsWith(inputText.toLowerCase())) {
                            for (var n = 0; n < inputText.length; n++) {
                                autoText = setCharAt(autoText, n, inputText[n]);
                            }
                            document.getElementById("underneath").innerHTML = autoText;

                        } else {
                            // The strings do not match, so clear the suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }

                    // If the element loses focus, stop the interval checking.
                    if (!$input.is(':focus')) clearInterval(interval);

                }, intervalDuration);
        });
    </script>
    ```

    Note a utilização inteligente da função de **intervalo** para limpar o texto subjacente quando já não corresponde ao que o utilizador está a escrever, e também para definir o mesmo estojo (superior ou inferior) como o utilizador está a escrever (como "pa" corresponde a "PA", "pA", "Pa" quando procura), de modo a que o texto sobreposto seja puro.

    Leia os comentários no guião para obter um entendimento mais completo.

4. Por último, precisamos de fazer um pequeno ajuste a duas classes HTML para torná-los transparentes. Adicione a seguinte linha às classes **searchBoxForm** e **searchBox,** no ficheiro hotels.css.

    ```html
        background: rgba(0,0,0,0);
    ```

5. Agora executa a aplicação. Introduza "pa" na caixa de pesquisa. Você recebe "palácio" como a sugestão autocompleta, juntamente com dois hotéis que contêm "pa"?

    ![Dactilografia com auto-completação inline e sugestões](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png)

6. Tente fazer tabbing para aceitar a sugestão autocompleta e tente selecionar sugestões utilizando as teclas de seta e a tecla de separador, e tente novamente usar o rato e um único clique. Verifique se o guião lida com todas estas situações com cuidado.

    Você pode decidir que é mais simples carregar numa biblioteca que oferece esta funcionalidade para você, mas agora você sabe pelo menos uma maneira de obter a conclusão automática inline para trabalhar!

## <a name="takeaways"></a>Conclusões

Considere os seguintes takeaways deste projeto:

* A auto-conclusão (também conhecida como "type-ahead") e sugestões podem permitir ao utilizador escrever apenas algumas teclas para localizar exatamente o que eles querem.
* A auto-conclusão e as sugestões de trabalho em conjunto podem proporcionar uma rica experiência do utilizador.
* Teste sempre as funções de auto-conclusão com todas as formas de entrada.
* A utilização da função **setInterval** pode ser útil para verificar e corrigir elementos ui.

## <a name="next-steps"></a>Passos seguintes

No próximo tutorial, temos uma outra forma de melhorar a experiência do utilizador, usando facetas para estreitar pesquisas com um único clique.

> [!div class="nextstepaction"]
> [C# Tutorial: Use facetas para ajudar na navegação - Pesquisa Cognitiva Azure](tutorial-csharp-facets.md)


