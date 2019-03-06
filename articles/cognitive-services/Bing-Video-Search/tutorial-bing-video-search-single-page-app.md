---
title: 'Tutorial: Criar uma aplicação de pesquisa de vídeos do Bing de página única'
titlesuffix: Azure Cognitive Services
description: Explica como utilizar a API de Pesquisa de Vídeos do Bing numa aplicação Web de página única.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: tutorial
ms.date: 01/31/2019
ms.author: rosh
ms.openlocfilehash: 182783576c8b47db8dd1c60ed2d5c5948fb98672
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57438997"
---
# <a name="tutorial-single-page-video-search-app"></a>Tutorial: Aplicação de pesquisa de vídeos de página única
A API de Pesquisa de Vídeos do Bing permite-lhe pesquisar na Web e obter resultados de vídeos relevantes para uma consulta de pesquisa. Neste tutorial, vamos compilar uma aplicação Web de página única que utiliza a API de Pesquisa do Bing para apresentar resultados da pesquisa na página. A aplicação inclui componentes HTML, CSS e JavaScript.

<!-- Remove until it can be replaced with a sanitized version.
![Single-page Bing Video Search app](./media/video-search-singlepage.png)
-->

> [!NOTE]
> Clicar nos cabeçalhos JSON e HTTP na parte inferior da página mostra a resposta JSON e as informações do pedido HTTP. Estes detalhes podem ser úteis para explorar o serviço.

![Resultados não processados de JSON e HTTP](./media/json-http-raw-results.png)

Esta aplicação de tutorial ilustra como:
> [!div class="checklist"]
> * Fazer uma chamada à API de Pesquisa de Vídeos do Bing em JavaScript
> * Transmitir opções de pesquisa à API de Pesquisa do Bing
> * Apresentar resultados da pesquisa de vídeo ou, opcionalmente, incluir páginas Web, notícias ou imagens
> * Procurar intervalos de tempo de 24 horas, semana passada, mês ou todo o tempo disponível
> * Navegar pelos resultados da pesquisa
> * Gerir o ID de cliente do Bing e a chave de subscrição da API
> * Resolver erros que possam aparecer

A página do tutorial é completamente independente. Não utiliza arquiteturas, folhas de estilo nem ficheiros de imagens externos. Só utiliza funcionalidades da linguagem JavaScript amplamente suportadas e funciona com as versões atuais dos principais browsers.

Neste tutorial, vamos abordar determinadas partes do código de origem. O [código de origem](tutorial-bing-video-search-single-page-app-source.md) completo está disponível. Para executar o exemplo, copie e cole o código de origem num editor de texto e guarde-o como `bing.html`.

## <a name="app-components"></a>Componentes da aplicação
Tal como qualquer aplicação Web de página única, esta aplicação de tutorial tem três partes:

> [!div class="checklist"]
> * HTML - define a estrutura e os conteúdos da página
> * CSS - define o aspeto da página
> * JavaScript - define o comportamento da página

A maioria do código HTML e do CSS é convencional, pelo que não são abordados no tutorial. O HTML contém o formulário de pesquisa no qual o utilizador introduz uma consulta e escolhe as opções de pesquisa. O formulário está ligado ao JavaScript que faz a pesquisa com o atributo `onsubmit` da tag `<form>`:

```html
<form name="bing" onsubmit="return bingWebSearch(this)">
```
O processador `onsubmit` devolve `false`, que impede o formulário de ser submetido para um servidor. O código JavaScript recolhe as informações necessárias do formulário e executa a pesquisa.

O HTML também inclui as divisões (tags `<div>` de HTML) nas quais os resultados da pesquisa aparecem.

## <a name="managing-subscription-key"></a>Gerir a chave de subscrição

Para evitar ter de incluir a chave de subscrição da API de Pesquisa do Bing no código, armazenamo-la no armazenamento persistente do browser. Antes de a chave ser armazenada, pedimos a chave do utilizador. Se a API rejeitar a chave mais tarde, invalidamos a chave armazenada e voltamos a pedi-la ao utilizador.

Definimos as funções `storeValue` e `retrieveValue` que utilizam o objeto `localStorage` (não suportada em todos os browsers) ou um cookie. A função `getSubscriptionKey()` utiliza essas funções para armazenar e obter a chave do utilizador.

