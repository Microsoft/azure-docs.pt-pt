---
title: 'Tutorial: Criar uma aplicação Web de página única – API de Pesquisa na Web do Bing'
titleSuffix: Azure Cognitive Services
description: Esta aplicação de página única demonstra como a API de Pesquisa na Web do Bing pode ser utilizada para obter, analisar e apresentar resultados de pesquisa relevantes numa aplicação de página única.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: c47a4fbf51b14d9a13237f77c75dbf2839fb5f80
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381291"
---
# <a name="tutorial-create-a-single-page-app-using-the-bing-web-search-api"></a>Tutorial: Criar uma aplicação de página única com a API de Pesquisa na Web do Bing

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](https://aka.ms/cogsvcs/bingmove)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](https://aka.ms/cogsvcs/bingmigration)

Esta aplicação de página única demonstra como obter, analisar e apresentar resultados de pesquisa a partir da API de Pesquisa na Web do Bing. O tutorial utiliza CSS e HTML automático e centra-se no código JavaScript. Os ficheiros HTML, CSS e JS estão disponíveis no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) com instruções de início rápido.

Esta aplicação de exemplo pode:

> [!div class="checklist"]
> * Chamar a API de Pesquisa na Web do Bing com opções de pesquisa
> * Apresentar resultados da Web, de imagens, de notícias e de vídeo
> * Paginar resultados
> * Gerir chaves de subscrição
> * Processar erros

Para utilizar esta aplicação, é necessária uma [conta dos Serviços Cognitivos do Azure](../cognitive-services-apis-create-account.md) com APIs de Pesquisa do Bing.

## <a name="prerequisites"></a>Pré-requisitos

Eis o que precisa para executar a aplicação:

* Uma subscrição do Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Assim que tiver a subscrição do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" Crie um recurso Bing Search crie um "  target="_blank"> recurso Bing Search no portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.

* Node.js 8 ou posterior

O primeiro passo é clonar o repositório com o código fonte da aplicação de exemplo.

```console
git clone https://github.com/Azure-Samples/cognitive-services-REST-api-samples.git
```

Em seguida, execute o `npm install`. Para este tutorial, Express.js é a única dependência.

```console
cd <path-to-repo>/cognitive-services-REST-api-samples/Tutorials/Bing-Web-Search
npm install
```

## <a name="app-components"></a>Componentes da aplicação

A aplicação de exemplo que estamos a criar é composta por quatro partes:

* `bing-web-search.js` – a nossa aplicação Express.js. Processa o encaminhamento e a lógica de pedidos/respostas.
* `public/index.html` – a estrutura da nossa aplicação. Define como os dados são apresentados aos utilizadores.
* `public/css/styles.css` – define os estilos de página, como tipos de letra, cores e tamanho do texto.
* `public/js/scripts.js` – contém a lógica para fazer pedidos à API de Pesquisa na Web do Bing, gerir chaves de subscrição, processar e analisar respostas e apresentar resultados.

Este tutorial centra-se no `scripts.js` e na lógica necessária para chamar a API de Pesquisa na Web do Bing e processar a resposta.

## <a name="html-form"></a>Formulário HTML

O `index.html` inclui um formulário que permite aos utilizadores procurar e selecionar opções de pesquisa. O atributo `onsubmit` é acionado quando o formulário é submetido, ao chamar o método `bingWebSearch()` definido no `scripts.js`. Exige três argumentos:

* Consulta de pesquisa
* Opções selecionadas
* Chave de subscrição

