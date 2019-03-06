---
title: 'Tutorial: Aplicação de web de página única de pesquisa de entidades do Bing'
titlesuffix: Azure Cognitive Services
description: Mostra como utilizar a API de Pesquisa de Entidades do Bing numa aplicação Web de página única.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: tutorial
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 569ec697738860dc339a270d828c8908c31ac157
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57454532"
---
# <a name="tutorial-single-page-web-app"></a>Tutorial: Aplicação Web de página única

A API de Pesquisa de Entidades do Bing permite-lhe procurar informações sobre *entidades* e *locais* na Web. Pode pedir um destes dois tipos de resultados, ou ambos, numa dada consulta. As definições dos locais e das entidades são indicadas abaixo.

|||
|-|-|
|Entidades|Pessoas famosas, sítios conhecidos e coisas que podem ser encontradas por nome|
|Locais|Restaurantes, hotéis e outros negócios locais que podem ser encontrados por nome *ou* por tipo (restaurantes italianos)|

Neste tutorial, vamos compilar uma aplicação Web de página única que utiliza a API de Pesquisa de Entidades do Bing para apresentar resultados de pesquisa diretamente na página. A aplicação inclui componentes HTML, CSS e JavaScript.

A API permite-lhe priorizar os resultados por localização. Numa aplicação móvel, pode pedir ao dispositivo a localização do mesmo. Numa aplicação Web, pode utilizar a função `getPosition()`. Contudo, esta chamada só funciona em contextos seguros e poderá não dar uma localização precisa. Além disso, o utilizador poderá querer procurar entidades perto de uma localização que não aquela onde está.

Por esse motivo, a nossa aplicação chama o serviço Mapas Bing para obter a latitude e a longitude de uma localização introduzida pelo utilizador. O utilizador pode, depois, introduzir o nome de um ponto de referência (“Space Needle”) ou uma morada completa ou parcial (“Nova Iorque”) e a API do Mapas Bing fornece as coordenadas.

<!-- Remove until we can replace with a sanitized version.
![[Single-page Bing Entity Search app]](media/entity-search-spa-demo.png)
-->

> [!NOTE]
> Clicar nos cabeçalhos JSON e HTTP na parte inferior da página mostra a resposta JSON e as informações do pedido HTTP. Estes detalhes são úteis para explorar o serviço.

A aplicação de tutorial ilustra como:

> [!div class="checklist"]
> * Fazer uma chamada à API de Pesquisa de Entidades do Bing em JavaScript
> * Fazer uma chamada à API `locationQuery` do Mapas Bing em JavaScript
> * Transmitir opções de pesquisa às chamadas à API
> * Apresentar os resultados da pesquisa
> * Gerir o ID de cliente do Bing e as chaves de subscrição da API
> * Lidar com erros que possam aparecer

A página do tutorial é completamente independente. Não utiliza arquiteturas, folhas de estilo nem mesmo ficheiros de imagens externos. Só utiliza funcionalidades da linguagem JavaScript amplamente suportadas e funciona com as versões atuais dos principais browsers.

Neste tutorial, só vamos abordar determinadas partes do código de origem. O código de origem completo está disponível [numa página separada](tutorial-bing-entities-search-single-page-app-source.md). Copie e cole esse código num editor de texto e guarde-o como `bing.html`.

> [!NOTE]
> Este tutorial é bastante parecido com o [tutorial da aplicação de Pesquisa na Web do Bing de página única](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md), mas lida apenas com resultados da pesquisa de entidades.

## <a name="app-components"></a>Componentes da aplicação

Tal como qualquer aplicação Web de página única, a aplicação de tutorial tem três partes:

> [!div class="checklist"]
> * HTML - define a estrutura e os conteúdos da página
> * CSS - define o aspeto da página
> * JavaScript - define o comportamento da página

Este tutorial não aborda a maioria do HTML ou do CSS em detalhe, uma vez que estes são simples.

O HTML contém o formulário de pesquisa no qual o utilizador introduz uma consulta e escolhe as opções de pesquisa. O formulário está ligado ao JavaScript que realmente executa a pesquisa com o atributo `<form>` da etiqueta `onsubmit`:

```html
<form name="bing" onsubmit="return newBingEntitySearch(this)">
```

O processador `onsubmit` devolve `false`, que impede o formulário de ser submetido para um servidor. Na verdade, o código JavaScript recolhe as informações necessárias do formulário e executa a pesquisa.

A pesquisa é feita em duas fases. Primeiro, se o utilizador tiver introduzido uma restrição de localização, é feita uma consulta do Mapas Bing para a converter em coordenadas. A chamada de retorno dessa consulta inicia, depois, a consulta da Pesquisa de Entidades do Bing.

O HTML também inclui as divisões (tags `<div>` de HTML) nas quais os resultados da pesquisa aparecem.

## <a name="managing-subscription-keys"></a>Gerir as chaves de subscrição

> [!NOTE]
> Esta aplicação precisa de chaves de subscrição para a API de Pesquisa do Bing e para a API Mapas Bing. Pode utilizar uma [chave da Pesquisa do Bing de avaliação](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) e uma [chave dos Mapas Bing básica](https://www.microsoft.com/maps/create-a-bing-maps-key).

Para evitar ter de incluir as chaves de subscrição da API de Pesquisa do Bing e da API Mapas Bing no código, armazenamo-las no armazenamento persistente do browser. Se uma das chaves não tiver sido armazenada, pedimo-la e armazenamo-la para utilização futura. Se a API rejeitar a chave mais tarde, invalidamos a chave armazenada e voltamos a pedi-la ao utilizador quando este fizer a sua próxima pesquisa.

Definimos as funções `storeValue` e `retrieveValue` que utilizam o objeto `localStorage` (se for suportado pelo browser) ou um cookie. A nossa função `getSubscriptionKey()` utiliza essas funções para armazenar e obter a chave do utilizador.

```javascript
// cookie names for data we store
SEARCH_API_KEY_COOKIE = "bing-search-api-key";
MAPS_API_KEY_COOKIE   = "bing-maps-api-key";
CLIENT_ID_COOKIE      = "bing-search-client-id";

// API endpoints
SEARCH_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/entities";
MAPS_ENDPOINT   = "http://dev.virtualearth.net/REST/v1/Locations";

// ... omitted definitions of storeValue() and retrieveValue()

// get stored API subscription key, or prompt if it's not found
function getSubscriptionKey(cookie_name, key_length, api_name) {
    var key = retrieveValue(cookie_name);
    while (key.length !== key_length) {
        key = prompt("Enter " + api_name + " API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(cookie_name, key);
    return key;
}

function getMapsSubscriptionKey() {
    return getSubscriptionKey(MAPS_API_KEY_COOKIE, 64, "Bing Maps");
}

function getSearchSubscriptionKey() {
    return getSubscriptionKey(SEARCH_API_KEY_COOKIE, 32, "Bing Search");
}
```

A tag `<body>` HTML inclui o atributo `onload` que chama `getSearchSubscriptionKey()` e `getMapsSubscriptionKey()` quando o carregamento da página for concluído. Essas chamadas servem para pedir imediatamente ao utilizador as chaves dele, caso ainda não as tenha introduzido.

```html
<body onload="document.forms.bing.query.focus(); getSearchSubscriptionKey(); getMapsSubscriptionKey();">
```

## <a name="selecting-search-options"></a>Selecionar as opções de pesquisa

![[Formulário de Pesquisa de Entidades do Bing]](media/entity-search-spa-form.png)

O formulário HTML inclui os controlos seguintes:

| | |
|-|-|
|`where`|Um menu pendente para selecionar o mercado (localização e idioma) utilizado para a pesquisa.|
|`query`|O campo de texto no qual introduzir os termos da pesquisa.|
|`safe`|Uma caixa de verificação que indica se SafeSearch está ativado (restringe resultados “para adultos”).|
|`what`|Um menu para escolher pesquisar por entidades, locais ou ambos.|
|`mapquery`|O campo de texto no qual o utilizador pode introduzir uma morada completa ou parcial, um ponto de referência, etc., para ajudar a Pesquisa de Entidades do Bing a devolver resultados mais relevantes.|

> [!NOTE]
> Atualmente, os resultados de locais só estão disponíveis nos Estados Unidos. Os menus `where` e `what` têm um código que aplica esta restrição. Se escolher um mercado fora dos EUA enquanto Locais estiver selecionado no menu `what`, `what` é alterado para Qualquer Coisa. Se escolher Locais enquanto estiver selecionado um mercado fora dos EUA no menu `where`, `where` é alterado para EUA.

A nossa função `bingSearchOptions()` JavaScript converte estes campos numa cadeia de consulta parcial para a APIs de Pesquisa do Bing.

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.what.selectedIndex) options.push("responseFilter=" + form.what.value);
    return options.join("&");
}
```

Por exemplo, a funcionalidade SafeSearch pode ter o valor `strict`, `moderate` ou `off`, sendo `moderate` a predefinição. Contudo, o nosso formulário utiliza uma caixa de verificação, que só tem dois estados. O código JavaScript converte esta definição em `strict` ou em `off` (não utilizamos `moderate`).

`bingSearchOptions()` não processa o campo `mapquery`, porque é utilizado na consulta de localização dos Mapas Bing, não na Pesquisa de Entidades do Bing.

## <a name="obtaining-a-location"></a>Obter uma localização

A API Mapas Bing oferece um [método `locationQuery`](//msdn.microsoft.com/library/ff701711.aspx), que é utilizado para encontrar a latitude e a longitude da localização introduzida pelo utilizador. Essas coordenadas são, depois, transmitidas à API de Pesquisa de Entidades do Bing com o pedido do utilizador. Os resultados da pesquisa dão prioridade às entidades e aos locais que estão perto da localização especificada.

Não é possível aceder à API Mapas Bing com uma consulta `XMLHttpRequest` comum numa aplicação Web, porque o serviço não suporta consultas com diversas origens. Felizmente, suporta JSONP (o “P” significa “padded” ou “preenchido”). Uma resposta JSON é uma resposta JSON comum encapsulada numa chamada à função. O pedido é feito mediante a inserção de uma tag `<script>` no documento. (O carregamento de scripts não está sujeito às políticas de segurança do browser.)

A função `bingMapsLocate()` cria e insere a tag `<script>` da consulta. O segmento `jsonp=bingMapsCallback` da cadeia de consulta especifica o nome da função que vai ser chamada com a resposta.

```javascript
function bingMapsLocate(where) {

    where = where.trim();
    var url = MAPS_ENDPOINT + "?q=" + encodeURIComponent(where) + 
                "&jsonp=bingMapsCallback&maxResults=1&key=" + getMapsSubscriptionKey();

    var script = document.getElementById("bingMapsResult")
    if (script) script.parentElement.removeChild(script);

    // global variable holds reference to timer that will complete the search if the maps query fails
    timer = setTimeout(function() {
        timer = null;
        var form = document.forms.bing;
        bingEntitySearch(form.query.value, "", bingSearchOptions(form), getSearchSubscriptionKey());
    }, 5000);

    script = document.createElement("script");
    script.setAttribute("type", "text/javascript");
    script.setAttribute("id", "bingMapsResult");
    script.setAttribute("src", url);
    script.setAttribute("onerror", "BingMapsCallback(null)");
    document.body.appendChild(script);

    return false;
}
```

> [!NOTE]
> Se a API Mapas Bing não responder, a função `bingMapsCallBack()` nunca é chamada. Normalmente, isso significa que `bingEntitySearch()` não é chamado e que os resultados da pesquisa de entidades não aparecem. Para evitar este cenário, `bingMapsLocate()` também define um temporizador para chamar `bingEntitySearch()` após cinco segundos. A função de chamada de retorno tem uma lógica para evitar fazer a pesquisa de entidades duas vezes.

Após a conclusão da consulta, a função `bingMapsCallback()` é chamada, conforme pedido.

```javascript
function bingMapsCallback(response) {

    if (timer) {    // we beat the timer; stop it from firing
        clearTimeout(timer);
        timer = null;
    } else {        // the timer beat us; don't do anything
        return; 
    }

    var location = "";
    var name = "";
    var radius = 1000;

    if (response) {
        try {
            if (response.statusCode === 401) {
                invalidateMapsKey();
            } else if (response.statusCode === 200) {
                var resource = response.resourceSets[0].resources[0];
                var coords   = resource.point.coordinates;
                name         = resource.name;

                // the radius is the largest of the distances between the location and the corners
                // of its bounding box (in case it's not in the center) with a minimum of 1 km
                try {
                    var bbox    = resource.bbox;
                    radius  = Math.max(haversineDistance(bbox[0], bbox[1], coords[0], coords[1]),
                                       haversineDistance(coords[0], coords[1], bbox[2], bbox[1]),
                                       haversineDistance(bbox[0], bbox[3], coords[0], coords[1]),
                                       haversineDistance(coords[0], coords[1], bbox[2], bbox[3]), 1000);
                } catch(e) {  }
                var location = "lat:" + coords[0] + ";long:" + coords[1] + ";re:" + Math.round(radius);
            }
        }
        catch (e) { }   // response is unexpected. this isn't fatal, so just don't provide location
    }

    var form = document.forms.bing;
    if (name) form.mapquery.value = name;
    bingEntitySearch(form.query.value, location, bingSearchOptions(form), getSearchSubscriptionKey());

}
```

Juntamente com a latitude e a longitude, a consulta da Pesquisa de Entidades do Bing precisa de um *raio* que indica a precisão das informações da localização. O raio é calculado com o *retângulo delimitador* disponibilizado na resposta dos Mapas do Bing. O retângulo delimitador é um retângulo que rodeia toda a localização. Por exemplo, se o utilizador introduzir `NYC`, o resultado contém coordenadas aproximadamente centrais de Nova Iorque e um retângulo delimitador que engloba a cidade. 

Primeiro, calculamos as distâncias desde as coordenadas principais até aos quatro cantos do retângulo delimitador através da função `haversineDistance()` (não mostrada). Das quatro distâncias, utilizamos a maior para o raio. O raio mínimo é de um quilómetro. Se não for fornecido um retângulo delimitador na resposta, esse valor também é utilizado como predefinição.

Depois de obtidas as coordenadas e o raio, chamamos `bingEntitySearch()` para fazer a pesquisa propriamente dita.

## <a name="performing-the-search"></a>Fazer a pesquisa

Dada a consulta, uma localização, uma cadeia de opções e a chave de API, a função `BingEntitySearch()` faz o pedido da Pesquisa de Entidades do Bing.

```javascript
// perform a search given query, location, options string, and API keys
function bingEntitySearch(query, latlong, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "error");

    var request = new XMLHttpRequest();
    var queryurl = SEARCH_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

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

    if (latlong) request.setRequestHeader("X-Search-Location", latlong);

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

Após a conclusão do pedido HTTP, o JavaScript chama o nosso processador de eventos `load`, a função `handleBingResponse()`, para processar um pedido HTTP GET bem-sucedido à API. 

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
            if (jsobj._type === "SearchResponse") {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    if (divHidden("pole") && divHidden("mainline") && divHidden("sidebar")) 
        showDiv("noresults", "No results.<p><small>Looking for restaurants or other local businesses? Those currently areen't supported outside the US.</small>");
    }

    // Any other HTTP status is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSearchKey();

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
> Um pedido HTTP bem-sucedido *não* significa necessariamente que a pesquisa em si foi concluída com êxito. Se ocorrer um erro na operação de pesquisa, a API de Pesquisa de Entidades do Bing devolve um código de estado HTTP que não 200 e inclui as informações do erro na resposta JSON. Além disso, se o pedido tiver uma limitação de frequência, a API devolve uma resposta vazia.

A maioria do código nas funções anteriores é dedicado ao processamento de erros. Poderão ocorrer erros nas seguintes fases:

|Fase|Potenciais erros|Resolvido por|
|-|-|-|
|Criar o objeto de pedido JavaScript|URL inválido|bloco `try`/`catch`|
|Fazer o pedido|Erros de rede, ligações abortadas|Processadores de eventos `error` e `abort`|
|Fazer a pesquisa|Pedido inválido, JSON inválido, limites de frequência|testes no processador de eventos `load`|

Os erros são processados mediante a chamada de `renderErrorMessage()` com todos os detalhes dos mesmos conhecidos. Se a resposta passar todos os testes de erros, chamamos `renderSearchResults()` para apresentar os resultados da pesquisa na página.

## <a name="displaying-search-results"></a>Apresentar os resultados da pesquisa

A API de Pesquisa de Entidades do Bing [requer que os resultados sejam apresentados numa ordem especificada](use-display-requirements.md). Uma vez que a API pode devolver dois tipos diferentes de respostas, iterar através da coleção `Entities` ou `Places` de nível superior na resposta JSON e apresentar esses resultados não é suficiente. (Se pretender apenas um tipo de resultado, utilize o parâmetro de consulta `responseFilter`.)

Em vez disso, utilizamos a coleção `rankingResponse` nos resultados da pesquisa para ordenar os resultados a apresentar. Este objeto diz respeito aos itens nas coleções `Entitiess` e/ou `Places`.

`rankingResponse` pode conter até três coleções de resultados da pesquisa, designados `pole`, `mainline` e `sidebar`. 

Caso esteja presente, `pole` é o resultado de pesquisa mais relevante e deve ser apresentado de forma destacada. `mainline` refere-se ao grosso dos resultados da pesquisa. Os resultados principais devem ser apresentados imediatamente a seguir a `pole` (ou primeiro, se `pole` não estiver presente). 

