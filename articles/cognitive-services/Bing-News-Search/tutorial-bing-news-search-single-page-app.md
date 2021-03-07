---
title: 'Tutorial: Criar uma aplicação web de uma página única utilizando a API de Pesquisa de Notícias Bing'
titleSuffix: Azure Cognitive Services
description: Utilize este tutorial para construir uma aplicação web de uma página que possa enviar consultas de pesquisa para a API Bing News e exibir os resultados dentro da página web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-js
ms.openlocfilehash: cb9e68efd27deb3bf66d3c286c0cd7a128d8bf59
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430586"
---
# <a name="tutorial-create-a-single-page-web-app"></a>Tutorial: Criar uma aplicação web de uma página

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

A API de Pesquisa de Notícias do Bing permite-lhe pesquisar na Web e obter resultados dos tipos de notícias relevantes para uma consulta de pesquisa. Neste tutorial, vamos compilar uma aplicação Web de página única que utiliza a API de Pesquisa de Notícias do Bing para apresentar resultados da pesquisa na página. A aplicação inclui componentes HTML, CSS e JavaScript. O código-fonte desta amostra está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/BingNewsSearchApp.html).

<!-- Remove until we can replace it with sanitized copy
![Single-page Bing News Search app](media/news-search-singlepage.png)
-->

> [!NOTE]
> Clicar nos cabeçalhos JSON e HTTP na parte inferior da página mostra a resposta JSON e as informações do pedido HTTP. Estes detalhes podem ser úteis para explorar o serviço.

A aplicação de tutorial ilustra como:
> [!div class="checklist"]
> * Fazer uma chamada à API de Pesquisa de Notícias do Bing em JavaScript
> * Transmitir opções de pesquisa à API de Pesquisa de Notícias do Bing
> * Apresentar resultados de pesquisa de notícias de quatro categorias: qualquer tipo, empresarial, estado de funcionamento ou política, de períodos de tempo de 24 horas, na última semana, mês ou todo o tempo disponível
> * Navegar pelos resultados da pesquisa
> * Gerir o ID de cliente do Bing e a chave de subscrição da API
> * Resolver erros que possam aparecer

A página do tutorial é completamente independente. Não utiliza arquiteturas, folhas de estilo nem ficheiros de imagens externos. Só utiliza funcionalidades da linguagem JavaScript amplamente suportadas e funciona com as versões atuais dos principais browsers.


## <a name="prerequisites"></a>Pré-requisitos

Para acompanhar o tutorial, precisa de chaves de subscrição para a API de Pesquisa de Bing. Se não tiver isto, terá de criá-los:

* Uma subscrição do Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Assim que tiver a subscrição do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" Crie um recurso Bing Search crie um recurso "  target="_blank"> Bing Search no portal </a> Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.

## <a name="app-components"></a>Componentes da aplicação
Tal como qualquer aplicação Web de página única, esta aplicação de tutorial tem três partes:

> [!div class="checklist"]
> * HTML - define a estrutura e os conteúdos da página
> * CSS - define o aspeto da página
> * JavaScript - define o comportamento da página

A maioria do código HTML e do CSS é convencional, pelo que não são abordados no tutorial. O HTML contém o formulário de pesquisa no qual o utilizador introduz uma consulta e escolhe as opções de pesquisa. O formulário está ligado ao JavaScript, que executa efetivamente a pesquisa com o atributo `onsubmit` da etiqueta `<form>`:

```html
<form name="bing" onsubmit="return newBingNewsSearch(this)">
```
O processador `onsubmit` devolve `false`, que impede o formulário de ser submetido para um servidor. O código JavaScript recolhe as informações necessárias do formulário e executa a pesquisa.

O HTML também inclui as divisões (tags `<div>` de HTML) nas quais os resultados da pesquisa aparecem.

## <a name="managing-subscription-key"></a>Gerir a chave de subscrição

Para evitar ter de incluir a chave de subscrição da API de Pesquisa do Bing no código, armazenamo-la no armazenamento persistente do browser. Antes de a chave ser armazenada, pedimos a chave do utilizador. Se a API rejeitar a chave mais tarde, invalidamos a chave armazenada e voltamos a pedi-la ao utilizador.

