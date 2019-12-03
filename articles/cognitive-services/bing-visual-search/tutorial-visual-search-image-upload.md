---
title: 'Tutorial: Como carregar uma imagem - Pesquisa Visual do Bing'
titleSuffix: Azure Cognitive Services
description: Discrimina o processo de carregamento de uma imagem para o Bing obter informações sobre a mesma e, depois, a análise e apresentação da resposta.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 11/29/2019
ms.author: scottwhi
ms.openlocfilehash: 84a8219221525400a9d3241c2f183d24344c2f6a
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689186"
---
# <a name="tutorial-upload-images-to-the-bing-visual-search-api"></a>Tutorial: carregar imagens para o API da Pesquisa Visual do Bing

O API da Pesquisa Visual do Bing permite pesquisar imagens da Web semelhantes àquelas que você carrega. Use este tutorial para criar um aplicativo Web que pode enviar uma imagem para a API e exibir as informações que ele retorna na página da Web. Observe que esse aplicativo não obedece a todos os [requisitos de exibição e uso do Bing](../bing-web-search/use-display-requirements.md) para usar a API.

Você pode encontrar o código-fonte completo para este exemplo com o tratamento de erros e as anotações adicionais no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchUploadImage.html).

A aplicação de tutorial ilustra como:

> [!div class="checklist"]
> * Carregar uma imagem no API da Pesquisa Visual do Bing
> * Exibir resultados da pesquisa de imagem em um aplicativo Web
> * Explore as diferentes informações fornecidas pela API

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-structure-the-webpage"></a>Criar e estruturar a página da Web

Crie uma página HTML que envia uma imagem para a API da Pesquisa Visual do Bing, recebe informações e as exibe. Em seu editor favorito ou IDE, crie um arquivo chamado "uploaddemo. html". Adicione a seguinte estrutura HTML básica ao arquivo:

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

Divida a página em uma seção de solicitação, em que o usuário fornece todas as informações necessárias para a solicitação e uma seção de resposta em que as ideias são exibidas. Adicione as seguintes marcas de `<div>` à `<body>`. A marca de `<hr>` separa visualmente a seção de solicitação da seção de resposta:

```html
<div id="requestSection"></div>
<hr />
<div id="responseSection"></div>
```

Adicione uma marca de `<script>` à marca de `<head>` para conter o JavaScript para o aplicativo:

```html
<script>
<\script>
```

## <a name="get-the-upload-file"></a>Obter o arquivo de carregamento

Para permitir que o usuário selecione uma imagem para carregar, o aplicativo usa a marca `<input>` com o atributo type definido como `file`. A interface do usuário precisa deixar claro que o aplicativo usa o Bing para obter os resultados da pesquisa.

Adicione as `<div>` a seguir ao `<div>`de `requestSection`. A entrada de ficheiro aceita um ficheiro individual de qualquer tipo de imagem (por exemplo, .jpg, .gif, .png). O evento `onchange` especifica o processador que é chamado quando um utilizador seleciona um ficheiro.

A marca de `<output>` é usada para exibir uma miniatura da imagem selecionada:

```html
<div>
    <p>Select image to get insights from Bing:
        <input type="file" accept="image/*" id="uploadImage" name="files[]" size=40 onchange="handleFileSelect('uploadImage')" />
    </p>

    <output id="thumbnail"></output>
</div>
```

## <a name="create-a-file-handler"></a>Criar um manipulador de arquivo

Crie uma função de manipulador que possa ler a imagem que você deseja carregar. Durante a iteração pelos arquivos no objeto `FileList`, o manipulador deve verificar se o arquivo selecionado é um arquivo de imagem e se seu tamanho é 1 MB ou menos. Se a imagem for maior, você deverá reduzir seu tamanho antes de carregá-la. Por fim, o manipulador exibe uma miniatura da imagem:

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

## <a name="add-and-store-a-subscription-key"></a>Adicionar e armazenar uma chave de assinatura