``` javascript
// Cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

// ... omitted definitions of store value and retrieve value
// Browsers differ in their support for persistent storage by 
// local HTML files. See the source code for browser-specific
// options.

// Get stored API subscription key, or prompt if it's not found.
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
A tag `<form>` de HTML `onsubmit` chama a função `bingWebSearch`, para devolver os resultados da pesquisa. `bingWebSearch` utiliza `getSubscriptionKey()` para autenticar cada consulta. Conforme mostrado na definição anterior, `getSubscriptionKey` pede a chave ao utilizador caso a mesma não tenha sido introduzida. Depois, a chave é armazenada e utilizada continuamente pela aplicação.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```
## <a name="selecting-search-options"></a>Selecionar as opções de pesquisa
A imagem abaixo mostra a caixa de texto da consulta e as opções que definem a pesquisa.

![Opções de Pesquisa de Notícias do Bing](media/video-search-options.png)

O formulário HTML inclui elementos com os nomes abaixo:

|Elemento|Descrição|
|-|-|
| `where` | Um menu pendente para selecionar o mercado (localização e idioma) utilizado para a pesquisa. |
| `query` | O campo de texto no qual introduzir os termos da pesquisa. |
| `modules` | Caixas de verificação para promover módulos específicos de resultados, todos os resultados ou vídeos relacionados. |
| `when` | Menu pendente para limitar, opcionalmente, a pesquisa ao dia, semana ou mês mais recente. |
| `safe` | Uma caixa de verificação que indica se deve ser utilizada a funcionalidade SafeSearch do Bing para filtrar resultados de conteúdos para adultos. |
| `count` | Campo oculto. O número de resultados da pesquisa a devolver em cada pedido. Altere-o para mostrar menos ou mais resultados por página. |
| `offset`|  Campo oculto. O desfasamento do primeiro resultado da pesquisa no pedido, utilizado para paginação. É reposto para `0` nos pedidos novos. |

> [!NOTE]
> A Pesquisa na Web do Bing disponibiliza outros parâmetros de consulta. Só vamos utilizar alguns desses parâmetros.

``` javascript
// build query options from the HTML form
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));

    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var what = [];
    for (var i = 0; i < form.what.length; i++) 
        if (form.what[i].checked) what.push(form.what[i].value);
    if (what.length) {
        options.push("modules=" + what.join(","));
        options.push("answerCount=9");
    }
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    options.push("textDecorations=true");
    options.push("textFormat=HTML");
    return options.join("&");
}
```

Por exemplo, o parâmetro `SafeSearch` numa chamada à API real pode ser `strict`, `moderate` ou `off`, sendo `moderate` a predefinição. Contudo, o formulário utiliza uma caixa de verificação, que só tem dois estados. O código JavaScript converte esta definição em `strict` ou em `off` (`moderate` não é utilizado).

## <a name="performing-the-request"></a>Fazer o pedido
Tendo a consulta, a cadeia de opções e a chave de API, a função `BingWebSearch` utiliza um objeto `XMLHttpRequest` para fazer o pedido ao ponto final da Pesquisa do Bing.