```html
<form name="bing" onsubmit="return bingWebSearch(this.query.value,
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="query-options"></a>Opções de consulta

O formulário HTML inclui opções que mapeiam aos parâmetros da consulta na [API de Pesquisa na Web do Bing v7](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query-parameters). Esta tabela fornece detalhes sobre como os utilizadores podem filtrar os resultados de pesquisa através da aplicação de exemplo:

| Parâmetro | Descrição |
|-----------|-------------|
| `query` | Um campo de texto para introduzir uma cadeia de consulta. |
| `where` | Um menu pendente para selecionar o mercado (localização e idioma). |
| `what` | Caixas de verificação para promover tipos de resultados específicos. Por exemplo, a promoção de imagens aumenta a classificação das imagens nos resultados da pesquisa. |
| `when` | Um menu pendente que permite ao utilizador restringir os resultados da pesquisa para hoje, esta semana ou este mês. |
| `safe` | Uma caixa de verificação para ativar a Pesquisa Segura do Bing, que filtra conteúdos para adultos. |
| `count` | Campo oculto. O número de resultados de pesquisa a devolver em cada pedido. Altere este valor para mostrar menos ou mais resultados por página. |
| `offset` | Campo oculto. O desfasamento do primeiro resultado de pesquisa no pedido, que é utilizado para paginação. O valor é reposto para `0` em todos os novos pedidos. |

> [!NOTE]
> A API de Pesquisa na Web do Bing oferece parâmetros de consulta adicionais para ajudar a refinar os resultados da pesquisa. No entanto, este exemplo só utiliza alguns. Para ver uma lista completa dos parâmetros disponíveis, veja [Bing Web Search API v7 reference](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query-parameters) (Referência da API de Pesquisa na Web do Bing v7).

A função `bingSearchOptions()` converte estas opções para corresponderem ao formato exigido pela API de Pesquisa do Bing.

```javascript
// Build query options from selections in the HTML form.
function bingSearchOptions(form) {

    var options = [];
    // Where option.
    options.push("mkt=" + form.where.value);
    // SafeSearch option.
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "moderate"));
    // Freshness option.
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var what = [];
    for (var i = 0; i < form.what.length; i++)
        if (form.what[i].checked) what.push(form.what[i].value);
    // Promote option.
    if (what.length) {
        options.push("promote=" + what.join(","));
        options.push("answerCount=9");
    }
    // Count option.
    options.push("count=" + form.count.value);
    // Offset option.
    options.push("offset=" + form.offset.value);
    // Hardcoded text decoration option.
    options.push("textDecorations=true");
    // Hardcoded text format option.
    options.push("textFormat=HTML");
    return options.join("&");
}
```

O parâmetro `SafeSearch` pode ser definido para `strict`, `moderate` ou `off`. A predefinição da Pesquisa na Web do Bing é `moderate`. Este formulário utiliza uma caixa de verificação, que tem dois estados: `strict` ou `moderate` .

Se alguma das caixas de verificação **Promote** (Promover) estiver selecionada, o parâmetro `answerCount` será adicionado à consulta. Ao utilizar o parâmetro `promote`, é necessário o parâmetro `answerCount`. Neste fragmento, o valor está definido para `9` para devolver todos os tipos de resultados disponíveis.
> [!NOTE]
> Promover um tipo de resultado não *garante* que o mesmo será incluído nos resultados da pesquisa. Pelo contrário, a promoção aumenta a classificação dos tipos de resultados em relação à classificação normal. Para restringir as pesquisas a tipos de resultados específicos, utilize o parâmetro de consulta `responseFilter` ou chame um ponto final mais específico, como a Pesquisa de Imagens do Bing ou a Pesquisa de Notícias do Bing.

Os parâmetros de consulta `textDecoration` e `textFormat` são codificados em script e fazem com que o termo de pesquisa seja colocado a negrito nos resultados da pesquisa. Estes parâmetros não são necessários.

## <a name="manage-subscription-keys"></a>Gerir chaves de subscrição

Para evitar que a chave de subscrição da API de Pesquisa do Bing seja codificada, esta aplicação de exemplo utiliza o armazenamento persistente de um browser para armazenar a chave de subscrição. Se não existir nenhuma chave de subscrição armazenada, será pedido ao utilizador para introduzir uma. Se a chave de subscrição for rejeitada pela API, será pedido ao utilizado para voltar a introduzir uma chave de subscrição.

A função `getSubscriptionKey()` utiliza as funções `storeValue` e `retrieveValue` para armazenar e obter a chave de subscrição de um utilizador. Estas funções utilizam o objeto `localStorage` (se for suportado) ou cookies.

```javascript
// Cookie names for stored data.
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/search";

// See source code for storeValue and retrieveValue definitions.

