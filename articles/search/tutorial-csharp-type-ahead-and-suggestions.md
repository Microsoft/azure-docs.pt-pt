---
title: C#tutorial sobre o preenchimento automático e sugestões - Azure Search
description: Este tutorial baseia-se no projeto "Paginação - Azure Search de resultados de pesquisa", para adicionar o preenchimento automático e sugestões. O objetivo é uma experiência de usuário mais sofisticada. Saiba como combinar na lista pendente de sugestões com preenchimento automático de inline.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 05/01/2019
ms.openlocfilehash: 48dde6ebe19f5a6c14008fd4e5e27a9fb0151928
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304142"
---
# <a name="c-tutorial-add-autocompletion-and-suggestions---azure-search"></a>C#Tutorial: Adicionar preenchimento automático e sugestões - Azure Search

Aprenda a implementar o preenchimento automático (antecipada e sugestões) quando um utilizador começa a escrever na sua caixa de pesquisa. Neste tutorial, iremos irá mostrar resultados antecipada e os resultados de sugestão em separado, em seguida, mostrar um método de combiná-los para criar uma experiência de usuário mais sofisticada. Um utilizador só poderá ter de escrever duas ou três chaves para localizar todos os resultados que estão disponíveis. Este tutorial baseia-se para o projeto de paginação criado no [ C# Tutorial: Resultados da pesquisa paginação - Azure Search](tutorial-csharp-paging.md) tutorial.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Adicionar sugestões
> * Adicionar detetor de ocorrências para as sugestões
> * Adicionar preenchimento automático
> * Combinar o preenchimento automático e sugestões

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

Tem a [ C# Tutorial: Resultados da pesquisa paginação - Azure Search](tutorial-csharp-paging.md) projeto em funcionamento. Este projeto pode ser a sua própria versão, o que concluiu o tutorial anterior, ou instalá-lo a partir do GitHub: [Criar a primeira aplicação](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="add-suggestions"></a>Adicionar sugestões

Vamos começar com o caso mais simples da oferta de cópia de segurança alternativas para o utilizador: uma lista pendente de sugestões.

1. No ficheiro Index. cshtml, alterar os **TextBoxFor** instrução para o seguinte.

    ```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

    O importante aqui é que poderíamos ter definido o ID da caixa de pesquisa para **azureautosuggest**.

2. Após esta instrução, após o fecho  **&lt;/div&gt;** , introduza este script.

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

    Vamos ligar este script para a caixa de pesquisa via o mesmo ID. Além disso, não é necessário um mínimo de dois caracteres para acionar a pesquisa e chamamos o **sugere** ação no controlador de casa com dois parâmetros de consulta: **destaca** e **difusa**, ambos definido como false nesta instância.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Adicionar referências aos scripts de jquery para a vista

A função de conclusão automática chamada no script acima não é algo que temos de criar como ele está disponível na biblioteca do jquery. 

1. Para acessar a biblioteca jquery, altere a &lt;head&gt; secção do ficheiro de vista para o código a seguir.

    ```cs
    <head>
        <meta charset="utf-8">
        <title>Autocomplete demo</title>
        <link href="https://code.jquery.com/ui/1.10.4/themes/ui-lightness/jquery-ui.css"
              rel="stylesheet">
        <script src="https://code.jquery.com/jquery-1.10.2.js"></script>
        <script src="https://code.jquery.com/ui/1.10.4/jquery-ui.js"></script>

        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

2. Também é necessário remover ou comentar, uma linha de referência a jquery no ficheiro Layout. cshtml (no **Views/Shared** pasta). Localize as seguintes linhas e comente a primeira linha do script, conforme mostrado. Esta alteração evita opostos referências a jquery.

    ```html
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

    Agora podemos usar as funções do jquery de conclusão automática predefinidas.

### <a name="add-the-suggest-action-to-the-controller"></a>Adicione a ação de sugerido para o controlador

1. No controlador de raiz, adicione a **sugere** ação (Digamos, após o **página** ação).

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

            // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and simply searches the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", parameters);

            // Convert the suggest query results to a list that can be displayed in the client.
            List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();

            // Return the list of suggestions.
            return new JsonResult(suggestions);
        }
    ```

    O **superior** parâmetro especifica quantos resultados a devolver (se não for especificado, a predefinição é 5). R _sugestor_ é especificada no índice do Azure, que é feito quando os dados são configurados e não em que uma aplicação de cliente, como neste tutorial. Neste caso, o sugestor é chamada de "sg", e ele pesquisa a **HotelName** campo - nada mais. 

    Correspondência difusa permite "near ausências" a serem incluídos na saída. Se o **destaca** parâmetro estiver definido como true, em seguida, tags de HTML em negrito são adicionados à saída. Definimos esses dois parâmetros como true na próxima seção.

2. Poderá receber alguns erros de sintaxe. Nesse caso, adicione as duas seguintes **usando** declarações na parte superior do ficheiro.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

3. Execute a aplicação. Obtém uma variedade de opções ao introduzir "oC", por exemplo? Experimente agora "pa".

    ![Digitar "oC" revela duas sugestões](./media/tutorial-csharp-create-first-app/azure-search-suggest-po.png)

    Tenha em atenção que as letras introduz _tem_ iniciar uma palavra e não simplesmente ser incluídas dentro do word.

4. No script de modo de exibição, defina **& difusa** como true e execute novamente a aplicação. Agora, introduza "oC". Tenha em atenção que a pesquisa parte do princípio de que tem uma letra mal!
 
    ![Escrever "pa" com difusa definido como verdadeiro](./media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png)

    Se estiver interessado, o [sintaxe de consulta Lucene no Azure Search](https://docs.microsoft.com/azure/search/query-lucene-syntax) descreve a lógica usada nas pesquisas difusas detalhadamente.

## <a name="add-highlighting-to-the-suggestions"></a>Adicionar detetor de ocorrências para as sugestões

Podemos melhorar a aparência das sugestões ao usuário um pouco, definindo a **destaca** parâmetro como true. No entanto, primeiro, precisamos adicionar um código para a vista para apresentar o texto em negrito.

1. Na vista de (Index. cshtml), adicione o seguinte script depois do **azureautosuggest** script que tenha introduzido acima.

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

2. Agora, altere o ID da caixa de texto para que ele lê da seguinte forma.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. Execute novamente a aplicação e deverá ver o negrito de texto introduzido nas sugestões. Por exemplo, experimente escrever "pa".
 
    ![Escrever "pa" com o realce](./media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png)

4. A lógica usada no script de realce acima não é à prova de falhas. Se introduzir um termo que aparece duas vezes no mesmo nome, os resultados em negrito não são muito o que desejaria. Experimente escrever "mês".

    Uma das perguntas que um desenvolvedor precisa responder é, quando é um script funcionar "bem suficiente", e quando deve suas sutilezas resolvido. Não extrairemos Realce qualquer ainda mais neste tutorial, mas encontrar que um algoritmo preciso é algo a ter em consideração se demorar realce ainda mais.

## <a name="add-autocompletion"></a>Adicionar preenchimento automático

Variação de outro, o que é ligeiramente diferente do sugestões, é o preenchimento automático (por vezes denominado "antecipada"). Novamente, começaremos com a implementação mais simples, antes de mover para melhorar a experiência do usuário.

1. Introduza o seguinte script para a vista, os scripts anteriores a seguir.

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

2. Agora, altere o ID da caixa de texto, para que ele lê da seguinte forma.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. No controlador da página inicial, tem de introduzir o **preenchimento automático** ação, digamos, abaixo da **sugira** ação.

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

    Tenha em atenção que estamos a utilizar o mesmo *sugestor* função, chamado "sg", na pesquisa de conclusão automática, como fizemos para sugestões (portanto, estamos apenas a tentar alterem os nomes de hotel).

    Existem diversas **AutocompleteMode** configurações e estão a utilizar **OneTermWithContext**. Consulte a [preenchimento automático do Azure](https://docs.microsoft.com/rest/api/searchservice/autocomplete) para obter uma descrição do intervalo de opções aqui.

4. Execute a aplicação. Observe como o intervalo das opções apresentadas na lista pendente são palavras individuais. Experimente escrever palavras começando com "re". Observe como o número de opções reduz conforme mais letras são escritas.

    ![Escrever com preenchimento automático básico](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png)

    Como está, o script de sugestões que executou anteriormente é provavelmente mais úteis do que este script de preenchimento automático. Para tornar o preenchimento automático mais amigável, ela é adicionada melhor para a pesquisa de sugestão.

## <a name="combine-autocompletion-and-suggestions"></a>Combinar o preenchimento automático e sugestões

A combinação de preenchimento automático e sugestões é a mais complexa das nossas opções e provavelmente fornece a melhor experiência de utilizador. O que queremos é a exibição, inline com o texto está sendo escrito, a primeira opção do Azure Search para autocompleting o texto. Além disso, queremos uma variedade de sugestões como uma lista pendente.

Há bibliotecas que oferecer essa funcionalidade – muitas vezes chamada "inline preenchimento automático" ou um nome semelhante. No entanto, vamos implementar nativamente esta funcionalidade, para que possa ver o que está acontecendo. Vamos começar o trabalho no controlador de primeiro neste exemplo.

1. É necessário adicionar uma ação para o controlador que retorna apenas um resultado de preenchimento automático, juntamente com um número especificado de sugestões. Vamos chamar esta ação **AutocompleteAndSuggest**. No controlador de raiz, adicione a seguinte ação, as outras novas ações a seguir.

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

    Uma opção de preenchimento automático é devolvida na parte superior a **resultados** lista, seguidos de todas as sugestões.

2. Na vista de, primeiro implementamos um truque para que uma palavra de preenchimento automático de cinza claro é processada direita em bolder texto a ser introduzido pelo utilizador. HTML inclui o posicionamento relativo para esta finalidade. Alteração o **TextBoxFor** instrução (e ao seu redor &lt;div&gt; instruções) ao seguinte, observar que uma segunda caixa de pesquisa identificada como **por baixo** é em nosso caixa de pesquisa normal, ao extrair esta pesquisa pixels de 39 caixa do seu local padrão!

    ```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

    Observe que estamos a alterar o ID de novamente, para **azureautocomplete** neste caso.

3. Também na exibição, introduza o seguinte script, afinal os scripts que introduziu até agora. Há muito a ele.

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

    Observe o uso inteligente do **intervalo** função para ambos apague o texto subjacente, quando ele já não coincide com o que o usuário está digitando, e também definir as maiúsculas (canto superior ou inferior) como o usuário está digitando (como "pa" corresponde a "PA", "pA", "Pa" quando Pesquisar), para que o texto overlaid é claro.

    Leia os comentários no script para obter uma compreensão mais completa.

4. Por fim, precisamos de fazer um pequeno ajuste para duas classes HTML para torná-los transparente. Adicione a seguinte linha para o **searchBoxForm** e **caixa de pesquisa** classes, no ficheiro hotels.css.

    ```html
        background: rgba(0,0,0,0);
    ```

5. Agora, execute a aplicação. Introduza "pa" na caixa de pesquisa. Obtém "palace" como a sugestão de preenchimento automático, juntamente com dois hotéis que contêm "pa"?

    ![Escrever com preenchimento automático de inline e sugestões](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png)

6. Experimente a tabulação para aceitar a sugestão de preenchimento automático e experimente selecionar sugestões usando as teclas de seta e a tecla tab e tente de novo com o mouse e um único clique. Certifique-se de que o script processa de forma organizada todas essas situações.

    Pode decidir que é mais simples carregar numa biblioteca que oferece esse recurso para, mas agora sabe, pelo menos, uma forma de preenchimento automático de inline para funcionar!

## <a name="takeaways"></a>Conclusões

Considere o seguintes das novidades deste projeto:

* Preenchimento automático (também conhecido como "antecipada") e sugestões podem permitir que o usuário digitar apenas algumas chaves para localizar exatamente o que desejam.
* Preenchimento automático e sugestões a trabalhar em conjunto, podem fornecer uma experiência de usuário.
* Teste sempre as funções de preenchimento automático com todos os formulários de entrada.
* Utilizar o **setInterval** função pode ser útil para verificar e corrigir os elementos de interface do Usuário.

## <a name="next-steps"></a>Passos Seguintes

Um dos problemas com preenchimento automático e sugestões é que envolvem chamadas repetidas para o servidor (um em cada traço chave após o número mínimo de carateres digitado é alcançado). Se repetem estes resultados de chamadas mais lentas do que as respostas esperadas no, a experiência do usuário diminui. A utilização de facetas fornece uma alternativa interessante para evitar estas chamadas repetidas, o que veremos seguinte.

> [!div class="nextstepaction"]
> [C#Tutorial: Utilizar facetas para uma eficiência de navegação e rede - Azure Search](tutorial-csharp-facets.md)


