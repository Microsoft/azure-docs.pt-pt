---
title: Adicionar sugestões e preenchimento automático na caixa de pesquisa - Azure Search
description: Ative ações de consulta de typeahead no Azure Search criando sugestores e formular pedidos que preencha a caixa de pesquisa com termos concluídos ou frases.
manager: pablocas
author: mrcarter8
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: mcarter
ms.custom: seodec2018
ms.openlocfilehash: 43d289f2688bbf4927ee244d6ae9992782bf380e
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009823"
---
# <a name="example-add-suggestions-or-autocomplete-to-your-azure-search-application"></a>Exemplo: Adicionar preenchimento automático ou sugestões para a sua aplicação do Azure Search

Neste artigo, saiba como utilizar [sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions) e [preenchimento automático](https://docs.microsoft.com/rest/api/searchservice/autocomplete) para criar uma caixa de pesquisa poderosa que oferece suporte a comportamentos de pesquisa-como--type.

+ *Sugestões* resultados sugeridos gerados à medida que escreve, onde cada sugestão é um único resultado do índice que corresponde ao que tiver digitado até agora. 

+ *Preenchimento automático*, [uma funcionalidade de pré-visualização](search-api-preview.md), "terminar" a palavra ou expressão que um usuário está digitando atualmente. Em vez de retornar resultados, ser concluída uma consulta, que pode ser executado para devolver resultados. Tal como acontece com sugestões, uma concluído palavra ou frase numa consulta se baseia numa correspondência no índice. O serviço não oferece consultas que devolvem zero resultados no índice.

Pode transferir e executar o código de exemplo **DotNetHowToAutocomplete** para avaliar esses recursos. O código de exemplo destina-se um índice criados previamente preenchido com [dados de demonstração de NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). O índice de NYCJobs contém um [construção Sugestor](index-add-suggesters.md), que é um requisito para usar sugestões ou preenchimento automático. Pode usar o índice preparado alojado num serviço de proteção de segurança, ou [preencher seu próprio índice de](#configure-app) usando um carregador de dados na solução de exemplo NYCJobs. 

O **DotNetHowToAutocomplete** exemplo mostra-lhe sugestões e preenchimento automático, tanto no C# e versões de idioma do JavaScript. C#os desenvolvedores podem percorrer uma aplicação baseada em ASP.NET MVC que utiliza a [SDK .NET da Azure Search](https://aka.ms/search-sdk). A lógica para tornar o preenchimento automático e chamadas de sugestões de consulta pode ser encontrada no ficheiro HomeController.cs. Os desenvolvedores de JavaScript encontrará lógica de consulta equivalente em IndexJavaScript.cshtml, que inclui as chamadas diretas para o [API REST da Azure Search](https://docs.microsoft.com/rest/api/searchservice/). 

Para ambas as versões de idioma, a experiência do usuário de front-end se baseia a [jQuery UI](https://jqueryui.com/autocomplete/) e [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) bibliotecas. Essas bibliotecas são utilizadas para criar a caixa de pesquisa, dar suporte a sugestões e preenchimento automático. Entradas recolhidas na caixa de pesquisa são emparelhadas com sugestões e ações de conclusão automática, como os, conforme definido na HomeController.cs ou IndexJavaScript.cshtml.

Neste exercício explica-lhe as seguintes tarefas:

> [!div class="checklist"]
> * Implementar uma caixa de entrada de pesquisa em JavaScript e emitir pedidos para correspondências sugeridas ou autocompleted termos
> * No C#, defina sugestões e ações de conclusão automática no HomeController.cs
> * No JavaScript, chamar as APIs de REST diretamente para fornecer a mesma funcionalidade

## <a name="prerequisites"></a>Pré-requisitos

Um serviço Azure Search é opcional neste exercício, uma vez que a solução utiliza um serviço de proteção de segurança em direto que aloja um índice de demonstração NYCJobs preparado. Se pretender executar este exemplo no seu próprio serviço de pesquisa, veja [índice de empregos em nova IORQUE configurar](#configure-app) para obter instruções.

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), qualquer edição. Código de exemplo e instruções foram testadas na edição de Comunidade gratuita.

* Transfira o [DotNetHowToAutoComplete exemplo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

O exemplo é abrangente, abrangendo sugestões, preenchimento automático, navegação por facetas e colocação em cache do lado do cliente. Revise o Leiame e comentários para uma descrição completa do que o exemplo oferece.

## <a name="run-the-sample"></a>Executar o exemplo

1. Open **AutocompleteTutorial.sln** no Visual Studio. A solução contém um projeto de MVC do ASP.NET com uma ligação para o índice de demonstração de empregos em nova IORQUE.

2. Prima F5 para executar o projeto e carregar a página no browser da sua opção.

Na parte superior, verá uma opção para selecionar C# ou JavaScript. O C# opção chama o HomeController do navegador e usa o SDK .NET da Azure Search para recuperar os resultados. 

A opção JavaScript chama a API REST do Azure Search diretamente a partir do browser. Esta opção, normalmente, terão visivelmente um melhor desempenho, uma vez que ele usa o controlador de fora o fluxo. Pode escolher a opção adequada às suas necessidades e preferências de linguagem. Existem vários exemplos de preenchimento automático na página com algumas orientações para cada um. Cada exemplo tem um texto recomendado que pode experimentar.  

Tente escrever algumas letras em cada caixa de pesquisa para ver o que acontece.

## <a name="search-box"></a>Caixa de pesquisa

Para os dois C# e versões de JavaScript, a implementação de caixa de pesquisa é exatamente o mesmo. 

Abra o **Index. cshtml** ficheiro, sob a pasta \Views\Home para ver o código:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Esta é uma caixa de texto de entrada simples com uma classe para definição de estilo, um ID para ser referenciada por JavaScript e o texto de marcador de posição.  A facilidade está no JavaScript incorporado.

O C# exemplo de linguagem utiliza JavaScript em index. cshtml para tirar partido do [biblioteca de preenchimento automático de interface do Usuário do jQuery](https://jqueryui.com/autocomplete/). Esta biblioteca adiciona a experiência de conclusão automática para a caixa de pesquisa por meio de chamadas assíncronas para o controlador MVC para obter sugestões. A versão de idioma do JavaScript é na IndexJavaScript.cshtml. Ele inclui o script abaixo para a barra de pesquisa, bem como as chamadas da REST API para o Azure Search.

Vamos examinar o código JavaScript para o primeiro exemplo, que chama a função de conclusão automática de interface do Usuário, passando um pedido para obter sugestões de jQuery:

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

O código acima é executado no navegador no carregamento da página para configurar o preenchimento automático de interface do Usuário do jQuery para a caixa de entrada "example1a".  `minLength: 3` garante que as recomendações só serão apresentadas quando existe, pelo menos, três carateres na caixa de pesquisa.  O valor de origem é importante:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

A linha acima informa a função de conclusão automática de interface do Usuário do jQuery, onde pode obter a lista de itens para mostrar na caixa de pesquisa. Como se trata de um projeto MVC, ele chama a função de sugira em HomeController.cs que contém a lógica para devolver sugestões de consulta (mais sobre sugerido na próxima seção). Esta função também transmite alguns parâmetros destaques de controle, a correspondência difusa e o termo. A API de JavaScript de conclusão automática adiciona o parâmetro do termo.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Expandir o exemplo para suportar a correspondência difusa

A pesquisa difusa permite-lhe obter os resultados com base em correspondências aproximadas, mesmo que o utilizador escreva uma palavra incorretamente na caixa de pesquisa. Embora não seja necessário, melhora significativamente a robustez de uma experiência de typeahead. Vamos experimentar isto ao alterar a linha de origem para ativar a correspondência difusa.

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

### <a name="jquery-autocomplete--backed-by-azure-search-autocomplete"></a>jQuery conclusão automática, apoiado por preenchimento automático do Azure Search

Até agora, a pesquisa de código UX tem sido centra-se nas sugestões. O bloco de código seguinte mostra a função de conclusão automática de interface do Usuário do jQuery (linha 91 em index. cshtml), passando um pedido para preenchimento automático do Azure Search:

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 http://xdsoft.net/jqplugins/autocomplete/
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

## <a name="c-example"></a>Exemplo do c#

Agora que examinamos o código JavaScript para a página da web, vamos examinar a C# código do lado do servidor de controlador que, na verdade, recupera as correspondências sugeridas com o SDK de .NET de pesquisa do Azure.

Abra o **HomeController.cs** ficheiro, sob o diretório de controladores. 

A primeira coisa que notará é um método na parte superior da classe chamada `InitSearch`. Esta ação cria um cliente de índice HTTP autenticado para o serviço do Azure Search. Para obter mais informações, consulte [como utilizar o Azure Search a partir de um aplicativo .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

Na linha 41, tenha em atenção a função de sugerido. Baseia-se a [DocumentsOperationsExtensions.Suggest método](https://docs.microsoft.com/dotnet/api/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet-preview).

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

A função Suggest aceita dois parâmetros que determinam se são devolvidos detetores de ocorrências ou é utilizada a correspondência difusa além da introdução do termo de pesquisa. O método cria um [SuggestParameters objeto](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), que é então passado para a API de sugerir. Em seguida, o resultado é convertido em JSON, para que possa ser mostrado no cliente.

Na linha 69, tenha em atenção a função de preenchimento automático. Baseia-se a [DocumentsOperationsExtensions.Autocomplete método](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet-preview).

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

A função de conclusão automática aceita a entrada do termo de pesquisa. O método cria um [AutoCompleteParameters objeto](https://docs.microsoft.com/rest/api/searchservice/autocomplete). Em seguida, o resultado é convertido em JSON, para que possa ser mostrado no cliente.

(Opcional) Adicione um ponto de interrupção à função Suggest e percorra o código. Tenha em atenção a resposta devolvida pelo SDK e como ela é convertida para o resultado retornado do método.

Outros exemplos na página siga o mesmo padrão para adicionar o detetor de ocorrências e facetas para suportar a colocação em cache do lado do cliente dos resultados de preenchimento automático. Reveja cada um deles para compreender como funcionam e como aproveitá-los na sua experiência de pesquisa.

## <a name="javascript-example"></a>Exemplo de JavaScript

Uma implementação de Javascript de preenchimento automático e sugestões chama a API de REST, usando um URI como origem para especificar o índice e a operação. 

Para rever a implementação de JavaScript, abra **IndexJavaScript.cshtml**. Tenha em atenção que a função de conclusão automática de interface do Usuário do jQuery também é utilizado para a caixa de pesquisa, coletar entradas do termo de pesquisa e fazer chamadas assíncronas para o Azure Search para obter sugestões de correspondências ou concluída termos. 

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

Se comparar isto ao exemplo acima que chama o controlador Home, verá várias semelhanças.  A configuração de preenchimento automático para `minLength` e `position` são exatamente iguais. 

A alteração significativa aqui é a origem. Em vez de chamar o método sugira no controlador da página inicial, uma solicitação REST é criada numa função de JavaScript e executado usando o Ajax. em seguida, a resposta é processada em "êxito" e utilizada como origem.

Chamadas REST utilizam URIs para especificar se um [preenchimento automático](https://docs.microsoft.com/rest/api/searchservice/autocomplete) ou [sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions) está a ser efetuada a chamada de API. Os seguintes URIs são em linhas 9 e 10, respectivamente.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

Na linha 148, pode encontrar um script que chama o `autocompleteUri`. A primeira chamada para `suggestUri` está em linha 39.

> [!Note]
> Fazendo chamadas REST para o serviço no JavaScript é oferecido aqui como uma demonstração prática da REST API, mas não devem ser interpretadas como uma melhor prática ou recomendação. A inclusão de uma chave de API e o ponto final num script abre-se o seu serviço até ataques denial of service a quem pode ler esses valores, o script. Ao seu seguro usar o JavaScript para efeitos de aprendizagem, talvez em índices alojados num serviço gratuito, recomendamos que utilize Java ou C# para operações de indexação e consulta no código de produção. 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>Configurar NYCJobs para ser executado no seu serviço

Até agora, que tem sido a utilizar o índice de demonstração NYCJobs alojado. Se pretender que o total visibilidade sobre todo o código, incluindo o índice, siga estas instruções para criar e carregar o índice no seu próprio serviço de pesquisa.

1. [Criar um serviço Azure Search](search-create-service-portal.md) ou [localizar um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na subscrição atual. Pode usar um serviço gratuito para este exemplo. 

   > [!Note]
   > Se estiver a utilizar o serviço do Azure Search gratuito, está limitado a três índices. O carregador de dados do NYCJobs cria dois índices. Confirme que o seu serviço tem espaço para aceitar os índices novos.

1. Baixe [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) código de exemplo.

1. Na pasta DataLoader NYCJobs código de exemplo, abra **DataLoader.sln** no Visual Studio.

1. Adicione as informações de ligação para o serviço Azure Search. Abra o ficheiro App.config no projeto DataLoader e altere as appSettings de TargetSearchServiceName e TargetSearchServiceApiKey para refletir o seu serviço do Azure Search e a chave de API do Serviço do Azure Search. Podem ser encontrados no portal do Azure.

1. Prima F5 para iniciar o aplicativo, criando dois índices e importar os dados de exemplo NYCJob.

1. Open **AutocompleteTutorial.sln** e editar o Web. config no **AutocompleteTutorial** projeto. Altere os valores SearchServiceName e SearchServiceApiKey para valores válidos para o seu serviço de pesquisa.

1. Prima F5 para executar a aplicação. O exemplo de aplicação web é aberto no browser predefinido. A experiência é idêntica para a versão de área restrita, apenas o índice e os dados estão alojados no seu serviço.

## <a name="next-steps"></a>Passos Seguintes

Este exemplo demonstra os passos básicos para a criação de uma caixa de pesquisa que suporte o preenchimento automático e sugestões. Viu como poderia criar uma aplicação ASP.NET MVC e utilizar o SDK .NET da Azure Search ou de uma REST API para obter sugestões.

Como passo seguinte, a tentar integrar sugestões e preenchimento automático na sua experiência de pesquisa. Os seguintes artigos de referência devem ajudar.

> [!div class="nextstepaction"]
> [API REST de Conclusão Automática](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [API REST de Sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Atributo do índice de facetas numa API REST de Criar Índice](https://docs.microsoft.com/rest/api/searchservice/create-index)

