---
title: 'Tutorial: Como carregar imagem - pesquisa Visual do Bing'
titleSuffix: Azure Cognitive Services
description: Discrimina o processo de carregamento de uma imagem para o Bing obter informações sobre a mesma e, depois, a análise e apresentação da resposta.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: scottwhi
ms.openlocfilehash: 188acaea134a00ca8e2412aafd8515ee26596590
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59049996"
---
# <a name="tutorial-upload-images-to-the-bing-visual-search-api"></a>Tutorial: Carregar imagens para a API de pesquisa Visual do Bing

A API de pesquisa Visual do Bing permite-lhe pesquisar na web para imagens similares àquelas que carrega. Utilize este tutorial para criar uma aplicação web que pode enviar uma imagem para a API e apresentar as informações que na página Web, ele retorna. Tenha em atenção que esta aplicação não adere a todos [requisitos de apresentação e utilização do Bing](../bing-web-search/use-display-requirements.md) para utilizar a API.

Encontrará o código-fonte completo para este exemplo com o tratamento de erros adicionais e anotações no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchUploadImage.html).

A aplicação de tutorial ilustra como:

> [!div class="checklist"]
> * Carregue uma imagem para a API de pesquisa Visual do Bing
> * Apresentar os resultados da pesquisa de imagem num aplicativo web
> * Explore as informações de diferentes fornecidas pela API

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-structure-the-webpage"></a>Criar e estruturar a página Web

Crie uma página HTML que envia uma imagem para a API de pesquisa Visual do Bing, recebe informações e apresenta-os. No seu editor favorito ou IDE, crie um ficheiro denominado "uploaddemo.html". Adicione a seguinte estrutura HTML básica para o ficheiro:

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

Divida a página numa seção de pedido, em que o utilizador fornece todas as informações necessárias para o pedido, e uma seção de resposta em que as informações são apresentadas. Adicione as seguintes `<div>` com as etiquetas para o `<body>`. O `<hr>` marca visualmente separa a secção de pedido da secção de resposta:

```html
<div id="requestSection"></div>
<hr />
<div id="responseSection"></div>
```

Adicionar uma `<script>` Etiquetar para o `<head>` tag para conter o JavaScript para a aplicação:

```html
<script>
<\script>
```

## <a name="get-the-upload-file"></a>Obter o ficheiro de carregamento

Para permitir que o utilizador selecionar uma imagem para carregar, a aplicação utiliza a `<input>` marca com o atributo de tipo definido como `file`. A interface do Usuário precisa para torná-lo claro que a aplicação utiliza o Bing para obter os resultados de pesquisa.

Adicione as seguintes `<div>` para o `requestSection` `<div>`. A entrada de ficheiro aceita um ficheiro individual de qualquer tipo de imagem (por exemplo, .jpg, .gif, .png). O evento `onchange` especifica o processador que é chamado quando um utilizador seleciona um ficheiro.

O `<output>` marca é usada para exibir uma miniatura da imagem selecionada:

```html
<div>
    <p>Select image to get insights from Bing:
        <input type="file" accept="image/*" id="uploadImage" name="files[]" size=40 onchange="handleFileSelect('uploadImage')" />
    </p>

    <output id="thumbnail"></output>
</div>
```

## <a name="create-a-file-handler"></a>Criar um manipulador de arquivo

Crie uma função de manipulador, que pode ler na imagem que pretende carregar. Durante a iteração por meio de arquivos no `FileList` de objeto, o manipulador deve certificar-se de que o ficheiro selecionado é um ficheiro de imagem e de que o seu tamanho é 1 MB ou menos. Se a imagem é maior, tem de reduzir seu tamanho antes de o carregar. Por último, o manipulador exibe uma miniatura da imagem:

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

## <a name="add-and-store-a-subscription-key"></a>Adicionar e armazenar uma chave de subscrição