O aplicativo requer uma chave de assinatura para fazer chamadas para o API da Pesquisa Visual do Bing. Para este tutorial, você o fornecerá na interface do usuário. Adicione a seguinte marcação de `<input>` (com o atributo type definido como Text) ao `<body>` logo abaixo da marca `<output>` do arquivo:

```html
    <div>
        <p>Subscription key: 
            <input type="text" id="key" name="subscription" size=40 maxlength="32" />
        </p>
    </div>
```

Com a imagem e a chave de assinatura, você pode fazer a chamada para Pesquisa Visual do Bing para obter informações sobre a imagem. Neste tutorial, a chamada usa o mercado padrão (`en-us`) e o valor de pesquisa segura (`moderate`).

Este aplicativo tem uma opção para alterar esses valores. Adicione os `<div>` a seguir abaixo da chave de assinatura `<div>`. O aplicativo usa uma marca de `<select>` para fornecer uma lista suspensa para valores de pesquisa de mercado e seguros. Ambas as listas exibem o valor padrão.

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

## <a name="add-search-options-to-the-webpage"></a>Adicionar opções de pesquisa à página da Web

O aplicativo oculta as listas em uma `<div>` recolhível controlada pelo link opções de consulta. Quando você clica no link opções de consulta, o `<div>` é expandido para que você possa ver e modificar as opções de consulta. Se você clicar no link opções de consulta novamente, o `<div>` será recolhido e ficará oculto. O trecho a seguir mostra o manipulador de `onclick` do link de opções de consulta. O manipulador controla se a `<div>` é expandida ou recolhida. Adicione esse manipulador à seção `<script>`. O manipulador é usado por todas as seções de `<div>` recolhíveis na demonstração.

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

## <a name="call-the-onclick-handler"></a>Chamar o manipulador de `onclick`

Adicione o seguinte botão de `"Get insights"` abaixo do `<div>` de opções no corpo. O botão permite que você inicie a chamada. Quando o botão é clicado, o cursor é alterado para o cursor de espera girado e o manipulador de `onclick` é chamado.

```html
<p><input type="button" id="query" value="Get insights" onclick="document.body.style.cursor='wait'; handleQuery()" /></p>
```

Adicione o manipulador de `onclick` do botão `handleQuery()` à marca `<script>`.

## <a name="handle-the-query"></a>Manipular a consulta

O manipulador `handleQuery()` garante que a chave de assinatura esteja presente e tenha 32 caracteres de comprimento e que uma imagem seja selecionada. Também limpa todas as informações relativas a uma consulta anterior. Posteriormente, ele chama a função `sendRequest()` para fazer a chamada.

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

## <a name="send-the-search-request"></a>Enviar a solicitação de pesquisa

A função `sendRequest()` formata a URL do ponto de extremidade, define o cabeçalho `Ocp-Apim-Subscription-Key` para a chave de assinatura, acrescenta o binário da imagem a ser carregada, especifica o manipulador de resposta e faz a chamada:

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

## <a name="get-and-handle-the-api-response"></a>Obter e manipular a resposta da API

A função `handleResponse()` manipula a resposta da chamada para Pesquisa Visual do Bing. Se a chamada for bem-sucedida, a função analisa a resposta JSON nas tags individuais, as quais contêm as informações. Em seguida, ele adiciona os resultados da pesquisa à página. Em seguida, o aplicativo cria um `<div>` recolhível para cada marca para gerenciar a quantidade de dados exibida. Adicione o manipulador à seção `<script>`.

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

A função `parseResponse` converte a resposta JSON em um objeto Dictionary Iterando por meio de `json.tags`.

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

### <a name="build-a-tag-section"></a>Criar uma seção de marca

A função `buildTagSections()` itera pelas marcas JSON analisadas e chama a função `buildDiv()` para criar uma `<div>` para cada marca. Cada marca é exibida como um link. Quando o link é clicado, a marca é expandida, mostrando as informações associadas à marca. Clicar no link novamente faz com que a seção seja recolhida.

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

## <a name="display-the-search-results-in-the-webpage"></a>Exibir os resultados da pesquisa na página da Web

A função `buildDiv()` chama a função `addDivContent` para criar o conteúdo da `<div>`recolhível de cada marca.

Os conteúdos das tags incluem o JSON das resposta das mesmas. Inicialmente, somente os primeiros 100 caracteres do JSON são mostrados, mas você pode clicar na cadeia de caracteres JSON para mostrar todos os JSON. Se clicar outra vez, a cadeia JSON retrai-se novamente para os cem carateres.

Em seguida, adicione os tipos de ações que estão na tag. Para cada tipo de ação, chame as funções apropriadas para adicionar suas informações:

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

## <a name="display-insights-for-different-actions"></a>Exibir informações para ações diferentes

As funções a seguir exibem informações para ações diferentes. As funções fornecem uma imagem clicável ou um link clicável que o envia para uma página da Web com mais informações sobre a imagem. Essa página é hospedada por Bing.com ou pelo site original da imagem. Nem todos os dados de insights são exibidos neste aplicativo. Para ver todos os campos disponíveis para um insight, consulte a referência de [pesquisa visual de imagens](https://aka.ms/bingvisualsearchreferencedoc) .

> [!NOTE]
> Há uma quantidade mínima de informações de insights que você deve exibir na página. Consulte os [requisitos de exibição e uso da API pesquisa do Bing](../bing-web-search/use-display-requirements.md) para obter mais informações.

### <a name="relatedimages-insights"></a>Informações do RelatedImages

A função `addRelatedImages()` cria um título para cada um dos sites que hospedam a imagem relacionada Iterando a lista de ações de `RelatedImages` e acrescentando uma marca de `<img>` à `<div>` externa para cada uma:

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

### <a name="pagesincluding-insights"></a>Informações do PagesIncluding

A função `addPagesIncluding()` cria um link para cada um dos sites que hospedam a imagem carregada Iterando pela lista de ações de `PagesIncluding` e acrescentando uma marca de `<img>` à `<div>` externa para cada uma:

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

### <a name="relatedsearches-insights"></a>Informações do RelatedSearches

A função `addRelatedSearches()` cria um link para o site que hospeda a imagem, iterando na lista de ações de `RelatedSearches` e acrescentando uma marca de `<img>` à `<div>` externa para cada uma:

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

### <a name="recipes-insights"></a>Análises de receitas

A função `addRecipes()` cria um link para cada uma das receitas retornadas Iterando pela lista de ações de `Recipes` e acrescentando uma marca de `<img>` à `<div>` externa para cada uma:

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

### <a name="shopping-insights"></a>Informações de compra

A função `addShopping()` cria um link para quaisquer resultados de compra retornados Iterando pela lista de ações de `RelatedImages` e acrescentando uma marca de `<img>` à `<div>` externa para cada:

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

### <a name="products-insights"></a>Informações de produtos

A função `addProducts()` cria um link para os resultados de produtos retornados Iterando pela lista de ações de `Products` e acrescentando uma marca de `<img>` à `<div>` externa para cada:

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

### <a name="textresult-insights"></a>Percepções de resultado

A função `addTextResult()` exibe qualquer texto reconhecido na imagem:

```javascript

    function addTextResult(div, action) {
        var text = document.createElement('p');
        text.textContent = action.displayName;
        div.appendChild(text);
    }
```

A função `addEntity()` exibe um link que leva o usuário para Bing.com, onde pode obter detalhes sobre o tipo de entidade na imagem, se algum foi detectado:

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

A função `addImageWithWebSearchUrl()` exibe uma imagem clicável para a `<div>` que leva o usuário para Pesquisar resultados em Bing.com:

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

## <a name="add-a-css-style"></a>Adicionar um estilo CSS

Adicione a seguinte seção `<style>` à marca de `<head>` para organizar o layout da página da Web:

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
> [Tutorial: localizar imagens semelhantes de pesquisas anteriores usando ImageInsightsToken](./tutorial-visual-search-insights-token.md)
