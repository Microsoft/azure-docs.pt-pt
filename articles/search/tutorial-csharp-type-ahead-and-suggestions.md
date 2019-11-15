---
title: C#tutorial sobre preenchimento automático e sugestões
titleSuffix: Azure Cognitive Search
description: Este tutorial demonstra a conclusão automática e sugestões como uma maneira de coletar entrada de termo de pesquisa de usuários usando a lista suspensa. Ele se baseia em um projeto de hotéis existente.
manager: nitinme
author: PeterTurcan
ms.author: v-pettur
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: b542476ac1c9b6d4368d97eb4db76518eb2dba03
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114555"
---
# <a name="c-tutorial-add-autocompletion-and-suggestions---azure-cognitive-search"></a>C#Tutorial: Adicionar AutoCompletar e sugestões-Azure Pesquisa Cognitiva

Saiba como implementar o preenchimento automático (tipo-antecipado e sugestões) quando um usuário começa a digitar na caixa de pesquisa. Neste tutorial, mostraremos resultados de tipo antecipado e resultados de sugestão separadamente e, em seguida, mostraremos um método de combiná-los para criar uma experiência de usuário mais rica. Um usuário pode precisar apenas digitar duas ou três chaves para localizar todos os resultados disponíveis. Este tutorial se baseia no projeto de paginação criado no [ C# tutorial: paginação dos resultados da pesquisa-tutorial de pesquisa cognitiva do Azure](tutorial-csharp-paging.md) .

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Adicionar sugestões
> * Adicionar realce às sugestões
> * Adicionar preenchimento automático
> * Combinar preenchimento automático e sugestões

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

Faça o [ C# tutorial: paginação dos resultados da pesquisa-o projeto pesquisa cognitiva do Azure](tutorial-csharp-paging.md) em funcionamento. Este projeto pode ser sua própria versão, que você concluiu no tutorial anterior ou o instalou no GitHub: [criar primeiro aplicativo](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="add-suggestions"></a>Adicionar sugestões

Vamos começar com o caso mais simples de oferecer alternativas para o usuário: uma lista suspensa de sugestões.

1. No arquivo index. cshtml, altere a instrução **TextBoxFor** para o seguinte.

    ```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

    A chave aqui é que definimos a ID da caixa de pesquisa como **azureautosuggest**.

2. Seguindo essa instrução, após o fechamento **&lt;/div&gt;** , insira esse script.

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

    Conectamos esse script à caixa de pesquisa por meio da mesma ID. Além disso, um mínimo de dois caracteres é necessário para disparar a pesquisa e chamamos a ação de **sugestão** no controlador Home com dois parâmetros de consulta: **destaques** e **difuso**, ambos definidos como false nessa instância.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Adicionar referências a scripts jQuery à exibição

A função de preenchimento automático chamada no script acima não é algo que precisamos nos escrever, pois está disponível na biblioteca jQuery. 

1. Para acessar a biblioteca jQuery, altere a seção &lt;Head&gt; do arquivo de exibição para o código a seguir.

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

2. Também precisamos remover, ou comentar, uma linha referenciando jQuery no arquivo _Layout. cshtml (na pasta **views/Shared** ). Localize as linhas a seguir e comente a primeira linha do script, conforme mostrado. Essa alteração evita referências conflitantes ao jQuery.

    ```html
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

    Agora, podemos usar as funções predefinidas de AutoCompletar jQuery.

### <a name="add-the-suggest-action-to-the-controller"></a>Adicionar a ação sugerir ao controlador

1. No controlador inicial, adicione a ação **sugerir** (digamos, após a ação da **página** ).

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

    O parâmetro **Top** especifica quantos resultados serão retornados (se não for especificado, o padrão será 5). Um _Sugestor_ é especificado no índice do Azure, que é feito quando os dados são configurados, e não por um aplicativo cliente, como este tutorial. Nesse caso, o Sugestor é chamado de "SG" e pesquisa o campo **hotelname** – nada mais. 

    A correspondência difusa permite que "próximos erros" sejam incluídos na saída. Se o parâmetro **realces** for definido como true, as marcas HTML em negrito serão adicionadas à saída. Definiremos esses dois parâmetros como true na próxima seção.

2. Você pode obter alguns erros de sintaxe. Nesse caso, adicione as duas instruções **using** a seguir à parte superior do arquivo.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

3. Execute o aplicativo. Você Obtém uma variedade de opções ao inserir "po", por exemplo? Agora experimente o "PA".

    ![Digitar "po" revela duas sugestões](./media/tutorial-csharp-create-first-app/azure-search-suggest-po.png)

    Observe que as letras inseridas _devem_ iniciar uma palavra e não simplesmente ser incluídas na palavra.

4. No script de exibição, defina **& difusa** como true e execute o aplicativo novamente. Agora, insira "po". Observe que a pesquisa pressupõe que você recebeu uma letra errada!
 
    ![Digitando "PA" com difusa definida como true](./media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png)

    Se você estiver interessado, a [sintaxe de consulta Lucene no Azure pesquisa cognitiva](https://docs.microsoft.com/azure/search/query-lucene-syntax) descreve a lógica usada em pesquisas difusas em detalhes.

## <a name="add-highlighting-to-the-suggestions"></a>Adicionar realce às sugestões

Podemos melhorar a aparência das sugestões para o usuário um pouco, definindo o parâmetro **realces** como true. No entanto, primeiro precisamos adicionar um código à exibição para exibir o texto em negrito.

1. No modo de exibição (index. cshtml), adicione o script a seguir após o script **azureautosuggest** que você inseriu acima.

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

2. Agora, altere a ID da caixa de texto para que ela seja lida da seguinte maneira.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. Execute o aplicativo novamente e você deverá ver o texto digitado em negrito nas sugestões. Digamos, tente digitar "PA".
 
    ![Digitando "PA" com realce](./media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png)

4. A lógica usada no script de realce acima não é provante. Se você inserir um termo que aparece duas vezes no mesmo nome, os resultados em negrito não serão exatamente o que você desejaria. Tente digitar "mo".

    Uma das perguntas que um desenvolvedor precisa responder é, quando um script funciona "bem suficiente" e quando suas sutilezas devem ser resolvidas. Não usaremos mais detalhes neste tutorial, mas encontrar um algoritmo preciso é algo a ser levado em consideração se estiver demorando mais.

## <a name="add-autocompletion"></a>Adicionar preenchimento automático

Outra variação, que é um pouco diferente das sugestões, é o preenchimento automático (às vezes chamado de "tipo antecipado"). Novamente, começaremos com a implementação mais simples, antes de continuar a melhorar a experiência do usuário.

1. Insira o script a seguir na exibição, seguindo os scripts anteriores.

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

2. Agora, altere a ID da caixa de texto para que ela seja lida da seguinte maneira.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. No controlador doméstico, precisamos inserir a ação de **preenchimento automático** , digamos, abaixo da ação **sugerir** .

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

    Observe que estamos usando a mesma função de *sugestão* , chamada "SG", na pesquisa de preenchimento automático, como fizemos para sugestões (portanto, estamos apenas tentando preencher os nomes de Hotel.

    Há uma variedade de configurações de **AutocompleteMode** e estamos usando **OneTermWithContext**. Consulte [preenchimento automático do Azure](https://docs.microsoft.com/rest/api/searchservice/autocomplete) para obter uma descrição do intervalo de opções aqui.

4. Execute o aplicativo. Observe como o intervalo de opções exibido na lista suspensa são palavras únicas. Tente digitar palavras começando com "re". Observe como o número de opções reduz à medida que mais letras são digitadas.

    ![Digitando com preenchimento automático básico](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png)

    Como se encontra, o script de sugestões executado anteriormente é provavelmente mais útil do que esse script de autocompletação. Para tornar o preenchimento automático mais fácil de usar, ele é melhor adicionado à pesquisa de sugestões.

## <a name="combine-autocompletion-and-suggestions"></a>Combinar preenchimento automático e sugestões

A combinação de preenchimento automático e sugestões é a mais complexa de nossas opções e, provavelmente, fornece a melhor experiência do usuário. O que queremos é exibir, embutido com o texto que está sendo digitado, a primeira opção do Azure Pesquisa Cognitiva para concluir o texto automaticamente. Além disso, queremos um intervalo de sugestões como uma lista suspensa.

Há bibliotecas que oferecem essa funcionalidade – geralmente chamadas de "preenchimento automático embutido" ou um nome semelhante. No entanto, vamos implementar nativamente esse recurso, para que você possa ver o que está acontecendo. Vamos começar a trabalhar no controlador primeiro neste exemplo.

1. Precisamos adicionar uma ação ao controlador que retorna apenas um resultado de preenchimento automático, juntamente com um número especificado de sugestões. Chamaremos essa ação de **AutocompleteAndSuggest**. No controlador inicial, adicione a seguinte ação, seguindo as outras novas ações.

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

    Uma opção de autocompletação é retornada na parte superior da lista de **resultados** , seguida por todas as sugestões.

2. Na exibição, primeiro implementamos um truque para que uma palavra de preenchimento automático cinza claro seja renderizada diretamente sob o texto com negrito sendo inserido pelo usuário. O HTML inclui o posicionamento relativo para essa finalidade. Altere a instrução **TextBoxFor** (e suas instruções &lt;div&gt;) para o seguinte, observando que uma segunda caixa de pesquisa identificada como **abaixo** está diretamente em nossa caixa de pesquisa normal, puxando esta caixa de pesquisa 39 pixels para fora de seu local padrão!

    ```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

    Observe que estamos alterando a ID novamente, para **azureautocomplete** nesse caso.

3. Além disso, na exibição, insira o script a seguir, após todos os scripts que você inseriu até agora. Há muito a respeito.

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

    Observe o uso inteligente da função **Interval** para limpar o texto subjacente quando ele não corresponde mais ao que o usuário está digitando e também para definir o mesmo caso (superior ou inferior) quando o usuário está digitando (como "PA" corresponde a "PA", "PA", "PA" ao Pesquisar), para que o texto sobreposto seja limpo.

    Leia os comentários no script para obter uma compreensão mais completa.

4. Por fim, precisamos fazer um pequeno ajuste em duas classes HTML para torná-las transparentes. Adicione a seguinte linha às classes **searchBoxForm** e **searchBox** , no arquivo Hotéis. css.

    ```html
        background: rgba(0,0,0,0);
    ```

5. Agora, execute o aplicativo. Digite "PA" na caixa de pesquisa. Você obtém "Palace" como sugestão de preenchimento automático, juntamente com dois hotéis que contêm "PA"?

    ![Digitação com preenchimento automático embutido e sugestões](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png)

6. Tente tabulação para aceitar a sugestão de preenchimento automático e tente selecionar sugestões usando as teclas de direção e a tecla Tab e tente novamente usando o mouse e um único clique. Verifique se o script manipula todas essas situações de uma boa.

    Você pode decidir que é mais simples carregar em uma biblioteca que oferece esse recurso para você, mas agora você conhece pelo menos uma maneira de colocar o preenchimento automático embutido em funcionamento!

## <a name="takeaways"></a>Conclusões

Considere as seguintes alternativas deste projeto:

* O preenchimento automático (também conhecido como "tipo antecipado") e as sugestões podem permitir que o usuário digite apenas algumas teclas para localizar exatamente o que desejam.
* O preenchimento automático e as sugestões que trabalham juntas podem fornecer uma experiência de usuário avançada.
* Sempre testar funções de conclusão automática com todas as formas de entrada.
* O uso da função **setInterval** pode ser útil na verificação e correção de elementos da interface do usuário.

## <a name="next-steps"></a>Passos seguintes

No próximo tutorial, temos uma olhada em outra maneira de melhorar a experiência do usuário, usando facetas para restringir as pesquisas com um único clique.

> [!div class="nextstepaction"]
> [C#Tutorial: usar facetas para auxiliar na navegação – Azure Pesquisa Cognitiva](tutorial-csharp-facets.md)