A aplicação requer uma chave de assinatura para fazer chamadas à API de pesquisa Visual do Bing. Para este tutorial, terá de fornecê-lo na interface do Usuário. Adicione as seguintes `<input>` Etiquetar (com o atributo de tipo definido como texto) para o `<body>` logo abaixo do ficheiro `<output>` etiqueta:

```html
    <div>
        <p>Subscription key: 
            <input type="text" id="key" name="subscription" size=40 maxlength="32" />
        </p>
    </div>
```

Com a imagem e a chave de assinatura, pode fazer a chamada para pesquisa Visual do Bing para obter informações sobre a imagem. Neste tutorial, a chamada utiliza o mercado de predefinição (`en-us`) e o valor de pesquisa segura (`moderate`).

Esta aplicação tem uma opção para alterar esses valores. Adicione as seguintes `<div>` abaixo da chave de subscrição `<div>`. O aplicativo usa um `<select>` etiqueta para fornecer uma lista pendente para o mercado e os valores de pesquisa segura. Ambas as listas de apresentam o valor predefinido.

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
                <option value="zh-HK">Hong Kong (Traditional Chinese)</option>
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

## <a name="add-search-options-to-the-webpage"></a>Adicionar opções de pesquisa para a página Web

O aplicativo oculta as listas numa recolhível `<div>` que é controlado através da ligação de opções de consulta. Ao clicar no link de opções de consulta, o `<div>` se expande para que possa ver e modificar as opções de consulta. Se clicar na ligação de opções de consulta mais uma vez, o `<div>` fecha e está oculto. O fragmento seguinte mostra a ligação de opções de consulta `onclick` manipulador. Os controles de manipulador se o `<div>` está expandido ou fechado. Adicionar esse manipulador para o `<script>` secção. O manipulador é utilizado por todos os recolhível `<div>` secções na demonstração.

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

## <a name="call-the-onclick-handler"></a>Chamar o `onclick` manipulador

Adicione as seguintes `"Get insights"` botão abaixo as opções `<div>` no corpo. O botão permite-lhe iniciar a chamada. Quando o botão é clicado, o cursor é alterado para o cursor de espera de rotação e o `onclick` manipulador é chamado.

```html
<p><input type="button" id="query" value="Get insights" onclick="document.body.style.cursor='wait'; handleQuery()" /></p>
```

Adicionar o botão `onclick` manipulador, `handleQuery()` para o `<script>` marca.

## <a name="handle-the-query"></a>Lidar com a consulta

O manipulador `handleQuery()` garante que a chave de subscrição está presente e é de 32 carateres de comprimento e que uma imagem está selecionada. Também limpa todas as informações relativas a uma consulta anterior. Em seguida, ele chama o `sendRequest()` função para fazer a chamada.

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

O `sendRequest()` função formata a URL do ponto final, conjuntos de `Ocp-Apim-Subscription-Key` cabeçalho para a chave de subscrição, acrescenta o binário da imagem para carregar, especifica o manipulador de resposta e faz a chamada:

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

## <a name="get-and-handle-the-api-response"></a>Obter e tratar da resposta de API

O `handleResponse()` função processa a resposta da chamada para pesquisa Visual do Bing. Se a chamada for bem-sucedida, a função analisa a resposta JSON nas tags individuais, as quais contêm as informações. Em seguida, ele adiciona os resultados da pesquisa para a página. O aplicativo, em seguida, cria um recolhível `<div>` para cada etiqueta gerir a quantidade de dados é apresentado. Adicione o manipulador para o `<script>` secção.

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

O `parseResponse` função converte a resposta JSON num objeto de dicionário fazendo a iteração `json.tags`.

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

### <a name="build-a-tag-section"></a>Crie uma secção de etiquetas

O `buildTagSections()` função percorre a analisado etiquetas JSON e chama o `buildDiv()` função para criar um `<div>` para cada marca. Cada etiqueta é apresentada como um link. Quando a ligação é clicada, a marca expande, mostrando as informações associadas com a marca. Clicar no link novamente faz com que a seção recolher.

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

## <a name="display-the-search-results-in-the-webpage"></a>Apresentar os resultados da pesquisa na Web