Definimos as funções `storeValue` e `retrieveValue` que utilizam o objeto `localStorage` (não suportada em todos os browsers) ou um cookie. A função `getSubscriptionKey()` utiliza essas funções para armazenar e obter a chave do utilizador. Pode utilizar o ponto final global abaixo ou o ponto final [personalizado subdomínio](../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal Azure para o seu recurso.

``` javascript
// Cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

// Bing Search API endpoint
BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/news";

// ... omitted definitions of storeValue() and retrieveValue()
// Browsers differ in their support for persistent storage by 
// local HTML files. See the source code for browser-specific
// options.

// Get stored API subscription key, or 
// prompt if it's not found.
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
A imagem abaixo mostra a caixa de texto da consulta e as opções que definem a pesquisa de notícias sobre financiamento de escolas.

![Opções de Pesquisa de Notícias do Bing](media/news-search-categories.png)

O formulário HTML inclui elementos com os nomes abaixo:

|Elemento|Descrição|
|-|-|
| `where` | Um menu pendente para selecionar o mercado (localização e idioma) utilizado para a pesquisa. |
| `query` | O campo de texto no qual introduzir os termos da pesquisa. |
| `category` | Caixas de verificação para promover tipos específicos de resultados. Por exemplo, promover a Saúde aumenta a classificação de notícias sobre saúde. |
| `when` | Menu pendente para limitar, opcionalmente, a pesquisa ao dia, semana ou mês mais recente. |
| `safe` | Uma caixa de verificação que indica se deve ser utilizada a funcionalidade SafeSearch do Bing para filtrar resultados de conteúdos para adultos. |
| `count` | Campo oculto. O número de resultados de pesquisa a devolver em cada pedido. Altere-o para mostrar menos ou mais resultados por página. |
| `offset`|  Campo oculto. O desfasamento do primeiro resultado da pesquisa no pedido, utilizado para paginação. É reposto para `0` nos pedidos novos. |

> [!NOTE]
> A Pesquisa na Web do Bing disponibiliza outros parâmetros de consulta. Só vamos utilizar alguns desses parâmetros.

``` javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);

    for (var i = 0; i < form.category.length; i++) {
        if (form.category[i].checked) {
            category = form.category[i].value;
            break;
        }
    }
    if (category.valueOf() != "all".valueOf()) { 
        options.push("category=" + category); 
        }
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    return options.join("&");
}
```

Por exemplo, o parâmetro `SafeSearch` numa chamada à API real pode ser `strict`, `moderate` ou `off`, sendo `moderate` a predefinição. Contudo, o formulário utiliza uma caixa de verificação, que só tem dois estados. O código JavaScript converte esta definição em `strict` ou em `off` (`moderate` não é utilizado).

## <a name="performing-the-request"></a>Fazer o pedido
Tendo a consulta, a cadeia de opções e a chave de API, a função `BingNewsSearch` utiliza um objeto `XMLHttpRequest` para fazer o pedido ao ponto final da Pesquisa de Notícias do Bing.

```javascript
// perform a search given query, options string, and API key
function bingNewsSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("results", "related", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
     if (category.valueOf() != "all".valueOf()) {
        var queryurl = BING_ENDPOINT + "/search?" + "?q=" + encodeURIComponent(query) + "&" + options;
    }
    else
    {
        if (query){
        var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;
        }
        else {
            var queryurl = BING_ENDPOINT + "?" + options;
        }
    }

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
Após a conclusão do pedido HTTP, o JavaScript chama o processador de eventos `load`, a função `handleBingResponse()`, para processar um pedido HTTP GET bem-sucedido à API. 

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
            if (jsobj._type === "News") {
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
> Um pedido HTTP bem-sucedido *não* significa necessariamente que a pesquisa em si foi concluída com êxito. Se ocorrer um erro na operação de pesquisa, a API de Pesquisa de Notícias do Bing devolve um código de estado HTTP que não 200 e inclui as informações do erro na resposta JSON. Além disso, se o pedido tiver uma limitação de frequência, a API devolve uma resposta vazia.

A maioria do código nas funções anteriores é dedicado ao processamento de erros. Poderão ocorrer erros nas seguintes fases:

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

    showDiv("results", renderResults(results.value));
    if (results.relatedSearches)
        showDiv("sidebar", renderRelatedItems(results.relatedSearches));
}
```
Os resultados principais da pesquisa são devolvidos como o objeto `value` de nível superior na resposta JSON. Transmitimo-los à nossa função `renderResults()`, que itera pelos mesmos e chama uma função separada para compor cada item em HTML. O HTML resultante é devolvido a `renderSearchResults()`, onde é inserido na divisão `results` da página.

```javascript
function renderResults(items) {
    var len = items.length;
    var html = [];
    if (!len) {
        showDiv("noresults", "No results.");
        hideDivs("paging1", "paging2");
        return "";
    }
    for (var i = 0; i < len; i++) {
        html.push(searchItemRenderers.news(items[i], i, len));
    }
    return html.join("\n\n");
}
```
A API de Pesquisa de Notícias do Bing devolve até quatro tipos diferentes de resultados relacionados, cada um no seu próprio objeto de nível superior. A saber:

|Relação|Descrição|
|-|-|
|`pivotSuggestions`|Consultas que substituem uma palavra “pivô” na pesquisa original por outra diferente. Por exemplo, se procurar "flores vermelhas", uma palavra pivô poderá ser "vermelhas" e uma sugestão pivô "flores amarelas".|
|`queryExpansions`|Consultas que reduzem a consulta original mediante a adição de mais termos. Por exemplo, se procurar "Microsoft Surface", uma expansão da consulta poderá ser "Microsoft Surface Pro".|
|`relatedSearches`|Consultas que também foram introduzidas por outros utilizadores que introduziram a pesquisa original. Por exemplo, se procurar "Monte Rainier", uma consulta relacionada poderá ser "Monte de Santa Helena".|
|`similarTerms`|Consultas cujo significado é semelhante ao da pesquisa original. Por exemplo, se pesquisar por “escolas”, um termo semelhante poderá ser “educação”.|

Conforme vimos anteriormente em `renderSearchResults()`, só convertermos as sugestões `relatedItems` e colocamos as ligações resultantes na barra lateral da página.

## <a name="rendering-result-items"></a>Compor os itens dos resultados

No código JavaScript, o objeto `searchItemRenderers` contém *compositores*, funções que geram o HTML de cada tipo de resultado da pesquisa.

```javascript
searchItemRenderers = {
    news: function(item) { ... },
    webPages: function (item) { ... }, 
    images: function(item, index, count) { ... },
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

O `news` renderizador é mostrado no seguinte excerto javaScript:
```javascript
    // render news story
    news: function (item) {
        var html = [];
        html.push("<p class='news'>");
        if (item.image) {
            width = 60;
            height = Math.round(width * item.image.thumbnail.height / item.image.thumbnail.width);
            html.push("<img src='" + item.image.thumbnail.contentUrl +
                "&h=" + height + "&w=" + width + "' width=" + width + " height=" + height + ">");
        }
        html.push("<a href='" + item.url + "'>" + item.name + "</a>");
        if (item.category) html.push(" - " + item.category);
        if (item.contractualRules) {    // MUST display source attributions
            html.push(" (");
            var rules = [];
            for (var i = 0; i < item.contractualRules.length; i++)
                rules.push(item.contractualRules[i].text);
                html.push(rules.join(", "));
                html.push(")");
            }
        html.push(" (" + getHost(item.url) + ")");
        html.push("<br>" + item.description);
        return html.join("");
    },
```
A função de compositor de notícias:
> [!div class="checklist"]
> * Cria uma tag de parágrafo, atribui-a à classe `news` e envia-a para a matriz html.
> * Calcula o tamanho da miniatura de imagem (a largura é fixa, com 60 pixéis, e a altura é calculada proporcionalmente).
> * Cria a etiqueta HTML `<img>` para apresentar a miniatura da imagem. 
> * Cria as tags `<a>` de HTML que ligam à imagem e à página que a contém.
> * Cria a descrição que apresenta as informações sobre a imagem e o site no qual a imagem se encontra.

O tamanho da miniatura é utilizado na etiqueta `<img>` e nos campos `h` e `w` do respetivo URL. Depois, o [serviço de miniaturas do Bing](../bing-web-search/resize-and-crop-thumbnails.md) entrega uma miniatura com exatamente esse tamanho.

## <a name="persisting-client-id"></a>ID de cliente persistente
As respostas das APIs de Pesquisa do Bing podem incluir um cabeçalho `X-MSEdge-ClientID`, o qual deve ser reenviado à API com os sucessivos pedidos. Se estiverem a ser utilizadas várias APIs de Pesquisa do Bing, deve ser utilizado o mesmo ID de cliente em todas as APIs, se possível.

Fornecer o cabeçalho `X-MSEdge-ClientID` permite às APIs do Bing associarem todas as pesquisas de um determinado utilizador, o que tem duas importantes vantagens.

Em primeiro lugar, permite que o motor de busca do Bing aplique um contexto passado às pesquisas para encontrar resultados que deixem o utilizador mais satisfeito. Se um utilizador tiver procurado termos relacionados com vela, por exemplo, ao procurar posteriormente a palavra "nós" poderá devolver, de preferência, informações sobre os nós utilizados em vela.

Em segundo lugar, o Bing pode selecionar utilizadores aleatoriamente para experimentarem funcionalidades novas antes de serem disponibilizadas ao grande público. Fornecer o mesmo ID de cliente em todos os pedidos garante que os utilizadores que veem uma funcionalidade a verão sempre. Sem o ID de cliente, os utilizadores poderão ver a funcionalidade aparecer e desaparecer, de forma aparentemente aleatória, nos resultados da pesquisa.

As políticas de segurança do browser (CORS) podem impedir que o cabeçalho `X-MSEdge-ClientID` esteja disponível para o JavaScript. Esta limitação ocorre quando a origem da resposta da pesquisa é diferente da página que a pediu. Num ambiente de produção, deve abordar esta política ao alojar um script do lado do servidor que faça a chamada à API no mesmo domínio da página Web. Uma vez que a origem do script é a mesma da página Web, o cabeçalho `X-MSEdge-ClientID` ficará disponível para o JavaScript.

> [!NOTE]
> Numa aplicação Web de produção, deve fazer o pedido no lado do servidor. Caso contrário, a chave da API de Pesquisa do Bing tem de ser incluída na página Web, onde ficará disponível para qualquer pessoa que veja a origem. São-lhe cobradas todas as utilizações feitas com a sua chave de subscrição da API, mesmo os pedidos feitos por partes não autorizadas, pelo que é importante que não revele a sua chave.

Para fins de programação, pode fazer o pedido da API de Pesquisa na Web do Bing através de um proxy do CORS. A resposta de tal proxy tem um `Access-Control-Expose-Headers` cabeçalho que permite cabeçalhos de resposta e os coloca disponíveis para JavaScript.

É fácil instalar um proxy do CORS para permitir que a nossa aplicação de tutorial aceda ao cabeçalho do ID de cliente. Em primeiro lugar, se ainda não o tiver, [instale Node.js](https://nodejs.org/en/download/). Em seguida, emita o comando seguinte numa janela de comando:

```console
npm install -g cors-proxy-server
```

Em seguida, altere o ponto de terminação Bing Web Search no ficheiro HTML para:\
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search`

Por fim, inicie o proxy do CORS com o comando seguinte:

```console
cors-proxy-server
```

Deixe a janela de comando aberta enquanto utiliza a aplicação de tutorial. Se a janela for fechada, o proxy é interrompido. Na secção Cabeçalhos HTTP expansíveis, abaixo dos resultados da pesquisa, pode agora ver o cabeçalho `X-MSEdge-ClientID` (entre outros) e confirmar se é o mesmo em todos os pedidos.

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Referência da API de Pesquisa de Notícias do Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference)