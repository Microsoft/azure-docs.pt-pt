---
title: 'Tutorial: Como carregar a imagem usando a API de Pesquisa Visual Bing'
titleSuffix: Azure Cognitive Services
description: Aprenda a enviar uma imagem para Bing, obtenha informações sobre isso, exiba a resposta.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: scottwhi
ms.custom: devx-track-js
ms.openlocfilehash: d5dc762499342f0004073fb6c240c98c4ac2bc09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91334570"
---
# <a name="tutorial-upload-images-to-the-bing-visual-search-api"></a>Tutorial: Faça upload de imagens para a API de Pesquisa Visual Bing

A API de Pesquisa Visual Bing permite-lhe pesquisar na web imagens semelhantes às que carrega. Utilize este tutorial para criar uma aplicação web que possa enviar uma imagem para a API e mostrar as introspeções que retorna dentro da página web. Note que esta aplicação não adere a todos os [requisitos de Utilização e Visualização de Bing](../bing-web-search/use-display-requirements.md) para a utilização da API.

Pode encontrar o código fonte completo para esta amostra com tratamento adicional de erros e anotações no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchUploadImage.html).

A aplicação de tutorial ilustra como:

> [!div class="checklist"]
> * Faça o upload de uma imagem para a API de Pesquisa Visual Bing
> * Mostrar resultados de pesquisa de imagem numa aplicação web
> * Explore os diferentes insights fornecidos pela API

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-structure-the-webpage"></a>Criar e estruturar a página web

Crie uma página HTML que envia uma imagem para a API de Pesquisa Visual Bing, recebe insights e exibe-as. No seu editor favorito ou IDE, crie um ficheiro chamado "uploaddemo.html". Adicione ao ficheiro a seguinte estrutura html básica:

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title>Visual Search Upload Demo</title>
    </head>

    <body>
    </body>
</html>
```

Divida a página numa secção de pedido, onde o utilizador forneça todas as informações necessárias para o pedido e uma secção de resposta onde os insights são apresentados. Adicione as `<div>` seguintes etiquetas ao `<body>` . A `<hr>` etiqueta separa visualmente a secção de pedidos da secção de resposta:

```html
<div id="requestSection"></div>
<hr />
<div id="responseSection"></div>
```

Adicione uma `<script>` etiqueta à etiqueta para conter o `<head>` JavaScript para a aplicação:

```html
<script>
<\script>
```

## <a name="get-the-upload-file"></a>Obtenha o ficheiro de upload

Para permitir que o utilizador selecione uma imagem para carregar, a aplicação utiliza a `<input>` etiqueta com o atributo tipo definido para `file` . A UI precisa de deixar claro que a aplicação utiliza o Bing para obter os resultados da pesquisa.

Adicione o seguinte `<div>` ao `requestSection` `<div>` . A entrada de ficheiro aceita um ficheiro individual de qualquer tipo de imagem (por exemplo, .jpg, .gif, .png). O evento `onchange` especifica o processador que é chamado quando um utilizador seleciona um ficheiro.

A `<output>` etiqueta é utilizada para exibir uma miniatura da imagem selecionada:

```html
<div>
    <p>Select image to get insights from Bing:
        <input type="file" accept="image/*" id="uploadImage" name="files[]" size=40 onchange="handleFileSelect('uploadImage')" />
    </p>

    <output id="thumbnail"></output>
</div>
```

## <a name="create-a-file-handler"></a>Criar um manipulador de ficheiros

Crie uma função de manipulador que possa ler na imagem que pretende carregar. Enquanto se iterando através dos ficheiros no `FileList` objeto, o manipulador deve certificar-se de que o ficheiro selecionado é um ficheiro de imagem e que o seu tamanho é de 1 MB ou menos. Se a imagem for maior, deve reduzir o seu tamanho antes de a carregar. Por último, o manipulador exibe uma miniatura da imagem:

```javascript
function handleFileSelect(selector) {

    var files = document.getElementById(selector).files; // A FileList object

    for (var i = 0, f; f = files[i]; i++) {

        // Ensure the file is an image file.
        if (!f.type.match('image.*')) {
            alert("Selected file must be an image file.");
            document.getElementById("uploadImage").value = null;
            continue;
        }

        // Image must be <= 1 MB and should be about 1500px.
        if (f.size > 1000000) {
            alert("Image must be less than 1 MB.");
            document.getElementById("uploadImage").value = null;
            continue;
        }

        var reader = new FileReader();

        // Capture the file information.
        reader.onload = (function(theFile) {
            return function(e) {
                var fileOutput = document.getElementById('thumbnail');

                if (fileOutput.childElementCount > 0) {
                    fileOutput.removeChild(fileOutput.lastChild);  // Remove the current pic, if it exists
                }

                // Render thumbnail.
                var span = document.createElement('span');
                span.innerHTML = ['<img class="thumb" src="', e.target.result,
                                    '" title="', escape(theFile.name), '"/>'].join('');
                fileOutput.insertBefore(span, null);
            };
        })(f);

        // Read in the image file as a data URL.
        reader.readAsDataURL(f);
    }
}
```

## <a name="add-and-store-a-subscription-key"></a>Adicione e guarde uma chave de subscrição

A aplicação requer uma chave de subscrição para fazer chamadas para a API de Pesquisa Visual Bing. Para este tutorial, você vai providenciar na UI. Adicione a seguinte `<input>` etiqueta (com o atributo tipo definido para texto) à `<body>` etiqueta logo abaixo do `<output>` ficheiro:

```html
    <div>
        <p>Subscription key: 
            <input type="text" id="key" name="subscription" size=40 maxlength="32" />
        </p>
    </div>
```

Com a imagem e a chave de subscrição, pode fazer a chamada para bing Visual Search para obter informações sobre a imagem. Neste tutorial, a chamada utiliza o mercado predefinido `en-us` e o valor de pesquisa seguro `moderate` ().

Esta aplicação tem a opção de alterar estes valores. Adicione o seguinte `<div>` abaixo da chave de subscrição `<div>` . A aplicação utiliza uma `<select>` etiqueta para fornecer uma lista de drop-down para valores de pesquisa de mercado e seguros. Ambas as listas apresentam o valor predefinido.

```html
<div>
    <p><a href="#" onclick="expandCollapse(options.id)">Query options</a></p>

    <div id="options" style="display:none">
        <p style="margin-left: 20px">Market: 
            <select id="mkt">
                <option value="es-AR">Argentina (Spanish)</option>
                <option value="en-AU">Australia (English)</option>
                <option value="de-AT">Austria (German)</option>
                <option value="nl-BE">Belgium (Dutch)</option>
                <option value="fr-BE">Belgium (French)</option>
                <option value="pt-BR">Brazil (Portuguese)</option>
                <option value="en-CA">Canada (English)</option>
                <option value="fr-CA">Canada (French)</option>
                <option value="es-CL">Chile (Spanish)</option>
                <option value="da-DK">Denmark (Danish)</option>
                <option value="fi-FI">Finland (Finnish)</option>
                <option value="fr-FR">France (French)</option>
                <option value="de-DE">Germany (German)</option>
                <option value="zh-HK">Hong Kong SAR(Traditional Chinese)</option>
                <option value="en-IN">India (English)</option>
                <option value="en-ID">Indonesia (English)</option>
                <option value="it-IT">Italy (Italian)</option>
                <option value="ja-JP">Japan (Japanese)</option>
                <option value="ko-KR">Korea (Korean)</option>
                <option value="en-MY">Malaysia (English)</option>
                <option value="es-MX">Mexico (Spanish)</option>
                <option value="nl-NL">Netherlands (Dutch)</option>
                <option value="en-NZ">New Zealand (English)</option>
                <option value="no-NO">Norway (Norwegian)</option>
                <option value="zh-CN">People's Republic of China (Chinese)</option>
                <option value="pl-PL">Poland (Polish)</option>
                <option value="pt-PT">Portugal (Portuguese)</option>
                <option value="en-PH">Philippines (English)</option>
                <option value="ru-RU">Russia (Russian)</option>
                <option value="ar-SA">Saudi Arabia (Arabic)</option>
                <option value="en-ZA">South Africa (English)</option>
                <option value="es-ES">Spain (Spanish)</option>
                <option value="sv-SE">Sweden (Swedish)</option>
                <option value="fr-CH">Switzerland (French)</option>
                <option value="de-CH">Switzerland (German)</option>
                <option value="zh-TW">Taiwan (Traditional Chinese)</option>
                <option value="tr-TR">Turkey (Turkish)</option>
                <option value="en-GB">United Kingdom (English)</option>
                <option value="en-US" selected>United States (English)</option>
                <option value="es-US">United States (Spanish)</option>
            </select>
        </p>
        <p style="margin-left: 20px">Safe search: 
            <select id="safesearch">
                <option value="moderate" selected>Moderate</option>
                <option value="strict">Strict</option>
                <option value="off">off</option>
            </select>
        </p>
    </div>
</div>
```

## <a name="add-search-options-to-the-webpage"></a>Adicionar opções de pesquisa à página web

A aplicação esconde as listas num desmontável `<div>` que é controlado pela ligação de opções de consulta. Quando clica no link de opções de consulta, o `<div>` expande para que possa ver e modificar as opções de consulta. Se clicar na ligação de opções de consulta novamente, o `<div>` colapso e está escondido. O seguinte corte mostra o manipulador de opções de `onclick` consulta. O manipulador controla se o `<div>` é expandido ou colapsado. Adicione este manipulador à `<script>` secção. O manipulador é utilizado por todas as `<div>` secções desmontáveis na demonstração.

```javascript
// Contains the toggle state of divs.
var divToggleMap = {};  // divToggleMap['foo'] = 0;  // 1 = show, 0 = hide


// Toggles between showing and hiding the specified div.
function expandCollapse(divToToggle) {
    var div = document.getElementById(divToToggle);

    if (divToggleMap[divToToggle] == 1) {   // if div is expanded
        div.style.display = "none";
        divToggleMap[divToToggle] = 0;
    }
    else {                                  // if div is collapsed
        div.style.display = "inline-block";
        divToggleMap[divToToggle] = 1;
    }
}
```

## <a name="call-the-onclick-handler"></a>Ligue para o `onclick` manipulador

Adicione o `"Get insights"` seguinte botão abaixo das opções `<div>` no corpo. O botão permite-lhe iniciar a chamada. Quando o botão é clicado, o cursor é alterado para o cursor de espera giratório, e o `onclick` manipulador é chamado.

```html
<p><input type="button" id="query" value="Get insights" onclick="document.body.style.cursor='wait'; handleQuery()" /></p>
```

Adicione o manipulador do `onclick` botão, `handleQuery()` à `<script>` etiqueta.

## <a name="handle-the-query"></a>Lidar com a consulta

O manipulador `handleQuery()` garante que a chave de subscrição está presente e tem 32 caracteres de comprimento, e que uma imagem é selecionada. Também limpa todas as informações relativas a uma consulta anterior. Depois, chama a `sendRequest()` função para fazer a chamada.

```javascript
function handleQuery() {
    var subscriptionKey = document.getElementById('key').value;

    // Make sure user provided a subscription key and image.
    // For this demo, the user provides the key but typically you'd
    // get it from secured storage.
    if (subscriptionKey.length !== 32) {
        alert("Subscription key length is not valid. Enter a valid key.");
        document.getElementById('key').focus();
        return;
    }

    var imagePath = document.getElementById('uploadImage').value;

    if (imagePath.length === 0)
    {
        alert("Please select an image to upload.");
        document.getElementById('uploadImage').focus();
        return;
    }

    var responseDiv = document.getElementById('responseSection');

    // Clear out the response from the last query.
    while (responseDiv.childElementCount > 0) {
        responseDiv.removeChild(responseDiv.lastChild);
    }

    // Send the request to Bing to get insights about the image.
    var f = document.getElementById('uploadImage').files[0];
    sendRequest(f, subscriptionKey);
}
```

## <a name="send-the-search-request"></a>Enviar o pedido de pesquisa

A `sendRequest()` função forma o URL de ponto final, define o `Ocp-Apim-Subscription-Key` cabeçalho para a chave de subscrição, anexa o binário da imagem para carregar, especifica o manipulador de resposta e faz a chamada:

```javascript
function sendRequest(file, key) {
    var market = document.getElementById('mkt').value;
    var safeSearch = document.getElementById('safesearch').value;
    var baseUri = `https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=${market}&safesearch=${safeSearch}`;

    var form = new FormData();
    form.append("image", file);

    var request = new XMLHttpRequest();

    request.open("POST", baseUri);
    request.setRequestHeader('Ocp-Apim-Subscription-Key', key);
    request.addEventListener('load', handleResponse);
    request.send(form);
}
```

## <a name="get-and-handle-the-api-response"></a>Obter e lidar com a resposta da API

A `handleResponse()` função lida com a resposta da chamada para a Procura Visual de Bing. Se a chamada for bem-sucedida, a função analisa a resposta JSON nas tags individuais, as quais contêm as informações. Em seguida, adiciona os resultados da pesquisa à página. A aplicação cria então um desmontável `<div>` para cada tag para gerir a quantidade de dados que são apresentados. Adicione o manipulador à `<script>` secção.

```javascript
function handleResponse() {
    if(this.status !== 200){
        alert("Error calling Bing Visual Search. See console log for details.");
        console.log(this.responseText);
        return;
    }

    var tags = parseResponse(JSON.parse(this.responseText));
    var h4 = document.createElement('h4');
    h4.textContent = 'Bing internet search results';
    document.getElementById('responseSection').appendChild(h4);
    buildTagSections(tags);

    document.body.style.cursor = 'default'; // reset the wait cursor set by query insights button
}
```

### <a name="parse-the-response"></a>Analisar a resposta

A `parseResponse` função converte a resposta JSON num objeto dicionário através da iterativa através `json.tags` de .

```javascript
function parseResponse(json) {
    var dict = {};

    for (var i =0; i < json.tags.length; i++) {
        var tag = json.tags[i];

        if (tag.displayName === '') {
            dict['Default'] = JSON.stringify(tag);
        }
        else {
            dict[tag.displayName] = JSON.stringify(tag);
        }
    }

    return(dict);
}
```

### <a name="build-a-tag-section"></a>Construir uma secção de etiquetas

A `buildTagSections()` função itera através das tags JSON analisadas e chama a `buildDiv()` função de construir uma `<div>` para cada tag. Cada tag é apresentada como um link. Quando o link é clicado, a etiqueta expande-se, mostrando os insights associados à etiqueta. Clicar novamente no link faz com que a secção desmorone.

```javascript
function buildTagSections(tags) {
    for (var tag in tags) {
        if (tags.hasOwnProperty(tag)) {
            var tagSection = buildDiv(tags, tag);
            document.getElementById('responseSection').appendChild(tagSection);
        }
    }  
}

function buildDiv(tags, tag) {
    var tagSection = document.createElement('div');
    tagSection.setAttribute('class', 'subSection');

    var link = document.createElement('a');
    link.setAttribute('href', '#');
    link.setAttribute('style', 'float: left;')
    link.text = tag;
    tagSection.appendChild(link);

    var contentDiv = document.createElement('div');
    contentDiv.setAttribute('id', tag);
    contentDiv.setAttribute('style', 'clear: left;')
    contentDiv.setAttribute('class', 'section');
    tagSection.appendChild(contentDiv);

    link.setAttribute('onclick', `expandCollapse("${tag}")`);
    divToggleMap[tag] = 0;  // 1 = show, 0 = hide

    addDivContent(contentDiv, tag, tags[tag]);

    return tagSection;
}
```

## <a name="display-the-search-results-in-the-webpage"></a>Mostrar os resultados da pesquisa na página web

A `buildDiv()` função chama a `addDivContent` função de construir o conteúdo do desmontável de cada tag `<div>` .

Os conteúdos das tags incluem o JSON das resposta das mesmas. Inicialmente, apenas são mostrados os primeiros 100 caracteres do JSON, mas pode clicar na cadeia JSON para mostrar todos os JSON. Se clicar outra vez, a cadeia JSON retrai-se novamente para os cem carateres.

Em seguida, adicione os tipos de ações que estão na tag. Para cada tipo de ação, ligue para as funções adequadas para adicionar os seus insights:

```javascript
function addDivContent(div, tag, json) {

    // Adds the first 100 characters of the json that contains
    // the tag's data. The user can click the text to show the
    // full json. They can click it again to collapse the json.
    var para = document.createElement('p');
    para.textContent = String(json).substr(0, 100) + '...';
    para.setAttribute('title', 'click to expand');
    para.setAttribute('style', 'cursor: pointer;')
    para.setAttribute('data-json', json);
    para.addEventListener('click', function(e) {
        var json = e.target.getAttribute('data-json');

        if (e.target.textContent.length <= 103) {  // 100 + '...'
            e.target.textContent = json;
            para.setAttribute('title', 'click to collapse');
        }
        else {
            para.textContent = String(json).substr(0, 100) + '...';
            para.setAttribute('title', 'click to expand');
        }
    });
    div.appendChild(para); 

    var parsedJson = JSON.parse(json);

    // Loop through all the actions in the tag and display them.
    for (var j = 0; j < parsedJson.actions.length; j++) {
        var action = parsedJson.actions[j];

        var subSectionDiv = document.createElement('div');
        subSectionDiv.setAttribute('class', 'subSection');
        div.appendChild(subSectionDiv);

        var h4 = document.createElement('h4');
        h4.innerHTML = action.actionType;
        subSectionDiv.appendChild(h4);

        if (action.actionType === 'ImageResults') {
            addImageWithWebSearchUrl(subSectionDiv, parsedJson.image, action);
        }
        else if (action.actionType === 'DocumentLevelSuggestions') {
            addRelatedSearches(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'RelatedSearches') {
            addRelatedSearches(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'PagesIncluding') {
            addPagesIncluding(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'VisualSearch') {
            addRelatedImages(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'Recipes') {
            addRecipes(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'ShoppingSources') {
            addShopping(subSectionDiv, action.data.offers);
        }
        else if (action.actionType === 'ProductVisualSearch') {
            addProducts(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'TextResults') {
            addTextResult(subSectionDiv, action);
        }
        else if (action.actionType === 'Entity') {
            addEntity(subSectionDiv, action);
        }
    }
}
```

## <a name="display-insights-for-different-actions"></a>Mostrar insights para diferentes ações

As seguintes funções exibem insights para diferentes ações. As funções fornecem uma imagem clicável ou um link clicável que o envia para uma página web com mais informações sobre a imagem. Esta página é hospedada por Bing.com ou pelo site original da imagem. Nem todos os dados dos insights são apresentados nesta aplicação. Para ver todos os campos disponíveis para obter uma visão, consulte a referência [Imagens - Procura Visual.](https://aka.ms/bingvisualsearchreferencedoc)

> [!NOTE]
> Há uma quantidade mínima de informação que deve apresentar na página. Consulte os [requisitos de utilização e visualização](../bing-web-search/use-display-requirements.md) da API de pesquisa de Bing para obter mais.

### <a name="relatedimages-insights"></a>Insights de Imagens Relacionadas

A `addRelatedImages()` função cria um título para cada um dos websites que hospedam a imagem relacionada, iterando através da lista de `RelatedImages` ações, e anexando uma `<img>` etiqueta para o exterior para `<div>` cada:

```javascript
    function addRelatedImages(div, images) {
        var length = (images.length > 10) ? 10 : images.length;

        // Set the title to the website that hosts the image. The title displays
        // when the user hovers over the image.

        // Make the image clickable. If the user clicks the image, they're taken
        // to the image in Bing.com.

        for (var j = 0; j < length; j++) {
            var img = document.createElement('img');
            img.setAttribute('src', images[j].thumbnailUrl + '&w=120&h=120');
            img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
            img.setAttribute('title', images[j].hostPageDisplayUrl);
            img.setAttribute('data-webSearchUrl', images[j].webSearchUrl)

            img.addEventListener('click', function(e) {
                var url = e.target.getAttribute('data-webSearchUrl');
                window.open(url, 'foo');
            })

            div.appendChild(img);
        }
    }
```

### <a name="pagesincluding-insights"></a>PáginasIncluindo insights

A `addPagesIncluding()` função cria um link para cada um dos websites que hospedam a imagem carregada através da lista de `PagesIncluding` ações, e anexando uma `<img>` etiqueta para o exterior para `<div>` cada:

```javascript

    // Display links to the first 5 webpages that include the image.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addPagesIncluding(div, pages) {
        var length = (pages.length > 5) ? 5 : pages.length;

        for (var j = 0; j < length; j++) {
            var page = document.createElement('a');
            page.text = pages[j].name;
            page.setAttribute('href', pages[j].hostPageUrl);
            page.setAttribute('style', 'margin: 20px 20px 0 0');
            page.setAttribute('target', '_blank')
            div.appendChild(page);

            div.appendChild(document.createElement('br'));
        }
    }
```

### <a name="relatedsearches-insights"></a>RelacionadosSearches insights

A `addRelatedSearches()` função cria um link para o site que hospeda a imagem, iterando através da lista de `RelatedSearches` ações e anexando uma `<img>` etiqueta para o exterior para `<div>` cada:

```javascript

    // Display the first 10 related searches. Include a link with the image
    // that when clicked, takes the user to Bing.com and displays the 
    // related search results.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addRelatedSearches(div, relatedSearches) {
        var length = (relatedSearches.length > 10) ? 10 : relatedSearches.length;

        for (var j = 0; j < length; j++) {
            var childDiv = document.createElement('div');
            childDiv.setAttribute('class', 'stackLink');
            div.appendChild(childDiv);

            var img = document.createElement('img');
            img.setAttribute('src', relatedSearches[j].thumbnail.url + '&w=120&h=120');
            img.setAttribute('style', 'margin: 20px 20px 0 0;');
            childDiv.appendChild(img);

            var relatedSearch = document.createElement('a');
            relatedSearch.text = relatedSearches[j].displayText;
            relatedSearch.setAttribute('href', relatedSearches[j].webSearchUrl);
            relatedSearch.setAttribute('target', '_blank');
            childDiv.appendChild(relatedSearch);

        }
    }
```

### <a name="recipes-insights"></a>Insights de receitas

A `addRecipes()` função cria um link para cada uma das receitas devolvidas através da lista de `Recipes` ações, e anexando uma `<img>` etiqueta para o exterior para `<div>` cada:

```javascript
    // Display links to the first 10 recipes. Include the recipe's rating,
    // if available.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addRecipes(div, recipes) {
        var length = (recipes.length > 10) ? 10 : recipes.length;

        for (var j = 0; j < length; j++) {
            var para = document.createElement('p');

            var recipe = document.createElement('a');
            recipe.text = recipes[j].name;
            recipe.setAttribute('href', recipes[j].url);
            recipe.setAttribute('style', 'margin: 20px 20px 0 0');
            recipe.setAttribute('target', '_blank')
            para.appendChild(recipe);

            if (recipes[j].hasOwnProperty('aggregateRating')) {
                var span = document.createElement('span');
                span.textContent = 'rating: ' + recipes[j].aggregateRating.text;
                para.appendChild(span);
            }

            div.appendChild(para);
        }
    }
```

### <a name="shopping-insights"></a>Insights de compras

A `addShopping()` função cria um link para quaisquer resultados de compras devolvidos, iterando através da lista de `RelatedImages` ações, e anexando uma `<img>` etiqueta para o exterior para `<div>` cada:

```javascript
    // Display links for the first 10 shopping offers.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addShopping(div, offers) {
        var length = (offers.length > 10) ? 10 : offers.length;

        for (var j = 0; j < length; j++) {
            var para = document.createElement('p');

            var offer = document.createElement('a');
            offer.text = offers[j].name;
            offer.setAttribute('href', offers[j].url);
            offer.setAttribute('style', 'margin: 20px 20px 0 0');
            offer.setAttribute('target', '_blank')
            para.appendChild(offer);

            var span = document.createElement('span');
            span.textContent = 'by ' + offers[j].seller.name + ' | ' + offers[j].price + ' ' + offers[j].priceCurrency;
            para.appendChild(span);

            div.appendChild(para);
        }
    }
```

### <a name="products-insights"></a>Insights de produtos

A `addProducts()` função cria um link para quaisquer resultados de produtos devolvidos, iterando através da lista de `Products` ações, e anexando uma `<img>` etiqueta para o exterior para `<div>` cada:

```javascript

    // Display the first 10 related products. Display a clickable image of the
    // product that takes the user to Bing.com search results for the product.
    // If there are any offers associated with the product, provide links to the offers.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addProducts(div, products) {
        var length = (products.length > 10) ? 10 : products.length;

        for (var j = 0; j < length; j++) {
            var childDiv = document.createElement('div');
            childDiv.setAttribute('class', 'stackLink');
            div.appendChild(childDiv);

            var img = document.createElement('img');
            img.setAttribute('src', products[j].thumbnailUrl + '&w=120&h=120');
            img.setAttribute('title', products[j].name);
            img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
            img.setAttribute('data-webSearchUrl', products[j].webSearchUrl)
            img.addEventListener('click', function(e) {
                var url = e.target.getAttribute('data-webSearchUrl');
                window.open(url, 'foo');
            })
            childDiv.appendChild(img);

            if (products[j].insightsMetadata.hasOwnProperty('aggregateOffer')) {
                if (products[j].insightsMetadata.aggregateOffer.offerCount > 0) {
                    var offers = products[j].insightsMetadata.aggregateOffer.offers;

                    // Show all the offers. Not all markets provide links to offers.
                    for (var i = 0; i < offers.length; i++) {  
                        var para = document.createElement('p');

                        var offer = document.createElement('a');
                        offer.text = offers[i].name;
                        offer.setAttribute('href', offers[i].url);
                        offer.setAttribute('style', 'margin: 20px 20px 0 0');
                        offer.setAttribute('target', '_blank')
                        para.appendChild(offer);

                        var span = document.createElement('span');
                        span.textContent = 'by ' + offers[i].seller.name + ' | ' + offers[i].price + ' ' + offers[i].priceCurrency;
                        para.appendChild(span);

                        childDiv.appendChild(para);
                    }
                }
                else {  // Otherwise, just show the lowest price that Bing found.
                    var offer = products[j].insightsMetadata.aggregateOffer;

                    var para = document.createElement('p');
                    para.textContent = `${offer.name} | ${offer.lowPrice} ${offer.priceCurrency}`; 

                    childDiv.appendChild(para);
                }
            }
        }
    }
```

### <a name="textresult-insights"></a>Insights de resultados de texto

A `addTextResult()` função exibe qualquer texto que tenha sido reconhecido na imagem:

```javascript

    function addTextResult(div, action) {
        var text = document.createElement('p');
        text.textContent = action.displayName;
        div.appendChild(text);
    }
```

A `addEntity()` função exibe um link que leva o utilizador a Bing.com onde pode obter detalhes sobre o tipo de entidade na imagem, se algum for detetado:

```javascript
    // If the image is of a person, the tag might include an entity
    // action type. Display a link that takes the user to Bing.com
    // where they can get details about the entity.
    function addEntity(div, action) {
        var entity = document.createElement('a');
        entity.text = action.displayName;
        entity.setAttribute('href', action.webSearchUrl);
        entity.setAttribute('style', 'margin: 20px 20px 0 0');
        entity.setAttribute('target', '_blank');
        div.appendChild(entity);
    }
```

A `addImageWithWebSearchUrl()` função exibe uma imagem clicável para o `<div>` que leva o utilizador a pesquisar os resultados em Bing.com:

```javascript
    function addImageWithWebSearchUrl(div, image, action) {
        var img = document.createElement('img');
        img.setAttribute('src', image.thumbnailUrl + '&w=120&h=120');
        img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
        img.setAttribute('data-webSearchUrl', action.webSearchUrl);
        img.addEventListener('click', function(e) {
            var url = e.target.getAttribute('data-webSearchUrl');
            window.open(url, 'foo');
        })
        div.appendChild(img);
    }

```

## <a name="add-a-css-style"></a>Adicione um estilo CSS

Adicione a seguinte `<style>` secção à etiqueta para organizar o layout da página `<head>` web:

```html
        <style>

            .thumb {
                height: 75px;
                border: 1px solid #000;
            }

            .stackLink {
                width:180px;
                min-height:210px;
                display:inline-block;
            }
            .stackLink a {
                float:left;
                clear:left;
            }

            .section {
                float:left;
                display:none;
            }

            .subSection {
                clear:left;
                float:left;
            }

        </style>
```

## <a name="next-steps"></a>Passos seguintes

>[!div class="nextstepaction"]
> [Tutorial: Encontre imagens semelhantes de pesquisas anteriores usando ImageInsightsToken](./tutorial-visual-search-insights-token.md)
