---
title: Adicione autocompleta e sugestões numa caixa de pesquisa
titleSuffix: Azure Cognitive Search
description: Ative ações de consulta de pesquisa como você tipo em Pesquisa Cognitiva Azure, criando sugestionantes e formulando pedidos que completem automaticamente uma caixa de pesquisa com termos ou frases terminadas. Também pode devolver os fósforos sugeridos.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: 8b64a583c11e794c30e1de12eb66941874a25462
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262230"
---
# <a name="add-suggestions-or-autocomplete-to-your-azure-cognitive-search-application"></a>Adicione sugestões ou complete automaticamente a sua aplicação de Pesquisa Cognitiva Azure

Este exemplo demonstra uma caixa de pesquisa que suporta comportamentos de pesquisa como você tipo. Existem duas funcionalidades, que podem utilizar em conjunto ou separadamente:

+ *Sugestões* geram resultados de pesquisa à medida que escreve, onde cada sugestão é um único resultado ou documento de pesquisa do índice que corresponde ao que escreveu até agora. 

+ *A autocompleta* gera consultas ao "terminar" a palavra ou frase. Em vez de devolver resultados, completa uma consulta, que pode então executar para devolver resultados. Tal como acontece com as sugestões, uma palavra ou frase completa numa consulta baseia-se numa correspondência no índice. O serviço não vai oferecer consultas que devolvam zero resultados no índice.

O código da amostra demonstra tanto sugestões como autocompletas, tanto nas versões linguísticas C# como javaScript. 

Os desenvolvedores de C# podem passar por uma aplicação baseada em MVC ASP.NET que utiliza a [Pesquisa Cognitiva Azure .NET SDK](https://aka.ms/search-sdk). A lógica para fazer chamadas de consulta automática e sugerida pode ser encontrada no ficheiro HomeController.cs. 

Os desenvolvedores javaScript encontrarão lógica de consulta equivalente em IndexJavaScript.cshtml, que inclui chamadas diretas para a [API de Pesquisa Cognitiva Azure](https://docs.microsoft.com/rest/api/searchservice/). 

Para ambas as versões linguísticas, a experiência do utilizador frontal baseia-se nas bibliotecas [jQuery UI](https://jqueryui.com/autocomplete/) e [XDSoft.](https://xdsoft.net/jqplugins/autocomplete/) Utilizamos estas bibliotecas para construir a caixa de pesquisa suportando ambas as sugestões e autocompletas. As inputs recolhidas na caixa de pesquisa são emparelhadas com sugestões e ações autocompletas, tais como as definidas em HomeController.cs ou IndexJavaScript.cshtml.

## <a name="prerequisites"></a>Pré-requisitos

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)

Um serviço de Pesquisa Cognitiva Azure é opcional para este exercício porque a solução utiliza um serviço hospedado e um índice de demonstração NYCJobs. Se quiser construir este índice no seu próprio serviço de pesquisa, consulte o [índice Create NYC Jobs](#configure-app) para obter instruções. Caso contrário, pode utilizar o serviço e o índice existentes para apoiar uma aplicação de cliente JavaScript.

<!-- The sample is comprehensive, covering suggestions, autocomplete, faceted navigation, and client-side caching. Review the readme and comments for a full description of what the sample offers. -->

## <a name="download-files"></a>Transferir ficheiros

O código de amostra para os desenvolvedores c# e javaScript pode ser encontrado na [pasta DotNetHowToAutoComplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) do repositório GitHub de início de **azure-samples/search-dotnet-start-start.**

A amostra tem como alvo um serviço de pesquisa de demonstração existente e um índice pré-construído povoado com [dados de demonstração de NYCJobs.](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) O índice NYCJobs contém uma [construção suggester,](index-add-suggesters.md)que é um requisito para usar sugestões ou auto-completar.

## <a name="run-the-sample"></a>Executar o exemplo

1. Abra **AutocompleteTutorial.sln** no Estúdio Visual. A solução contém um projeto ASP.NET MVC com ligação a um serviço e índice de pesquisa existentes.

1. Atualizar os pacotes NuGet:

   1. No Solution Explorer, clique no **dotNetHowToAutoComplete** e selecione **Gerir pacotes NuGet**.  
   1. Selecione o separador **'Actualizações',** selecione todos os pacotes e clique em **Atualizar**. Aceite quaisquer contratos de licença. Mais de um passe pode ser necessário para atualizar todos os pacotes.

1. Pressione f5 para executar o projeto e carregue a página num browser.

Na parte superior, verá uma opção para selecionar C# ou JavaScript. A opção C# entra no HomeController a partir do navegador e utiliza o Azure Cognitive Search .NET SDK para recuperar resultados. 

A opção JavaScript chama a API de pesquisa cognitiva Azure diretamente do navegador. Esta opção terá normalmente um desempenho visivelmente melhor, uma vez que retira o controlador do fluxo. Pode escolher a opção adequada às suas necessidades e preferências de linguagem. Existem vários exemplos autocompletos na página com alguma orientação para cada um. Cada exemplo tem um texto recomendado que pode experimentar.  

![Página de arranque de amostra](media/search-autocomplete-tutorial/startup-page.png "Página de arranque de amostra no local anfitrião")

Tente escrever algumas letras em cada caixa de pesquisa para ver o que acontece.

## <a name="query-inputs"></a>Inputs de consulta

Tanto para as versões C# como JavaScript, a implementação da caixa de pesquisa é exatamente a mesma. 

Abra o ficheiro **Index.cshtml** sob a pasta \Views\Home para ver o código:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Este exemplo é uma simples caixa de texto de entrada com uma classe para styling, um ID a ser referenciado por JavaScript, e texto de espaço reservado.  A magia está no JavaScript embutido.

A amostra de língua C# utiliza javaScript em Index.cshtml para alavancar a [biblioteca jQuery UI Autocomplete](https://jqueryui.com/autocomplete/). Esta biblioteca adiciona a experiência autocompleta à caixa de pesquisa fazendo chamadas assíncronas para o controlador MVC para recuperar sugestões. A versão javaScript idioma está em IndexJavaScript.cshtml. Inclui o script abaixo para a barra de pesquisa, bem como as chamadas REST API para A Pesquisa Cognitiva Azure.

Vamos olhar para o código JavaScript para o primeiro exemplo, que chama a função jQuery UI Autocomplete, passando um pedido de sugestões:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

O código acima é executado no navegador na carga da página para configurar o jQuery UI autocompleto para a caixa de entrada "exemplo1a".  `minLength: 3` garante que as recomendações apenas serão apresentadas quando existe, pelo menos, três carateres na caixa de pesquisa.  O valor de origem é importante:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

A linha acima indica a função jQuery UI Autocomplete onde obter a lista de itens para mostrar sob a caixa de pesquisa. Uma vez que este projeto é um projeto MVC, chama a função Sugerir em HomeController.cs que contém a lógica para devolver sugestões de consulta (mais sobre sugerir na secção seguinte). Esta função também passa alguns parâmetros para controlar os destaques, a correspondência difusa e o termo. O JavaScript API autocompleto adiciona o termo parâmetro.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Expandir o exemplo para suportar a correspondência difusa

A pesquisa difusa permite-lhe obter os resultados com base em correspondências aproximadas, mesmo que o utilizador escreva uma palavra incorretamente na caixa de pesquisa. Embora não seja necessário, melhora significativamente a robustez de uma experiência tipo à frente. Vamos experimentar isto ao alterar a linha de origem para ativar a correspondência difusa.

Altere a seguinte linha de:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

para este:

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

Inicie a aplicação ao premir F5.

Experimente escrever algo como "execativo" e observe como são devolvidos resultados para "executivo", mesmo que não sendo uma correspondência exata das letras que escreveu.

### <a name="jquery-autocomplete--backed-by-azure-cognitive-search-autocomplete"></a>jQuery Autocomplete apoiado por Azure Cognitive Search autocomplete

Até agora, o código UX de pesquisa foi centrado nas sugestões. O bloco de código seguinte mostra a função jQuery UI Autocomplete (linha 91 em index.cshtml), passando num pedido para azure Cognitive Search autocomplete:

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#example2").autocompleteInline({
        appendMethod: "replace",
        source: [
            function (text, add) {
                if (!text) {
                    return;
                }

                $.getJSON("/home/autocomplete?term=" + text, function (data) {
                    if (data && data.length > 0) {
                        currentSuggestion2 = data[0];
                        add(data);
                    }
                });
            }
        ]
    });

    // complete on TAB and clear on ESC
    $("#example2").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#example2").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#example2").val("");
        }
    });
});
```

## <a name="c-example"></a>C# exemplo

Agora que revimos o código JavaScript para a página web, vamos ver o código do controlador do lado do servidor C# que realmente recupera os jogos sugeridos usando o Azure Cognitive Search .NET SDK.

Abra o ficheiro **HomeController.cs** sob o diretório dos Controladores. 

A primeira coisa que se nota é um `InitSearch`método no topo da classe chamado . Este método cria um cliente índice HTTP autenticado para o serviço de Pesquisa Cognitiva Azure. Para mais informações, consulte [Como utilizar a Pesquisa Cognitiva Azure a partir de uma aplicação .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

Na linha 41, note a função Sugerir. Baseia-se nas Extensões de Operações de [Documentos.Sugerir método](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet).

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult resp = _indexClient.Documents.Suggest(term, "sg", sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = resp.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

A função Suggest aceita dois parâmetros que determinam se são devolvidos detetores de ocorrências ou é utilizada a correspondência difusa além da introdução do termo de pesquisa. O método cria um [objeto SuggestParameters,](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet)que é depois passado para a API sugestiva. Em seguida, o resultado é convertido em JSON, para que possa ser mostrado no cliente.

Na linha 69, note a função Autocomplete. Baseia-se no [método DocumentsOperationsExtensions.Autocomplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet).

```csharp
public ActionResult AutoComplete(string term)
{
    InitSearch();
    //Call autocomplete API and return results
    AutocompleteParameters ap = new AutocompleteParameters()
    {
        AutocompleteMode = AutocompleteMode.OneTermWithContext,
        UseFuzzyMatching = false,
        Top = 5
    };
    AutocompleteResult autocompleteResult = _indexClient.Documents.Autocomplete(term, "sg", ap);

    // Conver the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

A função Autocomplete toma a entrada do termo de pesquisa. O método cria um [objeto AutoCompleteParameters](https://docs.microsoft.com/rest/api/searchservice/autocomplete). Em seguida, o resultado é convertido em JSON, para que possa ser mostrado no cliente.

(Opcional) Adicione um ponto de interrupção à função Suggest e percorra o código. Note a resposta devolvida pelo SDK e como é convertida para o resultado devolvido do método.

Os outros exemplos na página seguem o mesmo padrão para adicionar destaque saqueado e facetas para suportar o cache do lado do cliente dos resultados autocompletos. Reveja cada um deles para compreender como funcionam e como aproveitá-los na sua experiência de pesquisa.

## <a name="javascript-example"></a>Exemplo javaScript

Uma implementação javaScript de autocompleta e sugestões chama a API REST, usando um URI como fonte para especificar o índice e o funcionamento. 

Para rever a implementação do JavaScript, abra **o IndexJavaScript.cshtml**. Note que a função jQuery UI Autocomplete também é usada para a caixa de pesquisa, recolhendo inputs de prazo de pesquisa e fazendo chamadas assíncronas para Azure Cognitive Search para recuperar correspondências sugeridas ou termos concluídos. 

Vamos analisar o código JavaScript do primeiro exemplo:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: function (request, response) {
        $.ajax({
            type: "POST",
            url: suggestUri,
            dataType: "json",
            headers: {
                "api-key": searchServiceApiKey,
                "Content-Type": "application/json"
            },
            data: JSON.stringify({
                top: 5,
                fuzzy: false,
                suggesterName: "sg",
                search: request.term
            }),
                success: function (data) {
                    if (data.value && data.value.length > 0) {
                        response(data.value.map(x => x["@@search.text"]));
                    }
                }
            });
        },
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Se comparar este exemplo com o exemplo acima que chama o controlador Home, irá notar várias semelhanças.  A configuração `minLength` autocompleta para e `position` são exatamente as mesmas. 

A alteração significativa aqui é a origem. Em vez de chamar o método Sugerir no controlador doméstico, um pedido REST é criado numa função JavaScript e executado usando o Ajax. em seguida, a resposta é processada em "êxito" e utilizada como origem.

As chamadas REST utilizam URIs para especificar se está a ser feita uma chamada [API autocompleta](https://docs.microsoft.com/rest/api/searchservice/autocomplete) ou [sugestões.](https://docs.microsoft.com/rest/api/searchservice/suggestions) As seguintes URIs estão nas linhas 9 e 10, respectivamente.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

Na linha 148, pode encontrar um `autocompleteUri`guião que chame o . A primeira `suggestUri` chamada está na linha 39.

> [!Note]
> Fazer chamadas REST para o serviço em JavaScript é oferecido aqui como uma demonstração conveniente da API REST, mas não deve ser interpretado como uma boa prática ou recomendação. A inclusão de uma chave API e ponto final num guião abre o seu serviço até à negação de ataques de serviço a quem conseguir ler esses valores fora do script. Embora seja seguro utilizar o JavaScript para fins de aprendizagem, talvez em índices alojados no serviço gratuito, recomendamos a utilização de Java ou C# para indexação e operações de consulta no código de produção. 

<a name="configure-app"></a>

## <a name="create-an-nycjobs-index"></a>Criar um índice NYCJobs

Até agora, tens usado o índice de demonstração de NYCJobs. Se quiser visibilidade total em todo o código, incluindo o índice, siga estas instruções para criar e carregar o índice no seu próprio serviço de pesquisa.

1. [Crie um serviço de Pesquisa Cognitiva Azure](search-create-service-portal.md) ou [encontre um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) sob a sua subscrição atual. Pode utilizar um serviço gratuito para este exemplo. 

   > [!Note]
   > Se estiver a utilizar o serviço gratuito de Pesquisa Cognitiva Azure, está limitado a três índices. O carregador de dados do NYCJobs cria dois índices. Confirme que o seu serviço tem espaço para aceitar os índices novos.

1. Baixe o código de amostra [nycJobs.](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)

1. Na pasta DataLoader do código de amostra NYCJobs, abra **DataLoader.sLn** no Estúdio Visual.

1. Adicione as informações de ligação para o seu serviço de Pesquisa Cognitiva Azure. Abra a App.config dentro do projeto DataLoader e altere as aplicações TargetSearchServiceName e TargetSearchServiceApiKey para refletir o seu serviço de pesquisa cognitiva Azure e chave API do serviço de pesquisa cognitiva Azure. Esta informação pode ser encontrada no portal Azure.

1. Pressione f5 para lançar a aplicação, criando dois índices e importando os dados da amostra NYCJob.

1. Abra **autocompleteTutorial.sln** e edite o Web.config no projeto **AutocompleteTutorial.** Altere os valores SearchServiceName e SearchServiceApiKey para valores válidos para o seu serviço de pesquisa.

1. Prima F5 para executar a aplicação. A aplicação web da amostra abre no navegador padrão. A experiência é idêntica à versão sandbox, apenas o índice e os dados estão hospedados no seu serviço.

## <a name="next-steps"></a>Passos seguintes

Este exemplo demonstra os passos básicos para a construção de uma caixa de pesquisa que suporte a autocompleta e sugestões. Viu como poderia construir uma aplicação ASP.NET MVC e usar a Pesquisa Cognitiva Azure .NET SDK ou REST API para recuperar sugestões.

Como próximo passo, tentando integrar sugestões e completar automaticamente na sua experiência de pesquisa. Os seguintes artigos de referência devem ajudar.

> [!div class="nextstepaction"]
> [Sugestões de Rest API](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [rest API](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Facets atributo num](https://docs.microsoft.com/rest/api/searchservice/create-index) Índice de Criação de API