Por fim, `sidebar` refere-se aos resultados da pesquisa secundários. Podem ser apresentados numa barra lateral ou simplesmente a seguir aos resultados principais. Para a nossa aplicação do tutorial, escolhemos a segunda opção.

Cada item numa coleção `rankingResponse` refere-se aos itens reais do resultado da pesquisa de duas formas diferentes, ainda que equivalentes.

| | |
|-|-|
|`id`|`id` é semelhante a um URL, mas não deve ser utilizado para ligações. O tipo `id` de um resultado de classificação corresponde ao `id` de um item do resultado da pesquisa numa coleção de respostas *ou* a uma coleção de respostas completa (como `Entities`).
|`answerType`<br>`resultIndex`|`answerType` refere-se à coleção de respostas de nível superior que contém o resultado (por exemplo, `Entities`). `resultIndex` refere-se ao índice dos resultados dentro dessa coleção. Se `resultIndex` for omitido, o resultado da classificação refere-se a toda a coleção.

> [!NOTE]
> Para obter mais informações sobre esta parte da resposta da pesquisa, veja [Rank Results](rank-results.md) (Classificar os Resultados).

Para encontrar o item do resultado da pesquisa referenciado, pode utilizar o método que for mais adequado para a sua aplicação. No código do nosso tutorial, utilizamos `answerType` e `resultIndex` para localizar cada resultado da pesquisa.

Por fim, vamos ver a nossa função `renderSearchResults()`. Esta função é iterada nas três coleções `rankingResponse` que representam as três secções dos resultados da pesquisa. Em cada secção, chamamos `renderResultsItems()` para compor os respetivos resultados.

