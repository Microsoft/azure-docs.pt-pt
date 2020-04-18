---
title: Adicione autocompleta e sugestões numa caixa de pesquisa
titleSuffix: Azure Cognitive Search
description: Ative ações de consulta de pesquisa como você tipo em Pesquisa Cognitiva Azure, criando sugestionantes e formulando pedidos que completem automaticamente uma caixa de pesquisa com termos ou frases terminadas. Também pode devolver os fósforos sugeridos.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 1d8085c6056cb0d2541999c3e9c249cde3da8834
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641260"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps"></a>Adicione automaticamente e sugestões às aplicações de clientes

Pesquisa-as-you-type é uma técnica comum para melhorar a produtividade das consultas iniciadas pelo utilizador. Na Pesquisa Cognitiva Azure, esta experiência é suportada através *de auto-complete*, que termina um termo ou frase com base na entrada parcial (completando "micro" com "microsoft"). Outro formulário são *sugestões:* uma pequena lista de documentos correspondentes (títulos de livro de devolução com identificação para que possa ligar a uma página de detalhes). Tanto a autocompleta como as sugestões são baseadas numa correspondência no índice. O serviço não vai oferecer consultas que devolvam zero resultados.

Para implementar estas experiências na Pesquisa Cognitiva Azure, você precisará:

+ Um *sugestor* na parte de trás.
+ Uma *consulta* que especifica a API autocompleta ou sugestões no pedido.
+ Um *controlo ui* para lidar com interações de pesquisa como você tipo na sua aplicação de cliente. Recomendamos a utilização de uma biblioteca JavaScript existente para este fim.

Na Pesquisa Cognitiva Azure, consultas auto-completadas e resultados sugeridos são recuperados do índice de pesquisa, a partir de campos selecionados que registou com um sugestor. Um sugestor faz parte do índice, e especifica quais os campos que fornecerão conteúdo que completa uma consulta, sugere um resultado, ou faz ambos. Quando o índice é criado e carregado, uma estrutura de dados sugestionante é criada internamente para armazenar prefixos usados para combinar em consultas parciais. Para sugestões, escolher campos adequados que sejam únicos, ou pelo menos não repetitivos, é essencial para a experiência. Para mais informações, consulte [Criar um sugestionador](index-add-suggesters.md).

O restante deste artigo está focado em consultas e código de cliente. Utiliza javaScript e C# para ilustrar pontos-chave. Os exemplos da API REST são usados para apresentar concisamente cada operação. Para ligações a amostras de código de ponta a ponta, consulte [os próximos passos](#next-steps).

## <a name="set-up-a-request"></a>Configurar um pedido