// Get stored subscription key, or prompt if it isn't found.
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // Always set the cookie in order to update the expiration date.
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```

Como vimos anteriormente, quando o formulário é submetido, o atributo `onsubmit` é acionado e chama o método `bingWebSearch`. Esta função inicializa e envia o pedido. A função `getSubscriptionKey` é chamada em cada submissão para autenticar o pedido.

## <a name="call-bing-web-search"></a>Chamar a Pesquisa na Web do Bing

Com a consulta, a cadeia de opções e a chave de subscrição, a função `BingWebSearch` criará um objeto `XMLHttpRequest` para chamar o ponto final da Pesquisa na Web do Bing.

```javascript
// Perform a search constructed from the query, options, and subscription key.
function bingWebSearch(query, options, key) {
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

    // Initialize the request.
    try {
        request.open("GET", queryurl);
    }
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // Add request headers.
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);

    // Event handler for successful response.
    request.addEventListener("load", handleBingResponse);

    // Event handler for errors.
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // Event handler for an aborted request.
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // Send the request.
    request.send();
    return false;
}
```

Após um pedido com êxito, o processador de eventos `load` será acionado e chamará a função `handleBingResponse`. A função `handleBingResponse` analisa o objeto do resultado, apresenta os resultados e contém a lógica de erros dos pedidos com falhas.

```javascript
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // Try to parse results object.
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
        return;
    }

    // Show raw JSON and the HTTP request.
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " +
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // If the HTTP response is 200 OK, try to render the results.
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "SearchResponse" && "rankingResponse" in jsobj) {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is considered an error.
    else {
        // 401 is unauthorized; force a re-prompt for the user's subscription
        // key on the next request.
        if (this.status === 401) invalidateSubscriptionKey();

        // Some error responses don't have a top-level errors object, if absent
        // create one.
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // Display the HTTP status code.
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // Add all fields from all error responses.
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // Display Bing Trace ID if it isn't blocked by CORS.
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // Display the error message.
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> Um pedido HTTP com êxito *não* significa que a pesquisa teve êxito. Se ocorrer um erro na operação de pesquisa, a API de Pesquisa na Web do Bing devolve um código de estado HTTP que não 200 e inclui as informações do erro na resposta JSON. Se o pedido tiver limites de velocidade, a API devolverá uma resposta vazia.

A maioria do código nas funções anteriores é dedicado ao processamento de erros. Poderão ocorrer erros nas seguintes fases:

| Fase | Potenciais erros | Resolvido por |
|-------|--------------------|------------|
| Criar o objeto do pedido | URL inválido | bloco `try` / `catch` |
| Fazer o pedido | Erros de rede, ligações abortadas | Processadores de eventos `error` e `abort` |
| Fazer a pesquisa | Pedido inválido, JSON inválido, limites de frequência | Testes no processador de eventos `load` |

Os erros são processados ao chamar a função `renderErrorMessage()`. Se a resposta passar em todos os testes de erros, será chamada a função `renderSearchResults()` para apresentar os resultados da pesquisa.

## <a name="display-search-results"></a>Apresentar os resultados da pesquisa

Existem [requisitos de utilização e apresentação](./use-display-requirements.md) dos resultados devolvidos pela API de Pesquisa na Web do Bing. Dado que uma resposta pode incluir vários tipos de resultados, não será suficiente efetuar uma iteração através da coleção `WebPages` de nível superior. Em alternativa, a aplicação de exemplo utilizará o objeto `RankingResponse` para ordenar os resultados a especificar.

> [!NOTE]
> Se quiser apenas um tipo de resultado, utilize o parâmetro de consulta `responseFilter` ou pondere utilizar um dos outros pontos finais de Pesquisa do Bing, como a Pesquisa de Imagens do Bing.

Cada resposta tem um objeto `RankingResponse`, que pode incluir até três coleções: `pole`, `mainline` e `sidebar`. Caso esteja presente, o objeto `pole` é o resultado de pesquisa mais relevante e tem de ser apresentado de forma destacada. O objeto `mainline` contém a maioria dos resultados de pesquisa e é apresentado imediatamente após o objeto `pole`. O objeto `sidebar` inclui resultados de pesquisa auxiliares. Se for possível, estes resultados deverão ser apresentados na barra lateral. Se os limites de ecrã tornarem a barra lateral pouco prática, estes resultados devem ser apresentados após os resultados do objeto `mainline`.

Cada objeto `RankingResponse` inclui uma matriz `RankingItem` que especifica como os resultados têm de ser ordenados. A nossa aplicação de exemplo utiliza os parâmetros `answerType` e `resultIndex` para identificar o resultado.

> [!NOTE]
> Existem outras formas de identificar e classificar os resultados. Para obter mais informações, veja [Using ranking to display results](rank-results.md) (Utilizar a classificação para apresentar resultados).

Vamos analisar o código:

```javascript
// Render the search results from the JSON response.
function renderSearchResults(results) {

    // If spelling was corrected, update the search field.
    if (results.queryContext.alteredQuery)
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // Add Prev / Next links with result count.
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    // Render the results for each section.
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

A função `renderResultsItems()` itera através dos itens em cada coleção `RankingResponse`, mapeia cada resultado de classificação a um resultado da pesquisa com os valores `answerType` e `resultIndex` e chama a função de composição adequada para gerar o HTML. Se o valor `resultIndex` não for especificado para um item, a função `renderResultsItems()` irá iterar através de todos os resultados desse tipo e chamar a função de composição para cada item. O HTML resultante será inserido no elemento `<div>` adequado no `index.html`.

```javascript
// Render search results from the RankingResponse object per rank response and
// use and display requirements.
function renderResultsItems(section, results) {

    var items = results.rankingResponse[section].items;
    var html = [];
    for (var i = 0; i < items.length; i++) {
        var item = items[i];
        // Collection name has lowercase first letter while answerType has uppercase
        // e.g. `WebPages` RankingResult type is in the `webPages` top-level collection.
        var type = item.answerType[0].toLowerCase() + item.answerType.slice(1);
        if (type in results && type in searchItemRenderers) {
            var render = searchItemRenderers[type];
            // This ranking item refers to ONE result of the specified type.
            if ("resultIndex" in item) {
                html.push(render(results[type].value[item.resultIndex], section));
            // This ranking item refers to ALL results of the specified type.
            } else {
                var len = results[type].value.length;
                for (var j = 0; j < len; j++) {
                    html.push(render(results[type].value[j], section, j, len));
                }
            }
        }
    }
    return html.join("\n\n");
}
```

## <a name="review-renderer-functions"></a>Rever funções de compositor

Na nossa aplicação de exemplo, o objeto `searchItemRenderers` inclui funções que geram HTMLs para todos os tipos de resultados de pesquisa.

```javascript
// Render functions for each result type.
searchItemRenderers = {
    webPages: function(item) { ... },
    news: function(item) { ... },
    images: function(item, section, index, count) { ... },
    videos: function(item, section, index, count) { ... },
    relatedSearches: function(item, section, index, count) { ... }
}
```

> [!IMPORTANT]
> A aplicação de exemplo tem compositores para páginas Web, notícias, imagens, vídeos e pesquisas relacionadas. A sua aplicação precisará de compositores para todos os tipos de resultados que possa receber, que podem incluir cálculos, sugestões ortográficas, entidades, fusos horários e definições.

Algumas funções de composição só aceitam o parâmetro `item`. Outras funções aceitam parâmetros adicionais, que podem ser utilizados para compor os itens de forma diferente com base no contexto. Um compositor que não utilize estas informações não precisará de aceitar estes parâmetros.

Os argumentos de contexto são:

| Parâmetro  | Descrição |
|------------|-------------|
| `section` | A secção de resultados (`pole`, `mainline` ou `sidebar`) na qual o item é apresentado. |
| `index`<br>`count` | Disponível quando o item `RankingResponse` especifica que todos os resultados numa determinada coleção serão apresentados; `undefined` em todos os outros casos. O índice do item na respetiva coleção e o número total de itens nessa coleção. Pode utilizar estas informações para numerar os resultados, gerar um HTML diferente para o primeiro ou o último resultado, etc. |

Na aplicação de exemplo, os compositores `images` e `relatedSearches` utilizam os argumentos de contexto para personalizar o HTML gerado. Vamos analisar melhor o compositor `images`:

```javascript
searchItemRenderers = {
    // Render image result with thumbnail.
    images: function(item, section, index, count) {
        var height = 60;
        var width = Math.round(height * item.thumbnail.width / item.thumbnail.height);
        var html = [];
        if (section === "sidebar") {
            if (index) html.push("<br>");
        } else {
            if (!index) html.push("<p class='images'>");
        }
        html.push("<a href='" + item.hostPageUrl + "'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width +
            "' height=" + height + " width=" + width + " title='" + title + "' alt='" + title + "'>");
        html.push("</a>");
        return html.join("");
    },
    // Other renderers are omitted from this sample...
}
```

O compositor de imagens:

* Calcula o tamanho da miniatura da imagem (a largura varia e a altura é fixa, com 60 píxeis).
* Insere o HTML que precede o resultado da imagem com base no contexto.
* Cria a etiqueta HTML `<a>` que liga à página que contém a imagem.
* Cria a etiqueta HTML `<img>` para apresentar a miniatura da imagem.

O compositor de imagens utiliza as variáveis `section` e `index` para apresentar resultados de forma diferente conforme os respetivos locais de origem. É inserida uma quebra de linha (etiqueta `<br>`) entre os resultados de imagens na barra lateral, para que a mesma apresente uma coluna de imagens. Nas outras secções, o primeiro resultado de imagem `(index === 0)` será precedido por uma etiqueta `<p>`.

O tamanho da miniatura é utilizado na etiqueta `<img>` e nos campos `h` e `w` do respetivo URL. Os atributos `title` e `alt` (uma descrição textual da imagem) são criados a partir do nome de anfitrião e do nome da imagem no URL.

Eis um exemplo da forma como as imagens são apresentadas na aplicação de exemplo:

![(Resultados de imagens do Bing)](media/cognitive-services-bing-web-api/web-search-spa-images.png)

## <a name="persist-the-client-id"></a>Manter o ID de cliente

As respostas das APIs de Pesquisa do Bing podem incluir um cabeçalho `X-MSEdge-ClientID`, o qual deve ser reenviado à API com os sucessivos pedidos. Se a sua aplicação utilizar mais do que uma das APIs de Pesquisa do Bing, certifique-se de que é enviado o mesmo ID de cliente nos pedidos em todos os serviços.

Fornecer o cabeçalho `X-MSEdge-ClientID` permite que as APIs do Bing associem as pesquisas de um utilizador. Em primeiro lugar, permite que o motor de busca do Bing aplique um contexto passado às pesquisas para encontrar resultados que melhor satisfaçam o pedido. Se um utilizador tiver procurado termos relacionados com vela, por exemplo, ao procurar posteriormente a palavra "nós" poderá devolver, de preferência, informações sobre os nós utilizados em vela. Em segundo lugar, o Bing pode selecionar utilizadores aleatoriamente para experimentarem funcionalidades novas antes de serem disponibilizadas ao grande público. Fornecer o mesmo ID de cliente em todos os pedidos garante que os utilizadores que foram escolhidos para ver uma funcionalidade a verão sempre. Sem o ID de cliente, os utilizadores poderão ver a funcionalidade aparecer e desaparecer, de forma aparentemente aleatória, nos resultados da pesquisa.

As políticas de segurança do browser, como a Partilha de Recursos Transversais à Origem (CORS), poderão impedir que a aplicação de exemplo aceda ao cabeçalho `X-MSEdge-ClientID`. Esta limitação ocorre quando a origem da resposta da pesquisa é diferente da página que a pediu. Num ambiente de produção, deve abordar esta política ao alojar um script do lado do servidor que faça a chamada à API no mesmo domínio da página Web. Uma vez que a origem do script é a mesma da página Web, o cabeçalho `X-MSEdge-ClientID` ficará disponível para o JavaScript.

> [!NOTE]
> Numa aplicação Web de produção, deve fazer o pedido no lado do servidor mesmo assim. Caso contrário, a sua chave de subscrição da API de Pesquisa do Bing terá de ser incluída na página Web, onde ficará disponível para qualquer pessoa que veja a origem. São-lhe cobradas todas as utilizações feitas com a sua chave de subscrição da API, mesmo os pedidos feitos por partes não autorizadas, pelo que é importante que não revele a sua chave.

Para fins de programação, pode fazer um pedido através de um proxy da CORS. A resposta deste tipo de proxy tem um `Access-Control-Expose-Headers` cabeçalho que filtra cabeçalhos de resposta e os coloca disponíveis para o JavaScript.

É fácil instalar um proxy da CORS para permitir que a nossa aplicação de exemplo aceda ao cabeçalho do ID de cliente. Execute este comando:

```console
npm install -g cors-proxy-server
```

Em seguida, altere o ponto final da Pesquisa na Web do Bing em `script.js` para:

```javascript
http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search
```

Inicie o proxy da CORS com este comando:

```console
cors-proxy-server
```

Deixe a janela de comando aberta enquanto utiliza a aplicação de exemplo. Se a janela for fechada, o proxy é interrompido. Na secção Cabeçalhos HTTP abaixo dos resultados da pesquisa, o cabeçalho `X-MSEdge-ClientID` deverá estar visível. Verifique que é o mesmo para todos os pedidos.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Referência da API de Pesquisa na Web do Bing v7](//docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)