```javascript
// Search on the query, using search options, authenticated by the key.
function bingWebSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_headers", "paging1", "paging2", "error");

    var endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search";
    var request = new XMLHttpRequest();
    var queryurl = endpoint + "?q=" + encodeURIComponent(query) + "&" + options;

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
    request.addEventListener("load", handleOnLoad);

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
Após a conclusão do pedido HTTP, o JavaScript chama o processador de eventos `load`, `handleOnLoad()`, para processar um pedido HTTP GET bem-sucedido à API. 

```javascript
// handle Bing search request results
function handleOnLoad() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
    }

    // show raw JSON and headers
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " + 
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "Videos") {//"SearchResponse" && "rankingResponse" in jsobj) {
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
> Se ocorrer um erro na operação de pesquisa, a API de Pesquisa de Notícias do Bing devolve um código de estado HTTP que não 200 e inclui as informações do erro na resposta JSON. Além disso, se o pedido tiver uma limitação de frequência, a API devolve uma resposta vazia.
Um pedido HTTP bem-sucedido *não* significa necessariamente que a pesquisa em si foi concluída com êxito. 

Grande parte do código em ambas as funções acima é dedicado à resolução de erros. Poderão ocorrer erros nas seguintes fases:

|Fase|Potenciais erros|Resolvido por|
|-|-|-|
|Criar o objeto de pedido JavaScript|URL inválido|bloco `try`/`catch`|
|Fazer o pedido|Erros de rede, ligações abortadas|Processadores de eventos `error` e `abort`|
|Fazer a pesquisa|Pedido inválido, JSON inválido, limites de frequência|testes no processador de eventos `load`|

Os erros são processados mediante a chamada de `renderErrorMessage()` com todos os detalhes dos mesmos conhecidos. Se a resposta passar todos os testes de erros, chamamos `renderSearchResults()` para apresentar os resultados da pesquisa na página.

## <a name="displaying-search-results"></a>Apresentar os resultados da pesquisa
A principal função para apresentar os resultados da pesquisa é `renderSearchResults()`. Esta função utiliza o JSON que o serviço Pesquisa de Notícias do Bing devolve e compõe os resultados de notícias e as pesquisas relacionadas, se existirem.

```javascript
// render the search results given the parsed JSON response
function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    // Render the results to the mainline section
    for (section in { mainline: 0 }) {
         showDiv(section, renderResultsItems(section, results));
    }
}
```
Os resultados da pesquisa são devolvidos como o objeto `value` de nível superior na resposta JSON. Transmitimo-los à nossa função `renderResultsItems()`, que itera pelos mesmos e chama uma função para compor cada item em HTML. O HTML resultante é devolvido a `renderSearchResults()`, onde é inserido na divisão `results` da página.

```javascript
// render search results
    function renderResultsItems(section, results) {   

        var items = results.value;
        var html = [];
        for (var i = 0; i < items.length; i++) { 
            var item = items[i];
            // collection name has lowercase first letter
            var type = "videos";
            var render = searchItemRenderers[type];
            html.push(render(item, section));  
        }
    return html.join("\n\n");
}
```

A API de Pesquisa de Notícias do Bing devolve até quatro tipos diferentes de resultados relacionados, cada um no seu próprio objeto de nível superior. São:

|Relação|Descrição|
|-|-|
|`pivotSuggestions`|Consultas que substituem uma palavra pivô na pesquisa original por outra diferente. Por exemplo, se procurar "flores vermelhas", uma palavra pivô poderá ser "vermelhas" e uma sugestão pivô "flores amarelas".|
|`queryExpansions`|Consultas que reduzem a consulta original mediante a adição de mais termos. Por exemplo, se procurar "Microsoft Surface", uma expansão da consulta poderá ser "Microsoft Surface Pro".|
|`relatedSearches`|Consultas que também foram introduzidas por outros utilizadores que introduziram a pesquisa original. Por exemplo, se procurar "Monte Rainier", uma consulta relacionada poderá ser "Monte de Santa Helena".|
|`similarTerms`|Consultas cujo significado é semelhante ao da pesquisa original. Por exemplo, se pesquisar por “escolas”, um termo semelhante poderá ser “educação”.|

Conforme vimos anteriormente em `renderSearchResults()`, só convertermos as sugestões `relatedItems` e colocamos as ligações resultantes na barra lateral da página.

## <a name="rendering-result-items"></a>Compor os itens dos resultados

No código JavaScript, o objeto `searchItemRenderers` pode conter *compositores*, funções que geram o HTML de cada tipo de resultado da pesquisa. A página da pesquisa de vídeo só utiliza `videos`. Relativamente aos diversos tipos de compositores, veja os outros tutoriais.

```javascript
searchItemRenderers = {
    news: function(item) { ... },
    webPages: function (item) { ... }, 
    images: function(item, index, count) { ... },
    videos: function (item, section, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```
Uma função de compositor pode aceitar os seguintes parâmetros:

|Parâmetro|Descrição|
|-|-|
|`item`| O objeto JavaScript que contém as propriedades do item, como o URL e a descrição.|
|`index`| O índice do item do resultado dentro da respetiva coleção.|
|`count`| O número de itens na coleção do item do resultado da pesquisa.|

Os parâmetros `index` e `count` podem ser utilizados para numerar os resultados, para gerar código HTML especial para o início ou o fim de uma coleção, para inserir quebras de linha após um determinado número de itens, etc. Se um compositor não precisar desta funcionalidade, não tem de aceitar estes dois parâmetros.

O compositor `video` é mostrado no seguinte fragmento de JavaScript. Ao utilizar o ponto final dos Vídeos, todos os resultados são do tipo `Videos`. `searchItemRenderers` são apresentados no segmento de código seguinte.

```javascript
// render functions for various types of search results
    searchItemRenderers = {

    videos: function (item, section, index, count) {
        var height = 60;
        var width = Math.round(height * item.thumbnail.width / item.thumbnail.height);
        var html = [];

        html.push("<p class='images'>");
        html.push("<a href='" + item.hostPageUrl + "'>");
        var title = escapeQuotes(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<img src='" + item.thumbnailUrl + "&h=" + height + "&w=" + width +
            "' height=" + height + " width=" + width + " title='" + title + "' alt='" + title + "'>");
        html.push("</a>");
        html.push("<br>");
        html.push("<nobr><a href='" + item.contentUrl + "'>Video page source</a> - ");
        html.push(title.replace("\n", " (").replace(/([a-z0-9])\.([a-z0-9])/g, "$1.<wbr>$2") + ")</p>");
        return html.join("");
    }
}
```

A função de compositor:
> [!div class="checklist"]
> * Cria uma tag de parágrafo, atribui-a à classe `images` e envia-a para a matriz html.
> * Calcula o tamanho da miniatura de imagem (a largura é fixa, com 60 pixéis, e a altura é calculada proporcionalmente).
> * Cria a tag `<img>` de HTML para apresentar a miniatura de imagem. 
> * Cria as tags `<a>` de HTML que ligam à imagem e à página que a contém.
> * Cria a descrição que apresenta as informações sobre a imagem e o site no qual a imagem se encontra.

O tamanho da miniatura é utilizado na tag `<img>` e nos campos `h` e `w` do respetivo URL. Depois, o [serviço de miniaturas do Bing](resize-and-crop-thumbnails.md) entrega uma miniatura com exatamente esse tamanho.

## <a name="persisting-client-id"></a>ID de cliente persistente
As respostas das APIs de Pesquisa do Bing podem incluir um cabeçalho `X-MSEdge-ClientID`, o qual deve ser reenviado à API com os sucessivos pedidos. Se estiverem a ser utilizadas várias APIs de Pesquisa do Bing, deve ser utilizado o mesmo ID de cliente em todas as APIs, se possível.

Fornecer o cabeçalho `X-MSEdge-ClientID` permite às APIs do Bing associarem todas as pesquisas de um determinado utilizador, o que tem duas importantes vantagens.

Em primeiro lugar, permite que o motor de busca do Bing aplique um contexto passado às pesquisas para encontrar resultados que deixem o utilizador mais satisfeito. Se um utilizador tiver procurado termos relacionados com vela, por exemplo, ao procurar posteriormente a palavra "nós" poderá devolver, de preferência, informações sobre os nós utilizados em vela.

Em segundo lugar, o Bing pode selecionar utilizadores aleatoriamente para experimentarem funcionalidades novas antes de serem disponibilizadas ao grande público. Fornecer o mesmo ID de cliente em todos os pedidos garante que os utilizadores que veem uma funcionalidade a verão sempre. Sem o ID de cliente, os utilizadores poderão ver a funcionalidade aparecer e desaparecer, de forma aparentemente aleatória, nos resultados da pesquisa.

As políticas de segurança do browser (CORS) podem impedir que o cabeçalho `X-MSEdge-ClientID` esteja disponível para o JavaScript. Esta limitação ocorre quando a origem da resposta da pesquisa é diferente da página que a pediu. Num ambiente de produção, deve abordar esta política ao alojar um script do lado do servidor que faça a chamada à API no mesmo domínio da página Web. Uma vez que a origem do script é a mesma da página Web, o cabeçalho `X-MSEdge-ClientID` fica então disponível para o JavaScript.

> [!NOTE]
> Numa aplicação Web de produção, deve fazer o pedido no lado do servidor. Caso contrário, a chave da API de Pesquisa do Bing tem de ser incluída na página Web, onde ficará disponível para qualquer pessoa que veja a origem. São-lhe cobradas todas as utilizações feitas com a sua chave de subscrição da API, mesmo os pedidos feitos por partes não autorizadas, pelo que é importante que não revele a sua chave.

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
> [Bing Video Search API reference](//docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) (Referência da API de Pesquisa de Vídeos do Bing)
