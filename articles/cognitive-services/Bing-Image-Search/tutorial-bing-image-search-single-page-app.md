---
title: 'Tutorial: Criar uma aplicação web de página única - API de pesquisa de imagens do Bing'
titleSuffix: Azure cognitive services
description: A API de Pesquisa de Imagens do Bing permite-lhe procurar na Web imagens relevantes e de alta qualidade. Utilize este tutorial para criar uma aplicação Web de página única que possa enviar consultas de pesquisa para a API e apresentar os resultados na página Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 353641c514c9171e211221b84b13c5f09a413a48
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341227"
---
# <a name="tutorial-create-a-single-page-app-using-the-bing-image-search-api"></a>Tutorial: Criar uma aplicação de página única com a API de pesquisa de imagens do Bing

A API de Pesquisa de Imagens do Bing permite-lhe procurar na Web imagens relevantes e de alta qualidade. Utilize este tutorial para criar uma aplicação Web de página única que possa enviar consultas de pesquisa para a API e apresentar os resultados na página Web. Este tutorial é semelhante ao [tutorial correspondente](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md) para Pesquisa na Web do Bing.

A aplicação de tutorial ilustra como:

> [!div class="checklist"]
> * Fazer uma chamada à API de Pesquisa de Imagens do Bing em JavaScript
> * Melhorar os resultados da pesquisa com opções de pesquisa
> * Apresentar e navegar pelos resultados da pesquisa
> * Pedir e processar uma chave de subscrição da API e o ID de cliente do Bing.

O código fonte completo para este tutorial está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Image-Search).

## <a name="prerequisites"></a>Pré-requisitos

