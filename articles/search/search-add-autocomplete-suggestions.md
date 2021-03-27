---
title: Adicione autocompleto a uma caixa de pesquisa
titleSuffix: Azure Cognitive Search
description: Ativar as ações de consulta do tipo de pesquisa no Azure Cognitive Search, criando sugestivos e formulando pedidos que precontam automaticamente uma caixa de pesquisa com termos ou frases acabados. Também pode devolver os jogos sugeridos.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 6bc45bb6eec9dbf46e039dd1e2c32197820bb09d
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626712"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps-using-azure-cognitive-search"></a>Adicionar autocompleto e sugestões a aplicativos de clientes usando Azure Cognitive Search

O search-as-you-type é uma técnica comum para melhorar a produtividade da consulta. Na Pesquisa Cognitiva Azure, esta experiência é suportada através de *autocomplete,* que termina um termo ou frase com base na entrada parcial (completando "micro" com "microsoft"). Uma segunda experiência de utilizador são *sugestões,* ou uma pequena lista de documentos correspondentes (devolvendo títulos de livro com um ID para que possa ligar a uma página detalhada sobre esse livro). Tanto o autocompleto como as sugestões são baseadas numa correspondência no índice. O serviço não oferece consultas que devolvam zero resultados.

Para implementar estas experiências na Azure Cognitive Search, você precisará:

+ Uma *definição sugestiva* que está incorporada no esquema de índice.
+ Uma *consulta que* especifica [API autocompleta](/rest/api/searchservice/autocomplete) ou [sugestões](/rest/api/searchservice/suggestions) sobre o pedido.
+ Um *controlo de UI* para lidar com interações de pesquisa como você-tipo na sua aplicação de cliente. Recomendamos a utilização de uma biblioteca JavaScript existente para este fim.

Na Pesquisa Cognitiva Azure, as consultas autocompletas e os resultados sugeridos são recuperados a partir do índice de pesquisa, a partir de campos selecionados que registou com um sugestivo. Um sugestivo faz parte do índice, e especifica quais os campos que fornecerão conteúdo que ou completa uma consulta, sugere um resultado, ou faz ambos. Quando o índice é criado e carregado, uma estrutura de dados sugestiva é criada internamente para armazenar prefixos usados para combinar em consultas parciais. Para sugestões, escolher campos adequados que sejam únicos, ou pelo menos não repetitivos, é essencial para a experiência. Para obter mais informações, consulte [Criar um sugestivo.](index-add-suggesters.md)

O restante deste artigo está focado em consultas e código de cliente. Utiliza JavaScript e C# para ilustrar pontos-chave. REST Exemplos de API são usados para apresentar concisamente cada operação. Para obter ligações às amostras de código de ponta a ponta, consulte [os próximos passos](#next-steps).

## <a name="set-up-a-request"></a>Configurar um pedido

Os elementos de um pedido incluem uma das APIs do tipo de pesquisa, uma consulta parcial e um sugestivo. O seguinte script ilustra componentes de um pedido, utilizando a API REST autocompleta como exemplo.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

O "nome do sugestivo" dá-lhe os campos de sugestivos utilizados para completar termos ou sugestões. Para sugestões em particular, a lista de campo deve ser composta por aqueles que oferecem escolhas claras entre resultados correspondentes. Num site que vende jogos de computador, o campo pode ser o título do jogo.

O parâmetro de "pesquisa" fornece a consulta parcial, onde os caracteres são alimentados ao pedido de consulta através do controlo jQuery Autocomplete. No exemplo acima, "minecraf" é uma ilustração estática do que o controlo pode ter passado.

As APIs não impõem requisitos mínimos de comprimento à consulta parcial; pode ser tão pouco quanto um personagem. No entanto, jQuery Autocomplete fornece um comprimento mínimo. Um mínimo de dois ou três caracteres é típico.

Os fósforos estão no início de um termo em qualquer lugar da cadeia de entrada. Dada a "raposa castanha rápida", tanto a autocompleta como as sugestões vão coincidir em versões parciais de "o", "rápido", "castanho", ou "raposa", mas não em termos parciais como "rown" ou "ox". Além disso, cada partida define o âmbito para as expansões a jusante. Uma consulta parcial de "br rápido" combinará com "brown rápido" ou "pão rápido", mas nem "marrom" ou "pão" por si só seria igual a menos que "rápido" os anteceda.

### <a name="apis-for-search-as-you-type"></a>APIs para pesquisa-como-você-tipo

Siga estes links para as páginas de referência REST e .NET SDK:

+ [Sugestões REST API](/rest/api/searchservice/suggestions) 
+ [API DE REST autocompleto](/rest/api/searchservice/autocomplete) 
+ [Sugerir Método Async](/dotnet/api/azure.search.documents.searchclient.suggestasync)
+ [Método AutocompleteAsync](/dotnet/api/azure.search.documents.searchclient.autocompleteasync)

## <a name="structure-a-response"></a>Estruturar uma resposta

As respostas para o preconto automático e as sugestões são o que se pode esperar para o padrão: [O preconto automático](/rest/api/searchservice/autocomplete#response) devolve uma lista de termos, [sugestões](/rest/api/searchservice/suggestions#response) devolve termos mais um documento de ID para que possa ir buscar o documento (use a API [do Documento de Procura](/rest/api/searchservice/lookup-document) para obter o documento específico para uma página de detalhe).

As respostas são moldadas pelos parâmetros do pedido:

+ Para concluir automaticamente, defina o [AutocompleteMode](/rest/api/searchservice/autocomplete#query-parameters) para determinar se a conclusão do texto ocorre em um ou dois termos. 

+ Para sugestões, desempata [$select](/rest/api/searchservice/suggestionse#query-parameters) para devolver campos que contenham valores únicos ou diferenciadores, tais como nomes e descrição. Evite campos que contenham valores duplicados (como uma categoria ou cidade).

Os seguintes parâmetros adicionais aplicam-se tanto a autocompletos como a sugestões, mas são talvez mais necessários para sugestões, especialmente quando um sugestivo inclui vários campos.

| Parâmetro | Utilização |
|-----------|-------|
| searchFields | Restrinja a consulta a campos específicos. |
| $filter | Aplicar critérios de correspondência no conjunto de resultados ( `$filter=Category eq 'ActionAdventure'` ). |
| $top | Limitar os resultados a um número específico `$top=5` ().|

## <a name="add-user-interaction-code"></a>Adicionar código de interação do utilizador

Preencher automaticamente um termo de consulta ou deixar cair uma lista de links correspondentes requer código de interação do utilizador, tipicamente JavaScript, que pode consumir pedidos de fontes externas, tais como consultas de autocomplete ou sugestões contra um índice cognitivo de pesquisa de Azure.

Embora possa escrever este código de forma nativa, é muito mais fácil utilizar funções da biblioteca JavaScript existente, como uma das seguintes. 

+ [Widget autocomplete (jQuery UI)](https://jqueryui.com/autocomplete/) aparece no corte do código sugestão. Pode criar uma caixa de pesquisa e, em seguida, faz referência numa função JavaScript que utiliza o widget autocomplete. Propriedades no widget definem a fonte (função de auto-completação ou sugestões), comprimento mínimo de caracteres de entrada antes de tomar medidas e posicionamento.

+ [O plug-in XDSoft Autocomplete](https://xdsoft.net/jqplugins/autocomplete/) aparece no corte de código autocompleto.

+ [sugestões](https://www.npmjs.com/package/suggestions) aparecem no [tutorial javaScript](tutorial-javascript-overview.md) e amostra de código.

Utilize estas bibliotecas no cliente para criar uma caixa de pesquisa suportando sugestões e precontos automáticos. As entradas recolhidas na caixa de pesquisa podem então ser emparelhadas com sugestões e ações de preconção automática no serviço de pesquisa.

## <a name="suggestions"></a>Sugestões

Esta secção acompanha-o através de uma implementação de resultados sugeridos, a começar pela definição da caixa de pesquisa. Também mostra como e script que invoca a primeira biblioteca autocompleta JavaScript referenciada neste artigo.

### <a name="create-a-search-box"></a>Criar uma caixa de pesquisa

Assumindo a [biblioteca jQuery UI Autocomplete](https://jqueryui.com/autocomplete/) e um projeto MVC em C#, pode definir a caixa de pesquisa usando o JavaScript no ficheiro **Index.cshtml.** A biblioteca adiciona a interação de pesquisa à caixa de pesquisa, fazendo chamadas assíncronos ao controlador MVC para obter sugestões.

Em **Index.cshtml** sob a pasta \Views\Home, uma linha para criar uma caixa de pesquisa pode ser a seguinte:

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

Este exemplo é uma caixa de texto de entrada simples com uma classe para estilo, um ID a ser referenciado por JavaScript, e texto de espaço reservado.  

Dentro do mesmo ficheiro, incorporar o JavaScript que faz referência à caixa de pesquisa. A seguinte função chama a API de sugestão, que solicita documentos de correspondência sugeridos com base em entradas de prazo parcial:

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

O `source` jQuery UI Autocomplete função onde obter a lista de itens para mostrar sob a caixa de pesquisa. Uma vez que este projeto é um projeto MVC, chama a função **Sugerir** no **HomeController.cs** que contém a lógica para sugestões de consulta de retorno. Esta função também passa alguns parâmetros para controlar destaques, correspondências difusas e termo. A API JavaScript auto-completa adiciona o parâmetro do termo.

As `minLength: 3` recomendações só serão apresentadas quando houver pelo menos três caracteres na caixa de pesquisa.

### <a name="enable-fuzzy-matching"></a>Ativar a correspondência difusa

A pesquisa difusa permite-lhe obter os resultados com base em correspondências aproximadas, mesmo que o utilizador escreva uma palavra incorretamente na caixa de pesquisa. A distância de edição é 1, o que significa que pode haver uma discrepância máxima de um personagem entre a entrada do utilizador e uma correspondência. 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>Permitir o destaque

O destaque aplica o estilo de letra aos caracteres no resultado que corresponde à entrada. Por exemplo, se a entrada parcial for "micro", o resultado apareceria como **micro** macio, **micro** âmbito, e assim por diante. O destaque baseia-se nos parâmetros HighlightPreTag e HighlightPostTag, definidos em linha com a função Sugestão.

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>Sugerir função

Se estiver a utilizar C# e uma aplicação MVC, o ficheiro **HomeController.cs** sob o diretório de controladores é onde poderá criar uma classe para resultados sugeridos. Em .NET, a função Sugerir baseia-se no [método SuggestAsync](/dotnet/api/azure.search.documents.searchclient.suggestasync). Para obter mais informações sobre o .NET SDK, consulte [Como utilizar a Azure Cognitive Search a partir de uma aplicação .NET](search-howto-dotnet-sdk.md).

O `InitSearch` método cria um cliente índice HTTP autenticado para o serviço de Pesquisa Cognitiva Azure. As propriedades na classe [Sugestoptions](/dotnet/api/azure.search.documents.suggestoptions) determinam quais os campos que são pesquisados e devolvidos nos resultados, o número de partidas e se é utilizada correspondência difusa. 

Para o autocompleto, a correspondência difusa é limitada a uma distância de edição (um carácter omitido ou extraviado). Note que a correspondência difusa em consultas autocompletas pode por vezes produzir resultados inesperados dependendo do tamanho do índice e de como é fragmento. Para mais informações, consulte [os conceitos de partição e de fragmentos.](search-capacity-planning.md#concepts-search-units-replicas-partitions-shards)

```csharp
public async Task<ActionResult> SuggestAsync(bool highlights, bool fuzzy, string term)
{
    InitSearch();

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

    // Only one suggester can be specified per index.
    // The suggester for the Hotels index enables autocomplete/suggestions on the HotelName field only.
    // During indexing, HotelNames are indexed in patterns that support autocomplete and suggested results.
    var suggestResult = await _searchClient.SuggestAsync<Hotel>(term, "sg", options).ConfigureAwait(false);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = suggestResult.Value.Results.Select(x => x.Text).ToList();

    // Return the list of suggestions.
    return new JsonResult(suggestions);
}
```

A função SuggestAsync requer dois parâmetros que determinam se os destaques de impacto são devolvidos ou se a correspondência felpuda é usada para além da entrada do termo de pesquisa. Até oito partidas podem ser incluídas nos resultados sugeridos. O método cria um [objeto SuggestOptions,](/dotnet/api/azure.search.documents.suggestoptions)que é depois passado para a API de Sugestão. Em seguida, o resultado é convertido em JSON, para que possa ser mostrado no cliente.

## <a name="autocomplete"></a>Preenchimento Automático

Até agora, o código UX de pesquisa tem sido centrado em sugestões. O próximo bloco de código mostra autocompleto, utilizando a função XDSoft jQuery UI Autocomplete, passando num pedido de autocomplete de pesquisa cognitiva Azure. Tal como acontece com as sugestões, numa aplicação C#, o código que suporta a interação do utilizador vai em **index.cshtml**.

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.8 https://xdsoft.net/jqplugins/autocomplete/
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

O autocomplete baseia-se no [método AutocompleteAsync](/dotnet/api/azure.search.documents.searchclient.autocompleteasync). Tal como nas sugestões, este bloco de código iria para o ficheiro **HomeController.cs.**

```csharp
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

A função Autocomplete requer a entrada do termo de pesquisa. O método cria um [objeto AutoCompleteParameters](/rest/api/searchservice/autocomplete). Em seguida, o resultado é convertido em JSON, para que possa ser mostrado no cliente.

## <a name="next-steps"></a>Passos seguintes

Siga estes links para instruções ou códigos de ponta a ponta que demonstrem ambas as experiências de pesquisa como você. A amostra demonstra a implementação híbrida de sugestões e autocompleto em conjunto.

+ [Adicionar pesquisa a um web site (JavaScript)](tutorial-javascript-search-query-integration.md#azure-function-suggestions-from-the-catalog) utiliza um pacote de sugestões de código aberto para conclusão parcial do prazo na aplicação do cliente.
+ [Tutorial: Crie a sua primeira aplicação em C# (lição 3)](tutorial-csharp-type-ahead-and-suggestions.md) com a amostra de  [código C# associada: azure-search-dotnet-samples/create-first-app/3-add-typeahead/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10/3-add-typeahead) demonstre suporte nativo para typeahead.