Os elementos de um pedido incluem a API ([Rest Autocomplete](https://docs.microsoft.com/rest/api/searchservice/autocomplete) ou [Sugestão REST),](https://docs.microsoft.com/rest/api/searchservice/suggestions)uma consulta parcial e um sugestionista.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2019-05-06
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

O **nome** sugeridor dá-lhe os campos de sugestão utilizados para completar termos ou sugestões. Para sugestões em particular, a lista de campo deve ser composta por aqueles que oferecem escolhas claras entre resultados correspondentes. Num site que vende jogos de computador, o campo pode ser o título do jogo.

O parâmetro de **pesquisa** fornece a consulta parcial, onde os caracteres são alimentados ao pedido de consulta através do controlo jQuery Autocomplete. No exemplo acima, "minecraf" é uma ilustração estática do que o controlo poderia ter passado.

As APIs não impõem requisitos mínimos de comprimento à consulta parcial; pode ser tão pouco quanto um personagem. No entanto, a jQuery Autocomplete fornece um comprimento mínimo. Um mínimo de dois ou três caracteres é típico.

Os fósforos estão no início de um termo em qualquer lugar da cadeia de entrada. Tendo em conta "a raposa castanha rápida", tanto a autocompleta como as sugestões corresponderão às versões parciais de "o", "rápido", "castanho" ou "raposa", mas não em termos de infixo parcial como "rown" ou "boi". Além disso, cada partida define a margem para expansões a jusante. Uma consulta parcial de "quick br" corresponderá a "quick brown" ou "quick bread", mas nem "castanho" nem "pão" por si só seria compatível a menos que "rápido" os precede.

### <a name="apis"></a>APIs

Siga estes links para as páginas de referência REST e .NET SDK:

+ [Sugestões REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [API DE DESCANSO Autocompleto](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SugestãoComHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Método AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

## <a name="structure-a-response"></a>Estruturar uma resposta

Respostas para auto-completar e sugestões são o que você pode esperar para o padrão: [A Complete Automática](https://docs.microsoft.com/rest/api/searchservice/autocomplete#response) devolve uma lista de termos, [Sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions#response) devolve termos mais um ID de documento para que possa obter o documento (use a API do [Documento de Procuração](https://docs.microsoft.com/rest/api/searchservice/lookup-document) para obter o documento específico para uma página detalhada).

As respostas são moldadas pelos parâmetros do pedido. Para completar automaticamente, defina [**o modo autocompleto**](https://docs.microsoft.com/rest/api/searchservice/autocomplete#autocomplete-modes) para determinar se a conclusão do texto ocorre em um ou dois termos. Para Sugestões, o campo que escolher determina o conteúdo da resposta.

Para aperfeiçoar ainda mais a resposta, inclua mais parâmetros sobre o pedido. Os seguintes parâmetros aplicam-se tanto à Autocomplete como às Sugestões.

| Parâmetro | Utilização |
|-----------|-------|
| **$select** | Se tiver vários campos **$select** de **origem,** use $select`$select=GameTitle`para escolher qual o campo que contribui com valores ( ). |
| **$filter** | Aplicar critérios de jogo`$filter=ActionAdventure`no conjunto de resultados (). |
| **$top** | Limite os resultados a`$top=5`um número específico ().|

## <a name="add-user-interaction-code"></a>Adicionar código de interação do utilizador

O preenchimento automático de um termo de consulta ou a queda de uma lista de links correspondentes requer código de interação do utilizador, tipicamente JavaScript, que pode consumir pedidos de fontes externas, tais como consultas de auto-completa ou sugestão contra um índice cognitivo de pesquisa azure.

Embora possa escrever este código de forma nativa, é muito mais fácil usar funções da biblioteca JavaScript existente. Este artigo demonstra dois, um para sugestões e outro para auto-completar. 

+ [Widget autocompleto (jQuery UI)](https://jqueryui.com/autocomplete/) é usado no exemplo sugestão. Pode criar uma caixa de pesquisa e, em seguida, referenciar numa função JavaScript que utiliza o widget Autocomplete. As propriedades no widget definiram a fonte (uma função autocompleta ou sugestões), o comprimento mínimo dos caracteres de entrada antes da ação e o posicionamento.

+ [O plug-in XDSoft Autocomplete](https://xdsoft.net/jqplugins/autocomplete/) é utilizado o exemplo autocompleto.

Utilizamos estas bibliotecas para construir a caixa de pesquisa suportando ambas as sugestões e autocompletas. As inputs recolhidas na caixa de pesquisa são emparelhadas com sugestões e ações autocompletas.

## <a name="suggestions"></a>Sugestões

Esta secção acompanha-o através de uma implementação de resultados sugeridos, a começar pela definição da caixa de pesquisa. Também mostra como e script que invoca a primeira biblioteca autocompleta JavaScript referenciada neste artigo.

### <a name="create-a-search-box"></a>Criar uma caixa de pesquisa

Assumindo a [biblioteca jQuery UI Autocomplete](https://jqueryui.com/autocomplete/) e um projeto MVC em C#, pode definir a caixa de pesquisa usando javaScript no ficheiro **Index.cshtml.** A biblioteca adiciona a interação de pesquisa como você-tipo à caixa de pesquisa, fazendo chamadas assíncronas para o controlador MVC para obter sugestões.

Em **Index.cshtml** sob a pasta \Views\Home, uma linha para criar uma caixa de pesquisa pode ser a seguinte:

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

Este exemplo é uma simples caixa de texto de entrada com uma classe para styling, um ID a ser referenciado por JavaScript, e texto de espaço reservado.  

Dentro do mesmo ficheiro, incorpora o JavaScript que faz referência à caixa de pesquisa. A seguinte função chama a API sugestionada, que solicita documentos correspondentes com base em inputs de prazo parcial:

```javascript
$(function () {
    $("#searchbox1").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

O `source` diz à função jQuery UI Autocomplete onde obter a lista de itens para mostrar sob a caixa de pesquisa. Uma vez que este projeto é um projeto MVC, chama a função **Sugerir** em **HomeController.cs** que contém a lógica para devolver sugestões de consulta. Esta função também passa alguns parâmetros para controlar os destaques, a correspondência difusa e o termo. O JavaScript API autocompleto adiciona o termo parâmetro.

As `minLength: 3` garantias de que as recomendações só serão mostradas quando houver pelo menos três caracteres na caixa de pesquisa.

### <a name="enable-fuzzy-matching"></a>Ativar correspondência fuzzy

A pesquisa difusa permite-lhe obter os resultados com base em correspondências aproximadas, mesmo que o utilizador escreva uma palavra incorretamente na caixa de pesquisa. A distância de edição é 1, o que significa que pode haver uma discrepância máxima de um personagem entre a entrada do utilizador e uma correspondência. 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>Ativar o realce

O destaque aplica o estilo de fonte aos caracteres no resultado que corresponde à entrada. Por exemplo, se a entrada parcial for "micro", o resultado apareceria como **micro**macio, **micro**âmbito, etc. O destaque baseia-se nos parâmetros HighlightPreTag e HighlightPostTag, definidos em linha com a função Sugestão.

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>Sugerir função

Se estiver a utilizar c# e uma aplicação MVC, **HomeController.cs** ficheiro sob o diretório de Controladores é onde poderá criar uma classe para resultados sugeridos. Em .NET, uma função Sugerir baseia-se nas Extensões de Operações de [Documentos.Sugerir método](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet).

O `InitSearch` método cria um cliente índice HTTP autenticado para o serviço de Pesquisa Cognitiva Azure. Para obter mais informações sobre o .NET SDK, consulte [Como utilizar a Pesquisa Cognitiva Azure a partir de uma aplicação .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

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

## <a name="autocomplete"></a>Preenchimento Automático

Até agora, o código UX de pesquisa tem sido centrado em sugestões. O próximo bloco de código mostra autocompleto, utilizando a função XDSoft jQuery UI Autocomplete, passando num pedido para azure Cognitive Search autocompleto. Tal como acontece com as sugestões, numa aplicação C#, o código que suporta a interação do utilizador vai para **o index.cshtml**.

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#searchbox1").autocompleteInline({
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
    $("#searchbox1").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#searchbox1").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#searchbox1").val("");
        }
    });
});
```

### <a name="autocomplete-function"></a>Função autocompleta

A conclusão automática baseia-se no [método DocumentsOperationsExtensions.Autocomplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet). Tal como acontece com as sugestões, este bloco de código sairia no ficheiro **HomeController.cs.**

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

    // Convert the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

A função Autocomplete toma a entrada do termo de pesquisa. O método cria um [objeto AutoCompleteParameters](https://docs.microsoft.com/rest/api/searchservice/autocomplete). Em seguida, o resultado é convertido em JSON, para que possa ser mostrado no cliente.

## <a name="next-steps"></a>Passos seguintes

Siga estes links para obter instruções de ponta a ponta ou código demonstrando experiências de pesquisa como o seu tipo. Ambos os exemplos de código incluem implementações híbridas de sugestões e autocompletas em conjunto.

+ [Tutorial: Crie a sua primeira aplicação em C# (lição 3)](tutorial-csharp-type-ahead-and-suggestions.md)
+ [Amostra de código C#: azure-search-dotnet-samples/create-first-app/3-add-typeahead/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/3-add-typeahead)
+ [C# e JavaScript com amostra de código lado a lado REST](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)