* A versão mais recente do [Node.js](https://nodejs.org/).
* A arquitetura [Express.js](https://expressjs.com/) para Node.js. Instruções de instalação para o código-fonte estão disponíveis no ficheiro Leia-me de exemplo do GitHub.

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="manage-and-store-user-subscription-keys"></a>Gerir e armazenar chaves de subscrição de utilizador

Esta aplicação utiliza o armazenamento persistente dos browsers para armazenar chaves de subscrição da API. Se não for armazenada nenhuma chave, a página Web irá pedir ao utilizador a respetiva chave e irá armazená-la para utilização posterior. Se a chave for rejeitada pela API mais tarde, a aplicação irá removê-la do armazenamento.


Defina as funções `storeValue` e `retrieveValue` para utilizar o objeto `localStorage` (se for suportado pelo browser) ou um cookie.

```javascript
// Cookie names for data being stored
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";
// The Bing Image Search API endpoint
BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/images/search";

try { //Try to use localStorage first
    localStorage.getItem;   

    window.retrieveValue = function (name) {
        return localStorage.getItem(name) || "";
    }
    window.storeValue = function(name, value) {
        localStorage.setItem(name, value);
    }
} catch (e) {
    //If the browser doesn't support localStorage, try a cookie
    window.retrieveValue = function (name) {
        var cookies = document.cookie.split(";");
        for (var i = 0; i < cookies.length; i++) {
            var keyvalue = cookies[i].split("=");
            if (keyvalue[0].trim() === name) return keyvalue[1];
        }
        return "";
    }
    window.storeValue = function (name, value) {
        var expiry = new Date();
        expiry.setFullYear(expiry.getFullYear() + 1);
        document.cookie = name + "=" + value.trim() + "; expires=" + expiry.toUTCString();
    }
}
```

A função `getSubscriptionKey()` tenta obter uma chave anteriormente armazenada com o método `retrieveValue`. Se não for encontrada nenhuma chave, será pedido ao utilizador a sua chave, que será armazenada com o método `storeValue`.

```javascript

// Get the stored API subscription key, or prompt if it's not found
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```

A tag `<form>` de HTML `onsubmit` chama a função `bingWebSearch`, para devolver os resultados da pesquisa. `bingWebSearch` utiliza `getSubscriptionKey` para autenticar cada consulta. Conforme mostrado na definição anterior, `getSubscriptionKey` pede a chave ao utilizador caso a mesma não tenha sido introduzida. Depois, a chave é armazenada e utilizada continuamente pela aplicação.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value,
bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="send-search-requests"></a>Enviar pedidos de pesquisa

Esta aplicação utiliza uma tag `<form>` de HTML para enviar inicialmente pedidos de pesquisa de utilizador, ao utilizar o atributo `onsubmit` para chamar `newBingImageSearch()`.

```html
<form name="bing" onsubmit="return newBingImageSearch(this)">
```

Por predefinição, o processador `onsubmit` devolve `false`, que impede o formulário de ser submetido.

## <a name="select-search-options"></a>Selecionar as opções de pesquisa

![[Formulário de Pesquisa de Imagens do Bing]](media/cognitive-services-bing-images-api/image-search-spa-form.png)

A API de Pesquisa de Imagens do Bing oferece vários [parâmetros de consulta de filtro](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#filter-query-parameters) para restringir e filtrar os resultados da pesquisa. O formulário HTML nesta aplicação utiliza e apresenta as seguintes opções de parâmetros:

|              |                                                                                                                                                                                    |
|--------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `where`      | Um menu pendente para selecionar o mercado (localização e idioma) utilizado para a pesquisa.                                                                                             |
| `query`      | O campo de texto no qual introduzir os termos da pesquisa.                                                                                                                                 |
| `aspect`     | Botões de opção para escolher as proporções das imagens encontradas: aproximadamente quadrada, larga ou alta.                                                                                     |
| `color`      |                                                                                                                                                                                    |
| `when`       | Menu pendente para limitar, opcionalmente, a pesquisa ao dia, semana ou mês mais recente.                                                                                          |
| `safe`       | Uma caixa de verificação que indica se deve ser utilizada a funcionalidade SafeSearch do Bing para filtrar resultados de conteúdos para adultos.                                                                                      |
| `count`      | Campo oculto. O número de resultados da pesquisa a devolver em cada pedido. Altere-o para mostrar menos ou mais resultados por página.                                                            |
| `offset`     | Campo oculto. O desfasamento do primeiro resultado da pesquisa no pedido, utilizado para paginação. É reposto para `0` nos pedidos novos.                                                           |
| `nextoffset` | Campo oculto. Ao receber um resultado de pesquisa, este campo é definido para o valor de `nextOffset` na resposta. Com este campo evita resultados sobrepostos em páginas sucessivas. |
| `stack`      | Campo oculto. Uma lista com codificação JSON dos desvios das páginas anteriores de resultados de pesquisa, para navegar novamente para as páginas anteriores.                                                      |

A função `bingSearchOptions()` formata estas opções numa cadeia de consulta parcial, que pode ser utilizada nos pedidos da API da aplicação.  

```javascript
// Build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var aspect = "all";
    for (var i = 0; i < form.aspect.length; i++) {
        if (form.aspect[i].checked) {
            aspect = form.aspect[i].value;
            break;
        }
    }
    options.push("aspect=" + aspect);
    if (form.color.value) options.push("color=" + form.color.value);
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    return options.join("&");
}
```

## <a name="performing-the-request"></a>Fazer o pedido

Com a consulta de pesquisa, a cadeia de opções e a chave de API, a função `BingImageSearch()` utiliza um objeto XMLHttpRequest para fazer o pedido ao ponto final da Pesquisa de Imagens do Bing.


```javascript
// perform a search given query, options string, and API key
function bingImageSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("results", "related", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

    // open the request
    try {
        request.open("GET", queryurl);
    }
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // add request headers
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);

    // event handler for successful response
    request.addEventListener("load", handleBingResponse);

    // event handler for erorrs
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // event handler for aborted request
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // send the request
    request.send();
    return false;
}
```

Após a conclusão do pedido HTTP com êxito, o JavaScript chama o processador de eventos de "carga" `handleBingResponse()`, para processar um pedido HTTP GET com êxito.

```javascript
// handle Bing search request results
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
    }

    // show raw JSON and HTTP request
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " +
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "Images") {
                if (jsobj.nextOffset) document.forms.bing.nextoffset.value = jsobj.nextOffset;
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSubscriptionKey();

        // some error responses don't have a top-level errors object, so gin one up
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // display HTTP status code
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // add all fields from all error responses
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // also display Bing Trace ID if it isn't blocked by CORS
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // and display the error message
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> Os pedidos HTTP bem-sucedidos podem conter informações de pesquisa com falhas. Se ocorrer um erro durante a operação de pesquisa, a API de Pesquisa de Imagens do Bing devolve um código de estado HTTP que não 200 e informações do erro na resposta JSON. Além disso, se o pedido tiver limites de velocidade, a API irá devolver uma resposta vazia.

## <a name="display-the-search-results"></a>Apresentar os resultados da pesquisa

Os resultados da pesquisa são apresentados pela função `renderSearchResults()`, que utiliza o JSON devolvido pelo serviço de Pesquisa de Imagens do Bing e chama uma função de compositor apropriada em quaisquer imagens e pesquisas relacionadas.

```javascript
function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    showDiv("results", renderImageResults(results.value));
    if (results.relatedSearches)
        showDiv("sidebar", renderRelatedItems(results.relatedSearches));
}
```

Os resultados da pesquisa de imagens estão incluídos no objeto `value` de nível superior na resposta JSON. Estes são transmitidos a `renderImageResults()`, que itera através dos resultados e converte cada item em HTML.

```javascript
function renderImageResults(items) {
    var len = items.length;
    var html = [];
    if (!len) {
        showDiv("noresults", "No results.");
        hideDivs("paging1", "paging2");
        return "";
    }
    for (var i = 0; i < len; i++) {
        html.push(searchItemRenderers.images(items[i], i, len));
    }
    return html.join("\n\n");
}
```

A API de Pesquisa de Imagens do Bing pode devolver quatro tipos de sugestões de pesquisa para ajudar a orientar as experiências de pesquisa dos utilizadores, cada um no seu próprio objeto de nível superior:

| Sugestão         | Descrição                                                                                                                                                                                                         |
|--------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `pivotSuggestions` | Consultas que substituem uma palavra pivô na pesquisa original por outra diferente. Por exemplo, se procurar "flores vermelhas", uma palavra pivô poderá ser "vermelhas" e uma sugestão pivô "flores amarelas". |
| `queryExpansions`  | Consultas que reduzem a consulta original mediante a adição de mais termos. Por exemplo, se procurar "Microsoft Surface", uma expansão da consulta poderá ser "Microsoft Surface Pro".                                   |
| `relatedSearches`  | Consultas que também foram introduzidas por outros utilizadores que introduziram a pesquisa original. Por exemplo, se procurar "Monte Rainier", uma consulta relacionada poderá ser "Monte de Santa Helena".                       |
| `similarTerms`     | Consultas cujo significado é semelhante ao da pesquisa original. Por exemplo, se procurar "gatinhos", um termo semelhante poderá ser "fofos".                                                                   |

Esta aplicação apenas apresenta as sugestões de `relatedItems` e coloca as ligações resultantes na barra lateral da página.

## <a name="rendering-search-results"></a>Composição de resultados da pesquisa

Nesta aplicação, o objeto `searchItemRenderers` contém funções de compositor que geram o HTML de cada tipo de resultado da pesquisa.

```javascript
searchItemRenderers = {
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```

Estas funções de compositor podem aceitar os seguintes parâmetros:

| Parâmetro         | Descrição                                                                                              |
|---------|----------------------------------------------------------------------------------------------|
| `item`  | O objeto JavaScript que contém as propriedades do item, como o URL e a descrição. |
| `index` | O índice do item do resultado dentro da respetiva coleção.                                          |
| `count` | O número de itens na coleção do item do resultado da pesquisa.                                  |

Os parâmetros `index` e `count` são utilizados para numerar os resultados, gerar HTML para coleções e organizar o conteúdo. Mais concretamente:

* Calcula o tamanho da miniatura da imagem (a largura varia, com um mínimo de 120 píxeis e a altura está fixa em 90 píxeis).
* Cria a tag `<img>` de HTML para apresentar a miniatura de imagem.
* Cria as tags `<a>` de HTML que ligam à imagem e à página que a contém.
* Cria a descrição que apresenta as informações sobre a imagem e o site no qual a imagem se encontra.

```javascript
    images: function (item, index, count) {
        var height = 120;
        var width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
        var html = [];
        if (index === 0) html.push("<p class='images'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<p class='images' style='max-width: " + width + "px'>");
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width +
            "' height=" + height + " width=" + width + "'>");
        html.push("<br>");
        html.push("<nobr><a href='" + item.contentUrl + "'>Image</a> - ");
        html.push("<a href='" + item.hostPageUrl + "'>Page</a></nobr><br>");
        html.push(title.replace("\n", " (").replace(/([a-z0-9])\.([a-z0-9])/g, "$1.<wbr>$2") + ")</p>");
        return html.join("");
    }, // relatedSearches renderer omitted
```

A `height` e `width` da imagem da miniatura são utilizadas na tag `<img>` e nos campos `h` e `w` no respetivo URL. Isto permite que o Bing devolva [uma miniatura](resize-and-crop-thumbnails.md) exatamente desse tamanho.

## <a name="persisting-client-id"></a>ID de cliente persistente

As respostas das APIs de Pesquisa do Bing podem incluir um cabeçalho `X-MSEdge-ClientID`, o qual deve ser reenviado à API com os sucessivos pedidos. Se estiverem a ser utilizadas várias APIs de Pesquisa do Bing, deve ser utilizado o mesmo ID de cliente em todas as APIs, se possível.

Fornecer o cabeçalho `X-MSEdge-ClientID` permite que as APIs do Bing associem todas as pesquisas de um utilizador, o que é útil porque:

Em primeiro lugar, permite que o motor de busca do Bing aplique um contexto passado às pesquisas para encontrar resultados que melhor satisfaçam o pedido. Se um utilizador tiver procurado termos relacionados com vela, por exemplo, ao procurar posteriormente a palavra "nós" poderá devolver, de preferência, informações sobre os nós utilizados em vela.

Em segundo lugar, o Bing pode selecionar utilizadores aleatoriamente para experimentarem novas funcionalidades antes de serem disponibilizadas para o público. Fornecer o mesmo ID de cliente em todos os pedidos garante que os utilizadores que foram escolhidos para ver uma funcionalidade a verão sempre. Sem o ID de cliente, os utilizadores poderão ver a funcionalidade aparecer e desaparecer, de forma aparentemente aleatória, nos resultados da pesquisa.

As políticas de segurança do browser (CORS) podem impedir que o cabeçalho `X-MSEdge-ClientID` esteja disponível para o JavaScript. Esta limitação ocorre quando a origem da resposta da pesquisa é diferente da página que a pediu. Num ambiente de produção, deve abordar esta política ao alojar um script do lado do servidor que faça a chamada à API no mesmo domínio da página Web. Uma vez que a origem do script é a mesma da página Web, o cabeçalho `X-MSEdge-ClientID` ficará disponível para o JavaScript.

> [!NOTE]
> Numa aplicação Web de produção, deve fazer o pedido no lado do servidor mesmo assim. Caso contrário, a chave da API de Pesquisa do Bing terá de ser incluída na página Web, onde ficará disponível para qualquer pessoa que veja a origem. São-lhe cobradas todas as utilizações feitas com a sua chave de subscrição da API, mesmo os pedidos feitos por partes não autorizadas, pelo que é importante que não revele a sua chave.

Para fins de programação, pode fazer o pedido da API de Pesquisa na Web do Bing através de um proxy do CORS. A resposta de um proxy deste tipo inclui um cabeçalho `Access-Control-Expose-Headers`, que adiciona os cabeçalhos das respostas à lista de permissões e os disponibiliza para o JavaScript.

É fácil instalar um proxy do CORS para permitir que a nossa aplicação de tutorial aceda ao cabeçalho do ID de cliente. Em primeiro lugar, se ainda não o tiver, [instale Node.js](https://nodejs.org/en/download/). Em seguida, emita o comando seguinte numa janela de comando:

    npm install -g cors-proxy-server

Depois, altere o ponto final da Pesquisa na Web do Bing no ficheiro HTML para:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Por fim, inicie o proxy do CORS com o comando seguinte:

    cors-proxy-server

Deixe a janela de comando aberta enquanto utiliza a aplicação de tutorial. Se a janela for fechada, o proxy é interrompido. Na secção Cabeçalhos HTTP expansíveis, abaixo dos resultados da pesquisa, pode agora ver o cabeçalho `X-MSEdge-ClientID` (entre outros) e confirmar se é o mesmo em todos os pedidos.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Extrair os detalhes da Imagem com a API de Pesquisa de Imagens do Bing](tutorial-image-post.md)

## <a name="see-also"></a>Consulte também

* [Bing Image Search API reference](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) (Referência da API de Pesquisa de Imagens do Bing)
