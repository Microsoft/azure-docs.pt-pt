---
title: Tutorial C# sobre autocomplete e sugestões
titleSuffix: Azure Cognitive Search
description: Adicione autocompleta e sugestões para recolher a entrada de termo de pesquisa dos utilizadores usando a lista de dropdown. Este tutorial baseia-se num projeto de hotéis existente.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/22/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 06c0b25bcf64cfce01b4144550ef69da8c96ee0e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98785858"
---
# <a name="tutorial-add-autocomplete-and-suggestions-using-the-net-sdk"></a>Tutorial: Adicione autocomplete e sugestões utilizando o .NET SDK

Saiba como implementar o autocomplete (consultas de tipa e resultados sugeridos) quando um utilizador começar a digitar numa caixa de pesquisa. Neste tutorial, mostraremos consultas autocompletas e resultados sugeridos separadamente, e depois juntos. Um utilizador só pode ter de escrever dois ou três caracteres para localizar todos os resultados disponíveis.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Adicionar sugestões
> * Adicionar destaque às sugestões
> * Adicionar autocompleto
> * Combine auto-privatização e sugestões

## <a name="overview"></a>Descrição Geral

Este tutorial adiciona auto-preenchimento e resultados sugeridos ao anterior Adicionar paging ao tutorial [de resultados de pesquisa.](tutorial-csharp-paging.md)

Uma versão acabada do código neste tutorial pode ser encontrada no seguinte projeto:

* [3-add-typeahead (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/3-add-typeahead)

## <a name="prerequisites"></a>Pré-requisitos

* [Solução 2a-add-paging (GitHub).](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2a-add-paging) Este projeto pode ser a sua própria versão construída a partir do tutorial anterior ou uma cópia do GitHub.

Este tutorial foi atualizado para utilizar o pacote [Azure.Search.Documents (versão 11).](https://www.nuget.org/packages/Azure.Search.Documents/) Para obter uma versão anterior do .NET SDK, consulte [a amostra de código Microsoft.Azure.Search (versão 10).](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10)

## <a name="add-suggestions"></a>Adicionar sugestões

Comecemos pelo caso mais simples de oferecer alternativas ao utilizador: uma lista de resultados sugeridos.

1. No ficheiro index.cshtml, altere `@id` a declaração **TextBoxFor** para **azureautosuggest**.

    ```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

1. Na sequência desta declaração, após o fecho **&lt; /div, &gt;** insira este script. Este script aproveita o [widget autocomplete](https://api.jqueryui.com/autocomplete/) da biblioteca jQuery UI de código aberto para apresentar a lista de resultados sugeridos.

    ```javascript
    <script>
        $("#azureautosuggest").autocomplete({
            source: "/Home/SuggestAsync?highlights=false&fuzzy=false",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

    O ID `"azureautosuggest"` liga o script acima à caixa de pesquisa. A opção de origem do widget é definida como um método de Sugestão que chama a API de sugestão com dois parâmetros de consulta: destaques e **fuzzy**, ambos definidos como **falsos** neste caso. Além disso, é necessário um mínimo de dois caracteres para desencadear a busca.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Adicionar referências aos scripts jQuery à vista

1. Para aceder à biblioteca jQuery, altere a &lt; secção principal do ficheiro de &gt; visualização para o seguinte código:

    ```html
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

2. Como estamos a introduzir uma nova referência jQuery, também precisamos de remover, ou comentar, a referência padrão do jQuery no ficheiro _Layout.cshtml (na pasta **Visualizações/Partilhas).** Localize as seguintes linhas e comente a primeira linha de script como mostrado. Esta alteração evita referências em confronto ao jQuery.

    ```html
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

    Agora podemos usar as funções pré-completas do jQuery.

### <a name="add-the-suggest-action-to-the-controller"></a>Adicione a ação de sugestão ao controlador

1. No controlador doméstico, adicione a ação **SuggestAsync** (após a ação **pageAsync).**

    ```cs
    public async Task<ActionResult> SuggestAsync(bool highlights, bool fuzzy, string term)
    {
        InitSearch();

        // Setup the suggest parameters.
        var options = new SuggestOptions()
        {
            UseFuzzyMatching = fuzzy,
            Size = 8,
        };

        if (highlights)
        {
            options.HighlightPreTag = "<b>";
            options.HighlightPostTag = "</b>";
        }

        // Only one suggester can be specified per index. It is defined in the index schema.
        // The name of the suggester is set when the suggester is specified by other API calls.
        // The suggester for the hotel database is called "sg", and simply searches the hotel name.
        var suggestResult = await _searchClient.SuggestAsync<Hotel>(term, "sg", options).ConfigureAwait(false);

        // Convert the suggested query results to a list that can be displayed in the client.
        List<string> suggestions = suggestResult.Value.Results.Select(x => x.Text).ToList();

        // Return the list of suggestions.
        return new JsonResult(suggestions);
    }
    ```

    O parâmetro **Tamanho** especifica quantos resultados deve ser retornado (se não for especificado, o padrão é 5). Um _sugestivo_ é especificado no índice de pesquisa quando o índice é criado. No índice de hotéis de amostra hospedado pela Microsoft, o nome sugestivo é "sg", e procura fósforos sugeridos exclusivamente no campo **HotelName.**

    A correspondência fuzzy permite que "quase falhas" sejam incluídas na saída, até uma distância de edição. Se o parâmetro **de destaques** for definido como verdadeiro, então as tags HTML ousadas são adicionadas à saída. Vamos definir ambos os parâmetros para verdade na próxima secção.

2. Podes ter alguns erros de sintaxe. Em caso afirmativo, adicione as duas seguintes **usando** declarações no topo do ficheiro.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

3. Execute a aplicação. Obtém uma gama de opções quando entra em "po", por exemplo? Agora tente "pa".

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-po.png" alt-text="Dactilografia *po* revela duas sugestões" border="false":::

    Note que as letras que inseriu _devem_ iniciar uma palavra, e não simplesmente ser incluídas dentro da palavra.

4. No script view, definir **&confuso** para verdade, e executar a app novamente. Agora insira "po". Note que a procura assume que errou com uma carta.
 
    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png" alt-text="Dactilografia *pa* com conjunto felpudo para verdade" border="false":::

    Se estiver interessado, a [sintaxe de consulta Lucene em Azure Cognitive Search](./query-lucene-syntax.md) descreve a lógica usada em pesquisas difusas em detalhe.

## <a name="add-highlighting-to-the-suggestions"></a>Adicionar destaque às sugestões

Podemos melhorar a aparência das sugestões ao utilizador definindo o parâmetro **de destaque para** o verdadeiro. No entanto, primeiro temos de adicionar algum código à vista para exibir o texto arrojado.

1. Na vista (index.cshtml), adicione o seguinte script após o `"azureautosuggest"` script descrito anteriormente.

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

1. Agora altere o ID da caixa de texto para que leia o seguinte.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

1. Execute novamente a aplicação e deverá ver o seu texto inserido arrombado nas sugestões. Tente escrever "pa".
 
    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png" alt-text="Dactilografia *pa* com destaque" border="false":::

   A lógica usada no roteiro de destaque acima não é infalível. Se introduzir um termo que aparece duas vezes no mesmo nome, os resultados arrojados não são bem o que desejaria. Tente escrever "mo".

   Uma das questões que um desenvolvedor precisa de responder é, quando é que um guião funciona "bem o suficiente", e quando deve ser abordado os seus caprichos. Não vamos levar mais destaque neste tutorial, mas encontrar um algoritmo preciso é algo a considerar se realçar não é eficaz para os seus dados. Para mais informações, consulte [hit highlighting](search-pagination-page-layout.md#hit-highlighting).

## <a name="add-autocomplete"></a>Adicionar autocompleto

Outra variação, ligeiramente diferente das sugestões, é a auto-conclusão (por vezes chamada de "tipo de frente") que completa um termo de consulta. Mais uma vez, começaremos com a implementação mais simples, antes de melhorar a experiência do utilizador.

1. Introduza o seguinte script na vista, seguindo os seus scripts anteriores.

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

1. Agora mude a identificação da caixa de texto, para que leia o seguinte.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

1. No controlador doméstico, insira a ação **AutocompleteAsync** após a ação **Do SuggestAsync.**

    ```cs
    public async Task<ActionResult> AutoCompleteAsync(string term)
    {
        InitSearch();

        // Setup the autocomplete parameters.
        var ap = new AutocompleteOptions()
        {
            Mode = AutocompleteMode.OneTermWithContext,
            Size = 6
        };
        var autocompleteResult = await _searchClient.AutocompleteAsync(term, "sg", ap).ConfigureAwait(false);

        // Convert the autocompleteResult results to a list that can be displayed in the client.
        List<string> autocomplete = autocompleteResult.Value.Results.Select(x => x.Text).ToList();

        return new JsonResult(autocomplete);
    }
    ```

    Note que estamos a usar a mesma função *de sugestivo,* chamada "sg", na pesquisa autocompleta como fizemos para sugestões (por isso estamos apenas a tentar completar automaticamente os nomes do hotel).

    Existem uma gama de configurações **AutocompleteMode,** e estamos a usar **oneTermWithContext**. Consulte a [API autocompleta](/rest/api/searchservice/autocomplete) para obter uma descrição de opções adicionais.

1. Execute a aplicação. Note como o leque de opções apresentadas na lista de suspensos são palavras únicas. Tente escrever palavras começando com "re". Note como o número de opções diminui à medida que mais letras são dactilografadas.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png" alt-text="Dactilografia com auto-contícula básica" border="false":::

    Tal como está, o script de sugestões que executou anteriormente é provavelmente mais útil do que este script de auto-premutável. Para tornar a auto-contígnia mais fácil de utilizar, considere usá-la com resultados sugeridos.

## <a name="combine-autocompletion-and-suggestions"></a>Combine auto-privatização e sugestões

Combinar auto-cálculo e sugestões é a mais complexa das nossas opções, e provavelmente proporciona a melhor experiência do utilizador. O que queremos é mostrar, de acordo com o texto que está a ser digitado, é a primeira escolha da Azure Cognitive Search para a preconização automática do texto. Além disso, queremos uma série de sugestões como uma lista de desistentes.

Existem bibliotecas que oferecem esta funcionalidade - muitas vezes chamada de "auto-completação inline" ou um nome semelhante. No entanto, vamos implementar esta funcionalidade de forma nativa para que possa explorar as APIs. Vamos começar a trabalhar no controlador primeiro neste exemplo.

1. Adicione uma ação ao controlador que devolve apenas um resultado de auto-premedição, juntamente com um número especificado de sugestões. Chamaremos a esta ação **AutoCompleteAndSuggestAsync**. No controlador doméstico, adicione a seguinte ação, seguindo as suas outras novas ações.

    ```cs
    public async Task<ActionResult> AutoCompleteAndSuggestAsync(string term)
    {
        InitSearch();

        // Setup the type-ahead search parameters.
        var ap = new AutocompleteOptions()
        {
            Mode = AutocompleteMode.OneTermWithContext,
            Size = 1,
        };
        var autocompleteResult = await _searchClient.AutocompleteAsync(term, "sg", ap);

        // Setup the suggest search parameters.
        var sp = new SuggestOptions()
        {
            Size = 8,
        };

        // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
        // The suggester for the hotel database is called "sg" and simply searches the hotel name.
        var suggestResult = await _searchClient.SuggestAsync<Hotel>(term, "sg", sp).ConfigureAwait(false);

        // Create an empty list.
        var results = new List<string>();

        if (autocompleteResult.Value.Results.Count > 0)
        {
            // Add the top result for type-ahead.
            results.Add(autocompleteResult.Value.Results[0].Text);
        }
        else
        {
            // There were no type-ahead suggestions, so add an empty string.
            results.Add("");
        }

        for (int n = 0; n < suggestResult.Value.Results.Count; n++)
        {
            // Now add the suggestions.
            results.Add(suggestResult.Value.Results[n].Text);
        }

        // Return the list.
        return new JsonResult(results);
    }
    ```

    Uma opção de auto-prengressão é devolvida no topo da lista de **resultados,** seguida de todas as sugestões.

1. Na vista, primeiro implementamos um truque para que uma palavra de auto-conteúdo cinza claro seja prestada sob texto mais ousado sendo introduzido pelo utilizador. HTML inclui posicionamento relativo para este fim. Altere a declaração **TextBoxPara** (e as suas declarações de &lt; div &gt; circundantes) para as seguintes, observando que uma segunda caixa de pesquisa identificada como **por baixo** está bem debaixo da nossa caixa de pesquisa normal, retirando esta caixa de pesquisa de 39 pixels da sua localização padrão!

    ```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

    Reparem que estamos a mudar o ID novamente, para **azureautocomplete** neste caso.

1. Também na vista, introduza o seguinte script, depois de todos os scripts que inseriu até agora. O script é longo e complexo devido à variedade de comportamentos de entrada que lida.

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
                var controllerUrl = "/Home/AutoCompleteAndSuggestAsync?term=" + $("#azureautocomplete").val();
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

    Note como a função **de intervalo** é usada para limpar o texto subjacente quando já não corresponde ao que o utilizador está a escrever, e também para definir o mesmo caso (superior ou inferior) como o utilizador está a escrever (como "pa" corresponde a "PA", "pA", "Pa" ao procurar), de modo a que o texto sobreposto seja limpo.

    Leia os comentários no script para obter um entendimento mais completo.

1. Por último, precisamos de fazer um pequeno ajuste a duas classes HTML para torná-los transparentes. Adicione a seguinte linha às aulas **searchBoxForm** e **searchBox,** no ficheiro .css hotéis.

    ```html
    background: rgba(0,0,0,0);
    ```

1. Agora executar a aplicação. Introduza "pa" na caixa de pesquisa. Você recebe "palácio" como a sugestão autocompleta, juntamente com dois hotéis que contêm "pa"?

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png" alt-text="Dactilografia com autocompleto inline e sugestões" border="false":::

1. Tente fazer tabbing para aceitar a sugestão de precontamento automático e tente selecionar sugestões utilizando as teclas de seta e tecla do separador, e tente novamente usando o rato e um simples clique. Verifique se o script lida com todas estas situações de forma ordenada.

    Pode decidir que é mais simples carregar numa biblioteca que lhe oferece esta funcionalidade, mas agora sabe pelo menos uma forma de obter a auto-premôdo inline para funcionar.

## <a name="takeaways"></a>Conclusões

Considere os seguintes takeaways deste projeto:

* A presaltutação automática (também conhecida como "type-ahead") e sugestões podem permitir ao utilizador escrever apenas algumas chaves para localizar exatamente o que quer.
* A auto-privatização e as sugestões que trabalham em conjunto podem proporcionar uma experiência de utilizador rica.
* Teste sempre funções de auto-completação com todas as formas de entrada.
* A utilização da função **setInterval** pode ser útil na verificação e correção de elementos de UI.

## <a name="next-steps"></a>Passos seguintes

No próximo tutorial, temos uma outra forma de melhorar a experiência do utilizador, usando facetas para reduzir as pesquisas com um único clique.

> [!div class="nextstepaction"]
> [C# Tutorial: Use facetas para ajudar na navegação - Azure Cognitive Search](tutorial-csharp-facets.md)