```javascript
// render the search results given the parsed JSON response
function renderSearchResults(results) {

    // if spelling was corrected, update search field
    if (results.queryContext.alteredQuery) 
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // for each possible section, render the results from that section
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

## <a name="rendering-result-items"></a>Compor os itens dos resultados

O nosso código JavaScript tem um objeto, `searchItemRenderers`, que contém *compositores*, que são funções que geram o HTML para cada tipo de resultado da pesquisa.

```javascript
searchItemRenderers = { 
    entities: function(item) { ... },
    places: function(item) { ... }
}
```

Uma função de compositor pode aceitar os parâmetros seguintes:

| | |
|-|-|
|`item`|O objeto JavaScript que contém as propriedades do item, como o URL e a descrição.|
|`index`|O índice do item do resultado dentro da respetiva coleção.|
|`count`|O número de itens na coleção do item do resultado da pesquisa.|

Os parâmetros `index` e `count` podem ser utilizados para numerar os resultados, para gerar código HTML especial para o início ou o fim de uma coleção, para inserir quebras de linha após um determinado número de itens, etc. Se um compositor não precisar desta funcionalidade, não tem de aceitar estes dois parâmetros. Na verdade, não são utilizados na nossa aplicação do tutorial.

Vamos analisar melhor o compositor `entities`:

```javascript
    entities: function(item) {
        var html = [];
        html.push("<p class='entity'>");
        if (item.image) {
            var img = item.image;
            if (img.hostPageUrl) html.push("<a href='" + img.hostPageUrl + "'>");
            html.push("<img src='" + img.thumbnailUrl +  "' title='" + img.name + "' height=" + img.height + " width= " + img.width + ">");
            if (img.hostPageUrl) html.push("</a>");
            if (img.provider) {
                var provider = img.provider[0];
                html.push("<small>Image from ");
                if (provider.url) html.push("<a href='" + provider.url + "'>");
                html.push(provider.name ? provider.name : getHost(provider.url));
                if (provider.url) html.push("</a>");
                html.push("</small>");
            }
        }
        html.push("<p>");
        if (item.entityPresentationInfo) {
            var pi = item.entityPresentationInfo;
            if (pi.entityTypeHints || pi.entityTypeDisplayHint) {
                html.push("<i>");
                if (pi.entityTypeDisplayHint) html.push(pi.entityTypeDisplayHint);
                else if (pi.entityTypeHints) html.push(pi.entityTypeHints.join("/"));
                html.push("</i> - ");
            }
        }
        html.push(item.description);
        if (item.webSearchUrl) html.push("&nbsp;<a href='" + item.webSearchUrl + "'>More</a>")
        if (item.contractualRules) {
            html.push("<p><small>");
            var rules = [];
            for (var i = 0; i < item.contractualRules.length; i++) {
                var rule = item.contractualRules[i];
                var link = [];
                if (rule.license) rule = rule.license;
                if (rule.url) link.push("<a href='" + rule.url + "'>");
                link.push(rule.name || rule.text || rule.targetPropertyName + " source");
                if (rule.url) link.push("</a>");
                rules.push(link.join(""));
            }
            html.push("License: " + rules.join(" - "));
            html.push("</small>");
        }
        return html.join("");
    }, // places renderer omitted
```

A nossa função de compositor de entidades:

> [!div class="checklist"]
> * Cria a tag `<img>` HTML para apresentar a miniatura da imagem, se existir. 
> * Cria a tag `<a>` HTML que liga à página que contém a imagem.
> * Cria a descrição que apresenta as informações sobre a imagem e o site no qual a imagem se encontra.
> * Incorpora a classificação da entidade mediante a utilização das sugestões de apresentação, se existirem.
> * Inclui uma ligação para uma pesquisa do Bing para obter mais informações sobre a entidade.
> * Apresenta eventuais informações relativas a licenciamento ou atribuição de que as origens de dados precisem.

## <a name="persisting-client-id"></a>ID de cliente persistente

As respostas das APIs de Pesquisa do Bing podem incluir um cabeçalho `X-MSEdge-ClientID`, o qual deve ser reenviado à API com os sucessivos pedidos. Se estiverem a ser utilizadas várias APIs de Pesquisa do Bing, deve ser utilizado o mesmo ID de cliente em todas as APIs, se possível.

Fornecer o cabeçalho `X-MSEdge-ClientID` permite às APIs do Bing associarem todas as pesquisas de um determinado utilizador, o que tem duas importantes vantagens.

Em primeiro lugar, permite que o motor de busca do Bing aplique um contexto passado às pesquisas para encontrar resultados que melhor satisfaçam o pedido. Se um utilizador tiver pesquisado termos relacionados com vela, por exemplo, uma pesquisa posterior pela palavra "cais" poderá devolver, preferencialmente, informações sobre cais onde se possa ancorar um barco à vela.

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
> [Bing Entity Search API reference](//docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference) (Referência da API de Pesquisa de Entidades do Bing)

> [!div class="nextstepaction"]
> [Bing Maps API documentation](//msdn.microsoft.com/library/dd877180.aspx) (Documentação da API Mapas do Bing)