O `buildDiv()` chamadas de função a `addDivContent` da recolhível função para criar o conteúdo de cada marca `<div>`.

Os conteúdos das tags incluem o JSON das resposta das mesmas. Inicialmente, apenas os primeiros 100 caracteres do JSON são apresentados, mas pode clicar na cadeia de caracteres do JSON para mostrar todos os o JSON. Se clicar outra vez, a cadeia JSON retrai-se novamente para os cem carateres.

Em seguida, adicione os tipos de ações que estão na tag. Para cada tipo de ação, chame as funções adequadas para adicionar o seu insights:

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

## <a name="display-insights-for-different-actions"></a>Apresentam informações para ações diferentes

As funções seguintes apresentam informações para ações diferentes. As funções de fornecem uma imagem clicável ou um link clicável que envia para uma página Web com mais informações sobre a imagem. Esta página é hospedada por Bing.com ou Web site da imagem original. Nem todos os dados do insights é apresentado nesta aplicação. Para ver todos os campos disponíveis para uma informação, consulte a [imagens - pesquisa Visual](https://aka.ms/bingvisualsearchreferencedoc) referência.

> [!NOTE]
> Há uma quantidade mínima de informações de informações que deve apresentar a página de início. Consulte a [a API de pesquisa do Bing utilizar e apresentam os requisitos](../bing-web-search/use-display-requirements.md) para obter mais informações.

### <a name="relatedimages-insights"></a>RelatedImages insights

O `addRelatedImages()` função cria um título para cada um dos sites que aloja a imagem relacionada com a iteração através da lista de `RelatedImages` ações e a acrescentar um `<img>` marcar para o exterior `<div>` para cada:

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

### <a name="pagesincluding-insights"></a>PagesIncluding insights

O `addPagesIncluding()` função cria uma ligação para cada um dos sites que aloja a imagem carregada fazendo a iteração a lista de `PagesIncluding` ações e a acrescentar um `<img>` marcar para o exterior `<div>` para cada:

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

### <a name="relatedsearches-insights"></a>RelatedSearches insights

O `addRelatedSearches()` função cria uma ligação para o Web site que aloja a imagem, fazendo a iteração a lista de `RelatedSearches` ações e acrescentando uma `<img>` marcar para o exterior `<div>` para cada:

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

### <a name="recipes-insights"></a>Informações de receitas

O `addRecipes()` função cria uma ligação para cada uma das receitas devolvidas fazendo a iteração a lista de `Recipes` ações e a acrescentar um `<img>` marcar para o exterior `<div>` para cada:

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

O `addShopping()` função cria uma ligação para qualquer devolveu resultados compras fazendo a iteração a lista de `RelatedImages` ações e a acrescentar um `<img>` marcar para o exterior `<div>` para cada:

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

O `addProducts()` função cria uma ligação para qualquer devolvidos resultados de produtos com a iteração através da lista de `Products` ações e a acrescentar um `<img>` marcar para o exterior `<div>` para cada:

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

### <a name="textresult-insights"></a>TextResult insights

O `addTextResult()` função apresenta qualquer texto que foi reconhecido na imagem:

```javascript

    function addTextResult(div, action) {
        var text = document.createElement('p');
        text.textContent = action.displayName;
        div.appendChild(text);
    }
```

O `addEntity()` função apresenta um link que leva o usuário para Bing.com onde pode obter detalhes sobre o tipo de entidade na imagem, se qualquer foi detetado:

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

O `addImageWithWebSearchUrl()` função exibe uma imagem clicável para o `<div>` que direciona o utilizador para os resultados no Bing.com da pesquisa:

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

Adicione as seguintes `<style>` secção para o `<head>` etiquetas para organizar o layout da página Web:

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

## <a name="next-steps"></a>Passos Seguintes

* [Tutorial: Encontrar imagens semelhantes de pesquisas anteriores com ImageInsightsToken](./tutorial-visual-search-insights-token.md).
